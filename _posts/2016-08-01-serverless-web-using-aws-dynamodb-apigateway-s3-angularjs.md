---
layout: post
title: AWS DynamoDB, API Gateway, S3, angularjs를 이용한 Serverless Web Service 구축   
description: AWS DynamoDB, API Gateway, S3, angularjs를 이용한 Serverless Web Service 구축   
modified: 2018-08-01
tags: [aws]
comments: true
image:
  feature: abstract-17.png
---
!!현재 작성 중!!  

최근 아주 핫한 기술인 Amazon Web Service를 활용한 Bankend의 Serviceless Architecture와 Frontend의 AngularJS를 이용해서 Web Service를 구축해보자. 복잡한 예보다는 간단한 메모장 서비스를 구현하는 예를 들어 설명할 것인데, 뼈대와 기본 구조를 이해하는 것에는 충분할 것이다. 
일반적인 Backend의 Serverless Architecture는 [Client - AWS API Gateway - AWS Lambda - AWS DynamoDB] 구조를 사용하는 경우가 많다. 이에 반해 이 예에서는 [Client - AWS API Gateway - AWS DynamoDB]로 하여 중간에 Application 로직을 담당하는 컴포넌트를 제거한 초경량 Serverless Architecture로 구성된다. 

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

## 0. 요구사항 및 참조 사항 

### 0.1 기능 요구사항 

구현할 메모장 서비스의 기능 요구사항은 다음과 같다. 

1. 메모 생성 (내용과 tag를 입력)
2. 메모 보기 (내용, tag)
3. 메모 내용 업데이트
4. 메모 삭제 
5. 특정 tag를 가진 메모 리스트만 보기 

### 0.2 참조 할 자료들

