---
layout: post
title: AWS - S3에 image file 업로드시 lambda를 통해 resize하기 
description: AWS - S3에 image file 업로드시 lambda를 통해 resize하기
modified: 2016-07-18
tags: [aws, lambda, s3, iam]
comments: true
image:
  feature: abstract-22.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

AWS S3에 이미지 파일이 upload가 되면 lambda를 통해 thumbnail을 추출하는 것을 해보자. 

- NOTE: AWS lambda는 Seoul Region에서는 지원하지 않으므로 여기서는 **tokyo region**을 기준으로 설명한다. 

## 참고

- [S3 + Lambda image resize 예제](http://docs.aws.amazon.com/ko_kr/lambda/latest/dg/with-s3-example.html)

## Architecture

사용자가 image 파일을 업로드 하는 S3 bucket은 ss-user-image, 처리후 저장하는 bucket은 ss-user-image-resized라고 가정한다. 원하는 구조는 다음과 같다.
그리고 lambda function은 여기서는 python code를 기준으로 설명한다. 

```
S3 Bucket(ss-user-image) --> lambda (python) --> S3 Bucket(ss-user-image-resized)
```

## 0. 사전 준비

- AWS Console은 **tokyo region**을 지정해야 한다. 
- IAM user는 administratorAccess 권한을 가지고 있어야 한다. 
- EC2 instance를 **Amazon AMI 64bit**로 구동하고 있어야 한다. 
- 해당 EC2 instance에 접속할 수 있는 pem 파일을 로컬에 다운로드 받아놓아야 한다. 
- chmod 400 [pem 파일]

- 저장할 것: EC2 instance의 public IP address

## 1. S3 bucket 생성하기

1. AWS에 Admin 권한 user로 로그인 한 후 region을 **tokyo**로 바꾼다.(확인한다.)
2. AWS S3 Console로 이동한다. 
3. **Create Bucket**으로 ss-user-image와 ss-user-image-resized를 생성한다. 

- 저장할 것: Bucket의 이름들

## 2. Lambda function 생성해서 수동으로 테스트하기

아래 내용은 Lambda function을 zip으로 압축해서 등록하고 테스트 하는 방법이다. AWS Console상에서 코드를 직접 붙여 넣는 경우 간단한 코드는 되지만, dependency가 있는 라이브러리를 올리는 것은 까다롭다. 그래서 아래와 같은 방법을 사용하는 것이다. 

### 2.1 실행을 위한 IAM role만들기 

1. AWS IAM console 
2. **Roles** > **Create New Role** 
3. **Role name**에 'lambda-s3-execution-role** 입력 후 **Next** 선택
4. Select Role Type에서 **AWS Service Roles** > **AWS Lambda**의 **select** 버튼 선택하여 해당 role에 Lambda 서비스를 동작할 수 있는 role permission을 부여한다. 
5. **Attach Policy**에서 Policy Type 검색에서 **AWSLambdaExecute**을 찾아서 선택하고 **Next Step**. 
6. 이제 Role ARN을 저장하고 **Create Role**을 선택한다. 

- 저장할 것: Role ARN

### 2.2 코드 준비하기 

다음의 python code는 source bucket의 image를 받아서 resize를 한 후 그 결과를 destination bucket에 저장하는 예이다. 다음의 코드는 아래 기능을 수행한다. 

- source bucket이름은 s3로부터의 event로부터 추출한다. 
- destination bucket의 이름은 source bucket이름 + '-resized' 로 만든다. 만약 bucket이름을 바꾸고 싶다면 이 부분을 수정하면 된다. 
- image resize를 위한 함수는 [Python Image Library(PIL)](http://effbot.org/imagingbook/introduction.htm)을 사용한다. 

```python
from __future__ import print_function
import boto3
import os
import sys
import uuid
from PIL import Image
import PIL.Image
     
s3_client = boto3.client('s3')
     
def resize_image(image_path, resized_path):
    with Image.open(image_path) as image:
        image.thumbnail(tuple(x / 2 for x in image.size))
        image.save(resized_path)
     
def handler(event, context):
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = record['s3']['object']['key'] 
        download_path = '/tmp/{}{}'.format(uuid.uuid4(), key)
        upload_path = '/tmp/resized-{}'.format(key)
        
        s3_client.download_file(bucket, key, download_path)
        resize_image(download_path, upload_path)
        s3_client.upload_file(upload_path, '{}-resized'.format(bucket), key)
```

### 2.3 패키지 만들기 

1. 위의 코드를 CreateThumbnail.py로 저장한다. 
2. scp -i [pem 파일] [CreateThumbnail.py 경로] ec2-user@public-ip-address:~/CreateThumbnail.py 로 py파일을 EC2로 upload한다. 
3. ssh -i [pem 파일] ec2-user@public-ip-address 로 EC2에 접속한다. 
4. 설치 및 압축을 위해 다음 명령들을 순차적으로 실행한다. 

```bash
sudo yum install python27-devel python27-pip gcc
sudo yum install libjpeg-devel zlib-devel   --> 실행한 후 중간에 y
virtualenv ~/shrink_venv
source ~/shrink_venv/bin/activate  --> shrink_venv 환경으로 모든 변환
pip install Pillow
pip install boto3

zip -9 ~/CreateThumbnail.zip --> error가 표시되지만 무시하고(믿고!) 그대로 진행한다. 
cd $VIRTUAL_ENV/lib/python2.7/site-packages
zip -r9 ~/CreateThumbnail.zip *
cd $VIRTUAL_ENV/lib64/python2.7/site-packages
zip -r9 ~/CreateThumbnail.zip *
cd ~
zip -g CreateThumbnail.zip CreateThumbnail.py
```

### 2.4 Lambda function 생성

#### Credentials 설정하기

1. id와 access key 정보가 저장된 .csv 파일을 연다. 
2. ssh -i [pem 파일] ubuntu@public-ip-address 로 EC2에 접속한다. 
3. ~/.aws 폴더가 있는지 확인해서 없으면 폴더를 만든다.

- ~/.aws/crendentials 파일을 만들고 아래와 같이 key id와 access key를 추가한다.

```
[{AWS user명}]
aws_access_key_id=YOUR_KEY
aws_secret_access_key=YOUR_SECRET
```
- ~/.aws/config 파일을 만들고 아래와 같이 region을 추가한다. (Tokyo라 가정)

```
[default]
region=ap-northeast-1
```

#### 함수 생성

1. ssh -i [pem 파일] ubuntu@public-ip-address 로 EC2에 접속한다. 
2. 다음 Lambda CLI command를 실행하여 Lambda function을 생성한다. 

- {Region} : 예를 들어 ap-northeast-1
- {file-path}: 루트일 경우 생략 (fileb://CreateThumbnail.zip)
- {role-arn}: 앞에서 저장한 role의 arn
- {runtime}: nodejs4.3, python2.7, java8 중 하나를 선택 
- {user-name}: ~/.aws/crendentials에 저장된 AWS user name

```
$ aws lambda create-function \
--region {Region} \
--function-name CreateThumbnail \
--zip-file fileb://{file-path}/CreateThumbnail.zip \
--role {role-arn} \
--handler CreateThumbnail.handler \
--runtime {runtime} \
--profile {user-name} \
--timeout 10 \
--memory-size 1024
```

위 명령을 수행한 뒤 나오는 아래와 같은 출력에서 **function ARN**을 저장한다. 

```
{
    "CodeSha256": "iNUGVflx7EYUW9Uvh1EvIFjk0y8lxxxxxxNs7Nk=", 
    "FunctionName": "CreateThumbnail", 
    "CodeSize": 8733802, 
    "MemorySize": 1024, 
    "FunctionArn": "arn:aws:lambda:ap-northeast-1:xxxxxx:function:CreateThumbnail", <-- 저장
    "Version": "$LATEST", 
    "Role": "arn:aws:iam::xxxxxx:role/lambda-s3-execution-role", 
    "Timeout": 10, 
    "LastModified": "2016-07-29T06:32:42.439+0000", 
    "Handler": "CreateThumbnail.handler", 
    "Runtime": "python2.7", 
    "Description": ""
}
```

- Note: AWS Lambda console > Dash board에서 생성된 function과 function ARN(우측 상단)을 확인할 수 있다. 

### 2.5 수동으로 테스트 

#### event 준비 

- 터미널을 Local에서 새로 열어 pem파일이 있는 폴더에 아래 내용을 input.txt 파일로 저장한다. 코드 중 {sourcebucket}에 'ss-user-image'를 넣는다. 이때, AWS S3 Console로 이동하여 {HappyFace.jpg}에 해당하는 image 파일을  'ss-user-image'에 upload해서 올려 놓는다. 

```
{  
   "Records":[  
      {  
         "eventVersion":"2.0",
         "eventSource":"aws:s3",
         "awsRegion":"ap-northeast-1",
         "eventTime":"1970-01-01T00:00:00.000Z",
         "eventName":"ObjectCreated:Put",
         "userIdentity":{  
            "principalId":"AIDAJDPLRKLG7UEXAMPLE"
         },
         "requestParameters":{  
            "sourceIPAddress":"127.0.0.1"
         },
         "responseElements":{  
            "x-amz-request-id":"C3D13FE58DE4C810",
            "x-amz-id-2":"FMyUVURIY8/IgAtTv8xRjskZQpcIZ9KG4V5Wp6S7S/JRWeUWerMUE5JgHvANOjpD"
         },
         "s3":{  
            "s3SchemaVersion":"1.0",
            "configurationId":"testConfigRule",
            "bucket":{  
               "name":"{sourcebucket}",
               "ownerIdentity":{  
                  "principalId":"A3NL1KOZZKExample"
               },
               "arn":"arn:aws:s3:::{sourcebucket}"
            },
            "object":{  
               "key":"{HappyFace.jpg}",
               "size":1024,
               "eTag":"d41d8cd98f00b204e9800998ecf8427e",
               "versionId":"096fKKXTRTtl3on89fVO.nfljtsv6qko"
            }
         }
      }
   ]
}
```

- scp -i [perm파일] input.txt ec2-user@[amazonlinux ip]:~/input.txt 로 upload한다. 

#### event 실행 

Amazon Linux EC2와 연결된 terminal에서 아래의 AWS Lambda CLI를 실행한다. 

- {region}: 예를 들어 ap-northeast-1
- {file-path}: 루트일 경우 생략 (fileb://input.txt
- {user-name}: ~/.aws/crendentials에 저장된 AWS user name

```
$ aws lambda invoke \
--invocation-type Event \
--function-name CreateThumbnail \
--region {region} \
--payload file://{file-path}/input.txt \
--profile {user-name} \
outputfile.txt
```

다음과 같은 Status Code가 뜨면 명령은 제대로 전달 된 것이다.  

```
{
    "StatusCode": 202
}
```

#### 결과 확인 

이제 S3의  'ss-user-image-resized' bucket에 thumbnail이 생성되어 있는지 확인하자. 

- 혹시 에러가 발생했다면 CloudWatch > Logs에서 확인 가능하다.  


## 3. S3를 event source로 동작시키기 

### 3.1 S3가 Lambda에 event를 전달할 수 있게 permssion 주기 

1. AWS Lambda Console로 이동하여 CreateThumbnail 함수를 선택한다. 
2. **Triggers** tap에서 **+Add trigger**를 선택한다. 
3. Add Trigger 창에서 점선 박스를 클린한후 s3를 선택한다. 
4. event를 보내는 bucket을 선택(ss-user-image)하고 Event type은 **put**, Suffix는 .jpg로 한다. 
5. Submit을 선택한다. 

- Note: S3 bucket의 Event 설정도 자동으로 추가되어 있다!

### 3.2 테스트

S3 ss-user-image bucket에 파일을 upload한다. 그리고 ss-user-image-resized로 이동해서 thumbnail이 생성되어 있는지 확인하자.

## 4. S3 설정 및 외부 사용자 테스트

### 4.1 S3 접근 Permission 문제 

S3 ss-user-image-resized로 이동해서 생성된 thumbnail의 **Properties**를 선택한 후 **Link**를 click한다. 
아마도 permission deny로 Webbrowser에서 file을 열수 없을 것이다. 

### 4.2 S3 설정

[S3 버킷 정책 예제](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/example-bucket-policies.html#example-bucket-policies-use-case-2)의 익명 사용자에게 읽기 전용 권한 부여를 참고하자. S3 ss-user-image-resized bucket의 **Properties** > **permissions** > **Add bucket policy**를 선택해서 아래 내용을 paste한다. {bucket-name} 대신 ss-user-image-resized를 넣는다.  

```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Sid":"AddPerm",
      "Effect":"Allow",
      "Principal": "*",
      "Action":["s3:GetObject"],
      "Resource":["arn:aws:s3:::{bucket-name}/*"]
    }
  ]
}
```

### 4.3 S3 테스트 

S3 ss-user-image-resized로 이동해서 생성된 thumbnail의 **Properties**를 선택한 후 **Link**를 click해본다. 정상적으로 열리거나 download 창이 뜨면 이제 성공이다!
