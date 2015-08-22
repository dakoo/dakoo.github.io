---
layout: post
title: OSX에서의 javascript를 위한 vim 에디터 설정
description: OSX에서의 javascript를 위한 vim 에디터 설정
modified: 2015-08-19
tags: [tool]
image:
  feature: abstract-5.png
---

WebStorm이나 Sublime Text와 같은 훌륭한 Javascript 프로그래밍을 위한 툴들이 있지만, vim 또한 여전히 매우 훌륭한 에디터이다. Javascript 개발자을 위한 최적의 설정을 알아보자. 

## .vimrc 만들기

vim 설정은 자신 계정 root 폴더의 .vimrc 파일이 담당한다. 최초엔 없으므로 터미털에서 `touch .vimrc`명령으로 만들어야 한다.

## colorscheme 설정하기 

highlighting을 색 설정을 담당하는 것이 vim colorscheme이다. 'vim colorscheme' 키워드로 구글 검색을 하면 다양한 colorscheme을 찾을 수 있다. javascript용 colorscheme중 하나인 [mango.vim](http://www.vim.org/scripts/script.php?script_id=3779)을 기준으로 설명한다. 

### 다운로드 및 압축 풀기 

 [mango.vim 다운로드 페이지](http://www.vim.org/scripts/script.php?script_id=3779)에서 다운로드를 받아 압축을 풀어 mango.vim을 얻는다.  

### 설치 및 설정 

자신 계정 root 폴더에 아래 명령으로 *.vim 폴더*와 *.vim/colors* 폴더를 만든다. 
> $ mkdir -p ~/.vim/colors

다운로드 폴더의 mango.vim을 *.vim/colors*폴더로 옮긴다. 
> $ mv 다운로드폴더/mango.vim ~/.vim/colors

*.virmrc* 파일에 colorscheme을 다음과 같이 설정한다. 참고로 *"*은 *.vimrc*의 comment이다.

{% highlight bash %}
syntax on "syntax highlight 켜기
colorscheme mango "mango.vim을 colorscheme으로 설정하기 
{% endhighlight %}

### 테스트

이제 vim을 다시 실행시켜서 제대로 syntax highlight가 동작하는지 살펴본다. 

## Vim Plugin manager 설치

vim Plugin을 통해 vim은 비로소 javascript 에디팅 툴로 동작할 수 있다. 다양한 Plugin을 편하게 설치하기 위해서는 [대표적인 Vim Plugin manager인 Vunlde](https://github.com/VundleVim/Vundle.vim)을 이용하는 것이 좋다. 

다음 명령을 통해 Vundle을 설치한다. 

>  $ git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim 

.vimrc에 다음 내용을 추가한다. 

{% highlight bash %}
set nocompatible
filetype off

set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

call vundle#end()
filetype plugin indent on
{% endhighlight %}

## 필요한 Plugin 설치 및 설정

### Plugin 설치 방법 

*.vimrc*의 *call vundle#begin()*과 *call vundle#end()*사이에 원하는 Plugin을 지정하고 다음 명령을 터미널에서 실행하면 그 Plugin이 설치된다. 

>$ vim +PluginInstall +qall

### 필요한 Plugin들

#### Javascript Syntax Highlighting

[jelera/vim-javascript-syntax](https://github.com/jelera/vim-javascript-syntax)이 Highlighting에 적합하다. 그 설정을 위해 .vimrc의 Plugin 지정 위치에 다음을 추가한다. 

{% highlight bash %}
Plugin 'jelera/vim-javascript-syntax'
{% endhighlight %}

#### Javascript Indenting

pangloss/vim-javascript와 특히 callback에 적합한 nathanaelkane/vim-indent-guides가 indenting에 적합하다.

{% highlight bash %}
Plugin 'pangloss/vim-javascript'
Plugin 'nathanaelkane/vim-indent-guides'
{% endhighlight %}

#### Typing을 편리하게

Raimondi/delimitMate는 자동으로 괄호나 "를 닫게 해줘서 편하게 코딩을 할 수 있게 한다. 

{% highlight bash %}
Plugin 'Raimondi/delimitMate'
{% endhighlight %}

추가적으로 .vimrc에 다음 내용을 추가한다. 

{% highlight bash %}
imap <C-c> <CR><Esc>O
{% endhighlight %}

#### 코드 자동 완성 

Valloric/YouCompleteMe을 이용해 코드 자동 완성기능을 사용할 수 있다. 

##### YouCompleteMe 설치 및 설정

{% highlight bash %}
Plugin 'Valloric/YouCompleteMe'
{% endhighlight %}

추가적으로 .vimrc에 다음 내용을 추가한다. 

{% highlight bash %}
let g:ycm_add_preview_to_completeopt=0
let g:ycm_confirm_extra_conf=0
set completeopt-=preview
{% endhighlight %}

또한, marijnh/tern_fo도 설치하면 더욱 좋다. 

{% highlight bash %}
Plugin 'marijnh/tern_for_vim'
{% endhighlight %}

##### Vundle을 이용해 YouComleteMe를 설치 중 vim 버전 문제 

vim version이 7.3.584보다 낮을 경우엔 YouCompleteMe를 지원하지 않는다. 터미널에서 `vim --version`을 이용해 현재 버전을 확인해보자.

만약 에러를 만나면 [설치 문서](https://github.com/Valloric/YouCompleteMe/blob/master/README.md)를 참고하여 해결해 보자. 

#### Javascript Linting

Javascript Linting 결과를 보여주는 Plugin은 scrooloose/syntastic이다. 

{% highlight bash %}
Plugin 'scrooloose/syntastic'
{% endhighlight %}

추가적으로 .vimrc에 다음 내용을 추가한다. 

{% highlight bash %}
let g:syntastic_check_on_open=1
{% endhighlight %}

이제 scrooloose/syntastic의 사용을 위해 Linting을 수행할 jslint을 터미널에서 설치한다.  
물론 nodejs가 미리 설치가 되어 있어야 할 것이다. 
> $ npm install -g jshint

### 최종 .vimrc와 설치 

최종 .vimrc는 다음과 같다. 위와 같이 설정한 후 터미널에서 `vim +PluginInstall +qall`를 실행하면 Vundle 창이 떠서 Plugin이 하나씩 설치되는 것을 볼 수 있다. 

{% highlight bash %}
syntax on
colorscheme mango

set nocompatible
filetype off

set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()

Plugin 'jelera/vim-javascript-syntax'
Plugin 'pangloss/vim-javascript'
Plugin 'nathanaelkane/vim-indent-guides'
Plugin 'Raimondi/delimitMate'
Plugin 'Valloric/YouCompleteMe'
Plugin 'marijnh/tern_for_vim'
Plugin 'scrooloose/syntastic'

call vundle#end()
filetype plugin indent on
imap <C-c> <CR><Esc>O
let g:ycm_add_preview_to_completeopt=0
let g:ycm_confirm_extra_conf=0
set completeopt-=preview
let g:syntastic_check_on_open=1
{% endhighlight %}





