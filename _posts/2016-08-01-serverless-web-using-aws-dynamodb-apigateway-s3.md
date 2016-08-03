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

응답의 Body는 없다({}). 

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

응답의 Body는 없다({}). 

#### 2.0.3 메모 업데이트 

메모 내용이나 tag를 update한다. 

```
Resource: /memos/{memoId}
HTTP Method: PUT
HTTP Request Body:
{
  "message": "This is an example message",
  "tag": "example-tag"
}
```

응답의 Body는 없다({}). 

#### 2.0.4 메모 보기

메모 id를 이용해 내용을 읽어온다. 

```
Resource: /memos/{memoId}
HTTP Method: GET
```

응답의 Body는 json type으로 메모의 message와 tag이다. 

#### 2.0.5 메모 리스트 보기

메모 id 리스트를 가져온다. 

```
Resource: /memos/
HTTP Method: GET
```

응답의 Body는 json type으로 메모들의 id를 가져온다. 

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
7. **Create index**를 선택한다. Table의 상태가 Updating으로 바뀌었다가 Active로 바뀐다. 
8. Table을 선택해서 Overview에서 ARN을 복사한다. 

#### 2.2 IAM Role 만들기 

API가 호출되었을 때 API Gateway가 DynamoDB table을 handling할 수 있도록 DynamoDB table에 접근할 수 있는 IAM role을 만들어야 한다. 

1. AWS IAM console로 이동한다. 
2. Roles > Create New Role
3. Role이름은 apigateway-dynamodb
4. AWS Service Roles에서 **Amazon API Gateway**를 선택한다. 
5. Attach Policy에 있는 AmazonAPIGatewayPushToCloudWatchLogs를 **선택하지 않고** **Next Step** > **Create Role**
6. Permission tab의 **Inline Policies** > **Click here**후 **policy generator** > **Select**
7. Effect - Allow, AWS Service - Amazon DynamoDB, Actions - all actions, ARN은 위에서 복사한 DynamoDB의 ARN을 입력한다. 
8. **Add Statement**를 선택한다. 
9. 모든 것이 성공적이면 새로운 Role의 permission이 추가된 것을 알 수 있다. **Next Step** > **Apply Policy**
10. 생성된 role을 선택한 후 Role ARN을 복사한다. 

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

앞에서 정의한 API는 다음과 같다. message와 tag외에 memoID가 dynamoDB item에 추가되어야 한다. 응답의 body는 없다({}). 

```
Resource: /memos
HTTP Method: POST
HTTP Request Body:
{
  "message": "This is an example message"
  "tag": "example-tag"
}
```

##### 설정

Dynamo DB의 Putitem API와 위에서 생성한 Resource로 들어오는 POST request를 mapping하자. 

1. 위에서 생성한 resource를 선택해서 **Create Method**의 dropdown 메뉴에서 POST를 선택 후  v마크를 선택한다. 
2. Integration type에서 **Show Advanced**를 선택하고 **AWS Service Proxy**를 선택 한다. 
3. AWS Region(Tokyo는 ap-northeast-1)을 선택한 후 AWS Service로 **DynamoDB**를 선택한다. 
5. HTTP method는 **POST**, Action type은 **use action name**를 선택하고, Action에는 **PutItem**을 입력한 후 Save한다. 
6. Execution role은 위에서 만든 IAM ROLE ARN을 입력한다. 
7. **Save**
8. 이제 memos - POST - Method Execution 화면이 뜬다. 

