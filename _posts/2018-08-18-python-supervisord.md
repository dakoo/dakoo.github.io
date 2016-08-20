---
layout: post
title: python - ubuntu 시작시 자동 실행(2) 
description: python - ubuntu 시작시 자동 실행(2) 
modified: 2016-08-18
tags: [python]
comments: true
image:
  feature: abstract-13.png
---
개발한 python 코드를 ubuntu 시작시 자동으로 실행되로록 해야 하는 경우가 있다. 
여기서는 supervisord를 이용하는 방법을 알아보자. 

## supervisord 사용하기 

supervisord를 이용해 자동으로 실행되도록 하자.

### 1 supervisord 설치


```
$ sudo apt-get update
$ sudo apt-get install -y supervisor
```

### 2 설정하기

`/etc/supervisor/conf.d/` 폴더 아래에 {프로그래명}.conf 라는 이름으로 파일을 만들고 그 내용을 아래와 같이 채운다. 

```
[program:{프로그램명}]
command = {실행할 command}
directory = {command가 실행되어야 하는 path}
user = {user name}
```

- Note: {실행할 command}에서 command는 `which` 명령으로 나오는 full path까지 적는다. 

### 3 supervisord를 통한 프로그램 실행

```
$ sudo supervisorctl reread
$ sudo supervisorctl update
$ sudo supervisorctl start {프로그램명}
```

### 4 python code를 update시 재구동 

```
$ sudo supervisorctl restart {프로그램명}
```

### 5. virtualenv 환경의 python 프로그램 설정하기 

다음과 같이 conf 파일에서 python 파일을 venv/bin/python을 지정한다.

```
command = /home/user/Sites/my-site/venv/bin/python /home/user/Sites/my-site/app.py
```

### 6. 일반 환경에서의 python 파일 사용 예제

#### 6.1 python 프로그램

/home/ubuntu 아래에서 다음과 같이 폴더와 파일을 만든다. 

```
$ mkdir work
$ cd work
$ vi test.py
```

그 내용은 다음과 같이 1초에 한번씩 점을 찍는 것으로 한다. 

```
import time

while True:
    time.sleep(1)
    print "."
```

#### 6.2 supervisord 설치 및 설정

- 위에 설명한 방법에 따라 supervisord를 설치한다. 
- /etc/supervisor/conf.d/test-settings.conf 파일을 만들고 아래 내용을 붙여 넣는다. 

```
[program:test-settings]
command = /usr/bin/python test.py
directory = /home/ubuntu/work/
user = ubuntu
```

### 7 virtualenv에서의 python 파일 사용 예제

#### 7.1 virtualenv 환경만들기 

/home/ubuntu 아래에서 다음과 같이 폴더와 파일을 만든다. 

```
$ mkdir nwork
$ cd nwork
```

[이전 글](http://hochulshin.com/python-virtualenv-ubuntu/)을 참조해서 virtualenv를 설치하고 환경을 만든다. 

```
$ virtualenv venv
$ source venv/bin/activate
```

#### 7.2 python 프로그램

/home/ubuntu/nwork 폴더에서 test.py 파일을 만들고 아래 내용을 넣는다. 

```
import time

while True:
    time.sleep(1)
    print "."
```

#### 7.3 supervisord 설치 및 설정

- /etc/supervisor/conf.d/test-settings2.conf 파일을 만들고 아래 내용을 붙여 넣는다. 

```
[program:test-settings]
command = /home/ubunutu/nwork/venv/bin/python test.py
directory = /home/ubuntu/nwork/
user = ubuntu
```
