---
layout: post
title: AWS Javascript SDK 설정하기
description: AWS Javascript SDK 설정하기
modified: 2016-07-13
tags: [aws]
comments: true
image:
  feature: abstract-13.png
---
Web Browser에서 AWS에 접근해서 사용하기 위해서는 AWS가 사용하는 Javascript SDK를 사용해야 한다. 이 SDK를 사용하는 방법을 알아보자. 
여기서는 **AWS S3 bucket에 파일을 web page에서 upload하는 시나리오**를 대상으로 설명한다. 

## Web Page에 AWS SDK 추가하기

Web Page에 AWS SDK를 추가하는 방법은 다음과 같이 여러 방법이 있다. 

- html파일에 다음을 붙여 넣기

```
<script src="https://sdk.amazonaws.com/js/aws-sdk-2.4.7.min.js"></script>
```

- [aws-sdk 파일](https://sdk.amazonaws.com/js/aws-sdk-2.4.7.min.js)을 다운로드하여 web server의 js 폴더에 놓고 아래와 같이 배포하기

```
<script src="js/aws-sdk-2.4.7.min.js"></script>
```

## AWS Javascript SDK에서 AWS에 접근할 수 있도록 권한 주기

### IAM User 만들기

AWS에 모두 접근 가능한 User가 아니라, S3에만 접근해서 쓰고, 읽기 할 수 있는 User를 만든다. 

#### ContentUploaders Group만들기 

1. AWS에 Admin으로 로그인한 후
2. **Identity and Access Management(IAM)** 서비스로 이동한다.
3. 왼편 탐색창에서 **Groups**를 선택한다.
4. **Create New Group**을 선택한다.
5. Group Name은 **ContentUploaders**로 하고 Next Step을 선택한다.
6. Attach Policy 단계애서 **AmazonS3FullAccess**를 선택하고, Next Step을 선택한다.
7. Review 단계에서 **Create Group**을 선택한다.
8. 이제 ContentUploaders 그룹이 생기고 Users가 0인 것을 확인 할 수 있다.

#### ContentUploader 사용자 생성하고 그룹에 등록하기 

IAM 서비스에서 다음과 같이 진행한다. 

1. 왼편 탐색창에서 **User**를 선택한 후 **Create New Users**를 선택한다.
2. 아래 **Generate an access key for each user** 선택을 해제 후 Enter user Names:에 ContentUploader를 입력하고 Create를 누른다.
3. Users에서 생성된 ContentUploader를 선택해서 Summary 정보 페이지의 아래에 위치한 Groups 탭의 **Add User to Groups**를 선택한다.
4. 위에서 생성한 ContentUploaders 그룹을 선택하고 **Add to Groups**을 선택한다.

#### WebUsers Group만들기 

ContentUploaders Group을 만든 후 IAM 서비스에서 다음과 같이 진행한다. 

1. 왼편 탐색창에서 **Groups**를 선택한다.
2. **Create New Group**을 선택한다.
3. Group Name은 **WebUsers**로 하고 Next Step을 선택한다.
4. Attach Policy 단계애서 **AmazonS3ReadOnlyAccess**를 선택하고, Next Step을 선택한다.
5. Review 단계에서 **Create Group**을 선택한다.
6. 이제 WebUsers 그룹이 생기고 Users가 0인 것을 확인 할 수 있다.

#### WebUser 사용자 생성하고 그룹에 등록하기 

WebUsers Group을 만든 후 IAM 서비스에서 다음과 같이 진행한다. 

1. 왼편 탐색창에서 **User**를 선택한 후 **Create New Users**를 선택한다.
2. 아래 **Generate an access key for each user** 선택을 해제 후 Enter user Names:에 WebUser를 입력하고 Create를 누른다.
3. Users에서 생성된 WebUser를 선택해서 Summary 정보 페이지의 아래에 위치한 Groups 탭의 **Add User to Groups**를 선택한다.
4. 위에서 생성한 WebUsers 그룹을 선택하고 **Add to Groups**을 선택한다.





