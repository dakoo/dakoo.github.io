---
layout: post
title: AWS S3 파일 생성시 SNS, SQS를 통해 EC2로 전달하기 
description: AWS S3 파일 생성시 SNS, SQS를 통해 EC2로 전달하기 
modified: 2016-07-18
tags: [aws]
comments: true
image:
  feature: abstract-14.png
---
S3에 file이 upload가 되면 그 Event가 AWS SNS(Simple Notification Service)를 통해 AWS SQS(Simple Queue Service)에 전달되고, 이를 EC2에서 읽어서 파일을 가공하는 것을 살펴보자. 

### Architecture

중간에 SNS와 SQS를 쓰는 이유는 S3에서 바로 EC2로 event를 전달하면 EC2가 busy인 경우 그 event를 처리할 수 없을 수 있기 때문이다. 
SNS와 SQS를 쓰는 이유는 향후 scalable한 확장을 할 수 있고 event를 적재해 놓을 수 있기 때문이다. 
SNS를 통해 S3의 event를 다양한 종류의 SQS에 등록해 놓으면 EC2에서 동작하는 각기 다른 기능들이 맵핑된 SQS로부터 event를 읽고 처리할 수 있다. 


```
S3 Bucket --> SNS --> SQS A ==> EC2 A 기능 
                            ==> EC2 A 기능
                   -> SQS B ==> EC2 B 기능
                   .... 
                          
--> : Push
==> : Pull
```

위와 같은 구성을 위해 SQS부터 SNS, S3를 설정한 뒤 마지막으로 Ec2를 설정하도록 하자. 

### AWS SNS 설정하기

AWS SNS는 topic이라는 것에 message들이 등록되고, subscriber들에게 전달되는 것이다. 그러므로 topic을 만들고, subscriber를 설정하는 것이 필요하다. 
AWS SNS에서는 subscriber를 엔드포인트라고 칭한다. 

#### 필요한 S3 bucket 정보 확인하기

AWS SNS 설정을 위해서는 event를 만들어 내는 S3 bucket arn이 필요하다. 

1. AWS S3 Console로 이동한다. 
2. Buckets 중 event를 발생시킬 bucket을 선택하여 이름을 저장해 둔다. 아래의 SNS Topic 권한 설정시 사용된다.  


#### topic 만들기 

1. AWS SNS console로 이동한다. 
2. **Topics** > **Create Topic**을 선택하면 Create Topic 대화 상자가 나온다. 
3. **Topic name**과 **Display name**에 적당한 topic 이름을 넣는다. 예를 들어 둘 다 'imageAdded'로 한다. 
4. **Create Topic**을 클릭하면 생성된 topic이 추가된것을 확인할 수 있다. 
5. 새로운 topic을 눌러 **topic arn**을 클릭하면 topic details 페이지가 나타난다. 
6. 이제 topic의 ARN을 저장해 두자. 

##### topic 접근 권한 설정하기 

S3 bucket에서 topic 접근 권한을 가져야만 event를 전달할 수 있다. 그 접근 권한은 다음과 같이 설정가능하다. 

1. AWS SNS Console에서 생성된 topic을 선택한다. 
2. **Other topic actions**을 선택하고, **edit topic policy**를 선택한다. 
3. Edit topic policy 창에서 **Advanced View** 탭을 선택한다. 
4. 다음과 같은 접근 권한을 변경하고 **Update Policy**를 선택한다.  

예를 들면, 아래는 default 설정이다. 

```
{
  "Version": "2008-10-17",
  "Id": "__default_policy_ID",
  "Statement": [
    {
      "Sid": "__default_statement_ID",
      "Effect": "Allow",
      "Principal": {
        "AWS": "*"
      },
      "Action": [
        "SNS:GetTopicAttributes",
        "SNS:SetTopicAttributes",
        "SNS:AddPermission",
        "SNS:RemovePermission",
        "SNS:DeleteTopic",
        "SNS:Subscribe",
        "SNS:ListSubscriptionsByTopic",
        "SNS:Publish",
        "SNS:Receive"
      ],
      "Resource": "SNS-ARN",
      "Condition": {
        "StringEquals": {
          "AWS:SourceOwner": "xxxxxxxxxx"
        }
      }
    }
  ]
}
```

위의 것을 다음과 같이 변경할 수 있다. SNS-ARN과 BUCKET-NAME은 실제 것으로 변경한다. 

```
{
 "Version": "2008-10-17",
 "Id": "example-ID",
 "Statement": [
  {
   "Sid": "example-statement-ID",
   "Effect": "Allow",
   "Principal": {
     "Service": "s3.amazonaws.com"  
   },
   "Action": [
    "SNS:Publish"
   ],
   "Resource": "SNS-ARN",
   "Condition": {
      "ArnLike": {          
      "aws:SourceArn": "arn:aws:s3:*:*:BUCKET-NAME"    
    }
   }
  }
 ]
}
```

### AWS S3 설정하기

1. AWS S3 Console로 이동한다. 
2. Buckets 중 event를 발생시킬 bucket을 선택하여 **Properties**를 선택한 후 **Events**를 클릭한다. 
3. **Name**상자에 이벤트를 구성하는 이름을 입력한다. 예를 들어 'ImageAddedEvent'
4. **Events** 상자를 클릭하고 이벤트 발생 시 전달할 이벤트 유형을 선택한다. 여기서는 ObjectCreated(All)에서 Put과 CompleteMultiPartUpload를 선택하면 새로운 파일이 추가되면 이벤트가 뜬다. 
5. **Prefix**와 **Suffix**를 정할 수 있다. 예를 들어 여기서는 **Suffix**에 'jpeg jpg png gif'로 여백을 띄운 텍스트로 설정해서 확장자가 이미지 인 파일이 업로드 되었을 때만 이벤트가 발생하도록 한다. 
6. **SendTo** 항목에는 **SNS topic**을 설정한다. 
7. 해당 topic을 선택하거나, **Add SNS topic ARN**을 선택하여 **SNS Topic ARN** 상자에 앞에서 만들어 둔 SNS topic arn을 붙여 넣기 한다. 
8. **Save**를 한다. 만약 위에서 Topic Policy를 제대로 설정하지 않았다면 에러가 뜨고 저장에 실패할 것이다. 
9. 이제 Events Section 아래에 새로운 이름의 Event가 추가된 것을 확인 할 수 있다. 

### AWS SQS 설정하기 

1. AWS SQS Console로 이동한다. 
2. **Create new Queue**를 선택하고 설정 페이지에서 **Queue Name**을 입력하고(예를 들어 'ImageQueue') 나머지는 default 설정으로 **Queue Settings**을 설정한 뒤 **Create Queue**를 클릭한다. 
3. Queues Page에서 생성된 Queue를 선택한 뒤 **Queue Actions**에서 **Subscribe Queues to SNS Topic**을 선택한다. 
4. **Choose a Topic**에서 앞에서 생성된 SNS topic을 선택한 후 **Subscribe**를 선택한다. 
5. 이제 AWS SNS Console로 가서 앞에서 만든 SNS topic을 보면 **Endpoint**가 이번에 만든 Queue로 설정이 되어 있다.
