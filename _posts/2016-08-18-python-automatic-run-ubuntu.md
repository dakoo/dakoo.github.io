---
layout: post
title: python - ubuntu 시작시 자동으로 실행시키기
description: python - ubuntu 시작시 자동으로 실행시키기
modified: 2016-08-18
tags: [python]
comments: true
image:
  feature: abstract-13.png
---
!!! 작성 중!!!
개발한 python 코드를 ubuntu 시작시 자동으로 실행되로록 해야 하는 경우가 있다. 그 방법을 알아보자. 

## 방법 1. python code를 execuatable로 만들기 

### 1.1 Python code

python code의 맨 위에 아래 라인을 추가한다. 

```
#!/usr/bin/env python
```

### 1.2 python file 설정 

python file을 실행 가능하도록 수정하자. 

```
$ chmod +x {python-file.py} 
```

### 1.3 부팅 시 실행 스크립트에 추가

/etc/rc.local 파일에 아래와 같이 python file 실행하는 내용을 추가한다. 

```
./{pathTo}/{python-file.py}
```

## 방법 2. supervisorctl 사용하기 

### 2.1 supervisorctl 설치

...


## 방법 3. virtualenv와 연동해서 실행하기 

...
