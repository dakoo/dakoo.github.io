---
layout: post
title: Python - ubuntu 시작시 자동 실행(2) - supervisord 
description: Python - ubuntu 시작시 자동 실행(2) - supervisord 
modified: 2016-08-18
tags: [python, ubuntu]
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
- Note: export 변수는 다음과 같이 command이전에 추가한다. 변수 사이는 ,로 구분한다. 

```
environment = 
    {변수1}={변수 내용1},
    {변수2}={변수 내용2},
command ... 
```

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


### 8 virtualenv에서의 flask 서버 구동 예제  

#### 8.1 virtualenv 환경만들기 

/home/ubuntu 아래에서 다음과 같이 폴더와 파일을 만든다. 

```
$ mkdir server
$ cd server
```

[virtualenv 관련 글](http://hochulshin.com/python-virtualenv-ubuntu/)과 [flask 서버 관련 글](http://hochulshin.com/aws-ec2-flask-dynamodb-angularjs/)을 참조해서 virtualenv를 설치하고 환경을 만들고 flask를 설치하자. 

```
$ virtualenv venv
$ source venv/bin/activate
$ sudo pip install Flask
```

#### 8.2 python 프로그램

/home/ubuntu/server 폴더에서 run-server.py 파일을 만들고 아래 내용을 넣는다. 

```
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return "Hello, World!"
```

#### 8.3 supervisord 설치 및 설정

- /etc/supervisor/conf.d/flask-settings.conf 파일을 만들고 아래 내용을 붙여 넣는다. export 변수인 `FLASK_APP=run-server.py`도 설정되어야 한다. 

```
[program:flask-settings]
environment=
    FLASK_APP=run-server.py
command = /home/ubunutu/server/venv/bin/python run-server.py
directory = /home/ubuntu/server/
user = ubuntu
```


#### 8.4 supervisord 실행

```
$ sudo supervisorctl reread
$ sudo supervisorctl update
$ sudo supervisorctl start all
```

#### 8.5 테스트

브라우저에서 localhost:5000으로 접근해서 hello, world가 보이면 성공이다. 