- [DynamoDB API](http://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/APIReference/API_Operations.html)를 이해해야 아래의 변환 template을 만들수 있다. 
- [API Gateway에서 Request로부터 variable 획득하기](http://docs.aws.amazon.com/ko_kr/apigateway/latest/developerguide/api-gateway-mapping-template-reference.html)를 를 알아야 한다. 

## 1. 전체 Architecture

### 1.1 시스템 구성 

Webbrowser에서는 S3로부터 HTML, CSS, JS, 이미지 파일등을 로딩하며, API Gateway를 통해 동적 컨텐츠인 메모를 추가/삭제/검색한다. 이 과정에서 Angular JS는 API Gateway와의 CRUD communication과 동적 page 구현을 담당하게 된다. 

```
( Webbrowser ) ----<----- (S3) - 정적 컨텐츠 (image, html, js, css)
    |
    |-----<CRUD>---------(API Gateway) -------- (DynamoDB) - 동적 컨텐츠 (data)
```

### 1.2 CRUD API 정의 

webbrowser와 API Gateway간의 CRUD API는 다음과 같이 정의된다. 

#### 1.2.1 메모 생성 API

새로운 메모를 추가하기 위한 API는 다음과 같다. 

- Resource: /memos
- HTTP Method: POST
- HTTP Request Body: application/json, 아래의 예와 같다. 

```
{
  "message": "This is an example message"
  "tag": "example-tag"
}
```

- Response: {} 

#### 1.2.2 메모 획득 API

메모 id를 이용해 내용을 읽어오는 API는 다음과 같다. 

- Resource: /memos/{memoId}
- HTTP Method: GET
- HTTP Request Body: 없음
- Response: application/json, 메모의 message와 tag, 아래 예와 같다. 
 
```
{
  "message": "This is an example message"
  "tag": "example-tag"
}
```

#### 1.2.3 메모 내용 업데이트 API

메모의 내용을 업데이트하는 API는 다음과 같다. 

- Resource: /memos/{memoId}
- HTTP Method: PUT
- HTTP Request Body: application/json, 아래의 예와 같다. 

```
{
  "message": "This is an example message"
}
```

- Response: {} 

#### 1.2.4 메모 삭제 API

memoId로 메모를 선택해서 삭제한다. 

- Resource: /memos/{memoId}
- HTTP Method: DELETE
- HTTP Request Body: 없음
- Response: {}

#### 1.2.5 메모 리스트 획득 API

tag에 해당하는 메모 id 리스트를 가져온다. 

- Resource: /memos/
- QueryString: tag, 아래와 같은 예 

```
/memos/?tag=X
```

- HTTP Method: GET
- HTTP Request Body: 없음
- Response: application/json, 해당하는 item의 숫자와 각 아이템의 메시지, memoId를 획득. 아래의 예와 같다. 

```
{
  "ConsumedCapacity": {
    "TableName": "Memos"
  },
  "Count": 3,
  "Items": [
    {
      "message": {
        "S": "111aaa"
      },
      "memoId": {
        "S": "2"
      }
    },
    {
      "message": {
        "S": "1"
      },
      "memoId": {
        "S": "1"
      }
    },
    {
      "message": {
        "S": "333"
      },
      "memoId": {
        "S": "3"
      }
    }
  ],
  "ScannedCount": 3
}
```

## 2. Backend 

AWS Console에서 순서대로 따라가보며 [AWS API Gateway - AWS DynamoDB]라는 초경량 Serverless Architecture를 구성해보자. 일본 도쿄 리전(ap-northeast-1)에서 구현했다는 것을 참고로 하자. 

### 2.1 AWS DynamoDB

이 예에서 활용될 AWS DynamoDB table을 만들어 보자. 

1. AWS DynamoDB console로 이동
2. **Create Table** >  **name**은 Memos로 입력
3. **Primary key**는 memoId,나머지는 default setting으로 하고 **Create**를 선택
4. 생성된 table의 **Indexes 탭**을 선택하고 **Create index** 선택 
5. **Primary key**는 tag(String)로 입력. 자동으로 index name은 tag-index이 된다. 
6. **Projected atttributes**는 **include**로 한뒤 tag와 message 추가 
7. **Create index**
8. Table을 선택 > Overview에서 ARN을 복사

#### 2.2 IAM Role 만들기 

API가 호출되었을 때 API Gateway가 DynamoDB table을 handling할 수 있도록 DynamoDB table에 접근할 수 있는 IAM role을 만들어 보자. 

1. AWS IAM console로 이동
2. **Roles** > **Create New Role**
3. **Role 이름**은 apigateway-dynamodb 입력 > **Next Step**
4. **AWS Service Roles** > **Amazon API Gateway** > **Select**
5. Attach Policy에 있는 AmazonAPIGatewayPushToCloudWatchLogs를 선택하지 않고(!) **Next Step** > **Create Role**
6. 생성된 Role을 클릭해서 Summary Page로 진입
7. **Permissions tab** > **Inline Policies** 선택
8. **click here** > **Custom Policy** > **Select**
9. **Policy name**은 MyDynamoDBAllAccess로 입력하고 아래의 policy를 붙여넣고 **Apply Policy** 선택

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1470209124000",
            "Effect": "Allow",
            "Action": [
                "dynamodb:*"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

### 2.2 API Gateway

이제 Client와 DynamoDB와의 연결을 담당하는 API Gateway를 구성해 보자. 먼저 API를 생성후에 다음 순서를 진행한다.  

1. AWS API Gateway console로 이동
2. **New API** 선택
3. **API name**은 MemosApi, description에는 설명을 추가한 후 **Create API**를 선택

#### 2.2.0 리소스 추가

API의 뼈대인 Resource를 만들자. API 정의에 따라 /memos와 /memos/{memoId}를 생성한다. 

1. 생성된 MemosApi에서 **Actions** > **Create Resource** 선택 
2. **Resource Name** - memos,  **Resource Path**는 자동으로 /memos 가 됨 
3. **Create Resource** 선택
4. /memos 선택 후 **Actions** > **Create Resource** 선택
5. **Resource Name** - memoId, **Resource Path**는 {memoId}로 입력 
 
#### 2.2.1 메모 생성 API 구현

생성한 /memos Resource로 들어오는 **POST request**와 Dynamo DB의 **PutItem API**을 mapping하는 것으로 메모 생성 API를 구현하자.  

##### 맵핑

1. 위에서 생성한 /memos resource를 선택 > **Create Method** >  **POST** 선택 후  **v** 마크 선택 
2. Integration type page에서 **Show Advanced** > **AWS Service Proxy** 
3. **AWS Region**에 Tokyo는 ap-northeast-1 선택, **AWS Service** > **DynamoDB** 선택 (아래 철차는 API Gateway와 DynamoDB간 프로토콜 정의)
4. **HTTP method**는 **POST**, **Action type**은 **use action name**를 선택
5. **Action**에는 **PutItem** 입력 후 **Save** 
6. Execution role은 위에서 만든 IAM ROLE ARN을 입력한다. 
7. **Save**

/memos Resource로 들어오는 POST request를 Dynamo DB의 PutItem API의 parameter로 변환하자. 

1. **Integaration Request** > **Body Mapping Templates** 섹션 
2. **+Add mapping template**을 선택한 후 application/json을 입력후 **v** 선택
3. drowdown 메뉴는 그대로 두고 아래 내용을 editor창에 추가 

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

1. memos - POST - Method Execution 화면 > **Test**를 선택
2. memoId에 test-invoke-request 입력 후 **Test**
3. **Request Body 창**에 다음을 추가하고 **Test** button 눌러  Response body가 `{}`이면 성공!

```
{
  "message":  "I really enjoyed the Galaxy note 7!!",
  "tag": "device"
}
```

default로 'test-invoke-request'가 memoId인 item이 생기는데 이는 아래 테스트에 활용한다. 

#### 2.2.2 메모 획득 API 구현

생성한 /memos/{memoId} Resource로 들어오는 **GET request**와 Dynamo DB의 **GetItem API**을 mapping하는 것으로 메모 획득 API를 구현하자.  

##### 맵핑

1. {memoId} resource 선택 후 > **Actions** > **Create Method** 
2. Dropdown 메뉴에서 **GET** > **v** 마크를 선택  
3. Integration type에서 **Show Advanced** >  **AWS Service Proxy** 
4. AWS Region(Tokyo는 ap-northeast-1)을 선택, AWS Service로 **DynamoDB** 선택 
5. HTTP method는 **POST**, Action type은 **use action name**를 선택
6. Action에는 **GetItem**을 입력한 후 Save한다. 
7. Execution role은 위에서 만든 IAM ROLE ARN을 입력 후 **Save**

{memoId} Resource로 들어오는 GET request를 Dynamo DB의 GetItem API의 parameter로 변환하자. 

1. **Integaration Request** > **Body Mapping Templates** 섹션 
2. **+Add mapping template**을 선택한 후 application/json을 입력후 **v** 선택
3. drowdown 메뉴는 그대로 두고 아래 내용을 editor창에 추가 

```
{ 
    "TableName": "Memos",
    "Key": {
      "memoId": { 
        "S": "$input.params('memoId')"
      }
    }
}
```

##### 테스트

1. memos - POST - Method Execution 화면 > **Test**를 선택
2. memoId에 test-invoke-request 입력 후 **Test**
3. item 정보가 response body에 나타나면 성공!


#### 2.2.3 메모 내용 업데이트 API 구현

생성한 /memos/{memoId} Resource로 들어오는 **PUT request**와 Dynamo DB의 **UpdateItem API**을 mapping하는 것으로 메모 업데이트 API를 구현하자.  

##### 맵핑

1. {memoId} resource 선택 후 > **Actions** > **Create Method** 
2. Dropdown 메뉴에서 **PUT** > **v** 마크를 선택  
3. Integration type에서 **Show Advanced** >  **AWS Service Proxy** 
4. AWS Region(Tokyo는 ap-northeast-1)을 선택, AWS Service로 **DynamoDB** 선택 
5. HTTP method는 **POST**, Action type은 **use action name**를 선택
6. Action에는 **UpdateItem**을 입력한 후 Save한다. 
7. Execution role은 위에서 만든 IAM ROLE ARN을 입력 후 **Save**

{memoId} Resource로 들어오는 PUT request를 Dynamo DB의 UpdateItem API의 parameter로 변환하자. 

1. **Integaration Request** > **Body Mapping Templates** 섹션 
2. **+Add mapping template**을 선택한 후 application/json을 입력후 **v** 선택
3. drowdown 메뉴는 그대로 두고 아래 내용을 editor창에 추가 

```
{
    "TableName": "Memos",
    "Key": {
        "memoId": {
            "S": "$input.params('memoId')"
        }
    },
    "UpdateExpression": "set message = :val1",
    "ExpressionAttributeValues": {
        ":val1": {"S": "$input.path('$.message')"}
    },
    "ReturnValues": "NONE"
}
```

##### 테스트

1. memos - POST - Method Execution 화면 > **Test**를 선택
2. memoId에 test-invoke-request, Request body에는 `{ "message" : "new message" }`
3. Response body가 {}이면 성공~!


#### 2.2.4 메모 삭제 API 구현

생성한 /memos/{memoId} Resource로 들어오는 **DELETE request**와 Dynamo DB의 **DeleteItem API**을 mapping하는 것으로 메모 삭제 API를 구현하자.  

##### 맵핑

1. {memoId} resource 선택 후 > **Actions** > **Create Method** 
2. Dropdown 메뉴에서 **DELETE** > **v** 마크를 선택  
3. Integration type에서 **Show Advanced** >  **AWS Service Proxy** 
4. AWS Region(Tokyo는 ap-northeast-1)을 선택, AWS Service로 **DynamoDB** 선택 
5. HTTP method는 **POST**, Action type은 **use action name**를 선택
6. Action에는 **DeleteItem**을 입력한 후 Save한다. 
7. Execution role은 위에서 만든 IAM ROLE ARN을 입력 후 **Save**

{memoId} Resource로 들어오는 DELETE request를 Dynamo DB의 DeleteItem API의 parameter로 변환하자. 

1. **Integaration Request** > **Body Mapping Templates** 섹션 
2. **+Add mapping template**을 선택한 후 application/json을 입력후 **v** 선택
3. drowdown 메뉴는 그대로 두고 아래 내용을 editor창에 추가 

```
{ 
    "TableName": "Memos",
    "Key": {
      "memoId": { 
        "S": "$input.params('memoId')"
      }
    }
}
```

##### 테스트

1. memos - POST - Method Execution 화면 > **Test**를 선택
2. memoId에 test-invoke-request를 입력하여 **Test**
3. Response body가 {}이면 성공~!

#### 2.2.5 메모 리스트 획득구현

생성한 /memos/ Resource로 들어오는 **GET request**와 Dynamo DB의 **Query API**을 mapping하는 것으로 메모 리스트 획득 API를 구현하자. 이때 Query String을 이용해 원하는 tag를 API Gateway에 전달한다.

> /memos/?tag='X'

##### 테스트 위한 아이템 만들기 

테스트를 용이하게 하기 위해 item을 미리 만들어 두자. 

1. AWS DynamoDB console > Tables > Memos table > Items 탭
2. Create Item을 눌러서 Text 모드로 변환 후 아이템을 여러개 추가. tag가 'X'인 아이템을 몇개 만듦 

```
{
  "memoId": "1",
  "tag": "X",
  "message": "111"
}
```

이제 AWS API Gateway로 이동

##### 맵핑

1. /memos resource 선택 후 > **Actions** > **Create Method** 
2. Dropdown 메뉴에서 **GET** > **v** 마크를 선택
3. Integration type에서 **Show Advanced** >  **AWS Service Proxy** 
4. AWS Region(Tokyo는 ap-northeast-1)을 선택, AWS Service로 **DynamoDB** 선택 
5. HTTP method는 **POST**, Action type은 **use action name**를 선택
6. Action에는 **Query**를 입력한 후 Save한다. 
7. Execution role은 위에서 만든 IAM ROLE ARN을 입력 후 **Save**

/memos Resource로 들어오는 GET request를 Dynamo DB의 Query API의 parameter로 변환하자. 

Query String을 이용하기 위해서는 **Method Request** box에서 설정을 해야 한다. 

1. **Method Request** > **URL Query String Parameters** 
2. **Add Query String** > **name** tag로 지정 (/memos/?tag=xxx에서 tag에 해당)

이제 **Integaration Request** box에서 설정하자. 

1 **Integaration Request** > **URL Query String Parameters** 
2. **Add Query String** > **name**은 **tag**, **mapped from**은 **method.request.querystring.tag** 지정(Method Request에 설정한 QueryString과 연결)
3. **Body Mapping Templates** 섹션 >**+Add mapping template** 
4. application/json을 입력후 **v** 선택
5. drowdown 메뉴는 그대로 두고 아래 내용을 editor창에 추가 

```
{
    "TableName": "Memos",
    "IndexName": "tag-index",
    "ProjectionExpression": "memoId, message",
    "KeyConditionExpression": "tag = :v1",
    "ExpressionAttributeValues": {
        ":v1": {
            "S": "$input.params('tag')"
        }
    },
    "ReturnConsumedCapacity": "TOTAL"
}
  
```

##### 테스트

1. memos - GET - Method Execution 화면 > **Test**를 선택
2. QueryString tag에 X를 입력하여 **Test**
3. 다음과 같은 결과를 얻으면 성공

```
{
  "ConsumedCapacity": {
    "CapacityUnits": 0.5,
    "TableName": "Memos"
  },
  "Count": 3,
  "Items": [
    {
      "message": {
        "S": "111aaa"
      },
      "memoId": {
        "S": "2"
      }
    },
    {
      "message": {
        "S": "1"
      },
      "memoId": {
        "S": "1"
      }
    },
    {
      "message": {
        "S": "333"
      },
      "memoId": {
        "S": "3"
      }
    }
  ],
  "ScannedCount": 3
}
```

### 2.3 배포 및 테스트

이제 AWS API Gateway를 실제 인터넷 망에 접속해서 테스트 해보자. 이를 위해서 API Gatway의 API를 배포해야 하고, Client로는 Chrome 앱인 Postman을 이용하자. 

#### 2.3.1 배포

1. AWS API Gateway Console로 이동
2. 생성된 Resource(/memos)를 선택 
3. **Actions** 버튼 > **Deploy API** 선택
4. Deployment stage > **New Stage** 선택, **Stage name**은 임의로 'prod'로 입력. (배포 후 이 이름은 나중에 URL의 일부가 됨. 예를 들어 `~~/prod/memos`가 됨)
5. 나머지는 그대로 두고 **Deploy** 선택 
6. 모든 것을 default로 한뒤 **invoke URL** 복사하여 저장 
7. **Save Changes**

#### 2.3.2 테스트

다음과 같이 postman을 설치하여 배포된 API에 접근해 테스트 해보자 

##### Postman 설치

1. Chrome Browser를 열고 앱으로 Postman을 설치한 후 실행
2. 계정이 없으면 만든 후 로그인 

##### 아이템 생성 테스트 

1. Postman창에서 **method**는 POST, **request URL**은 {invoke URL}/memos 
2. **Body**는 **raw** 선택 후 오른쪽 dropdown 메뉴 > **JSON(application/json)**을 선택 
3. Body 입력 box에 아래 내용을 붙여넣기 후 **Send** 
 
```
{
  "message":  "This message should reach the dynamoDB",
  "tag": "X"
}
```

응답으로 `{}`가 왔는지 확인

##### 아이템 리스트 획득 테스트 

1. **method**를 GET, **request URL**은 {invoke URL}/memos/?tag=X
2. **Send** 후 위에서 만든 item을 포함해서 결과가 잘 오는지 확인

##### 아이템 정보 획득 테스트 

1. 위의 결과에서 memoId를 하나 복사 
2. **method**를 GET, **request URL**은 {invoke URL}/memos/{memoId} 
3. Send하고 결과가 잘 오는지 확인 

##### 아이템 메시지 업데이트 테스트 

1. **method**를 PUT, **request URL**은 {invoke URL}/memos/{memoId} 
2. Body 입력 box에 아래 내용을 붙여넣기 후 **Send** 
3. GET,  {invoke URL}/memos/{memoId}로 **Send**하여 update되었는지 확인
 
```
{
  "message":  "This is updated"
}
```

##### 아이템 메시지 삭제 테스트 

1. **method**를 DELETE, **request URL**은 {invoke URL}/memos/{memoId} 
2. **Send**
3. GET, **{invoke URL}/memos/?tag=X 으로 삭제되었는지 확인
 
#### 이제 Backend 쪽은 끝났다. Frontend를 구현해 보자. ####

## 3. Frontend

### 3.1 Angular JS를 이용한 Webpage 구현

### 3.2 Local data를 이용한 테스트

### 3.3 S3 Hosting을 이용한 테스트

## 4. 통합 검증
