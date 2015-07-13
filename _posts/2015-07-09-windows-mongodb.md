---
layout: post
title: Windows에 mongodb 설치하기
description: Windows에 mongodb 설치하기
modified: 2015-07-09
tags: [hapi]
categories: hapi
image:
  feature: abstract-16.png
---

Windows에서 MongoDB 설치하고 구동하는 방법을 알아보자. 

## 준비

Command Prompt에서 `wmic os get osarchitecture` 명령을 실행해 Windows architecture가 32bit인지 64bit인지 확인한다. 


## 설치와 설정

#### MongoDB 설치 폴더  

먼저, MongoDB를 설치하고 데이터 및 로그를 저장할 폴더를 만들어야 한다. MongoDB를  C:\mongodb폴더에 설치한다고 가정하자. 

다음 방법으로 command prompt를 관리자 모드로 실행시킨다.

- Win7: Windows key + 'R'을 누르고 'cmd'를 입력한 후 Control+Shift+enter key를 눌러 실행
- Win8: Windows key + 'X'를 누른 후 'A' 키 입력 

MongoDB가 설치될 폴더인 C:\mongodb와 그 아래에 데이터와 로그를 저장할 폴더를 만든다. 관리자 모드 Command Prompt에서 아래와 같이 폴더 위치를 가정하고 폴더를 생성한다.  

{% highlight bash %}
C:\>mkdir C:\mongodb
C:\>cd mongodb 
C:\mongodb>mkdir data 
C:\mongodb>mkdir log
{% endhighlight %}

### MongoDB 설치파일 다운로드

[MongoDB download 사이트](https://www.mongodb.org/downloads) 에서 적합한 버전의 MongoDB 설치 파일을 다운받는다.

### MongoDB 설치

설치 파일을 다운로드 받은 폴더로 이동한다. 관리자 모드 Command Prompt에서 다음 명령으로 MongoDB 설치파일을 실행한다. 설치 파일명을 mongodb-win32-x86_64-2008plus-ssl-3.0.4-signed.msi라고 가정한다. 

{% highlight bash %}
> msiexec.exe /i mongodb-win32-x86_64-2008plus-ssl-3.0.4-signed.msi INSTALLLOCATION="C:\mongodb" ADDLOCAL="all"
{% endhighlight %}

C:\mongodb 폴더로 이동하여 설치가 되었는지 확인한다. bin 폴더가 생성되어 있으면 설치가 성공적인 것이다. 

#### MongoDB configuration 설정 

MongoDB가 실행시 MongoDB 로그와 데이터 폴더 위치를 참조하기 위한 configuration file을 만든다. 아래와 같이 관리자 모드 Command Prompt에 입력해서 **mongod.cfg** 파일을 만들고 log를 저장하는 경로(C:\mongodb\data)와 db 데이터를 저장하는 경로(C:\mongodb\data)를 내용으로 저장한다. 

{% highlight json %}
systemLog:
    destination: file
    path: c:\mongodb\log\mongod.log
storage:
    dbPath: c:\mongodb\data
{% endhighlight %}

## MongoDB를 Windows 서비스로 등록

MongoDB는 다른 어플리케이션에 바인딩되는 라이브러리가 아니라 시스템 프로세스로 동작하며, socket(default port: 27017)을 통해 어플리케이션에 DB 서비스를 제공한다. Windows에서는 Windows 서비스로 등록하여 동작을 시키면 된다. 그 방법을 살펴보자. 

### 서비스 등록 명령

c:\Windows 폴더로 이동하여 **관리자 모드 Command Prompt**에서 아래와 같이 입력하여 MongoDB를 Windows 서비스로 등록한다. 

{% highlight bash %}
C:\Windows>sc.exe create MongoDBService binPath= "C:\mongodb\bin\mongod.exe --service --config=\"C:\mongodb\mongod.cfg\"" DisplayName= "MongoDBService" start= "auto"
{% endhighlight %}

위의 과정을 모두 마치면 아래와 같은 메시지가 Command Prompt에 나타난다. 

{% highlight bash %}
	[SC] CreateService 성공
{% endhighlight %}

이제 PC가 재 부팅되어도 MongoDB는 자동으로 실행될 것이다. 확인을 위해 제어판의 컴퓨터 관리를 통해 등록된 서비스를 확인해 보자. 

<figure>
	<img src="/images/mongodbservice.PNG" alt="">
</figure>


참고로, 서비스를 제거하는 것은 `sc.exe delete MongoDBService` 명령을 사용하면 된다.

### 서비스 시작

Command Prompt에서 `net start MongoDBService` 명령을 통해 MongoDB(서비스 이름)가 시작하도록 해보자. 아래 결과를 볼 수 있을 것이다. 

{% highlight bash %}
	MongoDB Service 서비스가 잘 시작되었습니다.
{% endhighlight %}

## MongoDB 사용

MongoDB를 어떻게 사용하는지에 대해 살펴봐야 할 것이다. hapi와 MongoDB를 활용해 REST API 서버를 구축하는 예제를 다음에 살펴보도록 하자. 
