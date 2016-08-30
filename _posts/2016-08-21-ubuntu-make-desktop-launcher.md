---
layout: post
title: Ubuntu - 터미널에서 실행되는 프로그램을 Launcher로 만들기
description: Ubuntu - 터미널에서 실행되는 프로그램을 Launcher로 만들기
modified: 2016-08-21
tags: [ubuntu, git]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

터미널에서 명령을 실행시키는 대신 Dash에서 실행시키거나 icon을 클릭하여 실행하기 위한 Launcher를 어떻게 만드는 지 알아보자. 
여기서 GitKraken의 경우를 예로 살펴보기로 하자. 

#### 사전 준비

미리 원하는 프로그램이 folder상에 설치되어 있어야 한다. 

- GitKraken의 경우 홈페이지로부터 압축파일을 다운로드 받아 압축을 푼 폴더의 하위 폴더 중 bin에 gitkraken이라는 실행파일이 있다. 

프로그램의 아이콘 이미지를 다운받아야 한다. 

- GitKraken의 경우 google image 검색으로 jpg 확장자의 아이콘을 다운로드 받아 둔다. 

#### Launcher 만들기 

1. 파일 탐색기를 실행
2. /usr/share/applications 폴더로 이동
3. 적당한 아이콘 아무거나 잡아서 복사를 하고 바탕화면에 paste
4. 바탕화면의 아이콘에서 오른쪽 마우스 > Propertites 선택
5. Name을 원하는 이름으로 바꾸고, Command는 실행하고자 하는 프로그램의 위치와 argument를 삽입. GitKraken의 경우 '/home/ubuntu/Downloads/GitKraken/gitkraken'이라 입력 
6. icon을 눌러 저장된 아이콘 이미지를 선택
7. 저장하고 테스트!

#### Launcher 등록

Dash에서 호출할 수 있도록 하자. 

1. 터미널을 연다. 
2. ~/Desktop 폴더로 이동
3. 만들어진 아이콘 파일을 /usr/share/applications 폴더로 이동. 이때 sudo 권한 필요. 

Dash에서 프로그램을 검색해서 실행해 보자! 
