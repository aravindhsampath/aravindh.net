---
title: "Securing a public facing SSH server with three-factor auth(3FA)"
date: 2018-07-20T17:55:47+02:00
draft: true
featured_image: "/images/SSH_3fa.png"
description: "Let's configure OpenSSH, PAM, and Duo together to securely validate a user's identity even when coming from hostile public networks."
toc: false
tags: ["SSH", "CentOS", "Security", "MFA", "backup", "Duo", "sysadmin"]
---
<style>
p {text-indent: 8%;}
</style>

I administer a HPC cluster for a research group, and the primary entry point for our users to the cluster is via SSH. Our users are students and industrial collaborators who login to the cluster from all around the world. Keeping this server secure, and authenticating legitimate users and keeping the service reliable is our atmost priority. 

Catering to external users from different organisations poses a challenging environment because we cannot rely on a trusted network or a trusted device. This challenge means that we cannot trust any single credential our legitimate users provide for verification. Here are our different choices.

* User logs in with just a strong password - <font color="red">Key loggers | User writing passwords in Postit notes/Cloud sync notes.</font>
* User logs in with passphrase-less SSH keys - <font color="red">User device gets compromised and attacker gets unfettered access. </font>
* User has a SSH key and uses a password - <font color="red">User device gets compromised and attacker installs a key logger. </font>

<div class="dt center pt3 pb3">
  <div class="db dtc-ns v-mid-ns">
    <img src="/images/encrypted_key.png" alt="Encrypted private key" class="w-100 mw4 w3-ns" />
  </div>
  <div class="db dtc-ns v-mid ph2 pr0-ns pl3-ns">
    <p class="lh-copy">
      But, hey a passphrase encrypted private key on end-user's device would make it useless without the passphrase... True, but there is no way to enforce the use of passphrase other than creating a policy. We all know how that goes....
    </p>
  </div>
</div>


A key strategy(pun intended) to counter a compromised device is the use of an additional device/factor that the user must use to prove their identity. There are plenty of second factor implementations available at this time, and it is not my interest to help you choose. Pick one that works and the rest of this post still applies to you. I chose Duo, and here is why I did.

 1. It is a popular choice among many institutions and enterprises, so I know I'm not alone, and have to worry less about them suddenly going out of business.
 2. They are a paid service, and my institution pays for their service.
 3. A key reason why I did not choose the free & opensource FreeOTP is that I do NOT want to get in the business of sending our users text/SMS of their 2FA codes. Some of our users are in China and other users travel for conferences worldwide. It is easier to lean on Duo to make sure our users get the SMS than I losing some more hair.

It is easy to over-engineer this solution. But, I'm always reminded of this xkcd when trying to secure something that others use. 

<a href="https://xkcd.com/538/"><img class="pt1 pb1" src="/images/user_security_xkcd.png" class="w-100 f5 " alt="XKCD user security"></img></a>
<blockquote class="athelas ml0 mt0 pl4 black-90 bl bw2 b--blue">
    <p class="f5 f4-m f3-l i lh-copy measure mt0">
      There is no absolute security, but we can try harder to keep our systems safer than it is.
    </p>
    <cite class="f6 ttu tracked fs-normal">― Aravindh.</cite>
</blockquote>

Back to our business, we settled on Duo to provide an external factor.

Here is another problem that factored into this solution. It is the wild wild Internet and any public SSH server that accepts passwords immediately gets pounded on by bots and people who newly learnt about Metasploit and Kali Linux trying out all the default passwords and user passwords they obtained from prior leaks and attacks. 

<img class="pt1 pb1" src="/images/groupattack.jpg" class="w-100 f5 " alt="Brute Force attack"></img>

So, accepting passwords as the first factor is a 🙅‍♂️. We want our system to rely on SSH keys first, and then proceed to verify password, and then verify the external factor(Duo). This way the server does not have to listen to the brute-force attack on the internet and yet make the user type in something that they know. 
<ul class="list pl0 ml0 center mw6 ba b--light-silver br2">
    <li class="ph3 pv1 bb b--light-silver">Password - Something you know</li>
    <li class="ph3 pv1 bb b--light-silver">Private key - Something you have(same device)</li>
    <li class="ph3 pv1 bb b--light-silver">Duo Push - Something you have(different device)</li>
</ul>

I manage user accounts and their privileges using FreeIPA, which the opensource pre-runner for IDM in the RedHat family. The reason I'm stating this is that the PAM configuration we're going to see below pertains to SSSD which is what FreeIPA uses internally. 


<img class="pt1 pb1" src="/images/duo_3fa.png" class="w-100 f5 " alt="Duo 3FA"></img>
<p class="tc">Fig. 3FA user authentication workflow</p>

## Implementation
**Reference:**

1. [Duo documentation](https://duo.com/docs/duounix)
2. [PAM guide](http://wpollock.com/AUnix2/PAM-Help.htm)

Sign up for an account with Duo and get the three config parameters - Integration key, Secret key, API hostname.

Install prerequisites:
```bash
> yum install openssl-devel
> yum install pam-devel
```
Install Duo's pam module:
```bash
wget https://dl.duosecurity.com/duo_unix-latest.tar.gz
tar zxf duo_unix-latest.tar.gz
./configure --with-pam --prefix=/usr && make && sudo make install
make -C pam_duo semodule
make -C pam_duo semodule-install
```
Create a Duo config file with parameters obtained from Duo account signup, tell Duo users from which group needs to be passed through Duo, and which ones to not bother. I created a unix group - nomfa and added a single user to it, to make sure I can login via SSH to troubleshoot Duo configuration. This will be removed once I'm happy with the Duo setup.
```bash
cat <<EOF >> /etc/duo/pam_duo.conf
[duo]
; Duo integration key
ikey = <Your_integration key from Duo>
; Duo secret key
skey = <Your secret key from Duo>
; Duo API host
host = <Yourhostname from Duo>
; Send command for Duo Push authentication
;pushinfo = yes
groups = users,<group1, group2>,!nomfa guests
EOF
```
Make sure of the following config in `/etc/ssh/sshd_config` file along with your pre-made configuration. Adjust this according to your specific needs.
```bash
# Do not allow root to login over SSH.
PermitRootLogin no
# disable tunneled clear text passwords and empty passwords
PermitEmptyPasswords no
PasswordAuthentication no
# Enable key based authentication
PubkeyAuthentication yes
# Enable PAM
UsePAM yes
ChallengeResponseAuthentication yes
# Check public key first, then continue with PAM for password and Duo auth.
AuthenticationMethods publickey,keyboard-interactive
```

Next up, PAM. 
Here is how I lined up PAM chains on my CentOS 7 server. Your PAM configuration may look slightly different than below. Use the PAM guide in the references section to tailor the config to your needs. 

> Content of /etc/pam.d/sshd

Test it with dummy user accounts:
test_pass
test_key




We have < 100 external users from all around the world.
I have no control over their devices..

We want to secure this SSH server as much as we reasonably could. I came up with the following solution. 
We run a couple of FreeIPA servers as our identity service, which authenticates users on this SSH server via SSSD. 

Flow diagram like in Duo documentation.

Notes:
Root user is not allowed to login over SSH. Get a serial console through a secure network.
One additional user is configured in the "nomfa" group to be able to debug the setup while I'm tweaking the configuration.

Refer to this post for guarding a public SSH server efficiently using SSHGuard. 

Testing this system..

SSH check from websiteor the tool

