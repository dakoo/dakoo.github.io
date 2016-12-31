---
layout: post
title: Linux sort 명령 - 크기 역순으로 정렬하기
description: Linux sort 명령 - 크기 역순으로 정렬하기
modified: 2016-12-30
tags: [linux]
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

로그를 분석하다보면 크기나 갯수 순으로 정렬해야 하는 경우가 많다. 이때 사용되는 sort 명령을 알아보자. 

###  예제

예제는 특정 폴더의 목록으로 하자. 

```bash
Hochului-MacBook-Pro:Applications hochulshin$ ls -al
total 16
drwx------   8 hochulshin  staff   272 Dec 30 23:53 .
drwxr-xr-x+ 68 hochulshin  staff  2312 Dec 30 23:53 ..
-rw-r--r--@  1 hochulshin  staff  6148 Aug 11  2015 .DS_Store
-rw-r-----@  1 hochulshin  staff     0 Aug  6  2015 .localized
drwx------@  6 hochulshin  staff   204 Dec 15 22:41 Chrome Apps.localized
drwxr-xr-x   3 hochulshin  staff   102 Dec  6 21:45 SourceTree.app
drwxr-xr-x   3 hochulshin  staff   102 Mar 19  2016 Ubuntu Linux 14.04 Desktop Applications.app
drwxr-xr-x   3 hochulshin  staff   102 Jul 30 19:33 Ubuntu Linux Applications.app
```

### sort

sort는 기본적으로 줄의 맨 앞 글자를 알파벳 순으로 오름 정렬한다. 아래와 같이 특수 문자가 알파벳보다 우선되어 정렬됨을 알 수 있다. 

```bash
Hochului-MacBook-Pro:Applications hochulshin$ ls -al | sort
-rw-r-----@  1 hochulshin  staff     0 Aug  6  2015 .localized
-rw-r--r--@  1 hochulshin  staff  6148 Aug 11  2015 .DS_Store
drwx------   8 hochulshin  staff   272 Dec 30 23:53 .
drwx------@  6 hochulshin  staff   204 Dec 15 22:41 Chrome Apps.localized
drwxr-xr-x   3 hochulshin  staff   102 Dec  6 21:45 SourceTree.app
drwxr-xr-x   3 hochulshin  staff   102 Jul 30 19:33 Ubuntu Linux Applications.app
drwxr-xr-x   3 hochulshin  staff   102 Mar 19  2016 Ubuntu Linux 14.04 Desktop Applications.app
drwxr-xr-x+ 68 hochulshin  staff  2312 Dec 30 23:53 ..
total 16
```

#### -r 옵션

-r은 내림차순 정렬이다. 

```bash
Hochului-MacBook-Pro:Applications hochulshin$ ls -al | sort -r
total 16
drwxr-xr-x+ 68 hochulshin  staff  2312 Dec 30 23:53 ..
drwxr-xr-x   3 hochulshin  staff   102 Mar 19  2016 Ubuntu Linux 14.04 Desktop Applications.app
drwxr-xr-x   3 hochulshin  staff   102 Jul 30 19:33 Ubuntu Linux Applications.app
drwxr-xr-x   3 hochulshin  staff   102 Dec  6 21:45 SourceTree.app
drwx------@  6 hochulshin  staff   204 Dec 15 22:41 Chrome Apps.localized
drwx------   8 hochulshin  staff   272 Dec 30 23:53 .
-rw-r--r--@  1 hochulshin  staff  6148 Aug 11  2015 .DS_Store
-rw-r-----@  1 hochulshin  staff     0 Aug  6  2015 .localized
```

#### -k숫자 옵션 

-k는 줄의 맨 앞 문자가 아닌 공백을 기준으로 n번째 문자를 기준으로 정렬하는 것이다. 

예제에서 9번째가 파일 이름이므로 파일이름으로 오름차순으로 정렬하려면 -k9옵션을 주면 된다. 

```bash
Hochului-MacBook-Pro:Applications hochulshin$ ls -al | sort -k9
total 16
drwx------   8 hochulshin  staff   272 Dec 30 23:53 .
drwxr-xr-x+ 68 hochulshin  staff  2312 Dec 30 23:53 ..
-rw-r--r--@  1 hochulshin  staff  6148 Aug 11  2015 .DS_Store
-rw-r-----@  1 hochulshin  staff     0 Aug  6  2015 .localized
drwx------@  6 hochulshin  staff   204 Dec 15 22:41 Chrome Apps.localized
drwxr-xr-x   3 hochulshin  staff   102 Dec  6 21:45 SourceTree.app
drwxr-xr-x   3 hochulshin  staff   102 Mar 19  2016 Ubuntu Linux 14.04 Desktop Applications.app
drwxr-xr-x   3 hochulshin  staff   102 Jul 30 19:33 Ubuntu Linux Applications.app
```


#### -g 옵션 

-g은 알파벳 순이 아닌 숫자 크기 순으로 정렬하는 것이다. -k와 결합하여 적용해서 크기순으로 정렬해보자. 

```bash
Hochului-MacBook-Pro:Applications hochulshin$ ls -al | sort -k5 -g
total 16
-rw-r-----@  1 hochulshin  staff     0 Aug  6  2015 .localized
drwxr-xr-x   3 hochulshin  staff   102 Dec  6 21:45 SourceTree.app
drwxr-xr-x   3 hochulshin  staff   102 Jul 30 19:33 Ubuntu Linux Applications.app
drwxr-xr-x   3 hochulshin  staff   102 Mar 19  2016 Ubuntu Linux 14.04 Desktop Applications.app
drwx------@  6 hochulshin  staff   204 Dec 15 22:41 Chrome Apps.localized
drwx------   8 hochulshin  staff   272 Dec 30 23:53 .
drwxr-xr-x+ 68 hochulshin  staff  2312 Dec 30 23:53 ..
-rw-r--r--@  1 hochulshin  staff  6148 Aug 11  2015 .DS_Store
```

#### -u 옵션

비교하는 값이 동일할 경우 제거하는 옵션이다. 

```bash
Hochului-MacBook-Pro:Applications hochulshin$ ls -al | sort -k5 -g -u
total 16
-rw-r-----@  1 hochulshin  staff     0 Aug  6  2015 .localized
drwxr-xr-x   3 hochulshin  staff   102 Dec  6 21:45 SourceTree.app
drwx------@  6 hochulshin  staff   204 Dec 15 22:41 Chrome Apps.localized
drwx------   8 hochulshin  staff   272 Dec 30 23:53 .
drwxr-xr-x+ 68 hochulshin  staff  2312 Dec 30 23:53 ..
-rw-r--r--@  1 hochulshin  staff  6148 Aug 11  2015 .DS_Store
```

#### 크기 역순으로 정렬하기 

이제 위에서 다룬 모든 옵션을 사용하여 크기 역순으로 중복을 제거하면서 파일을 정렬해보자. 

```bash
Hochului-MacBook-Pro:Applications hochulshin$ ls -al | sort -k5 -g -u -r
-rw-r--r--@  1 hochulshin  staff  6148 Aug 11  2015 .DS_Store
drwxr-xr-x+ 68 hochulshin  staff  2312 Dec 30 23:53 ..
drwx------   8 hochulshin  staff   272 Dec 30 23:53 .
drwx------@  6 hochulshin  staff   204 Dec 15 22:41 Chrome Apps.localized
drwxr-xr-x   3 hochulshin  staff   102 Dec  6 21:45 SourceTree.app
-rw-r-----@  1 hochulshin  staff     0 Aug  6  2015 .localized
total 16
```
