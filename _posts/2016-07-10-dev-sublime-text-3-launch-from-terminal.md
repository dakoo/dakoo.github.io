---
layout: post
title: Sublime text - 터미널에서 sublime 바로 열기
description: Sublime text - 터미널에서 sublime 바로 열기
modified: 2016-07-10
tags: [sublime]
comments: true
image:
  feature: abstract-11.png
---
터미널에서 sublime을 폴더 또는 파일과 함께 바로 여는 방법

## OSX

OSX에서는 sublime은 "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" 에 설치되어 있는데 터미널에서 바로 실행이 어렵다. 그래서 $PATH에 추가된 폴더에 심볼릭 링크를 건다. 
$PATH는 다음과 같이 확인한다. 

> echo $PATH

/usr/local/bin이 $PATH에 포함되어 있다면 거기에 link를 걸어보자. 

```
ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" /usr/local/bin/sublime
```

이제 특정 폴더에서 다음과 같이 입력하면 바로 그 폴더와 함께 sublime이 실행된다.

> sublime .

### 

## Ubuntu

sublime text를 설치 후 특정 폴더에서 다음과 같이 입력하면 바로 그 폴더와 함께 sublime이 실행된다.

> subl .

OSX와의 통일을 위해 ~/.bashrc에 다음을 추가한다. 

```
alias sublime='subl'
```

이제 다음과 같이 실행하면 된다. 

> sublime .
