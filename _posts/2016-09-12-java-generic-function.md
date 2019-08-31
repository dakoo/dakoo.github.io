---
layout: post
title: Java - Generic function
modified: 2016-09-12
tags: [java]
---

예제를 통해 Generic function의 사용 방법을 살펴보자. 

### 1. 요구사항 

String array와 integer array등 다양한 타입의 array를 입력으로 받아 element를 하나씩 출력하는 printArray()함수를 구현한다. 


### 2. 구현


```java
public class Printer
{
    <T> void printArray(T [] array) {
        for(T t: array) {
            System.out.println(t);
        }
    }
 
}
```
