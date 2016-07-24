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

1. sudo add-apt-repository ppa:webupd8team/java
2. sudo apt-get update
3. sudo apt-get install oracle-java8-installer
4. sudo update-alternatives --config java  ##If multiple jdks have been installed, you can choose a primary jdk.  
5. sudo vi /etc/environment 
6. JAVA_HOME="/usr/lib/jvm/java-8-oracle" should be added to the /etc/environment
7. source /etc/environment
8. java -version # To verify the steps above
