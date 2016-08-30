---
layout: post
title: AWS - IAM admin group 만들고 사용자 추가하기
description: AWS - IAM admin group 만들고 사용자 추가하기
modified: 2016-07-03
tags: [aws, iam]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

처음 AWS를 사용할 때 과금이 되는 AWS Account를 사용하지 않고, 별도로 Admin group을 만들고 새로 User를 추가해 이를 이용해 작업하는 것이 좋다.
이 방법을 알아보자. 

### Administrators Group 만들기 

1. AWS에 AWS Account로 로그인한 후 
2. **Identity and Access Management(IAM)** 서비스로 이동한다. 
3. 왼편 탐색창에서 **Groups**를 선택한다. 
4. **Create New Group**을 선택한다. 
5. **Group Name**은 Administrators로 하고 **Next Step**을 선택한다. 
6. Attach Policy 단계애서 **AdministratorAccess**를 선택하고, **Next Step**을 선택한다. 
7. Review 단계에서 **Create Group**을 선택한다. 
8. 이제 Administrators 그룹이 생기고 Users가 0인 것을 확인 할 수 있다. 

### User 만들어 Admin Group에 등록하기

1. AWS > **IAM** > **Users**로 이동한다. 
2. **Create New Users**를 선택한다. 
3. 아래 **Generate an access key for each user** 선택을 **해제** 후 **Enter user Names:**에 원하는 User 이름을 입력하고 **Create**를 누른다.
4. **Users**에서 생성된 User를 선택해서 Summary 정보 페이지의 아래에 위치한 **Groups 탭**의 **Add User to Groups**를 선택한다. 
5. 위에서 생성한 **Administrators** 그룹을 선택하고 **Add to Groups**을 선택한다.  
6. Summary 정보 페이지의 아래에 위치한 **Security Credentials 탭**을 선택하고 **Sign-In Credentials**의 **Manage Password**를 선택한다. 
7. Manage Password 페이지에서 **Assign a custom password**를 선택해서 password를 입력하고 **Apply**를 선택한다. 

### IAM User로 AWS 접속하기

1. AWS > **IAM**으로 이동한다. 
2. "IAM Users sign-in link:" 라는 문구 아래의 URL을 복사하여 bookmark를 걸어둔다. 이것이 바로 IAM User가 접속하는 URL이다.  
3. AWS Account로 접속한 것을 logout한다. 
4. 앞으로는 위에서 복사한 URL로 접속하고 생성한 User 명과 password로 접속한다. 

### IAM User로 AWS API 접속하기 위한 Access Key 만들기

1. 위에서 생성한 IAM User로 AWS에 접속한다. 
2. **IAM** > **Users**로 이동한다. 
3. User(자기 자신)을 선택해서 **Security Credentials 탭**을 누른다. 
4. **Create Access Key**를 선택해서 **Download Credentials**를 눌러 Access Key ID와 Secret Access Key를 저장한 csv 파일을 다운로드 받는다. 

### IAM User로 EC2 접속하기 위한 pem 파일 만들기

주의: 기존 instance에 새로운 key-pair를 적용하는 것은 [매우 귀찮은 작업](http://stackoverflow.com/questions/7881469/change-key-pair-for-ec2-instance)이다. 그러므로 pem과 User는 별도로 생각하는 것이 좋다. 즉, pem 파일을 user마다 별도로 만드는 것이 아니라 pem 파일의 공유 범위를 제어해서 해결하는 것이 더 좋다. 

1. 위에서 생성한 IAM User로 AWS에 접속한다. 
2. **EC2** > (NETWORK &B SECURITY) **Key Pairs**로 이동한다. 
3. **Create Key Pair**를 선택한다. 
4. Key Pair name을 입력하고 **Create**를 누른다. 
5. 생성된 pem 파일을 로컬에 저장한다. 

#### EC2 생성 시 적용

1. EC2 instance 마지막 단계에서 key-pair를 선택하는 것이 나온다. 이때 생성된 key-pair를 선택하자. 
2. EC2 Instance를 실행시킨 후 Description에서 Key-pair name값을 확인한다. 
3. 위에서 생성한 key pair name이 아니면 당연히 접속 안된다. 

#### 테스트 

1. Console에서 pem 파일을 다운받은 폴더로 이동한다. 
2. chmod 400 [새로운 pem 파일]
3. 생성된 EC2 instance의 public IP를 확인한다.    
4. 다음과 같이 접속해 보자. 

```
# ubuntu이외
 ssh -i [pem파일경로] ec2-user@[ec2 instance의 publicIP]
 
# ubuntu
 ssh -i [pem파일경로] ubuntu@[ec2 instance의 publicIP]
 
- Amazon Linux AMI의 경우 사용자 이름은 ec2-user
- RHEL5 AMI의 경우 사용자 이름은 root 또는 ec2-user
- Ubuntu AMI의 경우 사용자 이름은 ubuntu
- Fedora AMI의 경우 사용자 이름은 fedora 또는 ec2-user
- SUSE Linux의 경우 사용자 이름은 root 또는 ec2-user 
```
