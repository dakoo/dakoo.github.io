---
layout: post
title: Making simple file server
description: Making simple file server
modified: 2015-08-17
tags: [javascript, backend, node]
image:
  feature: abstract-22.png
---

I am reading the book, **Pro Angular.js** and practicing the samples on the book. However, many samples have been already deprecated. The static file server using connect.js is as well. Furthermore, the hapi file server that I implemented has been deprecated too. Therefore, I had to find the alternatives.

### serve-static in expressjs

1. Make a project folder 

2. Run `npm init` to initialize the node project. 

3. Install finalhandler and serve-static npms. 

> npm init
> npm install —save-dev final handler serve-static

4. Make a file - server.js - in the project root folder.  

5. Add the following contents to the server.js file. The file path with the serveStatic function should be changed for your file path.
{% highlight javascript %}
var http = require('http');

var finalhandler = require('finalhandler');
var serveStatic = require('serve-static');

var serve = serveStatic("./“); 

var server = http.createServer(function(req, res) {
  var done = finalhandler(req, res);
  serve(req, res, done);
});

server.listen(3000);
{% endhighlight %}
 
6. Let's run the file server. 

> node server.js 

7. Test with `localhost:3000/filename` on a Web browser. 
