---
title: "Lean & fast self-hosted blog"
date: 2018-07-14T17:55:47+02:00
draft: false
featured_image: "/images/lean_fast.jpg"
description: "In the days of obese internet, this is my tale of building a fast blog that uses HTTP/2, TLS, compression, and Tachyons CSS."
toc: true
tags: ["blog", "static website", "Hugo", "Performance", "Tachyons"]
---
<style>
p {text-indent: 8%;}
</style>
## Internet obesity
Many websites we routinely visit are just too fat, loading huge amounts of data in the form of javascript files, unoptimized images, web fonts, ads etc. This is just plain inefficient. Take a look of a few sites that I recently visited...
<img class="pt3" src="/images/internet_obesity.png" class="w-100 f5 " alt="Internet Obesity"></img>
Source: My tests using online tool at https://tools.pingdom.com/
## Why self host?
Why not? It's fun.

Say no to walled gardens and own the Internet. 
## A new blog
So, when I wanted to build a blog for myself, I decided it must be simple, efficient, fast, modern, and yet look easy on the eyes of the reader. I thought about building a Staic Site Generator(SSG) myself for this purpose, but decided against it considering there are plenty of open source SSGs available already that fits my needs. I chose [Hugo](https://gohugo.io). The default theme ([Ananke](https://github.com/budparr/gohugo-theme-ananke)) is already built using my favourite functional CSS framework - [Tachyons](http://tachyons.io), So, I'm set from the get go.
<div class="dt center pt3 pb3">
  <div class="db dtc-ns v-mid-ns">
    <a href="http://tachyons.io"><img src="/images/tachyons_logo.png" alt="Tachyons.io logo" class="w-100 mw7 w5-ns" /></a>
  </div>
  <div class="db dtc-ns v-mid ph2 pr0-ns pl3-ns">
    <p class="lh-copy">
      I ❤ the minimalist, functional design system that is Tachyons. This website is my concoction of their <a href="http://tachyons.io/components/"> component examples</a>.  
    </p>
  </div>
</div>
Once I decided on the CMS and a design system, I spent an entire day of a weekend customizing the default theme to my liking. BTW, this website is available for your introspection as public Github repository.

## Making it public
Next step on the way is to host this beautiful website somewhere. I'm going to need a domain name, a Virtual Private Server(VPS) to run a webserver, DNS service, and a TLS certificate provider.

**Domain:** I purchased a domain through Google domains. 

**VPS:** A cloud VM on Vultr. 
<a href="https://www.vultr.com/pricing/"><img src="/images/vultr_vm.png" alt="Vultr VM" class="w-100 mt3" /></a>

**DNS :**

Google domains provide a free DNS service that probably is more than enough for personal websites. I chose to use a more business oriented DNS service provided by Google for its Google Cloud Platform customers - <a href="https://cloud.google.com/dns/">Google Cloud DNS</a>. Google describes this service as to offer a reliable, resilient, low-latency DNS serving from Google’s worldwide network. Just a better use of the Free tier that Google Cloud Platform offers.
I will <a href="https://zwischenzugs.com/2018/01/26/how-and-why-i-run-my-own-dns-servers/">setup my own DNS server</a> in the future, but let's use Google's for now. 

**Web server (Nginx):**

DigitalOcean has a couple of excellent guides on setting up Nginx with HTTP/2 and adding TLS.

<a href="https://www.digitalocean.com/community/tutorials/how-to-set-up-nginx-with-http-2-support-on-ubuntu-18-04">How to set up Nginx with HTTP/2 on Ubuntu 18.04</a>

<a href="https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04">Secure Nginx with Let's Encrypt</a>

With a secure webserver and a domain name pointing to it, all I had left to do was SCP the website built locally on my Mac to the web server. 

## Performance

A little tip to optimize images used in any website: Use either the free online service - https://tinypng.com or if you're using a Mac, try https://imageoptim.com/mac

The result of all the above work is a good looking website that happily hits the goal as shown below.
<img class="pt3" src="/images/perf_aravindh.net.png" class="w-100 f5 " alt="Performance Aravindh.net"></img>

I hope my website gave you a little inspiration to build/optimize your own. Have fun! and subscribe to my RSS feed if you'd like to see my future posts. 

<blockquote class="Alegreya ml0 mt0 pl4 black-90 bl bw2 b--blue">
    <p class="f5 f4-m f3-l i lh-copy measure mt0">
      Simplicity is the ultimate sophistication.
    </p>
    <cite class="f6 ttu tracked fs-normal">― LEONARDO DA VINCI</cite>
  </blockquote>
