---
layout: post
title: Tool - Mac iterm2에서 작업을 다른 tab에서 실행하기 
modified: 2018-11-12
tags: [tool]
parent: Tool
nav_order: 2
---

mac iterm2에서 작업을 실행할 경우 새로 탭을 열어서 그 폴더로 이동해서 작업을 실행해야 하는 경우가 종종 있다. 이를 한번에 하도록 하자. 

## 목표

mac iterm2에서 현재 폴더에서 명령을 실행시킬 때 현재 탭이 아닌 다른 탭을 열어서 실행되도록 하자. 이때 되도록 알맞은 profile을 선택해서 실행하자. 

## osa script 작성

~/scripts folder 아래에 newtab.scpt를 만들고 아래 내용을 붙여 넣는다. 

```
#!/usr/bin/osascript
on run argv
        tell application "iTerm 2" to activate
        tell application "iTerm 2" to tell current window to create tab with profile "iTerm_Profile_name"
        #tell application "iTerm 2" to create window with profile "iTerm_Profile_name"
        tell application "System Events" to tell process "iTerm 2" to keystroke item 1 of argv
        tell application "System Events" to tell process "iTerm 2" to key code 52
        tell application "System Events" to tell process "iTerm 2" to keystroke item 2 of argv
        tell application "System Events" to tell process "iTerm 2" to key code 52
end run
```

만약 새로운 탭이 아닌 새로운 윈도우 창을 열어서 실행시키고 싶은 경우 위의 주석처리한 줄의 주석 마크(#)를 지우고, 그 바로 위의 줄을 주석처리한다. 
그리고 미리 iterm에 profile을 만들어서 연결해주거나 또는 프로파일 관련 부분을 삭제한다. 

## shell script 작성

~/scripts folder 아래에 newtab.sh 파일을 만들고 다음 내용을 붙여 넣는다. 

```
function run {
        _d_curDir=`pwd`
        osascript /Users/hochshin/scripts/newtab.scpt "cd $_d_curDir" "$1"
}
```

## RC 파일에 추가 

.bashrc 또는 .zshrc 파일에 다음을 추가해서 터미널이 실행시 function이 등록되도록 한다. 

```
#Custom Scripts
. ~/scripts/newtab.sh
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
% run some_long_running_command

or argument가 있거나 여러 명령을 함께 수행하는 경우

% run "some_long_running_command arguments && noti"
```

이렇게 하면 새로운 탭이 열리며 some_long_running_command가 현재 명령이 실행하는 폴더에서 실행되게 된다. 
