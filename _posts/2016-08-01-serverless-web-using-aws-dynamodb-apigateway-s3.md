---
layout: post
title: AWS DynamoDB, API Gateway, S3를 이용한 Serverless Web Server 구축   
description: AWS DynamoDB, API Gateway, S3를 이용한 Serverless Web Server 구축 
modified: 2018-08-01
tags: [aws]
comments: true
image:
  feature: abstract-17.png
---
퀵 메모장 기능을 지원하는 Serverless Web Server를 간단히 구축해 보자. 

## 0. 기능 요구사항

기능 요구사항은 다음과 같다. 

1. 메모 생성 (내용과 tag를 입력)
2. 메모 삭제 
3. 메모 업데이트 (내용과 tag)
4. 메모 보기 (내용, tag, 시간)
5. 메모 리스트 보기 (시간 순 정렬)
6. 특정 tag를 가진 메모 리스트만 보기 (시간 순 정렬)

## 1. Architecture

```
( Webbrowser ) --------- (S3) - Static contents(image, html, js, css 파일들)
    |
    |--------------------(API Gateway) -------- (DynamoDB) - Dynamic contents
```
## 2. Dynamic Contents 처리

### 2.0 API 정의

#### 2.0.1 메모 생성

새로운 메모를 추가한다. 

```
Resource: /memos
HTTP Method: POST
HTTP Request Body:
{
  "message": "This is an example message"
  "tag": "example-tag"
}
```

#### 2.0.2 메모 삭제 

메모를 선택해서 삭제한다. 

```
Resource: /memos/{memoId}
HTTP Method: DELETE
HTTP Request Body:
{
  "memoId": "example-memo-id"
}
```

메모 내용을 업데이트한다. 

#### 2.0.3 메모 업데이트 

```
Resource: /memos/{memoId}
HTTP Method: PUT
HTTP Request Body:
{
  "message": "This is an example message",
  "tag": "example-tag"
}
```

#### 2.0.4 메모 보기

메모 id를 이용해 내용을 읽어온다. 

```
Resource: /memos/{memoId}
HTTP Method: GET
```

#### 2.0.5 메모 리스트 보기

메모 id 리스트를 가져온다. id의 리스트이다. 

```
Resource: /memos/
HTTP Method: GET
```

#### 2.0.6 tag를 이용해 메모 리스트 얻기

tag에 해당하는 메모 id 리스트를 가져온다. 

```
Resource: /memos/tag='example-tag'
HTTP Method: GET
```

### 2.1 DynamoDB

DynamoDB table을 만들자. 

1. AWS DynamoDB console로 이동한다. 
2. **Create Table**을 선택한 후 이름을 Memos로 선택한다. 
3. memoId를 Primary key로 하고 나머지는 default setting으로 하고 **Create**를 선택한다.table 생성이 완료되면 Status가 Active로 바뀐다. 
4. 생성된 table의 Indexes 탭을 선택하고 **Create index**를 선택한다. 
5. Primary key는 tag(String)로 입력한다. 그러면 자동으로 index name은 tag-index이 된다.
6. **Projected atttributes**는 include로 한뒤 tag, message를 추가한다. 
6. **Create index**를 선택한다. Table의 상태가 Updating으로 바뀌었다가 Active로 바뀐다. 

#### 2.2 IAM Role 만들기 

API가 호출되었을 때 API Gateway가 DynamoDB table을 handling할 수 있도록 DynamoDB table에 접근할 수 있는 IAM role을 만들어야 한다. 

1. AWS IAM console로 이동한다. 
2. Roles > Create New Role
3. Role이름은 apigateway-dynamodb
4. AWS Service Roles에서 **Amazon API Gateway**를 선택한다. 
5. 

### 2.2 API Gateway

새로운 API를 만들어보자. 

1. AWS API Gateway console로 이동한다. 
2. **New API**를 선택한다. 
3. API이름은 MemosApi로 하고 description에는 설명을 추가한 후 **Create API**를 선택한다. 

#### 2.2.0 리소스 추가 

Resource를 만들자. 

1. 생성된 MemosApi에서 **Actions**버튼을 눌러 **Create Resource**를 선택한다. 
2. Resource Name: memos로 입력한다. Resource Path는 자동으로 /memos 가 된다. 
4. **₢reate Resource**를 선택

#### 2.2.1 메모 생성 API 추가 

Dynamo DB의 Putitem API와 위에서 생성한 Resource로 들어오는 POST request를 mapping하자. 

1. 위에서 생성한 resource를 선택해서 **Create Method**의 dropdown 메뉴에서 POST를 선택 후  v마크를 선택한다. 
2. Integration type에서 **Show Advanced**를 선택하고 **AWS Service Proxy**를 선택 한다. 
3. AWS Region(Tokyo는 ap-northeast-1)을 선택한 후 AWS Service로 **DynamoDB**를 선택한다. 
5. HTTP method는 **POST**, Action type은 **use action name**를 선택하고, Action에는 **Putitem**을 입력한 후 Save한다. 
6. Execution role은 
6. 아직 우리는 DynamoDB를 table의 상세를 정의하지 않았지만, **mapping template**을 이용해 진행하자. 

 
### 2.3 Test

### 3. Static Contents 처리

### 3.1 Web page 

### 3.2 Local data를 이용한 테스트

## 4. 테스트
