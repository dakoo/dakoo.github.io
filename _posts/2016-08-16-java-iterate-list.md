---
layout: post
title: Java - list 아이템 순회하기
description: Java - list 아이템 순회하기
modified: 2016-08-16
tags: [java]
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

Java의 List를 iteration하는 방법들을 알아보자. 

### 개요

Java의 List는 다음과 같은 방법으로 iteration할 수 있다. 

- 전통적인 index와 함께 하는 for loop
- 전통적인 index와 함께 하는 while loop 
- C++과 동일한 syntax의 for each 문 
- list iterator의 hasNext()를 사용한 while loop
- List의 forEach()문

### 전통적인 index와 함께 하는 for loop

```java
        List<String> myItems = new ArrayList<String>();
        myItems.add("Hello");
        myItems.add("World");
        myItems.add("!");

        int len = myItems.size();
        for(int i = 0; i<len ; i++){
            System.out.println(myItems.get(i));
        }
```

### 전통적인 index와 함께 하는 while loop 

```java
        List<String> myItems = new ArrayList<String>();
        myItems.add("Hello");
        myItems.add("World");
        myItems.add("!");

        int len = myItems.size();
        int index = 0;
        while(index < len){
            System.out.println(myItems.get(index++));
        }
```

### C++과 동일한 syntax의 for each 문 

```java
        List<String> myItems = new ArrayList<String>();
        myItems.add("Hello");
        myItems.add("World");
        myItems.add("!");

        for(String item : myItems){
            System.out.println(item);
        }
```

### list iterator의 hasNext()를 사용한 while loop

```java
        List<String> myItems = new ArrayList<String>();
        myItems.add("Hello");
        myItems.add("World");
        myItems.add("!");

        Iterator<String> iterator = myItems.iterator();
        while (iterator.hasNext()){
            System.out.println(iterator.next());
        }
```

### List의 forEach()문

```java
        List<String> myItems = new ArrayList<String>();
        myItems.add("Hello");
        myItems.add("World");
        myItems.add("!");

        myItems.forEach((item) -> {
            System.out.println(item);
        });
```
