---
layout: post
title: Nodejs - Mac(OSX)에 nodejs를 설치하는 최고의 방법
description: Mac(OSX)에 nodejs를 설치하는 최고의 방법
modified: 2015-08-18
tags: [osx, nodejs]
comments: true
image:
  feature: abstract-20.png
---

node를 nodejs.org 사이트에서 다운로드하여 설치하는 경우 향후 파일 시스템 접근 시 권한 문제가 발생할 수 있다. 이런 문제를 해결하기 위해서는 nodejs.org에서 제공하는 설치 파일을 이용해 node를 설치하지 않는 것이 좋다.대신 **nvm** 또는 **[brew](http://brew.sh/)**를 이용해 설치를 해야 한다.

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

## 설치 파일을 이용해 설치할때의 문제

권한 문제의 대표적인 예는 npm을 global로 설치할 때 권한문제로 인해 실패하는 것으로 다음과 같은 메시지를 볼 수 있다.

{% highlight bash %}
npm ERR! tar.unpack untar error ...
{% endhighlight %}

일반적으로 `npm cache clean`을 수행하거나 `sudo` 권한으로 설치하면 해결된다고 하는데, `npm cache clean`의 경우 해결이 안되는 경우가 많고, `sudo`를 이용하는 경우 권한이 꼬여서 나중에 npm 설치 시 어려움을 겪을 수 있다. 

### 해결책

#### node 제거 

만약 이미 node를 설치했을 경우 다음과 같이 삭제한다. 

- node와 npm을 시스템에서 제거한다. 이를 위해 *sudo*권한이 필요하다. 

```bash
$ cd /usr/local; sudo rm -r bin/node bin/npm include/node /lib/node_modules
```

- 만약 이전에 brew를 통해 node를 설치했다면 다음 명령을 통해 node를 제거한다. 

```bash
$ brew uninstall node
```

#### node 설치 

- 설치 시 Xcode가 필요하다. 앱스토어에서 Xcode를 다운로드 받아 한 번 실행해서 시스템 권한을 갖도록 한다. 
- [homebrew](http://brew.sh/)를 설치한다.

```bash
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

- node를 설치한다. 이때 npm은 설치하지 않는다. 

```bash
$ brew install node --without-npm
```

- 자신 계정 폴더에서 `.bash_profile` 파일을 생성하고 다음 내용을 추가한다. 아래의 경로는 nodejs 버전 0.12.7인 경우 **node** 실행 파일이 존재하고 있는 */usr/local/Cellar/node/0.12.7/bin* 폴더를 가리키는 것이다. 이 것은 nodejs 버전에 따라 수정되어야 한다.

```bash
export PATH="/usr/local/Cellar/node/0.12.7/bin:$PATH"
```

- 다음과 같이 실행하여 node파일의 경로를 system에 등록하여 어디서나 node 명령을 수행할 수 있도록 한다.

```bash
$ . .bash_profile 
```

- 이제 마지막으로 npm을 설치한다. 설치 메시지에 큰 문제가 없다면 성공이다. 시스템에 nodejs가 제대로 등록안된 경우 npm 설치가 안될 수 있다.  

```bash
$ curl -L https://www.npmjs.com/install.sh | sh
```

#### 테스트

- 현재 터미널에서 npm을 실행해 보자. 실행이 실패한다면 `brew uninstall node` 명령을 이용해 nodejs를 삭제하자. 이후 다시 설치 순서를 진행해 보자. 

- 터미널을 종료한 후 다시 실행해서 터미널에서 npm을 실행해 보자. 만약 npm을 찾을 수 없다면 설정의 문제가 있는 것이니 `.bash_profile`의 내용을 다시 한번 꼼꼼히 확인해 보자. 

