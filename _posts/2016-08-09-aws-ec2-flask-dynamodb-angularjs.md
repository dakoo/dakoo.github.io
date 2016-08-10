---
layout: post
title: Flask와 EC2를 이용해서 DynamoDB와 연동하는 API Gateway 구현
description: Flask와 EC2를 이용해서 DynamoDB와 연동하는 API Gateway 구현 
modified: 2016-08-09
tags: [aws]
comments: true
image:
  feature: abstract-12.png
---
이 예제는 [이전에 구현한 Memos dynamodb table에 접근하는 AWS Gateway](http://hochulshin.com/serverless-web-using-aws-dynamodb-apigateway-s3/#api-gateway-)을 기준으로 설명한다. 즉, AWS API Gateway 대신 여기서 설명하는 Flask/EC2 구성을 사용할 수 있도록 구현한다. 

## 1. EC2 설정

- IAM Role에 dynamodb
- Security Group에서 Inbound Rule에 TCP 5000, source anywhere 추가

## 2. virtualenv와 flask 설치 

### 2.1 설치

```
$ mkdir api-gateway
$ cd api-gateway
$ sudo apt-get install python-virtualenv
$ virtualenv venv
$ . venv/bin/activate
$ sudo pip install Flask
```

## 2.2 flask 테스트

- EC2 터미널에서 api-gateway 폴더로 이동
- 다음 코드를 app.py파일로 저장

```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return "Hello, World!"

```

아래 명령을 EC2 터미널의 api-gateway 폴더에서 실행

```bash
$ export FLASK_APP=app.py
$ flask run --host=0.0.0.0
```

- 웹브라우저를 열어 주소창에 EC2 instance의 public IP (또는 public domain) + ":5000"을 입력하고 결과 확인

## 3. API Gateway 구현

### 3.1 요구사항

1. 메모 리스트 획득: /memos
2. tag를 이용한 메모 리스트 검색: /memos?tag=XXX 
3. 특정 memoId의 메모 획득: /memos/{memoId}

### 3.2 구현

#### 3.2.1 메모 리스트 획득 구현

Python에서 table내의 아이템 리스트를 얻는 예는 다음과 같다. 

```python
from __future__ import print_function
import boto3
import json
import decimal
from boto3.dynamodb.conditions import Key, Attr

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('Memos')

response = table.scan()

for i in response['Items']:
    print(i['memoId'], ":", i['message'], ",", i['tag'])
```

위의 코드를 바탕으로 app.py를 다음과 같이 수정해 보자. 


```python
from __future__ import print_function
import boto3
import json
import decimal
from boto3.dynamodb.conditions import Key, Attr
from flask import Flask

# Helper class to convert a DynamoDB item to JSON.
class DecimalEncoder(json.JSONEncoder):
    def default(self, o):
        if isinstance(o, decimal.Decimal):
            if o % 1 > 0:
                return float(o)
            else:
                return int(o)
        return super(DecimalEncoder, self).default(o)

app = Flask(__name__)
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('Memos')
    
@app.route('/memos')
def memos():
    response = table.scan()
    return json.dumps(response["Item"], cls=DecimalEncoder)    	
```

chrome의 postman에서 EC2 instance의 public IP (또는 public domain) + ":5000/memos", GET method로 요청을 보내고 응답을 정상적으로 받는지 확인하자. 


#### 3.2.2 tag를 이용한 메모 리스트 검색

flask에서 string query는 다음과 같이 얻을 수 있다. 

```python
from flask import request

@app.route('/data')
def data():
    # here we want to get the value of user (i.e. ?user=some-value)
    user = request.args.get('user')
```


Dynamodb에 index의 primary key로 query하는 예는 다음과 같다.

```python
from __future__ import print_function
import boto3
import json
import decimal
from boto3.dynamodb.conditions import Key, Attr

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('Memos')

response = table.query(
    IndexName="tag-index",
    KeyConditionExpression=Key('tag').eq('newtag')
)

for i in response['Items']:
    print(i['memoId'], ":", i['message'], ",", i['tag'])

```


이 둘을 결합하여 app.py를 다음과 같이 수정해 보자. 

```python
from __future__ import print_function
import boto3
import json
import decimal
from boto3.dynamodb.conditions import Key, Attr
from flask import Flask
from flask import request

# Helper class to convert a DynamoDB item to JSON.
class DecimalEncoder(json.JSONEncoder):
    def default(self, o):
        if isinstance(o, decimal.Decimal):
            if o % 1 > 0:
                return float(o)
            else:
                return int(o)
        return super(DecimalEncoder, self).default(o)

app = Flask(__name__)
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('Memos')

@app.route('/memos')
def memos():
    query = request.args.get('tag')
    if query != None :
        response = table.query(
            IndexName="tag-index",
            KeyConditionExpression=Key('tag').eq(query)
        )
        return json.dumps(response["Items"], cls=DecimalEncoder)
    else:
        response = table.scan()
        return json.dumps(response["Items"], cls=DecimalEncoder)
    
```

chrome의 postman에서 public IP (또는 public domain) + ":5000/memos를 통해 획득한 아이템의 tag중 하나를 저장한다. 

EC2 instance의 public IP (또는 public domain) + ":5000/memos?tag=X" 형태, GET method로 요청을 보내고 응답을 정상적으로 받는지 확인하자. 여기서 X가 위에서 저장한 tag이다. 


#### 3.2.3 memoId를 이용해 메모 획득


path가 /{name}인 것을 처리하는 flask의 예는 아래와 같이 다룬다. 

```python
@app.route('/articles/<articleid>')
def api_article(articleid):
    return 'You are reading ' + articleid
```


위의 예를 참고하여 memoId를 이용해 메모 획득하는 것을 구현한 코드는 아래와 같다. 위의 코드에 추가한다. 

```python
@app.route('/memos/<memoId>')
def getMemo(memoId):
    response = table.query(
        KeyConditionExpression=Key('memoId')
    )
    return json.dumps(response["Items"], cls=DecimalEncoder)    
    
```

chrome의 postman에서 public IP (또는 public domain) + ":5000/memos를 통해 획득한 아이템의 memoId중 하나를 저장한다. 
그리고 public IP (또는 public domain) + ":5000/memos/Y형태로 요청을 보낸다. Y가 memoId이다. 

## 4. Front-end 구현 

간단히 angularJS를 이용하여 front-end를 구현한다. 

### 4.1 html

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>SOMA Videos</title>
        <script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.0.2/angular.min.js"></script>
        <script src="js/app.js"></script>
    </head>
<body>
    <div ng-app="myApp" ng-controller="myCtrl">
        <table>
            <tr ng-repeat="item in items">
                <td> {{item.message}} </a></td>
            </tr>
        </table>
    </div>
</body>
</html>

```

### 4.2 Javascript

app.js파일에 다음과 같이 추가한다. 

```
var app = angular.module('myApp', []);

app.controller('myCtrl', function($scope, $http) {

    $http.get('memos').then(function(response){
        $scope.items = response.data;
    }, function(response){
        console.log("ERROR: GET /memos");
    });
});
```

## 5. 정적 컨텐츠 서버 및 webpage 구현

### 5.1 요구사항

- /으로 요청시 default로 index.html 파일을 제공한다. 
- /js/* 경로로 요청시 javascript 정적 파일들을 제공한다. 

### 5.2 Flask를 이용한 파일 서버 구현  

#### 5.2.1 Flask의 예

Flask는 정적 파일을 다루는 데 좋지 않지만, 간단히 할 수 있는 방법을 살펴보자. 다음은 send_from_directory를 이용한 예이다. 

```python
from flask import Flask, request, send_from_directory

# set the project root directory as the static folder, you can set others.
app = Flask(__name__, static_url_path='')

@app.route('/js/<path:path>')
def send_js(path):
    return send_from_directory('js', path)
```

다음은 send_static_file을 이용한 예이다. 

```
from flask import Flask, request
# set the project root directory as the static folder, you can set others.
app = Flask(__name__, static_url_path='')

@app.route('/')
def root():
    return app.send_static_file('index.html')
```

둘 다 경로는 static_url_path를 이용해서 경로를 설정해야 한다는 것이다. 

#### 5.2.2 서버 폴더 구조

위의 예를 이용하여 간단히 구현해 보자. soma-ap-gateway아래에 다음과 같은 구조를 만든다.

```
  /static/index.html
         /js/app.js
```

#### 5.2.3 file 서버 구현

다음을 app.py에 구현한다. 

```
app = Flask(__name__, static_url_path='/static')

@app.route('/')
def default_file():
    return app.send_static_file('index.html')

@app.route('/js/<path:path>')
def sendjs(path):
    if os.path.exists('static/js/%s' % path) :
         return send_from_directory('static/js', path)
    else :
        return ""
```

## 6. 결과 파일 및 테스트 

### 6.1 flask 파일 

아래 app.py 파일은 project folder의 root에 저장되며 다음 명령으로 실행된다. 

- flask run --host=0.0.0.0

```
from __future__ import print_function
import boto3
import json
import os
import decimal
from boto3.dynamodb.conditions import Key, Attr
from flask import Flask, request, send_from_directory

# Helper class to convert a DynamoDB item to JSON.
class DecimalEncoder(json.JSONEncoder):
    def default(self, o):
        if isinstance(o, decimal.Decimal):
            if o % 1 > 0:
                return float(o)
            else:
                return int(o)
        return super(DecimalEncoder, self).default(o)

app = Flask(__name__, static_url_path='/static')
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('Memos')

@app.route('/memos')
def memos():
    query = request.args.get('tag')
    if query != None :
        response = table.query(
            IndexName="creation-index",
            KeyConditionExpression=Key('creation').eq(query)
        )
        return json.dumps(response["Items"], cls=DecimalEncoder)
    else:
        response = table.scan()
        return json.dumps(response["Items"], cls=DecimalEncoder)

@app.route('/memos/<memoId>')
def getVideo(memoId):
    response = table.query(
        KeyConditionExpression=Key('memoId').eq(memoId)
    )
    return json.dumps(response["Items"], cls=DecimalEncoder)

@app.route('/')
def default_file():
    return app.send_static_file('index.html')

@app.route('/js/<path:path>')
def sendjs(path):
    if os.path.exists('static/js/%s' % path) :
         return send_from_directory('static/js', path)
    else :
         return ""
                       
```

### 6.2 index.html 파일

프로젝트 폴더 아래 static 폴더에 저장되는 index.html파일은 다음과 같다. 

이제 browser에서 EC2의 public IP (또는 public domain)로 접속을 해서 memo list가 보이면 성공이다!

```
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <title>SOMA Videos</title>
        <script src="http://ajax.googleapis.com/ajax/libs/angularjs/1.0.2/angular.min.js"></script>
        <script src="js/app.js"></script>
    </head>
<body>
    <div ng-app="myApp" ng-controller="myCtrl">
        <table>
            <tr ng-repeat="item in items">
                <td> {{item.message}} </td>
            </tr>
        </table>
    </div>
</body>
</html>
```

### 6.3 app.js 파일

프로젝트 폴더 아래 static/js 폴더에 저장되는 app.js파일은 다음과 같다. 

```
var app = angular.module('myApp', []);

app.controller('myCtrl', function($scope, $http) {

    $http.get('memos').then(function(response){
        $scope.items = response.data;
    }, function(response){
        console.log("ERROR: GET /Memos");
    });
});

```

### 6.4 테스트 

브라우저에서 EC2 public domain으로 접속했을 때 아이템 리스트가 뜨면 성공이다.!!!
