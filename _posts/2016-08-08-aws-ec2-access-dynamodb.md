---
layout: post
title: AWS - EC2에서 dynamodb 접근하기
description: AWS EC2에서 dynamodb 접근하기
modified: 2016-08-08
tags: [aws, ec2, dynamodb, iam]
comments: true
image:
  feature: abstract-22.png
---
AWS EC2에서 DynamoDB에 접근하여 아이템을 추가/삭제/갱신 등을 수행하는 방법을 알아보자. 
여기서는 EC2의 bash에서 python 코드를 실행하여 table에 아이템을 추가하는 것을 예로 들어 설명한다. [dynamodb python api](http://boto3.readthedocs.io/en/latest/guide/dynamodb.html)를 참조하면 다른 operation 구현도 용이하다. 


## 1. EC2에 IAM Role 할당

EC2 생성시 dynamodb에 접근할 수 있는 Role을 할당해야 한다. [이전 글](http://hochulshin.com/aws-how-ec2-use-iam/)을 참고해 진행하자. 

## 2. EC2에 boto3 설치 

[EC2에서 Event 처리에 대한 글](http://hochulshin.com/aws-s3-sns-sqs-event/)에서 내용을 참조해 아래와 같이 진행해보자. 

### 2.1 EC2 인스턴스에 연결하기

1. AWS EC2 Console로 이동
2. 해당 instance를 선택 후 아래 Description에 Public IP를 저장
3. 터미널에서 pem 파일이 있는 폴더로 이동
4. ‘chmod 400 pem파일명’으로 pem 파일의 permission을 변경한다.
5. instance로 연결 (ssh -i [pem파일경로] [ubuntu 또는 ec2-user]@[ec2 instance의 publicIP] )


### 2.2 필요 package 설치 

1. sudo apt-get update
2. sudo apt-get -y install python-pip
3. sudo pip install boto3

### 2.3 Credentials 설정하기

이 단계를 위해 AWS IAM console > Users > User 선택 > Security Credentials > Access Keys에서 생성한 후 id와 access key 정보를 복사한다.

1. 앞에서 열어둔 EC2 instance와 연결된 terminal로 이동한다.
2. ~/.aws 폴더가 있는지 확인해서 없으면 폴더를 만든다.
3. ~/.aws/crendentials 파일을 만들고 아래와 같이 key id와 access key를 추가한다.

```
[default]
aws_access_key_id = YOUR_KEY
aws_secret_access_key = YOUR_SECRET

```

### 2.4 Region 설정하기

1. 앞에서 열어둔 EC2 instance와 연결된 terminal로 이동한다.
2. ~/.aws/config 파일을 만들고 아래와 같이 region을 추가한다. 서울의 경우는 다음과 같다.

```
[default]
region=ap-northeast-2
```

## 3. Dynamodb 접근 

여기서는 아이템 생성 코드하는 예만 들어 설명한다. 아래 코드를 보고 [dynamodb python api](http://boto3.readthedocs.io/en/latest/guide/dynamodb.html)를 참조하면 다른 operation도 쉽게 작성할 수 있을 것이다. 

### 3.1 dynamodb table 생성 

1. AWS dynamodb console
2. **Tables** > **Create table**
3. Table name - table-name, Primary key - title
4. 생성 후 Indexes 탭에서 **Create Index**
5. Primary key - tag, Projeted attributes - include로 변경 후 tag와 url을 add하고 **Create index**

### 3.2 Python 코드 

아래 putItem.py python code는 다음과 같이 실행하는 코드이다. 

- python putItem.py [title] [url] [tag]


```python
from __future__ import print_function
import boto3
from boto3.dynamodb.conditions import Key, Attr
import botocore.exceptions
import sys

def put_item(argv):
    title = argv[0]
    url = argv[1]
    tag = argv[2]
    dynamodb = boto3.resource('dynamodb')
    table = dynamodb.Table('table-name')  ## table 이름
    table.put_item(
        Item={
            'title': key,
            'tag': tag,
            'url': url
        }
    )

def main(argv):
    if len(argv) != 3:
        print("Invalid argument!")
        sys.exit(2)
    put_item(argv)

if __name__ == "__main__":
    main(sys.argv[1:])

```

### 3.3 Bash에서 python 코드 호출하기 

bash script에서 python code를 실행하는 것은 간단하지만, 강력한 툴이 된다. 다음과 같이 put.sh를 만들고 `chmod +x put.sh`로 저장한 후 실행해보자. 

```bash
#!/bin/bash
title='mytitle'
url='http://sample.com'
tag='fake'
python putItem.py $region $bucket $title
```

## 4. 테스트

1. terminal에서 위의 bash script를 호출 
2. AWS dynamodb console의 table로 이동
3. Items 탭에서 확인하여 성공적으로 아이템이 추가되었으면 성공!!!!


