---
layout: post
title: 개발 환경 - sublime text3 설치와 설정
description: 개발 환경
modified: 2016-03-20
tags: [개발 환경]
comments: true
image:
  feature: algorithm.jpeg
---
### 다운로드 및 설치 

Sublime Text3를 [여기](https://www.sublimetext.com/3)에서 다운로드 받아 설치한다.

### Package Control 설치 

Package control을 다음 순서대로 설치한다. 

1. [여기](https://packagecontrol.io/installation#st3)에서 Sublime text3용 script를 복사한다. 
2. Sublime Text3를 실행한다. 
3. Control + ` 키를 눌러서 console창을 연 후 위에서 복사한 script를 붙여넣기하여 스크립트를 실행한다.  
4. Sublime Text3를 재실행한다. 

#### Trouble Shooting

If you face a ssl-related error while installing Package control, do the following on terminal.

- unzip -l ~/.config/sublime-text-3/Installed\ Packages/0_package_control_loader.sublime-package

After unzip the package, relaunch the Sublime Text3

### Install new packages and configuration

- AdvancedNewFile : Control + Alt + N으로 생성
- BracketHighlighter
- SideBarEnhancement
- SublimeLinter
- SublimeCodeIntel
- Theme - soda
- etc

#### 기본 설정

##### vi mode

default로 disable인 Vintage를 활성화하면 vi mode로 동작하게 된다. 

1. Preferences > Settings - User를 선택한다. 
2. ignored_packages에서 "Vintage"를 제거한 후 저장한다. 
3. Sublime Text 3를 재시작한다. 

### basic package를 빠르게 설치하고 설정하기

아래 두개의 파일을 운영체제 별로 다음 path에 추가한다. 이미 존재하는 파일이 있다면 덮어쓴다.

- Ubuntu: ~/.config/sublime-text-3/Packages/User/
- OSX: ~/Library/Application Support/Sublime Text 3/Packages/User/
- Windows7: %APPDATA%/Roaming/Sublime Text 3/Packages/

#### Package Control.sublime-settings 파일

```
{
        "bootstrapped": true,
        "in_process_packages":
        [
        ],
        "installed_packages":
        [
                "AdvancedNewFile",
                "BracketHighlighter",
                "GitGutter",
                "Package Control",
                "Python Flake8 Lint",
                "Python PEP8 Autoformat",
		"SublimeAStyleFormatter",
                "SideBarEnhancements",
                "SublimeCodeIntel",
                "SublimeLinter",
                "Theme - Soda"
        ]
}
```
#### Preferences.sublime-settings 파일

`"auto_match_enabled": false` truns off automatic parenthesis matching

```
{
	"fade_fold_buttons": false,
	"findreplace_small": true,
	"highlight_line": true,
	"highlight_modified_tabs": true,
	"ignored_packages":
	[
		""
	],
	"line_padding_top": 1,
	"sidebar_font_big": true,
	"soda_classic_tabs": true,
	"tab_size": 4,
	"translate_tabls_to_spaces": true,	
	"tabs_small": true,
	"wide_caret": true,
	"word_wrap": true,
	"auto_match_enabled": false
}
```

### 단축키 

- Command + Shift + P : package control (Ubuntu: Control + Shift + P)
- Control + Alt + N: 파일 생성 (Ubuntu: Windows + Alt + N)
- Command + K, Command + B: 사이드 바 열고 닫기(Ubunu: Control + Shift + Alt + S as below)

Ubuntu: To toggle the side bar, add the following line in `Default\ \(Linux\).sublime-keymap` file (Menu: Preference : Key Bindings - User)

```
{ "keys": ["ctrl+shift+alt+s"], "command": "toggle_side_bar" },
```
