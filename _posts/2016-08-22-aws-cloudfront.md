---
layout: post
title: AWS - CloudFront 사용하기
description: AWS - CloudFront 사용하기
modified: 2016-08-22
tags: [aws]
comments: true
image:
  feature: abstract-16.png
---
HLS 스트리밍을 위해 Seoul Region에서 생성한 S3 bucket에 저장된 video 파일들을 미국에서 테스트해야하는 상황이 생겨 cloud front를 사용하고자 한다. 

### 0. 사전 준비 

S3 bucket에 video 파일들이 이미 저장되어 있다. 

### 1. 파일 Permission 설정

1. AWS S3의 해당 bucket으로 이동 
2. [S3 권한 설정에 관한 글](http://hochulshin.com/aws-s3-permission-to-access-object/)을 참고하여 bucket의 파일들이 getobject로 접근가능하도록 설정
3. 이제 이 파일들은 http://s3.{s3-region}.amazonaws.com/{s3-bucket-name}/{filename}로 접근 가능해야 함. 

### 2. CloudFront Web Distribution 생성

1. AWS CloudFront로 이동
2. **Create Distribution**
3. Step1: Select a delivery method for your content > **Web** section에서 **Get Started** 선택
4. Step2: Create Distribution > Origin Settings section에서 **Origin Domain Name**은 원하는 S3 bucket 선택. 나머지는 default로 둠
5. Default Cache Behavior Settings section은 default value로 둠
6. Distribution Settings section의 Price Class에서 원하는 Coverage를 선택하고 나머지는 default로 둠
7. **Create Distribution**
8. CloudFront Distributions page에서 Status가 In Progess에서 **deployed**로 바뀌면 끝

이때 Distribution의 Domain Name을 저장해 둔다. 

### 3. 테스트

아래와 같이 index.html파일을 만들어 local에 저장. image파일 하나는 S3 bucket에 upload

- {cloud-front-domain-name}: cloud front의 domain name
- {object-name}: s3에 저장된 image file의 이름

```
<html>
<head>My CloudFront Test</head>
<body>
<p>My text content goes here.</p>
<p><img src="http://{cloud-front-domain-name}/{object-name}" alt="my test image"/>
</body>
</html>
```

local의 index.html파일을 browser에서 열어서 image가 보이면 성공!
