---
layout: post
title: aws lambda에서 dynamodb data update하기 
description: aws lambda에서 dynamodb data update하기 
modified: 2016-07-31
tags: [dev]
comments: true
image:
  feature: abstract-21.png
---
앞의 AWS S3 -> Lambda에 이은 Lambda -> DynamoDB를 다루어보자. 

## 요구사항

Lambda에서 thumbnail을 성공적으로 추출한 경우 DynamoDB에 orignial image URL, thumbnail URL, 날짜를 추가한다. 

DynamoDB의 Table은 다음과 같다. 이름은 'UserImageTable'이라고 하자. 

```
    {       'ImageURL': { 'S': 'XXXXXXXXXXXXXXXXXXXXXX' },
            'ThumbnailURL': { 'S': 'XXXXXXXXXXXXXXXXXXXXXX' },
            'Date': { 'S': 'YYYY-MM-DD' }
    }
```

## 0. 사전 준비

- [S3 이미지를 resize하는 예제](http://hochulshin.com/aws-s3-lambda/)를 미리 완료해 놓는다. 
- 알고 있을 것: EC2 instance의 public IP address, bucket 이름, region

## 1. DynamoDB table 생성하기
