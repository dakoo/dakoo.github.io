---
layout: post
title: hapi.js를 이용한 초 간단 서버 만들기 예제
description: hapi.js를 이용한 초 간단 서버 만들기 예제, scaffolding 포함 
modified: 2015-07-06
tags: [hapi.js]
categories: hapi.js
image:
  feature: abstract-11.png
---

hapi.js는 node.js framework 중 가장 hot한 framework이라 할만한다. 더욱 널리 쓰이는 express.js framework이 여러가지 이유로 불안하기에 더욱 hapi.js를 주목해야 할 것이다. hapi.js에 대해서 앞으로 계속 다루게 될 것인데 그 가장 기초가 되는 초 간단 서버와 폴더 구조를 구성해 보자.  

## 사전 준비

[node.js 개발 환경](http://dakoo.github.io/node.js/how-to-set-up-node.js-dev-env-on-windows/)이 설치되어 있어야 한다. 

## 프로젝트 폴더 구조(Scaffolding) 구성하기 

### 폴더 구조

이제 프로젝트를 시작하자. 탐색기에서 임의의 위치에 프로젝트 폴더를 만든다. 아래와 같은 구조로 그 아래에 폴더를 만든다.  

<figure>
	<img src="/images/very-simple-hapi-server-sample-folders.PNG" alt="">
</figure>

### client 폴더의 기본 파일 만들기

client/html 폴더 아래 index.html 파일을 간단히 만든다. 단순한 테스트를 위한 것이므로 간단한 html 파일이면 된다. 

{% highlight html %}
<!DOCTYPE html>
<html>
<head lang="en">
    <meta charset="UTF-8">
    <title></title>
</head>
<body>
hello, hapi!
</body>
</html>
{% endhighlight %}

## hapi.js 서버 구성하기 

### hapi 설치 및 구성하기

server 폴더 <u>아래</u>에서 Command Prompt로 `npm init`을 실행한다. 이 명령어는 server 폴더에 package.json을 만들며, 기본적인 설정을 한다.  그리고 `npm install –-save-dev hapi`명령을 실행하여 hapi를 설치한다. 

### hapi 서버 만들기 

server 폴더 <u>아래</u>에 **index.js** 파일을 만들고 아래 내용을 채운다. 

{% highlight javascript %}
var Hapi = require('hapi');
var server = new Hapi.Server();
server.connection({ port: 3000 });
server.route({
    method: 'GET',
    path: '/{filename}',
    handler: {
        file: function (request) {
            return '../client/html/' + request.params.filename;
        }
    }
});
server.start(function () {
    console.log('Server running at:', server.info.uri);
});
{% endhighlight %}

### 테스트
 
Command Prompt를 열어 server 폴더에서 `node .`로 서버를 돌리고 브라우저에서 *http://localhost:3000/index.html*로 접근해서 hello, hapi!가 보이는지 확인한다. 

## Github 프로젝트

[github의 hapi.js를 이용한 초 간단 서버 만들기 예제](https://github.com/dakoo/very-simple-hapi-server-sample)를 방문하면 모든 소스를 받을 수 있다. 