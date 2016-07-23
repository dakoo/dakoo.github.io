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

Facebook, Google, Amazon과 같은 Login Provider와 Amazon Cognito를 연결해 놓으면, 사용자는 Login Provider의 인증을 받으면 AWS STS의 token을 발급받아 AWS 서비스에 접근할 수 있게 된다. 아래 글은 다음 2단계에 거쳐 설명한다. 

1. Web Browser에서 Login Provider의 인증 후 S3 접근하기 
2. Login Provider가 Amazon Cognito와 연계해 사용자 인증하기

### 1단계: Facebook을 Login Provider로 설정하여 S3에 접근하기

#### Facebook App ID 획득하기 

##### Facebook 개발자로 등록하기

다음과 같이 facebook 개발자로 등록하자 

1. [facebook 개발자 page](https://developers.facebook.com/apps)에 이동한다. 
2. 자신이 이미 facebook id가 있고 login이 되어 있다면 그 계정으로 가입할지를 묻는 창이 뜬다. 선택을 하자. 

##### Facebook 개발자 인 경우 

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

##### facebook app 설정 테스트 

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


#### Facebook을 통해 Login할 수 있는 AWS IAM Role을 생성하기 

진행 이전에 미리 AWS Bucket이름과 생성한 Facebook App ID를 준비하자. 

##### facebook id로 S3에 컨텐츠를 추가할 수 있는 Policy 생성 

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

##### Facebook id로 S3 접근 할 수 있는 Policy를 적용한 Role 생성

1. IAM > **Roles** 섹션으로 이동하여 **Create New Role**을 선택한다. 
2. Role이름을 입력한 후 다음 page에서 **Role for Identity Provider Access** -> **Grant access to web identity providers.**를 선택한다. 
3. **Identity Provider**로 Facebook을 선택하고 앞에서 만든 Facebook app id를 입력한다. 
4. Verify Role Trust page에서 **Next Step**를 선택한다. 
5. **Attach Poliy** 단계에서 앞에서 만든 policy를 선택하고 **Next Step**을 누른후 **Create Role**을 선택한다. 
6. 생성된 Role을 선태하면 Summary 섹션에 다음 단계에 필요한 ARN Role이 있다. 이를 복사해 놓는다. 

#### S3에 facebook id로 권한 얻어 웨브라우저로 파일 업로드하기 

다음 과정을 진행하기 이전에 다음 내용이 필요하다. 

- Facebook App id(Facebook developer page에서 확인)
- Bucket이름(S3 Console)
- Bucket Region(S3 Console > 해당 버킷 선택 > Properites > Static Website Hosting에서 Endpoint의 ap-xxx나 us-xxx로 시작하는 부분을 복사한다. 예를 들어 Seoul의 경우 ap-northeast-2이다.)
- URL(3 Console > 해당 버킷 선택 > Properites > Static Website Hosting에서 Endpoint)
- Role ARN(IAM Console> Roles> 해당 Role 선택해서 Summary 섹션에서 확인)

##### File upload 페이지

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

##### Facebook Login Provider 테스트

1. Webbrowser에서 Bucket EndPoint를 입력하여 업로드한 index.html파일을 로딩하자. 
2. 혹시 Popup을 막는 메시지가 뜨면 허용하도록 한다. 
3. Facebook 로그인 팝업이 뜨면 **확인**을 누른다. 
4. **Choose File** 버튼을 눌러 upload할 파일을 선택한다. 
5. **Upload to S3** 버튼을 누른다. 
6. 'facebook.... '와 같은 메시지가 뜨면 성공이다. 
7. AWS S3 console로 해당 bucket으로 이동한다. 
8. facebook-xxxxxxx 폴더 아래에 아이템이 추가되어 있음을 확인할 수 있다!!!! 성공!!!!

### 2단계: Amazon Cognito와 Facebook Login Provider 연결하기

#### 구조 

<figure>
	<img src="http://docs.aws.amazon.com/ko_kr/cognito/latest/developerguide/images/amazon-cognito-ext-auth-enhanced-flow.png" alt="">
</figure>

위 그림에 따라 구현해야 할 것들은 다음과 같다는 것을 이해하자. 일부는 1단계에서 구현했으나 상당 부분은 아직 구현되어 있지 않다. 

##### 브라우저에서 구현해야 할 것들

브라우저의 javascript 코드는 다음을 지원해야 한다. 

- Facebook에 Login (이것은 1단계에서 완료)
- Amazon Cognito에 GetId를 호출하여 Cognito ID를 전달받기 
- Amazon Cognito에 GetCredentialsForIdentity를 호출하여 Cognito로부터 Credentials를 획득하기 

##### Amazon Cognito에서 설정해야 할 것들

1. GetId이나 GetCredentialsForIdentity 요청이 오면 Facebook과 일련의 validation 과정을 진행해야 한다. 이를 위해 Cognito에 **facebook의 AppID가 등록**되어 있어야 하고 반환해 줄 **Identity(ID) pool**을 만들어 두어야 한다.  
2. Cognito에 의해 전달된 Credential를 가지고 AWS 서비스(여기서는 S3)에 접근하기 할 수 있도록 **AWS 서비스에 맞게 Role과 Policy를 설정**하는 것을 해야 한다. 

#### Amazon Cognito 설정하기 

##### Amazon Cognito에서 facebook app id 등록하고 Identity pool 생성하기 

- NOTE: Cognito는 Seoul Region은 현재 지원하지 않는다. 그러므로 **tokyo region**을 선택하여 진행한다. Cognito만 별개의 region으로 두어도 서비스는 가능하다!! 

1. AWS > AWS Cognito console로 이동한다. 처음이동한 경우 *Manage Federated Identity**를 선택한다. 
2. Step 1: Create identity pool에서 **identity pool 이름**을 입력한다. 
3. **Authorization Providers** 섹션의 **Facebook** 탭으로 이동해 페이스북 App ID를 입력한다. facebook app id는 [facebook 개발자 page](https://developers.facebook.com/apps/)에서 확인 할 수 있다. 
4. **Create Pool** 버튼을 선택해 pool을 만든다. 
5. IAM Role 설정 창은 default로 두고 **Allow** 버튼을 눌러 진행한다. 
6. **Dashboard**을 선택해 들어가 **Edit identity pool**을 선택해 pool ID가 나오면 이를 복사해 저장하고 그 page를 빠져나온다. 

##### Amazon Cognito를 이용해서 S3 접근하도록 IAM Role 설정하기

1단계에서는 facebook id를 가지고 접근하도록 되어 role과 policy가 설정되어 있다. 그것을 삭제하고 대신 Cognito가 발급한 Credential를 가지고 접근 하도록 해야 한다. 

1. AWS IAM Console > **Roles**로 이동한다. 
2. 앞에서 Cognito의 identity pool 생성과 함께 만들어져 있는 2개의 Role이 있다는 것을 확인한다. Cognito_[pool이름]Auth_Role과 Cognito_[pool이름] Unauth_Role이다.  
3. 1단계에서 만들어둔 Facebook을 이용해 접근하는 Role을 선택해, 설정했던 Policy 이름을 확인한다. 
4. Cognito_[pool이름]Auth_Role을 선택한다. Permissions 탭의 **Attach Policy**를 선택하고, 위에서 확인한 Policy를 선택한다. 즉, 1단계의 Policy를 동일하게 적용하는 것이다. 
5. **AttachPolicy**를 한다. 
6. 1단계에서 만들어둔 Facebook을 이용해 접근하는 Role을 선택해 삭제한다. 

#### Browser에서 구동될 Javascript 수정하기

##### facebook login 코드 

```python
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
    <div id="status"></div>

    <script type="text/javascript">
        var FacebookAppId = '{FACEBOOK-APP-ID}';
        var roleArn = '{ROLE-ARN}';
        var bucketName = '{BUCKET-NAME}';
        var AwsRegion = '{AWS-REGION}';
        var AwsCognitoPoolId = '{AWS-COGNITO-ID}';
        var fbUserId;

        AWS.config.region = AwsRegion;
        var bucket = new AWS.S3({
            params: {
                Bucket: bucketName
            }
        });

        var fileChooser = document.getElementById('file-chooser');
        var button = document.getElementById('upload-button');
        var results = document.getElementById('results');
        button.addEventListener('click', function() {
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
                bucket.putObject(params, function(err, data) {
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
            }, function(err, data) {
                if (err) {
                    results.innerHTML = 'ERROR: ' + err;
                } else {
                    var objKeys = "";
                    data.Contents.forEach(function(obj) {
                        objKeys += obj.Key + "<br>";
                    });
                    results.innerHTML = objKeys;
                }
            });
        }

        function statusChangeCallback(response) {
            fbUserId = response.authResponse.userId;

            if (response.status === 'connected') {
                // Logged into your app and Facebook.
                FB.api('/me', function(response) {
                    console.log('Successful login for: ' + response.name);
                });

                // Add the Facebook access token to the Cognito credentials login map.
                AWS.config.credentials = new AWS.CognitoIdentityCredentials({
                    IdentityPoolId: AwsCognitoPoolId,
                    Logins: {
                        'graph.facebook.com': response.authResponse.accessToken
                    }
                });

                //Get IdentityId
                var cognitoidentity = new AWS.CognitoIdentity({
                    apiVersion: '2014-06-30'
                });
                var paramsForGetId = {
                    IdentityPoolId: AwsCognitoPoolId,
                    Logins: {
                        'graph.facebook.com': response.authResponse.accessToken
                    }
                };
                cognitoidentity.getId(paramsForGetId, function(err, data) {
                    if (err) {
                        console.log(err, err.stack);
                    } else {
                        console.log("IdentityId : ", data.IdentityId);
                        var paramsForgetCreditialsForIdentity = {
                            IdentityId: data.IdentityId,
                            Logins: {
                                'graph.facebook.com': response.authResponse.accessToken

                            }
                        };
                        cognitoidentity.getCredentialsForIdentity(paramsForgetCreditialsForIdentity, function(err, data) {
                            if (err) console.log(err, err.stack);
                            else console.log("getCredentialsForIdentity : ", data);
                        });
                    }
                });
            } else if (response.status === 'not_authorized') {
                document.getElementById('status').innerHTML = 'Please log ' +
                    'into this app.';
            } else {
                document.getElementById('status').innerHTML = 'Please log ' +
                    'into Facebook.';
            }
        }

        window.fbAsyncInit = function() {
            FB.init({
                appId: FacebookAppId,
                cookie: true,
                xfbml: true,
                version: 'v2.5'
            });
            FB.getLoginStatus(function(response) {
                statusChangeCallback(response);
                button.style.display = 'block';
            });
        };
        // Load the Facebook SDK asynchronously
        (function(d, s, id) {
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
        var AWSRegion = 'ap-northeast-1';
        var AWSS3BucketName = 'soma-ss-service';
        var AWSCognitoPoolId = 'ap-northeast-1:8e8e2f38-9c93-473b-8ccb-b19dc03a279f';
        var FacebookAppId = '1278483742163649';






        function statusChangeCallback(response) {
            
            var fileChooser = document.getElementById('file-chooser');
            var button = document.getElementById('upload-button');
            var results = document.getElementById('results');


            console.log('statusChangeCallback');
            console.log(response);
            console.log("userId : " + response.authResponse.userId);
            // The response object is returned with a status field that lets the
            // app know the current login status of the person.
            // Full docs on the response object can be found in the documentation
            // for FB.getLoginStatus().
            if (response.status === 'connected') {
                // Logged into your app and Facebook.
                testAPI();

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

                button.addEventListener('click', function() {
                    console.log('clicked');
                    var file = fileChooser.files[0];
                    if (file) {
                        results.innerHTML = '';
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
                    var prefix = '';
                    bucket.listObjects({
                        Prefix: prefix
                    }, function(err, data) {
                        if (err) {
                            results.innerHTML = 'ERROR: ' + err;
                        } else {
                            var objKeys = "";
                            data.Contents.forEach(function(obj) {
                                objKeys += obj.Key + "<br>";
                            });
                            results.innerHTML = objKeys;
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
                cookie: true, // enable cookies to allow the server to access
                // the session
                xfbml: true, // parse social plugins on this page
                version: 'v2.5' // use version 2.2
            });

            // Now that we've initialized the JavaScript SDK, we call
            // FB.getLoginStatus().  This function gets the state of the
            // person visiting this page and can return one of three states to
            // the callback you provide.  They can be:
            //
            // 1. Logged into your app ('connected')
            // 2. Logged into Facebook, but not your app ('not_authorized')
            // 3. Not logged into Facebook and can't tell if they are logged into
            //    your app or not.
            //
            // These three cases are handled in the callback function.

            FB.getLoginStatus(function(response) {
                statusChangeCallback(response);

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

        // Here we run a very simple test of the Graph API after login is
        // successful.  See statusChangeCallback() for when this call is made.
        function testAPI() {
            console.log('Welcome!  Fetching your information.... ');
            FB.api('/me', function(response) {
                console.log('Successful login for: ' + response.name);

                document.getElementById('status').innerHTML =
                    'Thanks for logging in, ' + response.name + '!';
            });
        }
    </script>

    <input type="file" id="file-chooser" />
    <button id="upload-button">Upload to S3</button>
    <div id="results"></div>
    <div id="fb-root"></div>
    <div id="status"></div>
</body>

</html>

```




