---
layout: post
title: Vi - vi plugin 설치
description: 개발 환경 - vi plugin 설치
modified: 2016-07-05
tags: [vi]
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

vi를 잘 쓰기 위해 vim에 plugin을 설치해서 사용하는 방법을 알아보자

### 필요 package 설치 

missing package가 있는 경우를 대비해서 다음 명령을 실행한다. 

```
sudo apt-get install vim-gui-common vim-runtime
```

NOTE: 만약 missing package가 있다면 나중 과정 진행 중 다음 메시지를 볼 수 있다. 

> Sorry, the command is not available in this version: ...

### Vim Plugin Manager 설치

#### 필요 폴더 설치

계정의 최상위 directory아래에 .vim/bundle 폴더가 없다면 만들어 둔다. 

```
mkdir -p ~/.vim/bundle
```

[Vundle](https://github.com/VundleVim/Vundle.vim)은 plugin을 관리하는 매니저로 반드시 설치되어야 한다. 

#### .vimrc 파일 생성

설치를 위해서는 계정 최상위 directory에 .vimrc 파일이 있어야 한다. 없으면 만들어 두자. 

#### Vundle Setup

Vundle을 다음 명령을 이용해서 ~/.vim/bundle아래에 위치 시킨다. 

```
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

#### Vundle Config

다음 내용을 .vimrc 파일의 맨 위에 붙여 넣기를 한 후 저장한다. 

```
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'

" The following are examples of different formats supported.
" Keep Plugin commands between vundle#begin/end.
" plugin on GitHub repo
Plugin 'tpope/vim-fugitive'
" Git plugin not hosted on GitHub
Plugin 'git://git.wincent.com/command-t.git'
" git repos on your local machine (i.e. when working on your own plugin)
Plugin 'file:///home/gmarik/path/to/plugin'
" The sparkup vim script is in a subdirectory of this repo called vim.
" Pass the path to set the runtimepath properly.
Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
" Install L9 and avoid a Naming conflict if you've already installed a
" different version somewhere else.
Plugin 'ascenator/L9', {'name': 'newL9'}

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line
```

#### Plugin 설치  

Console에서 다음 명령을 실행 시킨다. 

```
vim +PluginInstall +qall
```

그러면 이상한 창이 뜨고 왼편에 Plugin 목록이 표시되었다가 사라진다. 
이제 Plugin이 모두 설치된 것이다. 

필요한 Plugin이 있다면 .vimrc 파일에 다음과 같은 형식으로 추가한 후 위의 명령을 실행 시킨다. 

```
Plugin 'plugin경로'
```

### Web developer를 위한 추천 plugin들 

```
Plugin 'https://github.com/itchyny/lightline.vim'
Plugin 'https://github.com/tpope/vim-surround'
Plugin 'https://github.com/skammer/vim-css-color'
Plugin 'https://github.com/Shutnik/jshint2.vim'
```

### 추가 vim 설정

[예전 자료](http://hochulshin.com/dev-linux-vi-mimimal-setting/)를 참고하자. 

```
syntax on
set autoindent
set tabstop=4
set shiftwidth=4
set nu
colorscheme default
set backspace=indent,eol,start
```

