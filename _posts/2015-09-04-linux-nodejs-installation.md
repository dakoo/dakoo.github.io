---
layout: post
title: Linux(Ubuntu, Amazon Linux)에 nodejs를 설치하는 최고의 방법
modified: 2015-09-04
tags: [nodejs]
comments: true
image:
  feature: abstract-10.png
---

nodejs 설치 파일을 이용해 Linux에 설치하는 경우 향후 파일 시스템 접근 시 권한 문제가 발생할 수 있다. 예를 들어 Yeoman, Bower, Grunt등을 설치할 때 permission 문제가 발생하여 곤란을 겪을 수 있다. 여러 가지 방법을 찾아보고 실행해 본 결과, 가장 좋은 방법은 node.js를 설치 시에 'sudo' permission이 아니라 일반 계정의 permission으로 설치를 하는 것이다. 

<section id="table-of-contents" class="toc">
  <header>Overview</header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section>
<!-- /#table-of-contents -->



# 기존 nodejs 제거 
시스템에 이미 nodejs가 설치되어 있는 경우 이를 제거 한다. 

> $ sudo rm -r /usr/local/bin/node /usr/local/bin/npm /usr/local/include/node /usr/local/lib/node_modules /usr/local/share/man/man/node.1

자신 계정의 `.npm` 폴더를 제거한다. 

> $ rm -r ~/.npm
> $ rm -r .npm

# nvm 설치 

## nvm 다운로드 및 설치 

사용자 계정의 루트 폴더에서 nvm을 설치하기 위해 다음 명령을 수행한다. 

{% highlight bash %}
curl https://raw.githubusercontent.com/creationix/nvm/v0.23.3/install.sh | bash
{% endhighlight %}

또는 `curl` 대신 `wget`을 이용할 수 있다.  

{% highlight bash %}
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.23.3/install.sh | bash
{% endhighlight %}

이제 shell에 .nvm 설정을 추가한다. 

> $ source ~/.bashrc

# nodejs 설치

## stable 버전 설치 

nvm을 이용하여 sudo permission없이 node.js를 설치하자. 

> $ nvm install stable

이제 nodejs가 잘 설치되었는지 확인해보자. 

> $ node -v 

이제, sudo 권한 없이도 node를 성공적으로 설치하였다.

## 설정 & Test

다음 명령을 통해 node version을 확인할 수 있다. 

> $ node -v

위 명령의 결과를 `~/.bashrc` 파일에 추가한다. (v4.0.0이 그 결과라고 가정한다.)

{% highlight bash %}
nvm use v4.0.0 
{% endhighlight %}



