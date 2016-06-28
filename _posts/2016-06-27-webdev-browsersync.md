---
layout: post
title: Web Dev - Browsersync
description: Web Dev - Browsersync
modified: 2016-06-27
tags: [web dev]
comments: true
image:
  feature: abstract-11.png
---
[Browsersync](https://www.browsersync.io) is a web server to support automatic reloading for easy web development. Very simple and easy to use.

## Installation of browsersync on Ubuntu

### Install Node.js 

Refer to [my article](http://hochulshin.com/linux-nodejs-installation/) to install node.js on ubuntu

### Chrome

Chrome browser should be installed.
 
### Browsersync

Let's use npm to install browsersync as below:

> npm install -g browser-sync

## How to use browsersync

### Sample code

1. Make a project folder
2. make a html file as the following.

index.html

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8">
  <title>BrowserSync</title> 
</head>
<body>
  <h1> BrowserSync </h1>
</body>
</html>
```

### Run browsersync

In the project folder, type the following command. 

> browser-sync start --server --directory --files "*"

If you can see that a new page is launched on a new chrome tab, it would succeed. Select the example file.

### Test browsersync 

Modify the html file and save it. The page on the chrome tab must be refreshed automatically. 

```html
  <h1> BrowserSync update! </h1> 
```
