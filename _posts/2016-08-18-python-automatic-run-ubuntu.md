---
layout: post
title: Python - ubuntu 시작시 자동 실행(1) 
description: Python - ubuntu 시작시 자동 실행(1) 
modified: 2016-08-18
tags: [python, ubuntu]
comments: true
image:
  feature: abstract-13.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

개발한 python 코드를 ubuntu 시작시 자동으로 실행되로록 해야 하는 경우가 있다. 
여기서는 간단히 python code를 executable로 만들어 자동으로 실행시키는 방법을 알아보자. 

## python code를 execuatable로 만들어 /etc/rc.local에 추가하기 

### 1 Python code

python code의 맨 위에 아래 라인을 추가한다. 

```
#!/usr/bin/env python
```

### 2 python file 설정 

python file을 실행 가능하도록 수정하자. 

```
$ chmod +x {python-file.py} 
```

### 3 부팅 시 실행 스크립트에 추가

/etc/rc.local 파일에 아래와 같이 python file 실행하는 내용을 추가한다. 

```
./{pathTo}/{python-file.py}
```

이제 시스템을 재부팅하면 자동으로 python 프로그램이 실행된다. 
