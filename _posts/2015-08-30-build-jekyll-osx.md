---
layout: post
title: Blog - OSX에서 Jekyll 블로그 테마 구동하기
excerpt: "OSX에서 Jekyll 블로그 테마 구동하기"
modified: 2015-08-30
tags: [blog, osx]
comments: true
image:
  feature: abstract-23.jpg
---

OSX Yosemite에서 Jekyll 블로그 테마를 구동하는 방법을 살펴보자. 이것은 호스팅 이전에 맥에서 테스트하기 위한 기본 설정이다. 


<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


# homebrew 설치 

[Homebrew](http://brew.sh/)를 설치한다. 터미널에서 다음 명령을 수행한다. ruby는 OSX에 기본으로 설치되어 있으므로 별도로 설치할 필요가 없다. 

> $ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# rvm 및 ruby 최신 버전 설치

ruby version manager인 [rvm](https://rvm.io/)과 ruby의 최신 안정화된 버전을 다음 명령으로 설치한다. 

> $ \curl -L https://get.rvm.io | bash -s stable --ruby

# rvm 실행

터미널을 재 시작하지 않고 아래 명령을 통해서도 rvm을 실행시킬 수 있다. 

> $ source /Users/hochulshin/.rvm/scripts/rvm

# Gem 설치

jekyll에 필요한 ruby gem들을 다음 명령으로 설치한다. 

> $ gem install jekyll bundler

# Jekyll theme 다운로드 

[jekyllthemes](http://jekyllthemes.org/)에서 원하는 Jekyll theme를 선택하여 다운로드 받아, 적당한 위치에 압축을 해제한다. 

# Bundle 설치

압축이 풀린 Jekyll theme 폴더로 이동하여 jekyll theme에 미리 설정이 되어 있는 필요한 bundle을 설치한다. (Jekyll, Jekyll-Sitemap 등이 설치된다.)

> $ bundle install 

# 테스트 

## theme build 및 구동

터미널에서 다음 명령으로 jekyll theme을 build하고 서버를 구동한다. 

> $ bundle exec jekyll build
> $ bundle exec jekyll serve

## 확인

browser에서 `127.0.0.1:4000`으로 접근해서 theme가 잘 보이는 지 확인한다. 

# theme 수정

이제 jekyll theme에서 설명하는 setup 방법에 따라 theme을 수정한다. 주로 최상위 폴더의 **_config.yml**과 **_data** 폴더 내 **navigation.yml**의 수정이 필요하다.  





