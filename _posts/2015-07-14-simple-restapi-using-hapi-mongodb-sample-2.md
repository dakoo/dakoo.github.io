---
layout: post
title: Hapi와 mongoDB를 이용한 간단 REST API 구현 예제 (2)
description: Hapi와 mongoDB를 이용한 간단 REST API 구현 예제 (2)
modified: 2015-07-14
tags: [hapi]
image:
  feature: abstract-16.png
---

[Hapi와 mongoDB를 이용한 간단 REST API 구현 예제(1)](http://dakoo.github.io/simple-restapi-using-hapi-mongodb-sample-1/) 글의 다음 순서를 진행해보자. 

## 준비 

hapi.js를 이용한 multi-port 지원 서버 예제 [블로그 글](http://dakoo.github.io/hapi/simple-multi-port-hapi-server-sample/)과 [프로젝트](https://github.com/dakoo/simple-multi-port-hapi-server-sample)를 시작 포인트로 삼아 진행하자. 프로젝트를 fork하여 새로운 프로젝트를 만들거나, clone을 한다. clone URL은 아래와 같다.

>https://github.com/dakoo/simple-multi-port-hapi-server-sample.git

hapi.js를 이용한 multi-port 지원 서버 예제에서 사용한 3001번 포트를 REST API에 할당해 사용할 것이다. 

## 필요 모듈 설치

server 폴더에서 Command Prompt로 `npm install --save-dev hapi-mongodb boom` 명령을 통해 mongodb 서비스와 연결하기 위한 모듈([hapi-mongodb](https://www.npmjs.com/package/hapi-mongodb))과 HTTP 에러 응답을 손쉽게 보낼 수 있게 하는 hapi의 유틸리티 모듈([boom](https://github.com/hapijs/boom))을 설치한다.    

### 에러 처리

Windows에서 hapi-mongodb 설치 중 아래 메시지와 함께 에러가 발생한다면, 그것은 mongodb 모듈을 위한 C++ 컴파일러가 설치되어 있지 않다는 것이다. mongodb 모듈은 hapi-mongodb에서 사용하는 모듈이다. 

>MSBUILD : error MSB3428: Could not load the Visual C++ component "VCBuild.exe". 

이를 해결하기 위해서는 무료 개발툴인 [Visual Studio Express 2013](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs#DownloadFamilies_2)을 [설치](http://blogs.msdn.com/b/jspark/archive/2014/02/17/gm-2013.aspx)하면 된다. for Web, for Windows, for Windows Desktop 중 무엇이라도 가능하다. Visual Studio 설치를 완료 후에 다시 필요 모듈 설치를 시도해보자.   

## MongoDB service의 db 연결

### db 설정 파일 

Server 폴더에 dbconfig.json 파일을 만들고 아래 내용을 추가한다. user-db는 [Hapi와 mongoDB를 이용한 간단 REST API 구현 예제(1)](http://dakoo.github.io/simple-restapi-using-hapi-mongodb-sample-1/)에서 만들어 둔 collection이다. 이 파일의 설정은 [mongodb 사이트](https://www.npmjs.com/package/hapi-mongodb)를 참조하면 더욱 다양하게 할 수 있다.   

{% highlight json %}
{
   "url"       : "mongodb://localhost:27017/user-db",
   "settings"   : {
     "db"    : {
       "native_parser" : "false"
     }
   }
} 
{% endhighlight %}
 
### db 연결
 
**index.js** 파일에서는 server.start를 호출하기 전에 mongodb와 연결을 하고, 실패시 종료하도록 한다.  

{% highlight javascript %}
var config = require('./manifest.json');
var Hapi = require('hapi');

var server = new Hapi.Server();
for(var i in config){
    server.connection(config[i]);
}

server.register([{
    register: require('./plugins/route')
    }, {
    register: require('hapi-mongodb'),
    options: require('./dbconfig.json')
    }], function (err) {
    if (err) {
        console.log('Failed loading plugin');
    }
    server.start(function() {
        console.log('Server started');
    });
});
{% endhighlight %}

## REST API 구현

### 불필요한 파일 제거

3001번 포트로 요청이 왔을때 api.html을 서비스하던 부분을 제거해야 한다. client\html 폴더의 **api.html** 파일을 제거하자. 

### API handler 구현  

server\plugins 폴더의 handlers.js 파일에서 **export.api** 부분을 제거하자. 대신, server\plugins 폴더에 **user.js** 파일을 생성한다. 

#### handler 틀  

정의한 API와 기능별로 handler의 layout을 만든다. 

{% highlight javascript %}
exports.handler이름 = function (request, reply) {
        //handler내용
};
{% endhighlight %}

이전 예제에 사용한 layout은 다음과 같다.  

{% highlight javascript %}
exports.handler이름 = {
    handler: function (request, reply) {
        //handler내용
    }
};
{% endhighlight %}

#### handler 구현

**user.js** 파일에 mongodb와 연동하여 사용자 정보를 추가, 획득, 수정, 삭제하는 로직을 구현한다. [hapi-mongodb](https://www.npmjs.com/package/hapi-mongodb) 사용법과 [mongodb 사용법](http://docs.mongodb.org/manual/core/crud-introduction/)을 참조하라.  

{% highlight javascript %}
var Boom = require('boom');
exports.add = function (request, reply) {
    var db = request.server.plugins['hapi-mongodb'].db;
    db.collection('users').findOne({"email" : request.payload.email}, function(err, ret) {
        if (err) {
            return reply(Boom.internal('Internal Database Error', err));
        }
        if (ret){
            return reply(Boom.conflict('Duplicated Resource Error', err));
        }
        var user = {
            email: request.payload.email,
            username: request.payload.username,
            nickname : request.payload.nickname
        };
        db.collection('users').insert(user, {w:1}, function (err){
            if (err){
                return reply(Boom.internal('Internal Database Error', err));
            }else{
                reply();
            }
        });
    });
};
exports.getlist = function (request, reply) {
    var db = request.server.plugins['hapi-mongodb'].db;
    db.collection('users').find().toArray(function (err, ret){
        var users = [];
        for (var i in ret){
            var user = {
                email: ret[i].email,
                username: ret[i].username,
                nickname : ret[i].nickname
            };
            users.push(user);
        }
        reply(users);
    });
};
exports.get = function (request, reply) {
    var db = request.server.plugins['hapi-mongodb'].db;
    db.collection('users').findOne({"email" : request.params.email}, function(err, ret) {
        if (err)
            return reply(Boom.internal('Internal Database Error', err));
        var user = {
            email: ret.email,
            username: ret.username,
            nickname : ret.nickname
        };
        reply(user);
    });

};
exports.update = function (request, reply) {
    var user = {
        email: request.payload.email,
        username: request.payload.username,
        nickname : request.payload.nickname
    };
    var db = request.server.plugins['hapi-mongodb'].db;
    db.collection('users').update({"email" : request.params.email}, user, function(err, ret) {
        if (err)
            return reply(Boom.internal('Internal Database Error', err));
        reply();
    });
};
exports.remove = function (request, reply) {
    var db = request.server.plugins['hapi-mongodb'].db;
    db.collection('users').remove({"email" : request.params.email}, function (err){
        if (err)
            return reply(Boom.internal('Internal Database error', err));
        reply();
    });
};
{% endhighlight %}

#### 3000 번 포트 handler 수정

3000번 포트는 web page를 제공하는 것이다. 이또한 확장성을 높이기 위해 handlers.js의 handler의 틀을 수정한다. 그리고 handlers.js의 이름도 **page.js**로 바꾼다. page.js의 내용은 다음과 같다. 

{% highlight javascript %}
exports.front = {
    file: function (request) {
        return '../client/html/' + 'index.html';
    }
};
exports.get = {
    file: function (request) {
        return '../client/html/' + request.params.filename;
    }
};
{% endhighlight %}

## API와 handler의 Routing

route.js에서 기존 api handler를 삭제하고 user.js에 만든 handler를 API와 연결시킨다. [Hapi와 mongoDB를 이용한 간단 REST API 구현 예제(1)](http://dakoo.github.io/simple-restapi-using-hapi-mongodb-sample-1/)에 정의된 API를 참고하여 작성한다. 또한, page.js의 handler와 API도 연결한다. 

{% highlight javascript %}
var page = require('./page');
var pages = [
    { method: 'GET', path: '/', handler: page.front },
    { method: 'GET', path: '/{filename*}', handler: page.get}];

var user = require('./user');
var apis = [
    { method: 'POST', path: '/user', handler: user.add },
    { method: 'GET', path: '/users', handler: user.getlist },
    { method: 'GET', path: '/user/{email*}', handler: user.get },
    { method: 'PATCH', path: '/user/{email*}', handler: user.update },
    { method: 'DELETE', path: '/user/{email*}', handler: user.remove }];

exports.register = function (server, options, next) {
    server.select("web-ui").route(pages);
    server.select("api").route(apis);
    next();
};

exports.register.attributes = {
    name: 'route',
    version: '1.0.0'
};
{% endhighlight %}

## 테스트

### HTTP 테스트 클라이언트 설치

Console 기반의 HTTP 테스트 클라이언트인 [curl](http://curl.haxx.se/download.html)을 많이 사용하지만 편리한 GUI를 가진 유틸리티를 사용하는 것도 좋다. 크롬 브라우저를 실행하여 크롬 웹스토어로 이동 후 *Advanced REST client*나 *DHC* 를 설치하여 사용하길 권장한다. 사용법은 여기서 다루지 않는다. 

### 서버 실행 

Command Prompt로 server 폴더에서 `node .`로 서버를 동작시킨다.  

### 테스트 실시 

아래는 테스트를 위한 **요청** 정보의 예이다. HTTP 테스트 클라이언트에 아래 정보를 입력하여 각 API가 잘 동작하는지 확인해본다.  사용자 추가를 위한 Body의 내용은 복사/붙여넣기 과정에서 큰따옴표가 이상하게 변하여 오류를 발생시킬 수 있다. 오류가 발생하면, 내용을 복사/붙여넣기하는 대신 직접 타이핑을 해보길 추천한다. 

1. 사용자 목록 획득
- 주소: *localhost:3001/users*
- method: *GET*
- Header: *Content-Type: application/json* 
2. 사용자 추가
- 주소: *localhost:3001/user*
- method: *POST*
- Header: *Content-Type: application/json* 
- Body: *{"email":"sampleid@sample.com","username":"Hochul Shin", "nickname":"dakoo"}*
3. 사용자 정보 획득
- 주소는 *localhost:3001/user/sampleid@sample.com
- method: *GET*
- Header: *Content-Type: application/json*
4. 사용자 정보 갱신
- 주소는 *localhost:3001/user/sampleid@sample.com
- method: *PATCH*
- Header: *Content-Type: application/json*
- Body: *{"email":"sampleid@sample.com", "username":"Hochul Shin", "nickname": "dakoo"}*
5. 사용자 정보 삭제
- 주소는 *localhost:3001/user/sampleid@sample.com
- method: *DELETE*
- Header: *Content-Type: application/json*

## github 프로젝트 

[github의 Hapi와 mongoDB를 이용한 간단 REST API 구현 예제 프로젝트](https://github.com/dakoo/simple-restapi-using-hapi-mongodb-sample)에서 모든 소스를 받을 수 있습니다. 
궁금한 점이나 수정이 필요한 점은 [프로젝트의 issue](https://github.com/dakoo/simple-restapi-using-hapi-mongodb-sample/issues)를 통해 알려주시기 바랍니다. 성심껏 답변드리고 반영하겠습니다. 
