---
layout: post
title: AWS EC2에서의 Boto3 개발 환경 설정
description: AWS EC2에서의 Boto3 개발 환경 설정
modified: 2016-08-09
tags: [aws, python, boto3, ec2]
comments: true
image:
  feature: abstract-11.png
---
AWS EC2 Ubuntu에서의 Python Boto3 개발 환경을 설정하는 방법을 살펴 보자. 

Credential과 virtualenv가 이미 설정/설치 되어 있는 경우에는 2번부터 진행한다. 

## 0. Credential 설정

1. id와 access key를 알고 있어야 한다. 
2. ssh -i [pem 파일] ubuntu@public-ip-address 로 EC2에 접속 
3. ~/.aws 폴더가 있는지 확인해서 없으면 폴더를 만든다.
4. ~/.aws/crendentials 파일을 만들고 아래와 같이 key id와 access key를 추가한다.

```
[{AWS user명}]
aws_access_key_id=YOUR_KEY
aws_secret_access_key=YOUR_SECRET
```

~/.aws/config 파일을 만들고 아래와 같이 region을 추가한다. (Seoul이라 가정)

```
[default]
region=ap-northeast-2
```

## 1. virtualenv 설치 

[이전 글](http://hochulshin.com/python-virtualenv-ubuntu/)을 참고하여 virtualenv를 설치한다. 

- sudo apt-get install python-virtualenv

## 2. 프로젝트 폴더 생성 및 Virtual environment 설정 후 진입

1. 프로젝트 폴더 생성 및 이동
2. virtualenv venv
3. . venv/bin/activate (빠져나올때는 deactivate)

## 3. boto3 설치 

1. 가상 환경에서 boto3를 설치 (`pip install boto3`)
2. python interpreter 실행하여 아래와 같이 테스트

```
>>> import boto3
>>> s3 = boto3.resource('s3')
>>> for bucket in s3.buckets.all():
        print(bucket.name)
```

이제 개발을 시작하자!
