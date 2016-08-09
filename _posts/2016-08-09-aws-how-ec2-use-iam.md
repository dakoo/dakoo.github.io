---
layout: post
title: AWS EC2에 IAM Role 설정하기 
description: AWS EC2에 IAM Role 설정하기 
modified: 2016-08-09
tags: [aws]
comments: true
image:
  feature: abstract-21.png
---
AWS의 핵심 Computing 서비스인 EC2에서 다른 AWS 서비스를 사용하기 위해서는 IAM Role을 가지고 있어야 한다. 
중요한 것은 EC2 생성 이후에는 변경은 용이하나 Role을 할당하는 것은 어려우므로, EC2 생성시에는 빈 Role이라도 반드시 할당하도록 하자.

- NOTE: EC2 생성시에 반드시 Role을 할당하자!

### IAM Role 생성

필요한 AWS 서비스에 대한 접근 권한을 EC2에 할당할 수 있도록 Role을 생성하자. 

1. AWS IAM Console로 이동
2. **Roles** > **Create New Role**
3. Role이름을 입력
4. Select Role Type page에서 **AWS Service Roles** >  EC2에 **Select** > **Next Step**
5. Attach Policy에서 적당한 Policy들을 찾아서 선택한 후 **Next Step** (주로 AmazonS3FullAccess, AmazonDynamoDBFullAccess, AmazonSQSFullAccess 등을 선택)
6. **Create Role**

Role Arn을 복사할 필요는 없다.  

### EC2 생성 

이제 생성된 IAM Role을 이용하여 EC2 instance를 만들자. 

1. AWS EC2 console로 이동
2. **Instances** > **Launch Instance** 
3. AMI 선택 > Instance Type 선택 > Configuration Instance Details로 이동
4. IAM role 항목에서 위에서 생성한 Role을 선택 후 이후 진행과정을 거쳐 실행

### Role의 Policy 변경

일단 EC2에 Role이 설정되어 실행되면 Role의 변경은 용이하다. 

1. AWS IAM Console로 이동
2. **Roles** > 해당 Role 선택
3. **Permissions** 탭에서 Policy를 추가/변경/삭제를 수행
4. **Apply Policy**

이제 EC2에 즉각 Policy가 반영될 것이다. 
