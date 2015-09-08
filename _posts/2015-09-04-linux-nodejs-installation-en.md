---
layout: post
title:  The best way to install Nodejs on Linux(Amazon Linux, Ubuntu)
modified: 2015-09-04
tags: [nodejs, en]
comments: true
image:
  feature: abstract-11.png
---

If you install the nodejs on Linux(Amazon Linux, Ubuntu) using the nodejs installer, permission problems could happen when you use Yeoman, Bower, Grunt and so on.
After I tried the nodejs installation in several ways, eventually I knew the best way is to avoid using 'sudo' permission while installing the nodejs.

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


# Cleanup the old nodejs

If the nodejs have been already installed in your system, you need to get rid of it as the following command.

> $ sudo rm -r /usr/local/bin/node /usr/local/bin/npm /usr/local/include/node /usr/local/lib/node_modules /usr/local/share/man/man/node.1

Them, remove the `.npm` folder in your account. 

> $ rm -r ~/.npm
> $ rm -r .npm

# Install nvm

## nvm download and installation 

To install the nodejs **without** the sudo permission, nvm is neccessary. Use the following command to install the nvm in the root folder of your account .

{% highlight bash %}
curl https://raw.githubusercontent.com/creationix/nvm/v0.23.3/install.sh | bash
{% endhighlight %}

You can use *wget* instead of *curl*.  

{% highlight bash %}
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.23.3/install.sh | bash
{% endhighlight %}

Then, Add .nvm configuration to the *.bashrc* file. 

> $ source ~/.bashrc

## nvm test and configuration 

By the following command, you can get the version of nvm

> $ nvm --version

Attach the result of the above command to `~/.bashrc`. (i.e v0.10.32)

{% highlight bash %}
nvm use v0.10.32 
{% endhighlight %}

Check if the nvm is installed properly.

> $ nvm --version

# Install the nodejs 

## the stable version of nodejs 

Install the stable version of the nodejs as follows.

> $ nvm install stable

## Test 

Check if the nodejs is installed properly.

> $ node -v 

Finally, you successfully installed the nodejs without *sudo* permission.


