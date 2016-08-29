---
layout: post
title: Python - on Windows 
description: python 
modified: 2016-03-02
tags: [python, windows]
comments: true
image:
  feature: algorithm.jpeg
---
Windows환경에서 python 언어로 개발하는 환경을 간단히 갖추어 보자. pyCharm과 같은 유료 editor가 아닌 Bracket을 사용해서 개발하는 환경을 가정한다. 

### Windows에서 python 개발 환경 설치하기 

Windows라고 해서 특별히 Ubuntu나 OSX와 다르지 않다. python.org에서 windows용을 다운로드 받아 설치하고, 자주 사용하는 editor에서 접근할 수 있도록 환경 변수를 등록하면 된다. 

#### python 설치하기

[파이썬 공식 사이트](https://www.python.org/downloads/)에서 3.x 또는 2.x 버전 중 원하는 것을 다운받아 설치한다. 설치시에는 default 설정을 따른다. 

#### 환경 변수 등록하기 

##### 3.5 버전 

python 3.5는 windows7의 경우 `C:\Users\admin\AppData\Local\Programs\Python\Python35-32\python.exe`가 default 경로이다. 자신의 PC에 설치된 python.exe 파일가 있는 경로를 복사한다. 그 다음 시스템 환경 변수의 PATH에 그 경로를 추가한다. 또한, 3rd party 모듈을 설치하기 위한 pip 명령의 경로인 `C:\Users\admin\AppData\Local\Programs\Python\Python35-32\Scripts`도 path에 추가한다. 만약 python 모듈을 등록해야 하는 일이 있다면 환경변수의 시스템 변수이름을 'PYTHONPATH'라는 이름으로 만들어 경로를 등록한다. 

##### 2.7 버전

python 2.7은 windows7의 경우 `C:\Python27\python.exe`가 default 경로이다. 자신의 PC에 설치된 python.exe 파일가 있는 경로를 복사한다. 그 다음 시스템 환경 변수의 PATH에 그 경로를 추가한다. 또한, 3rd party 모듈을 설치하기 위한 pip 명령의 경로인 `C:\Python27\Scripts`도 path에 추가한다. 만약 python 모듈을 등록해야 하는 일이 있다면 환경변수의 시스템 변수이름을 'PYTHONPATH'라는 이름으로 만들어 경로를 등록한다.

#### Brackets 에디터에서 실행

##### Brackets-builder plugin 설치 

Brackets 에디터에서 python 파일을 바로 실행할 수 있도록 하는 플러그인을 설치한다. 

- [플러그인 사이트](https://github.com/Vhornets/brackets-builder)에서 zip 파일을 다운로드 받는다. 
- Brackets을 실행한 뒤 파일 > 확장 기능 관리자를 실행시킨다. 
- 확장 기능 관리자의 plugin설치 부분에 미리 다운로드 받아둔 brakcets-builder.zip 파일을 드래그앤 드롭한다. 
- 설치가 완료되면 편집 > edit builder를 실행해서 builder.json 파일이 열리는 지 확인한다. 수정을 하면 재부팅하라고 하는데 수정할 필요가 없으므로 무시한다. 

##### 테스트

Brackets에서 `.py`확장자의 파일을 만들고 다음과 같이 간단한 코드를 붙여 넣고 저장한다. 3.5 버전의 경우는 

```
print('Hi Alice')
```

2.7버전의 경우는

```
print 'Hi Alice'
```

Control + Alt + B 키를 눌러 실행이 되는지 확인한다. 실행이 되면 OK!