mapping template은 DynamoDB의 PutItem API를 호출할 때 필요한 JSON 구조를 만들자. POST request로부터 2개의 변수($.input)를 얻는다. 그리고 각각의 memo는 unique Id를 가지게 된다. 이것은 API Gateway가 제공하는 $context 변수로부터 추출한다.  ($context.requestId). 자세한 내용은  [여기](http://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-mapping-template-reference.html)를 참조하자. 아래의 구성을 통해 API가 어떻게 호출되었을지라도 PutItem API는 memoId, tag, message를 이용해 호출되게 된다. 

1. diagram에서 우측 DynamoDB box로 들어가는 화살표를 내보내는 것이 **Integaration Request** Box이다. **Integaration Request**을 선택하자. 
2. /memos - POST - Integration Request page에서 **Body Mapping Templates** 섹션을 선택한다. 
3. **+Add mapping template**을 선택한 후 application/json을 입력후 v를 선택한다. 
4. Generate template이라는 dropdown 메뉴와 editor 창이 뜨는 데 drowdown 메뉴는 그대로 두고 아래 내용을 editor창에 추가한다. 

- 테이블 이름: Memos
- Item: memoId, tag, message
```
{ 
    "TableName": "Memos",
    "Item": {
        	"memoId": {
                    "S": "$context.requestId"
          },
          "tag": {
              "S": "$input.path('$.tag')"
          },
          "message": {
              "S": "$input.path('$.message')"
          }
      }
}
```

##### 테스트

1. memos - POST - Method Execution 화면으로 돌아간다 .
2. 왼쪽 Client box상단의 **Test**를 선택한다. 
3. **Request Body 창**에 다음을 추가하고 **Test** button을 선택한다. 

```
{
  "message":  "I really enjoyed the Galaxy note 7!!",
  "tag": "device"
}
```

오른쪽에 로그 창이 뜨면서 처리 내용이 나타난다. 특별한 이상이 없고 Response body가 `{}`이면 정상적으로 처리된 것이다. AWS DynamoDB console로 이동해서 Memos table > items tab에서 정상적으로 추가되었는지 확인해보자. 

#### 2.2.2 메모 삭제 API 추가 

앞에서 진행한 메모 생성 API를 추가한 것을 참고하여 삭제 API도 추가해보자.  

```
Resource: /memos/{memoId}
HTTP Method: DELETE
HTTP Request Body:
{
  "memoId": "example-memo-id"
}
```

응답의 Body는 없다({}). 

##### 설정

Dynamo DB의 Putitem API와 위에서 생성한 Resource로 들어오는 POST request를 mapping하자. 

1. 위에서 생성한 resource를 선택해서 **Create Method**의 dropdown 메뉴에서 POST를 선택 후  v마크를 선택한다. 
2. Integration type에서 **Show Advanced**를 선택하고 **AWS Service Proxy**를 선택 한다. 
3. AWS Region(Tokyo는 ap-northeast-1)을 선택한 후 AWS Service로 **DynamoDB**를 선택한다. 
5. HTTP method는 **POST**, Action type은 **use action name**를 선택하고, Action에는 **PutItem**을 입력한 후 Save한다. 
6. Execution role은 위에서 만든 IAM ROLE ARN을 입력한다. 
7. **Save**
8. 이제 memos - POST - Method Execution 화면이 뜬다. 

mapping template은 DynamoDB의 PutItem API를 호출할 때 필요한 JSON 구조를 만들자. POST request로부터 2개의 변수($.input)를 얻는다. 그리고 각각의 memo는 unique Id를 가지게 된다. 이것은 API Gateway가 제공하는 $context 변수로부터 추출한다.  ($context.requestId). 자세한 내용은  [여기](http://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-mapping-template-reference.html)를 참조하자. 아래의 구성을 통해 API가 어떻게 호출되었을지라도 PutItem API는 memoId, tag, message를 이용해 호출되게 된다. 

1. diagram에서 우측 DynamoDB box로 들어가는 화살표를 내보내는 것이 **Integaration Request** Box이다. **Integaration Request**을 선택하자. 
2. /memos - POST - Integration Request page에서 **Body Mapping Templates** 섹션을 선택한다. 
3. **+Add mapping template**을 선택한 후 application/json을 입력후 v를 선택한다. 
4. Generate template이라는 dropdown 메뉴와 editor 창이 뜨는 데 drowdown 메뉴는 그대로 두고 아래 내용을 editor창에 추가한다. 

- 테이블 이름: Memos
- Item: memoId, tag, message
```
{ 
    "TableName": "Memos",
    "Item": {
        	"memoId": {
                    "S": "$context.requestId"
          },
          "tag": {
              "S": "$input.path('$.tag')"
          },
          "message": {
              "S": "$input.path('$.message')"
          }
      }
}
```

##### 테스트

1. memos - POST - Method Execution 화면으로 돌아간다 .
2. 왼쪽 Client box상단의 **Test**를 선택한다. 
3. **Request Body 창**에 다음을 추가하고 **Test** button을 선택한다. 

```
{
  "message":  "I really enjoyed the Galaxy note 7!!",
  "tag": "device"
}
```

오른쪽에 로그 창이 뜨면서 처리 내용이 나타난다. 특별한 이상이 없고 Response body가 `{}`이면 정상적으로 처리된 것이다. AWS DynamoDB console로 이동해서 Memos table > items tab에서 정상적으로 추가되었는지 확인해보자. 



### 2.3 Test

### 3. Static Contents 처리

### 3.1 Web page 

### 3.2 Local data를 이용한 테스트

## 4. 테스트
