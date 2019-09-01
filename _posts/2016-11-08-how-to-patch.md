---
layout: post
title: Tool - patch하기
modified: 2016-11-07
tags: [git]
---

patch를 만들고 적용하는 것을 살피보자. 

### 1. patch 만들기 

```
diff -Naur ./Old ./New > files.patch
```

### 2. 적용

이제 적용을 하려면 일단 먼저 files.patch를 열어본다. 

그러면 어느 디렉토리의 구조가 맨 처음에 보일 것이다. 

```
iff -Naur ./OLD/AAA/bye.c ./NEW/AAA/bye.c
```

./Old 안에서 files.patch를 적용하려면 ./Old밖에서 만든 patch이므로 디렉토리 하나를 생략해야 한다. 
즉, ./OLD가 아닌 AAA부터 시작되어야 현재 디렉토리 구조와 동일할 것이다. 이때 사용하는 것이 -p 플래그이다. 만약 절대 경로를 사용했다면 여러 개의 디렉토리를 생략해야 할 것이다.
p는 슬래쉬의 숫자를 지정하는 것인데 위의 경우 ./OLD/를 제외하면 되므로 -p2으로 하면 된다. 슬래쉬 숫자이므로 반드시 patch파일을 확인해서 현재 디렉토리 구조와 확인해 봐야 한다. 

그래서 ./Old안으로 files.patch를 복사한 후  다음 명령을 실행한다. 

```
patch -p2 < files.patch
```

### 3. 백업

만약 백업이 필요할 경우 -b 플래그를 추가하면 이전 파일이 .orig 확장자로 남게 된다. 

```
patch -p2 -b < files.patch를 실행하면 된다. 
```

### 4. 패치한 것 되돌리기!

patch -p2 -R < files.patch

그런데 백업을 해 놓은 경우 .orig파일들은 그냥 남아있게 된다. 

--> find . -name *.orig -type f -delete 를 해서 깨끗이 지운다. 
