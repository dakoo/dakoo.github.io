---
layout: post
title: sed 명령 - 특정 문자열을 변경하기
modified: 2016-12-30
tags: [tool]
parent: Tool
nav_order: 2
---

로그를 분석하다보면 call stack이 한 줄에 쭉 나와있는 경우가 있다. 이를 보기 편하게 만들기 위해서는 call stack의 API call 사이의 |를  줄바꿈 문자로 변경해야 한다. 
이를 포함해 빠르게 문자열을 바꾸는 방법을 알아보자. 

###  예제

예제로할 파일 aaa.txt의 내용이 다음과 같다고 가정하자. 

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat sample.txt
1234|5678|90123
ABCD|EFG
abcd|efg
```

### sed

sed는 다른 리눅스 명령과 결합하여 문자열을 빠르게 변경할 수 있는 도구이다. 

#### s/a/b/g 옵션

기본 명령은 다음과 같다. 

- sed -e 's/패턴A/패턴B/g' 파일이름

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat sample.txt | sed -e 's/ABCD/XXX/g'
1234|5678|90123
XXX|EFG
abcd|efg
```

#### regex 

패턴은 regex를 이용해서도 가능하다. 

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat sample.txt | sed -e 's/[Aa]..[Dd]/XXX/g'
1234|5678|90123
XXX|EFG
XXX|efg
```

#### 범위 지정

sed의 적용 범위를 줄 번호로 지정할 수 있다. 
1번째 줄과 두번째 줄만 적용하는 경우 다음과 같이 할 수 있다. 

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat sample.txt | sed -e '1,2  s/[Aa]..[Dd]/XXX/g'
1234|5678|90123
XXX|EFG
abcd|efg
```

#### 특수문자 처리

sed에서 '\'와 같은 문자를 처리하기 위해서는 다음과 같이 $를 넣어야 한다. 

- sed -e $'s/패턴A/패턴B/g' 파일이름

참고로 줄바꿈은 '\\\n'이다. 

```bash
Hochului-MacBook-Pro:~ hochulshin$ cat sample.txt | sed -e $'s/|/\\\n/g'
1234
5678
90123
ABCD
EFG
abcd
efg
```
