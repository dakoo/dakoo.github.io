---
layout: post
title: The best way to install Nodejs on Mac(OSX)
description: The best way to install Nodejs on Mac(OSX)
modified: 2015-08-18
tags: [javascript, backend, node, english]
comments: true
image:
  feature: abstract-21.png
---

If the node is installed by the installer provided nodejs.org, a kind of file acceess problems might be happened. To avoid the issue, you need to install node and npm using nvm or brew instead of the node installer. Nvm is for Linux, whereas [brew](http://brew.sh/) is good for OSX.


<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


## Problem with the node installer

The typical problem caused by the node installer is a file access problem while installing npms.

{% highlight bash %}
npm ERR! tar.unpack untar error ...
{% endhighlight %}

Performing `npm cache clean` or using `sudo` prompt is usually recommended. However, they are not proper solution to the problem. `npm cache clean` is depending on the situation and `sudo` might make you annoyed when you install npms. 

### Solution

#### node cleanup

- If node and npm are already installed on OSX, you need to remove them. 

> cd /usr/local; sudo rm -r bin/node bin/npm include/node /lib/node_modules

#### proper node installation 

- Xcode is prerequisite. Download Xcode from Appstore, and install. At least once, launch Xcode to give it the permission.

- Copy, paste, and run the `ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` command on a terminal to install [homebrew](http://brew.sh/). 

- Install node using the following command on a terminal: `brew install node --without-npm`

- Make the .bash_profile file on your account root folder and add the following content to the file.
The */usr/local/Cellar/node/0.12.7/bin* indicates to location where *node* is. The path is depending on the version. Therefore, you need to check the path carefully and change the following content.

{% highlight bash %}
export PATH="/usr/local/Cellar/node/0.12.7/bin:$PATH"
{% endhighlight %}

- Run the .bash_profile script to register the node path to the OSX system.

>. .bash_profile 

- Install npm using `curl -L https://www.npmjs.com/install.sh | sh`. If the path to the node is not proper, npm installation is failed.

#### Test

- Run `npm` on the current terminal. If failed, run `brew uninstall node` to uninstall node and reinstall node.

- After terminating the terminal, relaunch a terminal and run npm. If failed, the content of .bash_profile should be checked carefully. 
