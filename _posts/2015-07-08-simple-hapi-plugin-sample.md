---
layout: post
title: Nodejs - 간단 hapi plugin 예제
description:  간단 hapi plugin 예제
modified: 2015-07-08
tags: [hapi]
comments: true
image:
  feature: abstract-21.png
---

[hapi plugin](http://hapijs.com/tutorials/plugins)은 서버의 비지니스 로직과 같은 웹 어플리케이션들을 재사용 가능한 조각으로 분리할 수 있게 하는 강력한 모듈이다. 그 간단한 예제를 살펴보자. 

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

## 준비 

[hapi.js를 이용한 multi-port 지원 서버 예제 프로젝트](https://github.com/dakoo/simple-multi-port-hapi-server-sample)를 시작 포인트로 삼아 진행하자. 프로젝트를 fork하여 새로운 프로젝트를 만들거나, clone을 한다. clone URL은 아래와 같다.

>https://github.com/dakoo/simple-multi-port-hapi-server-sample.git
 
## 서버 코드

### route.js와 handler.js의 plugin화 

[예제 프로젝트](https://github.com/dakoo/simple-multi-port-hapi-server-sample)에서 route.js와 handlers.js이 routing을 담당한다. server 폴더 아래에 plugins 폴더를 만들고 route.js와 handlers.js를 옮겼다. 그리고, route.js를 다음과 같이 수정해서 plugin을 만들었다.   
<u>기존 index.js에 있던 routing 관련 내용을 가져와서 모듈화</u>했다. 
    
{% highlight javascript %}
var handler = require('./handlers');
var webendpoints = [
    { method: 'GET', path: '/', config: handler.getentry },
    { method: 'GET', path: '/{filename*}', config:handler.get}];
var apiendpoints = [
    { method: 'GET', path: '/', config: handler.api }];

exports.register = function (server, options, next) {
    server.select("web-ui").route(webendpoints);
    server.select("api").route(apiendpoints);
    next();
};
exports.register.attributes = {
    name: 'route',
    version: '1.0.0'
};
{% endhighlight %}

### 서버 구동을 담당하는 index.js

index.js에서는 connection 부분과 routing 부분을 plugin으로 호출하도록 수정하였다.   

{% highlight javascript %}
var config = require('./manifest.json');
var Hapi = require('hapi');

var server = new Hapi.Server();
for(var i in config){
    server.connection(config[i]);
}

server.register({
        register: require('./plugins/route')
    }, function (err) {
        if (err) {
            console.log('Failed loading plugin');
        }
        server.start(function() {
            console.log('Server started');
        });
});
{% endhighlight %}

## 테스트

server 폴더에서 `node .` 명령을 통해 서버를 구동하고 크롬 브라우저에서 *http://localhost:3000/* 주소와 *http://localhost:3001/* 주소로 접근해 본다.
3000으로 접근하면 client폴더의 index.html이 읽히고, 3001로 접근하면 api.html이 읽힌다.  

## github 프로젝트 

[github의 간단 hapi plugin 예제 프로젝트](https://github.com/dakoo/simple-hapi-plugin-sample)에서 모든 소스를 받을 수 있습니다. 
궁금한 점이나 수정이 필요한 점은 [프로젝트의 issue](https://github.com/dakoo/simple-hapi-plugin-sample/issues)를 통해 알려주시기 바랍니다. 성심껏 답변드리고 반영하겠습니다. 
