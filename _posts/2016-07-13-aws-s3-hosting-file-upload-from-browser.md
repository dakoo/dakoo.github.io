---
layout: post
title: AWS S3에서 Web hosting하며 file upload하기 
description: AWS S3에서 Web hosting하며 file upload하기 
modified: 2016-07-13
tags: [aws]
comments: true
image:
  feature: abstract-13.png
---
Web Browser에서 AWS에 접근해서 사용하기 위해서는 AWS가 사용하는 Javascript SDK를 사용해야 한다. 이 SDK를 사용하는 방법을 알아보자. 여기서는 **facebook id로 인증받은 사용자가 AWS S3 bucket에 파일을 web page에서 upload하는 시나리오**를 대상으로 설명한다. 이를 통해 다음을 확인할 수 있다. 

- S3에서 웹 호스팅하기 
- S3로 브라우저에서 접근할 때 Facebook id로 허용하기 

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

```html
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
7. Setup 페이지의 우측 상단에 **Skip ... **선택하여 App 관리 페이지로 이동한다. 
8. **설정**에서 **앱 도메인**에 Endpoint URL(https://제외)을 붙여 넣는다. 
9. 변경 내용을 저장한다. 

### 테스트 

index.html을 다음과 같은 내용으로 만든후 위에 만든 bucket에 upload한다.  아래에서 <script></script> 내용은 facebook Setup 페이지의 **Setup the Facebook SDK for JavaScript**의 내용을 복사해서 붙여 넣기 한 것인데, 해당 app id만 넣어도 된다.  

```html
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

진행 이전에 미리 AWS Bucket이름과 생성한 Facebook App ID를 준비하자. 

### facebook id로 S3에 컨텐츠를 추가할 수 있는 Policy 생성 

1. AWS Console에서 **IAM** > **Policies**로 이동한다. **Get Started** 버튼이 보이면 누른다. 
2. **Create Policy** > **Create Your Own Policy**를 선택한다. 
3. Policy의 이름을 추가한 후 아래의 내용을 복사해서 **Policy Document**에 붙여 넣는다. 이때 'YOUR_BUCKET_NAME'을 실제 bucket이름으로 변경한다. 
4. 이제 **Create Policy**를 선택한다. 

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Action": [
            "s3:PutObject",
            "s3:PutObjectAcl"
         ],
         "Resource": [
            "arn:aws:s3:::YOUR_BUCKET_NAME/facebook-${graph.facebook.com:id}/*"
         ],
         "Effect": "Allow"
      },
      {
         "Action": [
            "s3:ListBucket"
         ],
         "Resource": [
            "arn:aws:s3:::YOUR_BUCKET_NAME"
         ],
         "Effect": "Allow",
         "Condition": {
            "StringEquals": {
               "s3:prefix": "facebook-${graph.facebook.com:id}"
            }
         }
      }
   ]
}
```

### Facebook id로 S3 접근 할 수 있는 Policy를 적용한 Role 생성

1. IAM > **Roles** 섹션으로 이동하여 **Create New Role**을 선택한다. 
2. Role이름을 입력한 후 다음 page에서 **Role for Identity Provider Access** -> **Grant access to web identity providers.**를 선택한다. 
3. **Identity Provider**로 Facebook을 선택하고 앞에서 만든 Facebook app id를 입력한다. 
4. Verify Role Trust page에서 **Next Step**를 선택한다. 
5. **Attach Poliy** 단계에서 앞에서 만든 policy를 선택하고 **Next Step**을 누른후 **Create Role**을 선택한다. 
6. 생성된 Role을 선태하면 Summary 섹션에 다음 단계에 필요한 ARN Role이 있다. 이를 복사해 놓는다. 

# S3에 facebook id로 권한 얻어 웨브라우저로 파일 업로드하기 

다음 과정을 진행하기 이전에 다음 내용이 필요하다. 

- Facebook App id(Facebook developer page에서 확인)
- Bucket이름(S3 Console)
- Bucket Region(S3 Console > 해당 버킷 선택 > Properites > Static Website Hosting에서 Endpoint의 ap-xxx나 us-xxx로 시작하는 부분을 복사한다. 예를 들어 Seoul의 경우 ap-northeast-2이다.)
- URL(3 Console > 해당 버킷 선택 > Properites > Static Website Hosting에서 Endpoint)
- Role ARN(IAM Console> Roles> 해당 Role 선택해서 Summary 섹션에서 확인)

## File upload 페이지

다음이 S3로 upload하는 예제이다. 여기서 'YOUR_APP_ID', 'YOUR_ROLE_ARN', 'YOUR_BUCKET_NAME', and 'YOUR_BUCKET_REGION'은 위에서 찾아놓은 적당한 값으로 바꾸고 index.html로 저장하자.  

```html
<!DOCTYPE html>
<html>
<head>
    <title>AWS SDK for JavaScript - Sample Application</title>
    <script src="https://sdk.amazonaws.com/js/aws-sdk-2.1.12.min.js"></script>
