---
layout: post
title: Regular expression - 간단 이해
description: Regular expression - 간단 이해
modified: 2016-08-16
tags: [re]
comments: true
image:
  feature: abstract-11.png
---

Regular expression에 대해 간단히 살펴보자. 

### 개요

|표현|의미|   
|---|---|
|abc...|abc가 들어있어야 함|
|123...|123이 들어있어야 함|
|\d|하나의 숫자|
|\D|숫자가 아닌 어떤 character라도 상관없이 하나|
|\w|알파벳 하나|
|\W|알파벳이 아닌 것 중 하나|
|\s|스페이스 하나|
|\S|스페이스를 제외한 것 하나|
|.|어떤 character라도 상관없이 하나|
|?|해당 문자가 있을 수도 있고 없을 수도 있는 경우|
|*|0번 이상의 반복|
|+|1번 이상의 반복|
|{m}|m번 반복|
|{m, n}|m번에서 n번 사이의 반복|
|[abc]|a,b,c 중 하나|
|[^abc]|a,b,c를 제외한 것들 중 하나|
|[a-z]|a에서 z까지의 character 중 하나|
|[^a-c]|a에서 c까지 character를 제외한 것 중 하나|
|[0-9]|0부터 9까지의 숫자 중 하나|
|\\.|.|
|\\b|단어의 경계를 표현, 예를 들어 on1 on on2가 있을 때 가운데 on만 뽑아내기 위해 '/\\bon\\b/'를 사용
|\\숫자|backreference. 찾아진 문자열이 여러 개일때 \\1, \\2, \\3 등으로 referencing|
|^abc|abc로 시작하는 문자열|
|abc$|abc로 끝나는 문자열|
|(...)|하나의 문자가 아닌 Group을 지정|
|(a(bc)) 또는 (ab)c(de)|문자열을 하나가 아니라 동시에 여러 개 추출|
|(abc\|def)|abc 또는 def|
|(.*)|아무것도 없는 것을 포함해 모두 선택|

### 설명

#### 반복

- 2개의 b가 반복 - ab{2}: abb
- 최소 2개 이상의 b가 반복 - ab{2,}: abb, abbb ...
- 2개에서 3개의 b가 반복 - ab{2,3}: abb, abbb
- 0회이상 반복 - ab*: a, ab, abb, ... abbbb ... = ab{0,}
- 1회이상 반복 - ab+: ab, abb, abbb ... = ab{1,}
- 1개 있거나 없음 - ab?: a, ab = ab{0,1}
- 문자열이 반드시 b와 함께 끝나야 함 - ab+$: ab, abb, ... abbbb ... = ab{1,}$

#### Group 지정

- 0회이상 반복 - (ab)*: {}, ab, abab, ababab ...
- 1회이상 반복 - (ab)+: ab, abab, ababab ...
- A 또는 B - (ab\|cd)e: abe, cde
- a 또는 b의 0회 이상 반복 - (a\|b)*c : c, ac, bc, aac, abc, bbc, bac, aaac....

#### 문자

- 어떤 character라도 상관없이 하나의 문자 - .: a, b, c, d, .... 1, 2, 3, 4... *, !, # ...
- 4개의 character로 구성된 문자열 - ^.{4}$:  abcd, aaaa, bcaz ...
- a 또는 b - [ab] = a\|b 
- 영문자 또는 숫자 - [a-zA-Z0-9]
- %앞에 숫자 - [0-9]%
- 소수점 뒤 3자리 숫자로 끝나는 문자 - [0-9]+\.[0-9]{3}$
- 숫자로 시작하는 문자열 - ^\d.*

### 예제

아래 예제는 [RegexOne](http://regexone.com/)에서 가져온 것이다. [RegexOne](http://regexone.com/)에 방문하면 실제 예제를 동작시켜 볼 수 있다. 

#### 예제 1

```
Match	wazzzzzup	
Match	wazzzup	
Skip	wazup
```

- 'waz{3,}up'

#### 예제 2

```
Match	aaaabcc	
Match	aabbbbc	
Match	aacc	
Skip	a
```

- 'aa+b*c+'

#### 예제 3

```
Match	1 file found?	
Match	2 files found?	
Match	24 files found?	
Skip	No files found.
```

- '\d+ files? found\?'

#### 예제 4

```
Match	1.   abc
Match	2.	abc
Match	3.           abc
Skip	4.abc
```

- '\d\.\s+abc'

#### 예제 5

```
Match	Mission: successful
Skip	Last Mission: unsuccessful
Skip	Next Mission: successful upon capture of target
```

- '^Mission: successful$' (즉, 목표로하는 문자열로만 구성된 문자열을 추출)

#### 예제 6

```
Capture	file_record_transcript.pdf	--> file_record_transcript	
Capture	file_07241999.pdf	--> file_07241999	
Skip	testfile_fake.pdf.tmp
```

- '^(file_.+)\.pdf$' 

#### 예제 7

'월 년도' 문자열과 '년도' 문자열을 동시에 추출

```
Capture	Jan 1987	--> Jan 1987,  1987	
Capture	May 1969	--> May 1969,  1969	
Capture	Aug 2011	--> Aug 2011,  2011	
```

- '(\w+ (\d+))'

##### 예제 8

가로와 세로를 동시에 추출

```
Capture	1280x720	1280 720	To be completed
Capture	1920x1600	1920 1600	To be completed
Capture	1024x768	1024 768
```

- '(\d+)x(\d+)'


#### 예제 9

```
Match	I love cats	
Match	I love dogs	
Skip	I love logs	
Skip	I love cogs	
```

- 'I love (cats\|dogs)'

#### 예제 10

IP address가 valid인지 검사하기 : valid IP 주소의 조건은 다음과 같다. 

- A.B.C.D form의 string
- A, B, C, D의 값은 0부터 255까지
- A, B, C, D 값은 0으로 시작해되 된다. 
- A, B, C, D의 길이는 3보다 클 수 없다. 

```
Match 000.12.12.034
Match 121.234.12.12
Match 23.45.12.56
Skip 000.12.234.23.23
Skip 666.666.23.23
Skip .213.123.23.32
Skip 23.45.22.32.
Skip I.Am.not.an.ip
```

- "((\\d{1,2}\|(0\|1)\\d{2}\|2[0-4]\\d\|25[0-5])\\.){3}(\\d{1,2}\|(0\|1)\\d{2}\|2[0-4]\\d\|25[0-5])";

