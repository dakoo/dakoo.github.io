---
layout: post
title: AWS S3에 image file 업로드시 lambda를 통해 resize하고 dynamodb에 file 정보 추가하기
description: AWS S3에 image file 업로드시 lambda를 통해 resize하고 dynamodb에 file 정보 추가하기
modified: 2016-07-18
tags: [aws]
comments: true
image:
  feature: abstract-22.png
---
AWS S3에 이미지 파일이 upload가 되면 lambda를 통해 dynamodb에 file 정보를 추가하는 것을 해보자.

- NOTE: AWS lambda는 Seoul Region에서는 지원하지 않으므로 여기서는 **tokyo region**을 기준으로 설명한다. 

## 참고

- [S3 + Lambda image resize 예제](http://docs.aws.amazon.com/ko_kr/lambda/latest/dg/with-s3-example.html)

## Architecture

사용자가 image 파일을 업로드 하는 S3 bucket은 ss-user-image, 처리후 저장하는 bucket은 ss-user-image-resized라고 가정한다. 원하는 구조는 다음과 같다.
그리고 lambda function은 여기서는 python code를 기준으로 설명한다. 

```
S3 Bucket(ss-user-image) --> lambda (python) --> S3 Bucket(ss-user-image-resized)
                                           --> dynamodb 
```

## 1. S3 bucket 생성하기

1. AWS에 Admin 권한 user로 로그인 한 후 region을 **tokyo**로 바꾼다.(확인한다.)
2. AWS S3 Console로 이동한다. 
3. **Create Bucket**으로 ss-user-image와 ss-user-image-resized를 생성한다. 

## 2. Lambda function 생성해서 수동으로 테스트하기

### 2.1 실행을 위한 IAM role만들기 

1. AWS IAM console 
2. **Roles** > **Create New Role** 
3. **Role name**에 'lambda-s3-execution-role** 입력
4. **Select Role Type**에서 **AWS Service Roles** > **AWS Lambda**. 이것으로 Lambda 서비스를 동작할 수 있는 role permission을 부여.
5. **Attach Policy**에서 **AWSLambdaExecute**을 선택
6. 이제 만들어진 role ARN을 저장해 놓는다. 

### 2.2 코드 준비

다음의 python code는 source bucket의 image를 받아서 resize를 한 후 그 결과를 destination bucket에 저장하는 예이다. 

- source bucket이름은 s3로부터의 event로부터 추출한다. 
- destination bucket의 이름은 source bucket이름 + '-resized' 로 만든다. 
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
        s3_client.upload_file(upload_path, '{}resized'.format(bucket), key)
```

### 2.3 패키지 만들기 

1. 위의 코드를 CreateThumbnail.py로 저장한다. 
2. scp -i [pem 파일] [CreateThumbnail.py 경로] ubuntu@public-ip-address:~/CreateThumbnail.py 로 py파일을 EC2로 upload한다. 
3. ssh -i [pem 파일] ubuntu@public-ip-address 로 EC2에 접속한다. 
4. 설치 및 압축을 위해 다음 명령들을 순차적으로 실행한다. 

```bash
sudo yum install libjpeg-devel zlib-devel
virtualenv ~/shrink_venv
source ~/shrink_venv/bin/activate
pip install Pillow
pip install boto3
zip -9 ~/CreateThumbnail.zip
cd $VIRTUAL_ENV/lib/python2.7/site-packages
zip -r9 ~/CreateThumbnail.zip *
cd $VIRTUAL_ENV/lib64/python2.7/site-packages
zip -r9 ~/CreateThumbnail.zip *
cd ~
zip -g CreateThumbnail.zip CreateThumbnail.py
```

### 2.4 Lambda function 생성

1. ssh -i [pem 파일] ubuntu@public-ip-address 로 EC2에 접속한다. 
2. 다음 Lambda CLI command를 실행하여 Lambda function을 생성한다. 

- {Region} : 예를 들어 ap-northeast-1
- {file-path}: 루트일 경우 생략 (fileb://CreateThumbnail.zip)
- {role-arn}: 앞에서 저장한 role의 arn
- {runtime}: nodejs4.3, python2.7, java8 중 하나를 선택 

```
$ aws lambda create-function \
--region {Region} \
--function-name CreateThumbnail \
--zip-file fileb://{file-path}/CreateThumbnail.zip \
--role {role-arn} \
--handler CreateThumbnail.handler \
--runtime {runtime} \
--profile adminuser \
--timeout 10 \
--memory-size 1024
```

위 명령을 수행한 뒤 나오는 **function ARN**을 저장한다. 

- Note: AWS Lambda Console을 통해서도 위의 것을 진행할 수 있다!

### 2.5 수동으로 테스트 

#### event 준비 

다음을 input.txt 파일로 저장한다. {sourcebucket}에 'ss-user-image'를 넣는다. 그리고 {HappyFace.jpg}에 해당하는 image 파일을  'ss-user-image'
에 upload해서 올려 놓는다. 

```
{  
   "Records":[  
      {  
         "eventVersion":"2.0",
         "eventSource":"aws:s3",
         "awsRegion":"us-west-2",
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

#### event 실행 

AWS Lambda CLI를 실행한다. 

```
$ aws lambda invoke \
--invocation-type Event \
--function-name CreateThumbnail \
--region us-west-2 \
--payload file://{file-path}/inputfile.txt \
--profile adminuser \
outputfile.txt
```

#### 결과 확인 

이제 S3의  'ss-user-image-resized' bucket에 thumbnail이 생성되어 있는지 확인하자. 그리고 AWS CloudWatch console로 이동하여 **Cloudwatch Metrics at a glance section**을 확인한다. 

## 3. S3를 event source로 동작시키기 

http://docs.aws.amazon.com/ko_kr/lambda/latest/dg/with-s3-example-configure-event-source.html
