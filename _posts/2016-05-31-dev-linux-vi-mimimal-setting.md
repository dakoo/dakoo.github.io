---
layout: post
title: Ubuntu - 최소한의 vi 설정
description: Ubuntu - 최소한의 vi 설정
modified: 2016-05-31
tags: [ubuntu, vi]
comments: true
image:
  feature: abstract-11.png
---
Linux에서의 최소한의 vi 설정을 알아보자. 

## vi setting on Linux

~/.vimrc 파일이 없다면 생성한 후 다음을 입력하자.

```
syntax on
set autoindent
set tabstop=4
set shiftwidth=4
set nu
colorscheme default
set backspace=indent,eol,start
```
