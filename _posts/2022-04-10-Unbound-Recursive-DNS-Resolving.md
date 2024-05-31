---
title: Unbound – Recursive DNS Resolving
date: 2022-04-10 12:00:00 +0100 
categories: [The Ultimate Pi-hole Project, 3a. Unbound]
tags: [ pi, selfhosting, pihole, ad, blocking]     # TAG names should always be lowercase
---

What exactly is Recursive DNS Resolving?

So normally when you request a web page from the internet it has to look up the address, this info comes from the DNS, normally the data is requested from Cloudflare (1.1.1.1), Google (8.8.8.8), Quad9 (9.9.9.9) or your ISP default DNS which is what most people use. So instead of this you have your Pi acts like one of these big providers and fetches the data itself from the server for the site. This increases your privacy as this means all of your webpages are not being logged by your ISP or a big company.

Now that your understand you can either choose for the Recursive DNS