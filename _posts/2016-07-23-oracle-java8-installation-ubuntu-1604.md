---
layout: post
title: Ubuntu, Java - How to install Oracle Java 8 SDK on Ubuntu 16.04
description: Ubuntu, Java - How to install Oracle Java 8 SDK on Ubuntu 16.04
modified: 2016-07-23
tags: [ubuntu, java]
comments: true
image:
  feature: abstract-20.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

On Ubunut 16.04, Oracle java 8 SDK can be installed by the following steps:

```
$ sudo add-apt-repository ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get install oracle-java8-installer
$ sudo update-alternatives --config java  ##If multiple jdks have been installed, you can choose a primary jdk.  
```

Open /etc/environment (sudo vi /etc/environment) and modify the content as follows:

```
JAVA_HOME="....:/usr/lib/jvm/java-8-oracle/" 
export JDK_HOME="/usr/lib/jvm/java-8-oracle/"
```

Then reload the configuration and test it. 

```
$ source /etc/environment
$ env # check JAVA_HOME and JDK_HOME
```
