---
layout: post
title: Nodejs - hapi.js를 이용한 구조화된 간단 서버 예제
description: hapi.js를 이용한 구조화된 간단 서버 예제
modified: 2015-07-08
tags: [hapijs, nodejs]
comments: true
image:
  feature: abstract-7.png
---

조금은 구조화된 hapi 서버의 예를 살펴보자. 이 예제를 통해 hapi를 이용해 자신의 서버 프로젝트를 어떻게 구조화 할 수 있는 지 알 게 될 것이다.    

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

[hapi.js를 이용한 초 간단 서버 만들기 예제 프로젝트](https://github.com/dakoo/very-simple-hapi-server-sample)를 시작 포인트로 삼아 진행하자. 프로젝트를 fork하여 새로운 프로젝트를 만들거나, clone을 한다. clone URL은 아래와 같다.

>https://github.com/dakoo/very-simple-hapi-server-sample.git
 
## 서버 코드

### 서버 설정 저장하는 manifest.json

server 폴더에 서버의 설정을 저장하는 **manifest.json** 파일을 생성하고 아래와 같이 수정한다. port는 3000번이고 label은 "web-ui"로 설정한다. port가 하나만 사용하므로 labels는 이 예제에서는 사용하지 않을 것이다.  

{% highlight json %}
{
  "server": {
    "port": "3000",
    "labels": ["web-ui"]
  }
}
{% endhighlight %}

### 경로와 handler간 routing을 담당하는 route.js

route.js에서는 입력 경로와 handler들과 맵핑을 담당한다. 코드보다는 설정파일처럼 보이는 것이 더 직관적이다.  

{% highlight javascript %}
var handler = require('./handlers');
exports.webendpoints = [
    { method: 'GET', path: '/', config: handler.getentry },
    { method: 'GET', path: '/{filename*}', config:handler.get}];
{% endhighlight %}

### handler를 위한 handlers.js

클라이언트로부터의 request를 처리하는 handler들을 등록해 둔다. 여기서는 파일을 읽어 응답을 주는 단순한 handler만 있다. 

{% highlight javascript %}
exports.getentry = {
    handler: {
        file: function (request) {
            return '../client/html/' + 'index.html';
        }
    }
};
exports.get = {
    handler: {
        file: function (request) {
            return '../client/html/' + request.params.filename;
        }
    }
};
{% endhighlight %}

### 서버 구동을 담당하는 index.js

서버 설정과 routing 설정을 읽어 hapi 서버 object에 등록한 후 서버를 구동한다.   

{% highlight javascript %}
var Hapi = require('hapi');
var Route = require('./route');
var config = require('./manifest.json');

var server= new Hapi.Server();
for(var i in config){
    server.connection(config[i]);
}

server.route(Route.webendpoints);
server.start(function() {
    console.log('Server started');
});
{% endhighlight %}

## 테스트

server 폴더에서 `node .` 명령을 통해 서버를 구동하고 크롬 브라우저에서 *http://localhost:3000/* 주소와 *http://localhost:3000/index.html* 주소로 접근해 본다. routing 설정으로 인해 두 주소 모두 client 폴더의 index.html 파일이 읽힌다. 

## github 프로젝트 

[github의 간단 hapi plugin 예제 프로젝트](https://github.com/dakoo/simple-organized-hapi-server-sample)에서 모든 소스를 받을 수 있습니다. 
궁금한 점이나 수정이 필요한 점은 [프로젝트의 issue](https://github.com/dakoo/simple-organized-hapi-server-sample/issues)를 통해 알려주시기 바랍니다. 성심껏 답변드리고 반영하겠습니다.
