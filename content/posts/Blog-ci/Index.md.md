+++
author = "Aravindh Sampathkumar"
categories = ["Computers"]
date = 2024-01-15T12:00:00Z
summary = “CI setup of my personal website using Github Actions"
tags = ["website", "hugo", "performance"]
title = “Poor man’s CICD for personal website"
type = "post"
images = ["img/lean_fast.jpg"]
draft=true
+++

## Internet obesity
Many websites we routinely visit are just too fat, loading huge amounts of data in the form of javascript files, unoptimized images, web fonts, ads etc. This is just plain inefficient. Take a look of a few sites that I recently visited… Source: My tests using online tool at https://tools.pingdom.com/

## Why self host?

Why not? It’s fun.

Say no to walled gardens and own the Internet.

## A new blog
So, when I wanted to build a blog for myself, I decided it must be simple, efficient, fast, modern, and yet look easy on the eyes of the reader. I thought about building a Staic Site Generator(SSG) myself for this purpose, but decided against it considering there are plenty of open source SSGs available already that fits my needs. I chose Hugo. The default theme (Ananke) is already built using my favourite functional CSS framework - Tachyons, So, I’m set from the get go.

**Update:** I have moved on from Hugo and chose Mkdocs with Material theme instead.
**Update to an update:** I went back to Hugo because the theme Congo fit my needs well, and I had to [scratch an itch](https://github.com/squidfunk/mkdocs-material/discussions/5519) that mkdocs couldn't..

## Making it public
Next step on the way is to host this beautiful website somewhere. I’m going to need a domain name, a Virtual Private Server(VPS) to run a webserver, DNS service, and a TLS certificate provider.

Domain: I purchased a domain through Google domains.

VPS: A cloud VM on Hetzner.

DNS :

Google domains provide a free DNS service that probably is more than enough for personal websites. I chose to use a more business oriented DNS service provided by Google for its Google Cloud Platform customers - Google Cloud DNS. Google describes this service as to offer a reliable, resilient, low-latency DNS serving from Google’s worldwide network. Just a better use of the Free tier that Google Cloud Platform offers. I will setup my own DNS server in the future, but let’s use Google’s for now.

Web server :

[Caddy](https://caddyserver.com/) is a web server that comes with automatic Let’s Encrypt integration to fetch those TLS certificates(https). It is one of those tools that just works and gets out of the way, highly recommended for personal projects.

With a secure webserver and a domain name pointing to it, all I had left to do was SCP the website built locally on my Mac to the web server.

## Performance
A little tip to optimize images used in any website: Use either the free online service - https://tinypng.com or if you’re using a Mac, try https://imageoptim.com/mac

The result of all the above work is a good looking website that happily hits the goal as shown below.

I hope my website gave you a little inspiration to build/optimize your own. Have fun! and subscribe to my RSS feed if you’d like to see my future posts.
