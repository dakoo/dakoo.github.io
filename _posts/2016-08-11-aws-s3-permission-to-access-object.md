---
layout: post
title: AWS - S3 object 접근 권한 설정하기
description: AWS - S3 object 접근 권한 설정하기
modified: 2016-08-11
tags: [aws, s3, iam]
comments: true
image:
  feature: abstract-11.png
---
AWS S3에 object를 bucket에 upload한 이후에 object에 접근할 때 permission denied가 발생한다. 
예를 들어, S3에서 Bucket을 Static Website Hosting으로 설정한 후 index.html 파일에 접근해도 permission denied가 발생한다. 
이 경우 직접 index.html파일을 `make public` 설정해야 해결이 되는데, 이렇게 수동으로 object를 'make public'하지 않고, 자동으로 접근 권한을 제어하는 것을 하고자 한다. 

### 1. Policy 만들기 

AWS Policy Generator를 이용해서 접근 Policy를 만들자. S3 Bucket 이름은 미리 알아둔다. 

1. [AWS Policy Generator](http://awspolicygen.s3.amazonaws.com/policygen.html)로 이동
2. Policy Type: S3 Bucket Policy
3. Effect: Allow
4. Principal: *
5. AWS Service: Amazon S3
6. Actions: GetObject
7. Amazon Resourec Name (ARN): arn:aws:s3:::{BUCKET-NAME}/*     - {BUCKET-NAME}아래의 모든 Object들에게 적용하는 것
8. **Generate Policy** 버튼을 누르면 생성된 Policy가 뜨는 데 복사해 둔다. 

### 2. S3 bucket 설정

1. AWS S3 console로 이동
2. 접근 제어를 원하는 bucket을 선택하고 이름을 기억
3. **Properites**를 선택
4. **Permissions** 섹션에서 **Add Bucket Policy**를 선택
5. Bucket Policy Editor에 앞에서 만든 Policy를 복사한다. 
6. **Save**
