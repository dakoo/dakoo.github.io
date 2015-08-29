---
layout: post
title: Hapi와 mongoDB를 이용한 간단 REST API 구현 예제 (1)
description: Hapi와 mongoDB를 이용한 간단 REST API 구현 예제 (1)
modified: 2015-07-13
tags: [hapi]
image:
  feature: abstract-5.png
---

Hapi와 mongoDB를 이용하여 간단한 REST API 서버를 구현하는 방법을 알아보자. 가장 기본이라 할 수 있는 데이터의 CRUD(Create, Retrieve, Update, Delete)를 위한 API를 어떻게 구현하는지 이해한다면 더 복잡한 operation의 구현도 가능할 것이다. 

## 준비 

[이전 블로그 글](http://hochulshin.com/windows-mongodb/)을 참조하여 Windows에 MongoDB를 설치하고 백그라운드 서비스로 동작시킨다. 

## REST API 설계

예제 서비스는 사용자 주소록으로 가정하자. 사용자 주소록은 이메일 주소, 사용자의 이름, 닉네임을 하나의 Document로 저장한다. Document는 RDBMS의 Row에 해당하는 개념이다. 

### 기능 

주소록 서버의 지원하는 기능은 다음과 같다. 

- 사용자 추가(Create):  이메일 주소, 사용자의 이름, 닉네임을 등록. 이메일 주소는 unique함. 즉, 요청의 이메일 주소와 동일한 이메일 주소가 이미 주소록에 존재하고 있다면 에러를 반환하고 등록을 거부해야 함. 
- 사용자 목록 얻기(Retrieve): 사용자들의 email 목록을 획득.
- 사용자 정보 얻기(Retrieve):이메일 주소를 이용해 요청. 사용자가 목록에 존재하면 이메일, 이름, 닉네임을 응답
- 사용자 수정(Update): 이메일 주소를 이용해 요청.  이메일 주소, 사용자의 이름, 닉네임을 모두 수정 가능. 
- 사용자 삭제(Delete): 이메일을 이용해 요청.

### API 정의 

이메일 주소, 사용자 이름, 닉네임의 담는 정보는 아래와 같은 JSON 포맷으로 전달되어진다.

{% highlight json %}
{"email":"sampleid@sample.com", "username":"Hochul Shin", "nickname": "dakoo"} 
{% endhighlight %}

서버의 API는 다음과 같이 정의된다. [RESTful API Method의 알맞은 역할 문서](http://blog.remotty.com/blog/2014/01/28/lets-study-rest/#method)를 참조해서 정의했다. 모두 요청시 *Content-Type:application/json*으로 정의한다.   
 
- 사용자 추가(Create): POST, */user*, body payload: {email, username, nickname}, 성공시 201:Created 응답
- 사용자 목록 얻기(Retrieve): GET, */users*, 성공시 200:OK와 JSON으로 email 목록 응답
- 사용자 정보 얻기(Retrieve):GET, */user/{email}*, 성공시 200:OK와 JSON으로 {email, username, nickname} 응답 
- 사용자 수정(Update): PATCH, */user/{email}*, body payload: {username, nickname}, 성공시 200:OK 응답
- 사용자 삭제(Delete): DELETE, */user/{email}*, 성공시 200:OK 응답 

## DB와 Collection 생성

### DB 생성

예제 REST API와 연동할 DB 공간을 MongoDB 서비스에 만든다. **mongo.exe**가 있는 폴더(*C:\mongodb\bin*)에서 Command Prompt에서 `mongo.exe`명령을 통해 **monogdb shell**을 구동한다. Shell에서 `use [db 이름]` 명령을 실행해서 서비스를 위한 DB 공간을 할당한다. 

{% highlight bash%}
>use user-db
switched to db user-db
{% endhighlight %} 

이제 Shell상의 *db* 키워드는 *user-db*를 가리키게 된다.

### Collection 생성

MongoDB에서 RDBMS의 table이란 개념에 해당하는 collection을 만든다. collection내의 item을 추가하면서 자연스럽게 collection은 생성해보자. 
샘플 아이템을 하나 추가하면서 users라는 collection을 만들어 보자. 

{% highlight bash%}
>var user = {email : "sampleid@sample.com", username : "Hochul Shin", nickname: "dakoo"}
>db.users.insert(user);
WriteResult({"nInserted":1})
{% endhighlight %} 

다음 명령을 통해 users collection이 잘 생성되고 아이템이 추가되었는지 확인한 후 collection을 비우자.  
{% highlight bash%}
>show collections
system.indexes
users
>db.users.find()
{ "_id":ObjectId("....."), "email" : "sampleid@sample.com", "username" : "Hochul Shin", "nickname": "dakoo"}
>db.users.remove({});
WriteResult({"nRemoved":1})
{% endhighlight %} 

이제 [Hapi와 mongoDB를 이용한 간단 REST API 구현 예제 (2)](http://hochulshin.com/simple-restapi-using-hapi-mongodb-sample-2)를 통해 API를 구현하는 예를 살펴보자. 
