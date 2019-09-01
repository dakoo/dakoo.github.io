---
layout: post
title: Tool - Mac iterm2에서 ssh를 다른 tab에서 실행하기 
modified: 2018-11-12
tags: [tool]
---

mac iterm2에서 ssh로 다른 host에 접속할 경우 새로 탭을 열어서 원하는 profile과 함께 연결되도록 하자. 

## 목표

mac iterm2에서 현재 폴더에서 gossh명령을 실행시키면 현재 탭이 아닌 다른 탭을 열어서 ssh로 정해진 host로 접속되도록 하자. 이때 되도록 알맞은 profile을 선택해서 실행하자. 

## osa script 작성

~/scripts folder 아래에 remote.scpt를 만들고 아래 내용을 붙여 넣는다. 

```
#!/usr/bin/osascript
tell application "iTerm 2" to activate
tell application "iTerm 2" to tell current window to create tab with profile "iterm_profile_name"
tell application "System Events" to tell process "iTerm 2" to keystroke "ssh ssh_address"
tell application "System Events" to tell process "iTerm 2" to key code 52
```

위에서 ssh_address대신 실제 접속할 host의 address를 붙여넣는다. 그리고, 미리 iterm에 profile을 만들어서 연결해주거나 또는 프로파일 관련 부분을 삭제한다. 

## shell script 작성

~/scripts folder 아래에 remote.sh 파일을 만들고 다음 내용을 붙여 넣는다. 

```
function gossh {
        osascript /Users/hochshin/scripts/remote.scpt
}
```

## RC 파일에 추가 

.bashrc 또는 .zshrc 파일에 다음을 추가해서 터미널이 실행시 function이 등록되도록 한다. 

```
#Custom Scripts
. ~/scripts/remote.sh
```

터미널을 재시작하거나 또는 아래 명령을 통해 alias를 등록한다. 

```
% source .bashrc 
or
% source .zshrc
```

## 실행

다음과 같이 사용한다. 

```
% gossh
```

이렇게 하면 새로운 탭이 원하는 profile과 함께 열리며 자동으로 ssh_address에 접속하게 된다.  
