---
layout: post
title: Ubuntu - Chrome Browser Installation
description: Ubuntu - Chrome Browser Installation
modified: 2016-08-22
tags: [ubuntu]
comments: true
image:
  feature: abstract-17.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

To install the chrome browser on Ubuntu 14.04 and 16.04, follow the procedures: 

1. $ sudo add-apt-repository universe
2. $ sudo apt-get update
3. $ sudo apt-get install libxss1 libgconf2-4 libappindicator1 libindicator7 libnss3-1d 
4. $ wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
5. $ sudo dpkg -i google-chrome*.deb
6. $ Launch "Chrome"
