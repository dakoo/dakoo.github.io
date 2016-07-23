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

AWS Cognito는 아직 Seoul Region에서는 이용할 수 없고, Cognito와 S3는 같은 Region에 있어야 한다. 그러므로 아래 내용을 테스트할 때는 **Cognito가 지원되는 Region에서 해야 한다**는 것을 명심하자. 

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

- **S3 Bucekt이름**과 **EndPoint**를 저장한다. 

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

## Upload 권한 설정하기

AWS 접근 권한에 대해서는 [AWS 문서](http://docs.aws.amazon.com/ko_kr/cognito/latest/developerguide/authentication-flow.html)를 참조하자. 이 문서에는 여러가지 방식의 권한 설정 방식이 있는데 여기서는 'External Provider Authflow' 중 'Enhanced (Simplified) Authflow'를 통해 인증하고 있다. 이것은 AWS STS의 key가 사용자 기기로 전달되지 않고, 대신 Cognito가 처리하는 방식이다. 
아래 그림을 보자.  S3에 접근하기 위한 권한을 어떻게 얻는지를 나타낸다. Device는 웹브라우저라고 생각해도 된다.

<figure>
	<img src="http://docs.aws.amazon.com/ko_kr/cognito/latest/developerguide/images/amazon-cognito-ext-auth-enhanced-flow.png" alt="">
</figure>

Facebook, Google, Amazon과 같은 Login Provider와 Amazon Cognito를 연결해 놓으면, 사용자는 Login Provider의 인증을 받으면 AWS STS의 token을 발급받아 AWS 서비스에 접근할 수 있게 된다. 

### Facebook을 Login Provider로 설정하기

#### Facebook 개발자로 등록하기

다음과 같이 facebook 개발자로 등록하자 

1. [facebook 개발자 page](https://developers.facebook.com/apps)에 이동한다. 
2. 자신이 이미 facebook id가 있고 login이 되어 있다면 그 계정으로 가입할지를 묻는 창이 뜬다. 선택을 하자. 

#### Facebook 개발자 인 경우 

1. [facebook 개발자 page](https://developers.facebook.com/apps)에 이동한다. 
2. **새 앱 만들기** 버튼을 누른다. 
3. 'Add a New App' 창에서 **웹사이트**를 선택한다. 
4. 'Quick Start for 웹사이트' 입력 창에 원하는 App이름을 넣는다.  
5. 필요한 이메일과 적당한 카테고리를 설정하고 **앱 ID만들기**를 선택한다. 
6. Setup 페이지가 뜨면 그 페이지 하단의 **Tell us about your website**에 위에서 복사한 Endpoint URL을 앞에 'https://'를 추가해서 붙여 넣는다.
7. Setup 페이지의 우측 상단에 **Skip ... **선택하여 App 관리 페이지로 이동한다. 
8. **설정**에서 **앱 도메인**에 Endpoint URL(https://제외)을 붙여 넣는다. 
9. 변경 내용을 저장한다. 

- **Facebook App ID**를 저장해 둔다.

#### facebook app 설정 테스트 

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

### Amazon Cognito와 Facebook Login Provider 연결하기

#### Amazon Cognito에서 facebook app id 등록하고 Identity pool 생성하기 

- NOTE: Cognito는 Seoul Region은 현재 지원하지 않는다!!! 

1. AWS > AWS Cognito console로 이동한다. 처음이동한 경우 **Manage Federated Identity**를 선택한다. 
2. Step 1: Create identity pool에서 **identity pool 이름**을 입력한다. 
3. **Authorization Providers** 섹션의 **Facebook** 탭으로 이동해 페이스북 App ID를 입력한다. facebook app id는 [facebook 개발자 page](https://developers.facebook.com/apps/)에서 확인 할 수 있다. 
4. **Create Pool** 버튼을 선택해 pool을 만든다. 
5. IAM Role 설정 창에서 **AuthRole**를 열어 **Policy edit**를 선택하여 아래의 Policy를 추가하고 **Allow**로 빠져나온다. 

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
            "arn:aws:s3:::{YOUR_BUCKET_NAME}/*"
         ],
         "Effect": "Allow"
      },
      {
         "Action": [
            "s3:ListBucket"
         ],
         "Resource": [
            "arn:aws:s3:::{YOUR_BUCKET_NAME}"
         ],
         "Effect": "Allow"
      }
   ]
}
```

- **Dashboard**을 선택해 들어가 **Edit identity pool**을 선택해 pool ID가 나오면 이를 복사해 저장하고 그 page를 빠져나온다.

#### Browser에서 구동될 Javascript 수정하기

아래의 코드로 index.html을 저장하고, S3 Bucket에 upload한다. upload하면서 permission은 **make everyone public**으로 한다. 

```
<!DOCTYPE html>
<html>

<head>
    <title>S3 Upload</title>
    <meta charset="UTF-8">
</head>

<body>
    <script src="https://sdk.amazonaws.com/js/aws-sdk-2.2.30.min.js"></script>
    <script>
        var AWSRegion = '{AWS-REGION}';
        var AWSS3BucketName = '{BUCKET-NAME}';
        var AWSCognitoPoolId = '{AWS-COGNITO-ID}';
        var FacebookAppId = '{FACEBOOK-APP-ID}';

        var fileChooser = document.getElementById('file-chooser');
        var button = document.getElementById('upload-button');
        var results = document.getElementById('results');

        function statusChangeCallback(response) {

            console.log('statusChangeCallback');
            console.log(response);
            console.log("userId : " + response.authResponse.userId);
            // The response object is returned with a status field that lets the
            // app know the current login status of the person.
            // Full docs on the response object can be found in the documentation
            // for FB.getLoginStatus().
            if (response.status === 'connected') {
                // Logged into your app and Facebook.

                console.log('Welcome!  Fetching your information.... ');
                FB.api('/me', function(response) {
                    console.log('Successful login for: ' + response.name);

                    document.getElementById('status').innerHTML =
                        'Thanks for logging in, ' + response.name + '!';
                });

                //AWS REGION
                AWS.config.region = AWSRegion;
                // Add the Facebook access token to the Cognito credentials login map.
                AWS.config.credentials = new AWS.CognitoIdentityCredentials({
                    IdentityPoolId: AWSCognitoPoolId,
                    Logins: {
                        'graph.facebook.com': response.authResponse.accessToken
                    }
                });

                //Get IdentityId
                var cognitoidentity = new AWS.CognitoIdentity({
                    apiVersion: '2014-06-30'
                });
                var paramsForGetId = {
                    IdentityPoolId: AWSCognitoPoolId,
                    Logins: {
                        'graph.facebook.com': response.authResponse.accessToken
                    }
                };
                cognitoidentity.getId(paramsForGetId, function(err, data) {
                    if (err) {
                        console.log(err, err.stack); // an error occurred
                    } else {
                        console.log("IdentityId : ", data.IdentityId);
                        var paramsForgetCreditialsForIdentity = {
                            IdentityId: data.IdentityId,
                            /* required */
                            Logins: {
                                'graph.facebook.com': response.authResponse.accessToken
                            }
                        };

                        cognitoidentity.getCredentialsForIdentity(paramsForgetCreditialsForIdentity, function(err, data) {
                            if (err) console.log(err, err.stack); // an error occurred
                            else console.log("getCredentialsForIdentity : ", data); // successful response
                        });
                        // successful response
                    }
                });

                var bucket = new AWS.S3({
                    params: {
                        Bucket: AWSS3BucketName
                    }
                });

                document.getElementById('upload-button').addEventListener('click', function() {
                    console.log('clicked');
                    var file = document.getElementById('file-chooser').files[0];
                    if (file) {
                        document.getElementById('results').innerHTML = '';
                        //Object key will be facebook-USERID#/FILE_NAME
                        var objKey = file.name;
                        var params = {
                            Key: objKey,
                            ContentType: file.type,
                            Body: file,
                            ACL: 'public-read'
                        };
                        bucket.putObject(params, function(err, data) {
                            if (err) {
                                document.getElementById('results').innerHTML = 'ERROR: ' + err;
                            } else {
                                listObjs();
                            }
                        });
                    } else {
                        document.getElementById('results').innerHTML = 'Nothing to upload.';
                    }
                }, false);

                function listObjs() {
                    var prefix = '';
                    bucket.listObjects({
                        Prefix: prefix
                    }, function(err, data) {
                        if (err) {
                            document.getElementById('results').innerHTML = 'ERROR: ' + err;
                        } else {
                            var objKeys = "";
                            data.Contents.forEach(function(obj) {
                                objKeys += obj.Key + "<br>";
                            });
                            document.getElementById('results').innerHTML = objKeys;
                        }
                    });
                }


            } else if (response.status === 'not_authorized') {
                // The person is logged into Facebook, but not your app.
                document.getElementById('status').innerHTML = 'Please log ' +
                    'into this app.';
            } else {
                // The person is not logged into Facebook, so we're not sure if
                // they are logged into this app or not.
                document.getElementById('status').innerHTML = 'Please log ' +
                    'into Facebook.';
            }

        }

        // This function is called when someone finishes with the Login
        // Button.  See the onlogin handler attached to it in the sample
        // code below.
        function checkLoginState() {
            FB.getLoginStatus(function(response) {
                statusChangeCallback(response);
            });
        }

        window.fbAsyncInit = function() {
            FB.init({
                appId: FacebookAppId,
                xfbml: true, // parse social plugins on this page
                version: 'v2.5' // use version 2.2
            });
            FB.login(function(response) {
                statusChangeCallback(response);
                document.getElementById('upload-button').style.display = 'block';
            });

        };

        // Load the SDK asynchronously
        (function(d, s, id) {
            var js, fjs = d.getElementsByTagName(s)[0];
            if (d.getElementById(id)) return;
            js = d.createElement(s);
            js.id = id;
            js.src = "//connect.facebook.net/en_US/sdk.js";
            fjs.parentNode.insertBefore(js, fjs);
        }(document, 'script', 'facebook-jssdk'));
    </script>

    <input type="file" id="file-chooser" />
    <button id="upload-button">Upload to S3</button>
    <div id="results"></div>
    <div id="fb-root"></div>
    <div id="status"></div>
</body>

</html>
```

### 테스트

1. 브라우저에서 S3 Bucket의 Endpoint URL로 접근한다. 
2. 만약 popup이 금지되어 있으면 허용으로 바꾼다. 
3. Facebook ID로 로그인하면 ID에 해당하는 사용자 이름이 화면에 보여야 한다. 
4. 파일을 추가하고 upload 버튼을 click하면 S3의 파일 목록이 떠야 한다. 


