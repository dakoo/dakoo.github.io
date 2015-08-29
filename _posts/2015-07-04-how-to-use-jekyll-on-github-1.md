---
layout: post
title: Windows에서 Jekyll 이용해 Github 블로그 만들기(1)
description: Windows에서 Jekyll 이용해 Github 블로그 만들기 첫번째 
modified: 2015-07-04
tags: [blog]
image:
  feature: abstract-11.png
---
Github에 블로그를 만들어 보자. 이를 위해 Windows에 jekyll 환경을 구축해 jekyll 테마를 적용하고, Github에 올려 블로그를 운영하는 방법을 정리해 본다. 그 첫번째 순서로 이 글은 Windows에 jekyll 환경을 구축해서 jekyll 테마를 적용하는 것을 설명한다. 


## 준비

### Windows Architecture
먼저, 자신의 Windows가 32비트인지 64비트인지 확인하자. command prompt에서 `wmic os get osarchitecture`를 실행하면 자신의 Windows architecture를 확인할 수 있다. 


## Ruby 설치

### ruby와 rubydevkit 다운로드

[rubyinstaller 사이트](http://rubyinstaller.org/downloads/)에서 ruby 설치파일과 rubydevkit 설치 파일을 다운로드 받는다. 이때 **2.2**이하의 버전이 안정적이다. <u>2.0</u>이나 <u>2.2</u> 버전으로 자신의 Windows architecture와 일치하는 설치파일을 다운로드 받는다. 

### ruby 설치
ruby installer를 실행하여 ruby를 설치한다. 설치 중 모든 설정을 default로 하되, *“Add Ruby executables to your PATH”*는 선택을 하여 설치한다. 
설치가 끝나면 command prompt에서 `ruby -v`로 제대로 설치되었는지 확인한다. 

### rubydevkit 설치

#### rubydevkit 압축풀기
임의의 빈 폴더를 만든다. 이때 폴더 이름과 경로에 <u>space</u>가 포함되어 있으면 안된다. 폴더 경로의 예는 아래와 같다. 

>C:\RubyDevKit

다운로드된 rubydevkit 설치 파일을 실행하면 압축을 풀 경로를 선택하게 되는데 이때 미리 만들어둔 빈 폴더를 선택한다. 

#### rubydevkit과 ruby와의 binding
ruby 실행파일의 경로를 설정 파일에 반영 하기 위해 rubydevkit이 압축이 풀린 폴더에서 command prompt로 `ruby dk.rb init` 명령을 실행한다. 그리고, `ruby dk.rb install`명령을 통해 ruby와 binding하며 rubydevkit을 설치한다.  


## Bundler 설치

### Command Prompt에서의 Bundler 설치

Command prompt에서 `gem install bundler`명령을 실행하여 ruby의 패키지 매니저인 bundler를 설치한다. 

### 에러 해결 
만약 bundler 설치 중 에러 메시지가 아래와 같이 출력되고 설치가 종료된다면, 이는 rubygems.org의 구조가 변경되었기 때문에 생기는 문제이다. 

>ERROR: Could not find a valid gem ‘xxx’, here is why: Unable to download data from https://rubygems.org/ - SSL_connect...

RubyGems버전이 2.0.x나 2.2.x이여야 하며, ubygems.org permission 관련 설정이 업데이트 되어야 한다. 아래 설명과 같이 진행한 후 다시 bundler 설치를 시도해 보자. 

#### RubyGems 버전 문제 해결 
버전이 너무 최신이라면 Ruby를 제거하고 다시 설치하거나 업데이트를 통해 버전을 낮추는 것이 좋다.

#### Rubygems.org permission 설정 업데이트
1. [Permission 링크](https://raw.githubusercontent.com/rubygems/rubygems/master/lib/rubygems/ssl_certs/AddTrustExternalCARoot-2048.pem)을 저장한다.  
2. Command Prompt로 `gem which rubygems`을 실행해 rubygems.rb가 설치된 폴더를 확인한다. 
3. rubygems.rb가 설치된 폴더 아래의 rubygems\ssl_certs 폴더로 이동한다 
4. 저장해 놓은 **.pem**을 그 폴더로 복사한다. 


## 테마와 함게 블로그 구성하기

### 테마 적용하기 

#### jekyll 테마 선정하기 

[jekyll 테마 사이트](http://jekyllthemes.org/)에서 원한는 jekyll 테마를 골라 테마 파일을 다운로드 받는다. 테마 파일의 압축을 풀고, 그 폴더 이름을 자신의 블로그 이름으로 변경한다. 

#### 필요한 gem 설치

블로그 폴더로 이동하여, Command Prompt로 `bundle install`명령을 실행해 필요한 gem을 설치한다. 설치가 완료되면 gem들을 최신 버전으로 업데이트하기 위해 `gem update`명령을 실행한다. 

## Syntax highlighter인  pygments 설치 
Jekyll의 default syntax highlighter인 pygments를 설치해보자. pygments는 python기반에서 동작하므로 python 개발환경을 설치해야 한다. 

### python 설치
python 2.7.x를 [다운로드](https://www.python.org/downloads/windows/)한다. 이때 반드시 architecture(32/64)를 확인해서 자신의 Architecture와 일치하는 것을 확인해야 한다. 설치 중 반드시 *Add Python.exe to Path*를 선택해 *“Entire feature will be install…”*를 선택해야 한다. 설치가 끝나면 Command Prompt에서 `python` 명령을 실행해 설치가 잘되었는지 확인한다. 

#### python 설치 중 에러 해결
`python` 명령 실행 시 에러가 생기면 Windows 시스템 변수인 path에 아래의 경로가 추가되어 있지 않아 생기는 문제일 가능성이 높다. 

>C:\Python27;C:\Python27\Scripts;C:\Python27\Lib\site-packages

시스템 변수 path는 *내 컴퓨터 > 속성> 고급 시스템 설정 > 고급 > 환경변수 > 시스템 변수*에서 확인 및 변경 가능하다. 

### pygments 설치
Command Prompt에서 `easy_install Pygments` 명령을 실행해 pygments를 설치하자. 


## 블로그 확인하기

### 블로그 build 및 서버 구동

Command Prompt에서 `bundle exec jekyll build  ` 명령을 통해 여러 설정파일을 통해 블로그 컨텐츠를 build한다. 성공적으로 완료되면 서버를 구동하기 위해 `bundle exec jekyll serve`명령을 실행한다. 

### 웹브라우저에서 확인
웹브라우저에서 주소로 **localhost:4000**을 입력하여 블로그가 잘 보이는지 확인한다. 


## 추가 사항

### 블로그 테마의 customization
블로그 테마의 설명을 보고 자신에게 맞게 블로그 제목이나 구성을 바꾸어 보자.

### 마크다운 에디터 설치 
쉽게 블로그를 작성 하기 위해 [markdownpad](http://markdownpad.com/download.html)를 설치한다. 

