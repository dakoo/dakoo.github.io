---
layout: post
title: Tool - Mac에서 터미널 작업 실행후 notification
description: Tool - Mac에서 터미널 작업 실행후 notification
modified: 2018-11-12
tags: [tool]
comments: true
image:
  feature: abstract-12.png
---

mac 터미널에서 오래 걸리는 작업을 실행할 경우 작업의 종료를 알기 위해서는 터미널의 해당 탭으로 가서 작업 상황을 확인해야 하는 번거로움이 있다. 이를 해결해보자.

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

## 목표

터미널에서 수행한 작업이 끝나면 소리와 pop-up 창으로 작업 종료를 알 수 있게 만들자. 

## script 작성

~/scripts folder 아래에 notification.sh 파일을 만들고 다음 내용을 붙여 넣는다. 

```
alias noti="say \'task complete\';osascript -e 'display notification \"task complete\"  with title \"terminal\"'"
```

## RC 파일에 추가 

.bashrc 또는 .zshrc 파일에 다음을 추가해서 터미널이 실행시 alias가 등록되도록 한다. 

```
#Custom Scripts
. ~/scripts/notification.sh
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
% some_long_running_command && noti
```

이렇게 하면 some_long_running_command가 종료거나 중간에 에러가 발생해서 더이상 진행되지 못할 경우 소리와 pop-up window를 통해 그 상황을 바로 알 수 있다. 
