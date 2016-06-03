---
layout: post
title: 개발환경 - AWS EC2 접속 기본
description: 개발환경 - AWS EC2 접속 기본
modified: 2016-06-02
tags: [dev]
comments: true
image:
  feature: abstract-11.png
---
EC2 instace에 SSH terminal로 접속하거나 file을 upload를 올리는 방법을 알아보자

## 준비

- AWS console에서 *.pem파일을 download받는다. 
- *.pem file을 'chmod 400 pem파일명'으로 permission을 변경한다. 
- EC2 instance의 public IP와 public DNS를 복사해 둔다. 

## SSH terminal 접속

- Terminal에서 다음 명령으로 접속한다. ec2-user 계정명은 ubuntu의 경우 ubuntu, amazon linux는 ec2-user이다. (확인 필요)

```
  ssh -i [pem파일경로] [ec2-user계정명]@[ec2 instance의 publicIP] 
```

## EC2로 파일 입력 

- 올리려는 파일이 있는 위치로 가서 다음 명령을 수행한다. 맨뒤의 :~는 계정의 root를 의미한다. 

```
scp -i [pem파일경로] [업로드할 파일 이름] [ec2-user계정명]@[ec2 instance의 public DNS]:~
```


