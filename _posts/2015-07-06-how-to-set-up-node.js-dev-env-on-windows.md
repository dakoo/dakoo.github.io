---
layout: post
title: Windows에서 node.js 개발을 위한 WebStorm 환경 설정 하기
description: Windows에서 node.js 개발을 위한 WebStorm 환경 설정 하기
modified: 2015-07-06
tags: [node.js, webstorm]
categories: node.js
image:
  feature: abstract-17.png
---

최고의 Web IDE인 WebStorm은 node.js와 front-end 개발 모두에 적합하다. Windows에 WebStorm을 이용한 node.js 개발 환경을 설치해보자. 
개발 환경 설치 후에는 프로젝트 폴더를 만들어 실제 프로젝트를 시작할 수 있다.  

## WebStorm 설치

### WebStorm License 복사(Optional) 

WebStorm License가 없다면, 1개월동안 공짜로 사용하고 구매할 지 여부를 선택할 수 있다. 만약, License를 가지고 있다면 [JetBrains 사이트](https://account.jetbrains.com/licenses/assets)에 로긴해서 구매한 License 키를 확인한다. 키 위에서 클릭하면 클립보드에 복사된다. 

### WebStorm 다운로드

[WebStorm 다운로드 사이트](https://www.jetbrains.com/webstorm/download/)에서 Windows용 최신 버전을 다운로드 한다. 

### WebStorm 설치 

1. 설치 중 키입력 창이 나오면 WebStorm 구매시 등록한 아이디와 복사한 License 키를 입력한다. 
2. WebStorm의 환경 설정 창이 뜨면 자신이 좋아하는 설정을 해보자. 검은 색을 좋아하는 개발자라면 아래의 설정을 추천한다. 

- Keymap: Visual Studio
- IDE Theme : Darcula
- Editor color/font: Darcula

## Node.js 설치

### Windows Architecture 확인 

Command prompt에서 `wmic os get osarchitecture` 명령을 입력하여 architecture를 확인한다. 

### Node.js 다운로드 

[node.js 사이트](https://nodejs.org/download/)에서 Windows Installer와 source code 압축 파일을 다운로드 받는다. 

### 설치 및 확인 

Windows Installer를 통해 설치를 진행한다. 설치가 완료되면, Command Prompt에서 `node –-v && npm --version` 명령을 실행하여 제대로 설치되었는지 확인한다. 

### Node.js 소스 코드 압축 해제

다운받은 node.js 소스코드의 압축을 풀어 향후 참조할 folder에 저장한다. 

## WebStorm 설정

1. WebStorm을 실행하여 프로젝트를 새로 시작한다.  
2. **File** > **Settings**으로 설정창을 연다. 
3. **Languages and Frameworks** > **Node.js and NPM**을 선택한다. 
4. Node Interpreter에 설치한 node.exe의 경로를 입력한다. default 경로는 *C:\Program Files\nodejs\node.exe* 이다. 
5. **Node.js Core Modules의 소스코드 경로**에는 압축을 푼 node.js 소스코드의 root folder를 지정한다. 
6. **Languages and Frameworks** > **Javascript** > **Libraries** 에서 node.js관련된 항목이 모두 check되어 있는 것을 확인한다.

## 기본 환경 설정 끝

이제 기본 설정은 완료되었다. 프로젝트 폴더를 만들어 실제 프로젝트를 시작할 수 있다.  