</head>
<body>
    <input type="file" id="file-chooser" />
    <button id="upload-button" style="display:none">Upload to S3</button>
    <div id="results"></div>
    <div id="fb-root"></div>

    <script type="text/javascript">
        var appId = 'YOUR_APP_ID';
        var roleArn = 'YOUR_ROLE_ARN';
        var bucketName = 'YOUR_BUCKET_NAME';
        AWS.config.region = 'YOUR_BUCKET_REGION';
        var fbUserId;
        var bucket = new AWS.S3({
            params: {
                Bucket: bucketName
            }
        });
        var fileChooser = document.getElementById('file-chooser');
        var button = document.getElementById('upload-button');
        var results = document.getElementById('results');
        button.addEventListener('click', function () {
            var file = fileChooser.files[0];
            if (file) {
                results.innerHTML = '';
                //Object key will be facebook-USERID#/FILE_NAME
                var objKey = 'facebook-' + fbUserId + '/' + file.name;
                var params = {
                    Key: objKey,
                    ContentType: file.type,
                    Body: file,
                    ACL: 'public-read'
                };
                bucket.putObject(params, function (err, data) {
                    if (err) {
                        results.innerHTML = 'ERROR: ' + err;
                    } else {
                        listObjs();
                    }
                });
            } else {
                results.innerHTML = 'Nothing to upload.';
            }
        }, false);
        function listObjs() {
            var prefix = 'facebook-' + fbUserId;
            bucket.listObjects({
                Prefix: prefix
            }, function (err, data) {
                if (err) {
                    results.innerHTML = 'ERROR: ' + err;
                } else {
                    var objKeys = "";
                    data.Contents.forEach(function (obj) {
                        objKeys += obj.Key + "<br>";
                    });
                    results.innerHTML = objKeys;
                }
            });
        }
        /*!
         * Login to your application using Facebook.
         * Uses the Facebook SDK for JavaScript available here:
         * https://developers.facebook.com/docs/javascript/gettingstarted/
         */
        window.fbAsyncInit = function () {
            FB.init({
                appId: appId
            });
            FB.login(function (response) {
                bucket.config.credentials = new AWS.WebIdentityCredentials({
                    ProviderId: 'graph.facebook.com',
                    RoleArn: roleArn,
                    WebIdentityToken: response.authResponse.accessToken
                });
                fbUserId = response.authResponse.userID;
                button.style.display = 'block';
            });
        };
         // Load the Facebook SDK asynchronously
        (function (d, s, id) {
            var js, fjs = d.getElementsByTagName(s)[0];
            if (d.getElementById(id)) {
                return;
            }
            js = d.createElement(s);
            js.id = id;
            js.src = "//connect.facebook.net/en_US/all.js";
            fjs.parentNode.insertBefore(js, fjs);
        }(document, 'script', 'facebook-jssdk'));
    </script>

</body>
</html>
```

원래 bucket에 있던 index.html을 삭제하고 이 파일을 upload하면서 혹은 이후에 **make public**을 선택하자. 

## 테스트

1. Webbrowser에서 Bucket EndPoint를 입력하여 업로드한 index.html파일을 로딩하자. 
2. 혹시 Popup을 막는 메시지가 뜨면 허용하도록 한다. 
3. Facebook 로그인 팝업이 뜨면 **확인**을 누른다. 
4. **Choose File** 버튼을 눌러 upload할 파일을 선택한다. 
5. **Upload to S3** 버튼을 누른다. 
6. 'facebook.... '와 같은 메시지가 뜨면 성공이다. 
7. AWS S3 console로 해당 bucket으로 이동한다. 
8. facebook-xxxxxxx 폴더 아래에 아이템이 추가되어 있음을 확인할 수 있다!!!! 성공!!!!

