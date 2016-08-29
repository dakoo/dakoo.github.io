---
layout: post
title: AWS - EC2 접속 기본
description: AWS - EC2 접속 기본
modified: 2016-06-02
tags: [aws, ec2]
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

## EC2로 파일 업로드

- 올리려는 파일이 있는 위치로 가서 다음 명령을 수행한다. 맨뒤의 :~는 계정의 root를 의미한다. 

```
scp -i [pem파일경로] [업로드할 파일 이름] [ec2-user계정명]@[ec2 instance의 public DNS]:~/[경로]
```

- 다음은 지정된 folder로 올리는 예이다. 

```bash
Hochului-MacBook-Pro:aws hochulshin$ scp -i qwikLABS-L1020-464704.pem test.txt ec2-user@ec2-52-197-3-20.ap-northeast-1.compute.amazonaws.com:~/uploaded/newtest.txt
test.txt                                                                                             100%   19     0.0KB/s   00:00
```

## EC2 파일 다운로드

- 업로드의 반대로 주면 된다. 

```
scp -i [pem파일경로] [ec2-user계정명]@[ec2 instance의 public DNS]:~/[경로] [다운로드 파일의 로컬 경로] 
```

- 다음이 그 예이다. 

```
Hochului-MacBook-Pro:aws hochulshin$ scp -i qwikLABS-L1020-464704.pem ec2-user@ec2-52-197-3-20.ap-northeast-1.compute.amazonaws.com:~/uploaded/newtest.txt downloaded.txt
newtest.txt                                                                                          100%   19     0.0KB/s   00:00
```
