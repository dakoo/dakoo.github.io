---
layout: post
title: AWS - S3 파일 upload시에 aws lambda를 통해 dynamodb에 아이템 추가하기 
description: AWS - S3 파일 upload시에 aws lambda를 통해 dynamodb에 아이템 추가하기 
modified: 2016-07-31
tags: [aws]
comments: true
image:
  feature: abstract-21.png
---
앞의 AWS S3 -> Lambda에 이은 Lambda -> DynamoDB를 다루어보자. 

## 요구사항

Lambda에서 thumbnail을 성공적으로 추출한 경우 DynamoDB에 orignial image URL, thumbnail URL, 날짜를 추가한다. 

DynamoDB의 Table은 다음과 같다. 이름은 'UserImage'이라고 하자. ImageID는 Image마다의 공유 id이고 Date는 향후 Sorting시에 사용될 수 있다. 

```
    { 
      'ImageID' : {'S': 'xxxxxxx' },
      'Date': { 'S': 'YYYY-MM-DD' }
      'ImageURL': { 'S': 'XXXXXXXXXXXXXXXXXXXXXX' },
      'ThumbnailURL': { 'S': 'XXXXXXXXXXXXXXXXXXXXXX' },
    }
```

## 0. 사전 준비

- [S3 이미지를 resize하는 예제](http://hochulshin.com/aws-s3-lambda/)를 미리 완료해 놓는다. 
- 알고 있을 것: EC2 instance의 public IP address, bucket 이름, region

## 1. DynamoDB table 생성하기

- 1. AWS dynamodb console으로 이동한다. 
- 2. Table Name: UserImage
- 3. Primary Key: Partition Key - ImageID(String), Add sort key - Date(String)
- 4. Table settings: Check 'Use default settings'
- 5. **Create**

## 2. Lambda function 생성해서 수동으로 테스트하기

### 2.1 실행을 위한 IAM role 수정하기  

1. AWS IAM console 
2. **Roles** > 이전에 생성한 **lambda-s3-execution-role** 선택
3. Policy를 추가하기 위해 Permissions tab에서 **Attach Policy** 선택한다. 
4. **Attach Policy**에서 Policy Type 검색에서 **AmazonDynamoDBFullAccess**를 찾아서 선택한다. 
5. 이제 lambda-s3-execution-role에 AWSLambdaExecute와 AmazonDynamoDBFullAccess가 추가되어 있는 걸 확인할 수 있다. 

- 참고: AWSLambdaExecute를 보면 웃기게도 S3 GetObject와 PutObject에 대한 내용이다. AWSLambdaxxxxx는 사실 Lambda에 대한 권한이 아닌 lambda가 다른 component/서비스를 access할 수 있는 권한에 대한 것이다.  
- 저장할 것: Role ARN

### 2.2 코드 준비하기 

다음의 python code는 source bucket의 image를 받아서 resize를 한 후 그 결과를 destination bucket에 저장한 후 그 내용을 dynamodb에 저장하는 것이다. [dynamodb와 python tutorial](http://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/gettingstartedguide/GettingStarted.Python.html)과 [boto3 dynamodb tutorial](http://boto3.readthedocs.io/en/latest/guide/dynamodb.html)을 참조하자!

- source bucket이름은 s3로부터의 event로부터 추출한다. 
- destination bucket의 이름은 source bucket이름 + '-resized' 로 만든다. 만약 bucket이름을 바꾸고 싶다면 이 부분을 수정하면 된다. 
- image resize를 위한 함수는 [Python Image Library(PIL)](http://effbot.org/imagingbook/introduction.htm)을 사용한다. 
- dynamodb에 입력할 ImageURL과 ThumbnailURL은 bucket이름과 key(파일명)으로 부터 추출한다. 
- dynamodb에 입력할 ImageID는 key(파일명)으로 한다. 
- dynamodb에 입력할 Date는 python에 내장된 datetime library를 이용해 현재 시간으로 저장한다. 
- 아래 내용에서 {REGION-NAME}, {TABLE-NAME}을 수정해야 한다. 

```python
from __future__ import print_function
import boto3
from boto3.dynamodb.conditions import Key, Attr
import botocore.exceptions
import os
import sys
import uuid
import json
from time import gmtime, strftime
from PIL import Image
import PIL.Image

region_name = {REGION-NAME}
table_name = {TABLE-NAME}
     
s3_client = boto3.client('s3')
dynamodb_table = boto3.resource('dynamodb', region_name).Table(table_name)

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
    output_bucket = '{}-resized'.format(bucket)
    s3_client.upload_file(upload_path, output_bucket, key)

    imageID = key
    date = strftime("%Y-%m-%d %H:%M:%S", gmtime())
    imageURL = "https://s3-{}.amazonaws.com/{}/{}".format(region_name, bucket,key)
    thumbnailURL = "https://s3-{}.amazonaws.com/{}-resized/{}".format(region_name, bucket,key)

    dynamodb_table.put_item(
      Item={
        'ImageID':key,
        'Date': date,
        'ImageURL': imageURL,
        'ThumbnailURL':thumbnailURL
      }
    )

    response = dynamodb_table.query(
      KeyConditionExpression=Key('ImageID').eq(key)
    )
    file_path = '/tmp/dynamodb_response'
    f = open(file_path, 'w')
    items = json.dumps(response['Items'])
    for item in items:
        f.write(str(item))
    f.close()
    s3_client.upload_file(file_path, output_bucket, 'dynamodb_response')


```

### 2.3 패키지 만들기

 [이전 포스트](http://hochulshin.com/aws-s3-lambda/)와 유사하며 python파일 이름만 다르다.

1. 위의 코드를 CreateThumbnail2.py로 저장한다. 
2. scp -i [pem 파일] [CreateThumbnail2.py 경로] ec2-user@public-ip-address:~/CreateThumbnail2.py 로 py파일을 EC2로 upload한다. 
3. ssh -i [pem 파일] ec2-user@public-ip-address 로 EC2에 접속한다. 
4. 설치를 하지 않은 경우 설치를 위해 다음 명령들을 순차적으로 실행한다. (이전 포스트에서 진행한 것이므로 아마 설치가 되어 있을 것임)

```bash
#### 이전 포스트에서 진행한 것이므로 SKIP
sudo yum install python27-devel python27-pip gcc
sudo yum install libjpeg-devel zlib-devel   --> 실행한 후 중간에 y
virtualenv ~/shrink_venv

source ~/shrink_venv/bin/activate  --> shrink_venv 환경으로 모드 변환

pip install Pillow
pip install boto3
#####
```

- 이제 python 코드와 환경을 압축하자. 아래 코드는 루트 폴더에서 **bash script로 만들어서** 진행하자. Compress_py.sh로 저장하고 `chmod +x Compress_py.sh`로 실행 권한을 준 후 실행한다. 

```bash
#!/bin/bash
zip_file_name=CreateThumbnail2.zip
python_file_name=CreateThumbnail2.py

echo "
Deleting the old compressed file
"

rm -rf $zip_file_name

echo "
Compressing the new lambda function and environment
"
source ~/shrink_venv/bin/activate
zip -9 ~/${zip_file_name}
cd $VIRTUAL_ENV/lib/python2.7/site-packages
zip -r9 ~/CreateThumbnail2.zip *
cd $VIRTUAL_ENV/lib64/python2.7/site-packages
zip -r9 ~/${zip_file_name} *
cd ~
zip -g ${zip_file_name} ${python_file_name}
~                                               
```

### 2.4 Lambda function 생성

#### Credentials 설정하기

!!!! 아래 내용은 이전에 이미 진행했으므로 SKIP !!!

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
2. 다음 Lambda CLI command를 실행하여 Lambda function을 생성한다. bash script로 만들어서 실행하는 것이 좋다.  

- {Region} : 예를 들어 ap-northeast-1
- {file-path}: 루트일 경우 생략 (예: CreateThumbnail2.zip)
- {role-arn}: 앞에서 저장한 role의 arn (IAM >ROLES에서 확인)
- {runtime}: nodejs4.3, python2.7, java8 중 하나를 선택. 여기서는 python2.7
- {user-name}: ~/.aws/crendentials에 저장된 AWS user name
- {handler} : py파일 내의 handler
- {function_name} : lambda function name

```bash
#!/bin/bash
region={Region}
role_arn={role-arn}
function_name={function_name}
handler={handler}
compressed_file_path={file-path}
runtime={runtime}
user_name={user-name}


echo "
deleting the exising lambda function
"
aws lambda delete-function \
 --function-name $function_name \
 --profile $user_name \
 --region $region

echo "
creating a new lambda function
"

aws lambda create-function \
 --function-name $function_name \
 --zip-file fileb://${compressed_file_path} \
 --role $role_arn \
 --handler ${function_name}.${handler} \
 --runtime $runtime \
 --profile $user_name \
 --region $region

```

아래와 같이 로그가 나오면 성공한 것이다. AWS Lambda console에서도 function 생성을 확인 가능하다.

```
{
    "CodeSha256": "iNUGVflx7EYUW9Uvh1EvIFjk0y8lxxxxxxNs7Nk=", 
    "FunctionName": "CreateThumbnail2", 
    "CodeSize": 8733802, 
    "MemorySize": 1024, 
    "FunctionArn": "arn:aws:lambda:ap-northeast-1:xxxxxx:function:CreateThumbnail2", 
    "Version": "$LATEST", 
    "Role": "arn:aws:iam::xxxxxx:role/lambda-s3-execution-role", 
    "Timeout": 10, 
    "LastModified": "2016-07-29T06:32:42.439+0000", 
    "Handler": "CreateThumbnail.handler", 
    "Runtime": "python2.7", 
    "Description": ""
}
```

#### event 준비 

- 터미널을 Local에서 새로 열어 pem파일이 있는 폴더에 아래 내용을 input.txt 파일로 저장한다. 코드 중 {sourcebucket}에 'ss-user-image'를 넣는다. 이때, AWS S3 Console로 이동하여 **{HappyFace.jpg}에 해당하는 image 파일을  'ss-user-image'에 upload해서 올려 놓는다**. 

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

Amazon Linux EC2와 연결된 terminal에서 아래의 AWS Lambda CLI를 실행한다. 이것도 bash script로 만들어 두는 것이 좋다. (send_fake_event.sh)

- {Region} : 예를 들어 ap-northeast-1
- {file-path}: 루트일 경우 생략 (예: input.txt)
- {user-name}: ~/.aws/crendentials에 저장된 AWS user name
- {function_name} : lambda function name
- {outputfile} : output file(생성될) (있어도 상관없음)

```
#!/bin/bash
region={region}
function_name={function_name}
event_file={file-path}
user_name={user-name}
output_file={outputfile}

echo "
testing new lambda function by sending an event
"
aws lambda invoke \
--invocation-type Event \
--function-name $function_name \
--region $region \
--log-type Tail \
--payload file://${event_file} \
--profile ${user_name} \
--output json \
${output_file}

```

다음과 같은 Status Code가 뜨면 명령은 제대로 전달 된 것이다.  

```
{
    "StatusCode": 202
}
```

#### 결과 확인 

- S3의  'ss-user-image-resized' bucket에 thumbnail이 생성되어 있는지 확인하자. 
- DynamoDB console의 table에서 item이 제대로 들어갔는지 확인한다. 
- 'ss-user-image-resized' bucket에 xxx-update.txt파일이 생성되어 있는지 확인한다.  
- dynamodb_update 파일을 download해서 정상적으로 dynamodb의 table로부터 URL을 읽었는지를 확인하자. 

- 혹시 에러가 발생했다면 CloudWatch > Logs에서 확인 가능하다.  

## 3. S3를 event source로 동작시키기 

### 3.1 S3가 Lambda에 event를 전달할 수 있게 permssion 주기 

1. AWS Lambda Console로 이동하여 CreateThumbnail2 함수를 선택한다. 
2. **Triggers** tap에서 **+Add trigger**를 선택한다. 
3. Add Trigger 창에서 점선 박스를 클린한후 s3를 선택한다. 
4. event를 보내는 bucket을 선택(ss-user-image)하고 Event type은 **put**, Suffix는 .jpg로 한다. 
5. Submit을 선택한다. 

- Note: S3 bucket의 Event 설정도 자동으로 추가되어 있다!

### 3.2 테스트

S3 ss-user-image bucket에 파일을 upload한다. 그리고 ss-user-image-resized로 이동해서 thumbnail이 생성되어 있는지 확인하고 dynamodb_update 파일이 잘 생성되고 update되었는지 내용을 확인한다. 

## 4. Future work

여기서는 구현하지 않지만, S3의 파일과 Dynamodb의 item과 동기화를 위해서는 S3의 Object가 Delete시에도 비슷한 trigger가 발생해서 Lambda를 통해 dynamodb를 update하면 해야 한다. 
