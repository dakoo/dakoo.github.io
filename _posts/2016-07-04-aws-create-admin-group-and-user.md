---
layout: post
title: AWS IAM - admin group 만들고 사용자 추가하기
description: AWS - IAM admin group 만들고 사용자 추가하기
modified: 2016-07-03
tags: [aws]
comments: true
image:
  feature: abstract-11.png
---
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
