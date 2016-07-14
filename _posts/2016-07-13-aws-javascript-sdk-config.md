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
Web Browser에서 AWS에 접근해서 사용하기 위해서는 AWS가 사용하는 Javascript SDK를 사용해야 한다. 이 SDK를 사용하는 방법을 알아보자. 여기서는 **facebook id로 인증받은 사용자가 AWS S3 bucket에 파일을 web page에서 upload하는 시나리오**를 대상으로 설명한다. 

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

### Credential 만들기 

1. IAM > Users에서 ContentUploader를 선택한다. 
2. Security Credentials 탭에서 Access Key항목의 **Create Access Key**를 선택한다. 
3. **Download Credentials** 버튼을 눌러서 credentials.csv 파일을 다운받는다. 

### AWS S3 Bucket 설정하기 

AWS Console에서 S3로 이동한다. 만약 새로 bucket을 만들어야 한다면 **Create Bucket**을 이용해 생성한다. 

1. bucket을 선택해서 **Properties** 탭을 선택한다. 
2. **Permissions**에서 **+Add more permissions**를 선택한다. 
3. Grantee는 **any Authenticated AWS User**를 선택하고, List와 Upload/Delete의 checkbox에 체크한다. 
4. **Add CORS Configuration**을 선택하고, 다음 내용을 붙여 넣고 Save를 하고 Close를 한후 Permission 변경도 Save한다. 

```
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01">
   <CORSRule>
      <AllowedOrigin>*</AllowedOrigin>
      <AllowedMethod>GET</AllowedMethod>
      <AllowedMethod>PUT</AllowedMethod>
      <AllowedMethod>POST</AllowedMethod>
      <AllowedMethod>DELETE</AllowedMethod>
      <AllowedHeader>*</AllowedHeader>
   </CORSRule>
</CORSConfiguration>
```

### Facebook App ID 획득하기 

#### 사전 준비 

Webpage를 호스팅할 사이트 URL이 필요하다. 여러가지 방식이 필요 하겠으나, 여기서는 테스트를 위해 S3에서 정적으로 호스팅하기로 한다. 

##### S3 bucket 설정

1. S3 Console로 이동한다. 
2. Bucket을 하나 만든다. 
3. Properties > Permissions Section에서 Add more permissions를 선택한다. 
4. Grantee는 Everyone으로 하고 List checkbox를 선택하고 저장한다. 
5. Static Website Hosting Section으로 이동한다. 
6. Enable Website hosting을 선택한다. 




#### Facebook 개발자로 등록하기

다음과 같이 facebook 개발자로 등록하자 

1. [facebook 개발자 page](https://developers.facebook.com/apps)에 이동한다. 
2. 자신이 이미 facebook id가 있고 login이 되어 있다면 그 계정으로 가입할지를 묻는 창이 뜬다. 선택을 하자. 

#### Facebook 개발자 인 경우 

1. [facebook 개발자 page](https://developers.facebook.com/apps)에 이동한다. 
2. **새 앱 만들기** 버튼을 누른다. 
3. 'Add a New App' 창에서 **웹사이트**를 선택한다. 
4. 'Quick Start for 웹사이트' 창의 오른편 상단의 **Skip and Create App ID**를 선택한다. 
5. 이름, 연락처 이메일, 카테고리를 적당히 입력한다. 예를 들어, 표시이름은 AWS S3 Uploader, 카테고리는 생산성으로 할 수 있다. 
6. 보안 확인을 거치면 왼쪽 상단에 APP ID 표시된다. 저장을 해 둔다. 
7. Facebook App 페이지의 Settings(설정)으로 이동한다. 
































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

