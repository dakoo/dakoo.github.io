---
layout: post
title: Eclipse - 추천 Plugin들 
description: Eclipse - Eclipse 추천 Plugin들 
modified: 2016-09-10
tags: [java, eclipse]
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

## 1. Plugin 설치 

market place URL은 해당 page에서 install icon을 eclipse로 drag & drop

## 2. 추천 Plugin들 


### 2.1 Eclipse Color Theme

Theme은 default가 제일 좋다. Eclipse Color Theme을 이용해서 highlight them만 변경하자. 

- 설치 URL: https://marketplace.eclipse.org/content/eclipse-color-theme
- 설정: Window > Preferences > General > Appearance> Color Theme에서 Tango 선택

### 2.2 Vrapper

VI plugin

- 설치: https://marketplace.eclipse.org/content/vrapper-vim
- 설치 시 package들을 모두 선택

설치가 되면 자동으로 vi 모드가 적용 된다. 

### 2.3 QuickSearch

매우 빠른 검색. 대소문자 구분

- 설치 URL: https://marketplace.eclipse.org/content/quick-search-eclipse
- 실행: Ctrl/CMD + Shift + L 키를 누르고 원하는 글자를 입력

### 2.4 IndentGrid

indent 표시 줄을 나타내는 툴. 설치는 help > install new software...를 통해서 한다. 

- 설치 URL: http://sschaef.github.io/IndentGuide/update

### 2.5 Findbugs

Java Static Analysis tool. 


- 설치 URL: https://github.com/findbugsproject/findbugs/releases/tag/3.0.2_preview2
- 실행: Project에서 Right-click >  Find Bugs > Find Bugs 
- 결과 보기:  View ▸ Zoom ▸ Reset Window ▸ Show View ▸ Other… 
- Automatic: Project > Properties > FindBugs > Check Run automatically

### 2.6 CheckStyle

Code Style checker

- 설치 URL: Market에서 check style 검색 후 install
- custom 설정: Preferences > Check Style > Global Check Configurations중 하나 doubleclick
- 설정 import: Preferences > Check StyleSettings > new > Remote Configuration > URL 입력 (file은 External Configuraiton)
- 실행: Project에서 Right-click >  Find Bugs > Find Bugs 
- 결과 보기:  Windows > Show View > Others > Checkstyle violations

### 2.7 More Clipboard

클립보드에서 선택하여 paste

- 설치 URL: https://marketplace.eclipse.org/content/more-clipboard
- 실행: Ctrl + Shift + V 후 선택


### 2.8 PMD For Eclipse 

정적 검사 툴

#### 2.8.1 PMD 설치 

- Help > Software Update...
- http://sourceforge.net/projects/pmd/files/pmd-eclipse/update-site/
- PMD for Eclipse 4 설치

#### 2.8.2 PMD plug-in 설치

- 설치 URL: https://marketplace.eclipse.org/content/eclipse-pmd
- 설정: **Preferences** > **PMD* > Rule Configuration에서 필요한 rule 선택
- 실행: 정적 검사 원하는 파일/패키지 선정하여 오른쪽 마우스 버튼 클릭 후 **PMD** > **Check Code**로 실행 

comment 관련한 rule(comment~로 시작)은 꺼놓는 것을 추천한다. 

### 2.9 umlet

- NOTE: eclipse plugin인 경우 property가 readonly가 되는 문제가 보고되었다. 문제가 생기면 standalone으로 사용하자. 

[umlet](http://www.umlet.com/)은 경량 uml tool. market place를 통해서 설치되지 않고 아래 방식으로 설치해야 한다. 자세한 내용은 [여기](http://www.umlet.com/faq.htm)를 참조하자. 

1. [umlet download 페이지](http://www.umlet.com/changes.htm)에서 최신 eclipse plugin을 선택하여 다운로드한다. 
2. 압축을 풀어 "com.umlet.plugin*.jar"를 얻는다. 
3. Eclipse를 실행해 Eclipse > About Eclipse > Installation Details > Configuration에서 15번째 줄 쯤에 --launcher.library를 찾아 그 plugins 폴더의 path를 확인한다. (나의 경우엔 /Applications/Eclipse.app/Contents/Eclipse/plugins/라는 것을 유추)
4. 위에서 확인한 path로 "com.umlet.plugin*.jar"를 이동한다. 
5. eclipse를 재 구동한다. 

이제 사용하기 위해서는 다음과 같이한다. 

1. Java 프로젝트 생성
2. File > New > Other.. 
3. Other > Umlet diagram

### 2.10 AnyEdit

edit를 쉽게하는 툴

- 설치 URL: http://marketplace.eclipse.org/content/anyedit-tools
- 설정: Preferences > General > Editors > AnyEdit Tools. Create new line at the end of the file 등을 설정한다. 
