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

# S3에서 웹호스팅하며 facebook id로 로그인하기

## S3이용한 웹 호스팅 

Webpage를 호스팅할 사이트 URL이 필요하다. 여러가지 방식이 필요 하겠으나, 여기서는 테스트를 위해 S3에서 정적으로 호스팅하기로 한다. AWS Console에서 S3로 이동한다. 만약 새로 bucket을 만들어야 한다면 **Create Bucket**을 이용해 생성한다. 

### S3 bucket 설정

1. S3 Console의 해당 bucket을 선택한다. 
2. **Static Website Hosting Section**으로 이동한다. 
3. **Enable Website hosting**을 선택한다.
4. Index Document에는 'index.html'을 입력한다. 
5. 저장하기 전에 **Endpoint**라고 되어 있는 부분의 URL을 복사해 둔다. 
6. 저장한다. 

### 테스트

#### 테스트 파일 준비

- 다음과 같이 아주 단순한 index.html 파일을 로컬 PC에 만든다. 

```
<html>
  <head>
    <title> S3 website hosting Test</title>
  </head>
  <body>
    <h1> Hello S3! </h1>
  <body>
</html>
```

- S3 Bucket에서 오른쪽 마우스를 눌러 Upload를 선택하여 생성한 index.html 파일을 업로드한다. 

업로드 시 **Set Details**로 상세 설정을 할 수 있는데, 이때 permission에서 Grantee는 Everyone으로 하고 Open/Download checkbox를 선택하는 것을 하면 아래 권한 할당 과정을 따로 할 필요가 없다. 

#### 권한 할당 

- S3 Console에서 Bucket에 index.html이 업로드가 완료된 것을 확인한다.
- S3 Console의 index.html 파일 표시 앞의 checkbox를 선택하여 Actions 버튼을 누르거나, index.html 파일 위해서 오른쪽 마우스를 눌러서 **Make Public**을 지정한다. 

#### 브라우저에서 테스트

위에서 복사한 Endpoint URL을 브라우저에서 열어서 업로드한 page가 열리면 성공이다. 

### AWS S3 Bucket 설정하기 

AWS Console에서 S3로 이동한다. 여기서는 위에서 웹호스팅을 한 Bucket을 사용하자. 

1. bucket을 선택해서 **Properties** 탭을 선택한다. 
2. **Add CORS Configuration**을 선택하고, 다음 내용을 붙여 넣고 Save를 하고 Close를 한후 Permission 변경도 Save한다. 

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


위에서 다룬 AWS S3 웹 호스팅의 URL을 얻기 위해 해당 Bucket의 Properties > Static Website Hosting에서 EndPointURL을 복사해 둔다. 


## Facebook App ID 획득하기 

### Facebook 개발자로 등록하기

다음과 같이 facebook 개발자로 등록하자 

1. [facebook 개발자 page](https://developers.facebook.com/apps)에 이동한다. 
2. 자신이 이미 facebook id가 있고 login이 되어 있다면 그 계정으로 가입할지를 묻는 창이 뜬다. 선택을 하자. 

### Facebook 개발자 인 경우 

1. [facebook 개발자 page](https://developers.facebook.com/apps)에 이동한다. 
2. **새 앱 만들기** 버튼을 누른다. 
3. 'Add a New App' 창에서 **웹사이트**를 선택한다. 
4. 'Quick Start for 웹사이트' 입력 창에 원하는 App이름을 넣는다.  
5. 필요한 이메일과 적당한 카테고리를 설정하고 **앱 ID만들기**를 선택한다. 
6. Setup 페이지가 뜨면 그 페이지 하단의 **Tell us about your website**에 위에서 복사한 Endpoint URL을 앞에 'https://'를 추가해서 붙여 넣는다. 

### 테스트 

index.html을 다음과 같은 내용으로 만든후 위에 만든 bucket에 upload한다.  아래에서 <script></script> 내용은 facebook Setup 페이지의 **Setup the Facebook SDK for JavaScript**의 내용을 복사해서 붙여 넣기 한 것인데, 해당 app id만 넣어도 된다.  

```
<html>
  <head>
    <title> S3 website hosting Test</title>
  </head>
  <body>
    <h1> Hello S3! </h1>
    <div
      class="fb-like"
      data-share="true"
      data-width="450"
      data-show-faces="true">
  </div>
  <script>
    window.fbAsyncInit = function() {
      FB.init({
        appId      : '여기는 app ID를 붙여 넣을 것',
        xfbml      : true,
        version    : 'v2.7'
      });
    };
  
    (function(d, s, id){
       var js, fjs = d.getElementsByTagName(s)[0];
       if (d.getElementById(id)) {return;}
       js = d.createElement(s); js.id = id;
       js.src = "//connect.facebook.net/en_US/sdk.js";
       fjs.parentNode.insertBefore(js, fjs);
     }(document, 'script', 'facebook-jssdk'));
  </script>
  <body>
</html>
```

이제 위에서 복사한 EndPoint URL로 브라우저에서 접근해서 Hello S3! 아래에 Like 버튼이 보이면 성공한 것이다. 


## Facebook을 통해 Login할 수 있는 AWS IAM Role을 생성하기 

1. AWS Console에서 **IAM**으로 이동한다. 
2. **Create Policy** > **Create Your Own Policy**를 선택한다. 
3. Policy의 이름을 추가한 후 아래의 내용을 복사해서 **Policy Document**에 붙여 넣는다. 이때 YOUR_BUCKET_NAME을 실제 bucket이름으로 변경한다. 
4. 이제 **Create Policy**를 선택한다. 






# S3에 facebook id로 권한 얻어 웨브라우저로 파일 업로드하기 
















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



## 외부 사용자와 AWS 접근 권한 설정

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

