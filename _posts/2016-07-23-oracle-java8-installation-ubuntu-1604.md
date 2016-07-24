---
layout: post
title: How to install Oracle Java 8 SDK on Ubuntu 16.04
description: How to install Oracle Java 8 SDK on Ubuntu 16.04
modified: 2016-07-23
tags: [dev]
comments: true
image:
  feature: abstract-20.png
---
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
JDK_HOME="/usr/lib/jvm/java-8-oracle/"
```

Then reload the configuration and test it. 

```
$ source /etc/environment
$ java -version
```