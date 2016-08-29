---
layout: post
title: Python - virtualenv 
description: Python - virtualenv 
modified: 2016-07-28
tags: [python, virtualenv]
comments: true
image:
  feature: abstract-08.png
---
Ubuntu에서 python virtualenv를 구성하는 것은 다음과 같다. 

- 설치: sudo apt-get install python-virtualenv
- 가상 환경 생성: virtualenv [가상환경이름] (특정 폴더 아래에서)
- 가상 환경으로 진입: source [가상환경이름]/bin/activate (생성한 folder에서)
- 가상 환경에서 빠져나오기: deactivate
- 버전: virtualenv --version
