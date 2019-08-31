---
layout: post
title: Java - LinkedList 클래스
modified: 2016-8-31
tags: [java]
image:
  feature: abstract-11.png
---

### 1. 개요

java.util.LinkedList는 Serializable, Cloneable, Iterable<E>, Collection<E>, Deque<E>, List<E>, Queue<E>과 같은 많은 인터페이스를 구현하고 있다. 특히 **List와 Deque 인터페이스의 doubly-linked list 구현**이다. 그래서 대부분의 성능은 doubly linked list의 성능을 따른다.

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/12/LinkedListClass.png" alt="Java LinkedList">
</figure>


### 2. Deque 구현

Deque 인터페이스는 first와 last로 삽입/삭제하는 것을 모두 지원하기 때문에 조금 복잡하다. 

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/11/HowDequeWorks.png" alt="Java Deque method">
</figure>

#### 3. LinkedList 클래스만의 메소드 - Queue와 Stack 자료 구조 지원

LinkedList는 이런 Deque 속성과 LinkedList를 모두 구현하며, 또한 스스로 Stack 자료구조와 Queue 자료구조를 지원하는 메소드를 제공하고 있다.  

#### 3.1 Queue 자료 구조를 위한 메소드

Queue는 알다시피 FIFO이다. 다음 Queue 메소드를 지원한다. 

- offer(): Queue에 삽입
- poll(): Queue에서 제거하며 읽기
- peek(): Queue에서 제거하지 않고 읽기

예제는 다음과 같다. 

```java
        LinkedList<Integer> list = new LinkedList<Integer>();
        list.offer(100);
        list.offer(200);
        list.offer(300);
        System.out.println(list);     //[100, 200, 300]
        System.out.println(list.poll());     //100
        System.out.println(list);     //[200, 300]
        System.out.println(list.peek());     //200
        System.out.println(list);     //[200, 300]
```

#### 3.2 Stack 자료 구조를 위한 메소드

Queue는 알다시피 LIFO이다. 다음 Stack 메소드를 지원한다. 

- push(): Stack에 삽입
- pop(): Stack에서 제거하며 읽기
- peek(): Stack에서 제거하지 않고 읽기 

예제는 다음과 같다. 

```java
        LinkedList<Integer> list = new LinkedList<Integer>();
        list.push(100);
        list.push(200);
        list.push(300);
        System.out.println(list);     //[300, 200, 100]
        System.out.println(list.pop());     //300
        System.out.println(list);     //[200, 100]
        System.out.println(list.peek());     //200
        System.out.println(list);     //[200, 100]
```

#### 3. Reference

- [Java 8 API 문서](https://docs.oracle.com/javase/8/docs/api/java/util/LinkedList.html)
- [Java LinkedList Programming Examples](http://javaconceptoftheday.com/java-linkedlist-programming-examples/)
- [Collection Framework – The Deque Interface](http://javaconceptoftheday.com/collection-framework-deque-interface/)
- [ArrayList Vs LinkedList In Java](http://javaconceptoftheday.com/arraylist-vs-linkedlist-java/)
