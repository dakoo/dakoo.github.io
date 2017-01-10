---
layout: post
title: Linux 유용한 bash 명령들
description: Linux 유용한 bash 명령들
modified: 2017-01-07
tags: [linux, bash]
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

로그를 분석시 유용한 명령 조합을 살펴보자.

####  압축 파일들이 있는 폴더에서 크기 역순으로 정렬해서 가장 큰 파일 열기

```bash
ls -al | sort -k 5 -nr | head -1 | cut -d " " -f 10 | xargs zmore
``` 

- ls -al: 폴더 파일들 목록 출력
- sort -k 5 -nr: 5번째 컬럼(여기서는 파일 크기)을  역순으로 정렬
- head -1 : 첫번째 아이템 만
- cut -d " " -f 10: 10번째 컬럼만 잘라내기 (여기서는 파일 이름)
- xargs zmore: 앞의 파이프 입력을 아큐먼트로 zmore 실행

#### 파일을 크기 역순으로 정렬해서 가장 큰 파일에서 getConnection이라는 단어가 몇번 등장하는지 카운트 하기

```bash
ls -al | sort -k 5 -nr | head -1 | cut -d " " -f 10 | xargs zgrep getConnection | wc -l
``` 

- ls -al: 폴더 파일들 목록 출력
- sort -k 5 -nr: 5번째 컬럼(여기서는 파일 크기)을  역순으로 정렬
- head -1 : 첫번째 아이템 만
- cut -d " " -f 10: 10번째 컬럼만 잘라내기 (여기서는 파일 이름)
- xargs zgrep getConnection: 앞의 파이프 입력을 아큐먼트로 getConnection을 검색하여 일치하면 줄마다 출력
- wc -l: 라인 수 출력

#### 파일의 각 줄의 마지막 단어 삭제하고 다른 파일로 저장하기

```bash
less AAA.txt | awk '{$NF="";sub(/[ \t]+$/,"")}1' > BBB.txt
``` 

- less AAA.txt : AAA.txt 읽기
- awk '{$NF="";sub(/[ \t]+$/,"")}1': 각 줄의 마지막 단어 삭제
- > BBB.txt: BBB.txt로 저장

#### error 종류별로 발생한 숫자 카운트 하기(관련 아이디가 줄 끝에 위치)

한 폴더 안에 여러 gz파일이 있고, 각 gz파일안에 ERROR, WARN, INFO 등으로 마킹된 로그가 연속으로 나타나는데 에러의 경우 'Caused by. 에러 종류: 아이디'의 형태로 출력된다고 가정하자.
에러 종류별로 빈번한 것부터 빈번하지 않은 것까지를 정렬하고자 한다. 이를 위해서는 아이디를 제거하고 중복인 것의 갯수를 카운트해서 횟수 역순으로 정렬하자. 
그리고 에러가 표시된 줄에 'batch'라는 단어가 들어가 있는 것은 무시해야 한다고 가정하자. 

 ```bash
 zgrep "Caused"  *gz | grep -v batch | sed -e $'s/\|/\\\n/g' |  awk '{$NF="";sub(/[ \t]+$/,"")}1' | sort | uniq -c | sort -nr > errors.txt
``` 

- zgrep "Caused"  *gz : 폴더에서 gz로 끝나는 파일들에서 "Caused" 패턴을 찾아서 일치하는 줄을 출력
- grep -v batch : batch라는 단어가 있으면 무시
- sed -e $'s/\|/\\\n/g' : 줄안에 | 표시가 있으면 줄바꿈
- awk '{$NF="";sub(/[ \t]+$/,"")}1': 마지막 단어 제거
- sort : 정렬
- uniq -c : 중복되는 것은 하나만 남기고 제거하며 맨 앞에 숫자 표시 
- sort -nr : 빈도 역순으로 정렬
- > errors.txt : 결과를 파일에 저장
