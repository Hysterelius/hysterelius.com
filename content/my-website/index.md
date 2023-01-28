+++
title = "My Website Journey"
date = 2023-01-23
draft = false

[taxonomies]
categories = ["Blog"]
tags = ["meta"]
[extra]
keywords = "blog, website, building website, building blog"
toc = true
+++

This post covers the journey of my website from an idea to the finished product you see before you today.

<!-- more -->

It is a story of many triumphs and failures and many hard-learned lessons.

It was a slow journey from WordPress to Vue to Zola
🦀.

## Wordpress

Like any enterprising developer, I began my journey on WordPress... I was awestruck and filled with inspiration by these amazing templates, so excited for the possibilities.

I knew I need to make a personal portfolio website. I heard WordPress was the site to make it. So, I quickly jumped on YouTube and followed the first tutorial.

Alas, the finished website looked a bit too corporate for my liking,

{{ img(src="Main_page.png" alt="Hysterelius Main Page | Corporate Style" w=1800 ) }}

I used a prebuilt theme to make my site look very slick. However, WordPress was starting to impede me with its clunky user interface (you had to coax elements **everywhere**...).

Also, I wanted to have a little more customisation or a better blogging theme. I also needed to run this on a more sustainable service, as it was currently running on my poor raspberry pi.

I needed a more customisable and performant solution for my website.

So then I iterated.

## Nuxt Content

At the time I was using Vue to build my [Emoji CP Website](https://emoji-cp.hysterelius.com/). So, I was looking at native Vue solutions to solve my website itch. I had heard of [Nuxt](https://nuxtjs.org/) and so I decided to have a little poke around. While I was playing with Nuxt, I discovered their static site generator called Nuxt Content. So, I thought it would be great to create my next website with this amazing, new tool.

### Version 1

But I made the mistake of thinking I would just rewrite and recreate my whole WordPress site in Vue,
which was not easy. So I spent the entire time, for the sake of "customisability", creating the Hysterelius WordPress Site 2.0 - ✨ Nuxt Editon ✨. Focusing most of my time on the landing page, neglecting the entire reason I switched - to create easy-to-write blog posts, not create a company website.

{{ img(src="Nuxt1_main_page.png" alt="Hysterelius Main Page - Nuxt Edition Version 1| Corporate Style" w=1800 ) }}

I was initially excited about the potential of Nuxt and its static site generation capabilities, but my excitement quickly turned to frustration. I found that building the blog templates from scratch was a daunting task, especially with the lack of documentation and features available.
Prompting me to just focus on the main page, as I did not have enough motivation to create these templates. It ultimately was a struggle to customize the website to my liking, as I kept on hitting roadblocks.

### Version 2

So I decided to iterate again, I naïvely thought that redoing it would fix it. Alas, I ended up making the same mistakes again. I ended up with Hysterelius WordPress 3.0, with the same landing page, and the same icons. Yet the only difference was a material theme with light ☀️ and dark 🌙 mode 😎. Still, I did not learn. I had to stop remaking my (quite bloated) WordPress site again and again, to improve.

{{ img(src="Nuxt2_main_page.png" alt="Hysterelius Main Page - Nuxt Edition Version 1| Corporate Style" w=1800 ) }}

Despite my initial enthusiasm, I soon realized that Nuxt was not the right solution for my website needs. I needed a more user-friendly and customizable platform, one that would give me the flexibility and control that I needed to create the website of my dreams.

So I iterated again, and this time I landed on Zola.

## Zola

So I was searching around, originally looking at creating a Hugo website. However, it seemed a bit too bloated for my needs. I was overwhelmed about the amount of themes to choose from, and how none them had exactly what I wanted. Coming from Nuxt with sparse documentation, the avalanche of documentation from Hugo did nothing to help its case.
At this time I was learning Rust (you might be seeing a pattern here) and stumbled across Zola, which is a fast, lightweight, batteries-included static site generator.

### Abridge

So I needed to find the right theme to pair with my new <abbr title="Static Site Generator">SSG</abbr>. I eventually found Abridge, which was a perfect fit for my needs with its focus on performance and efficiency. It gets top scores on [Chrome lighthouse](https://pagespeed.web.dev/report?url=https%3A%2F%2Fhysterelius.com%2F) and [Mozilla observatory](https://observatory.mozilla.org/analyze/hysterelius.com). Meaning that my new site was perfect for the modern web without any tedious customisation.

## Deployment 🚀

To deploy my site quickly to the World Wide Web, I use a GitHub action that runs on every push. This action builds my Zola site and publishes it to Cloudflare Pages. Overall, I'm thrilled with the combination of Zola and Abridge for my website needs and happy with how the deployment process turned out.

## Conclusion

It was a very long journey, from idea to completion. I had originally started from a well-used tool - WordPress - and from that starting website, I realised I needed something to call my own not just some slick corporate site. I originally thought, I could switch tools and just remake my WordPress site to get what I wanted. Alas, I found out that was not what I wanted my site to be. So, I switched to Zola, to make an easy site with just enough customisability.
