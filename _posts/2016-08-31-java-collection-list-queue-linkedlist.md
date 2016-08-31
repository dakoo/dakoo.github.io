---
layout: post
title: Java - Collection - List/Queue - LinkedList
description: Java - Collection - List/Queue - LinkedList 
modified: 2016-8-31
tags: [java, datastructure]
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


### 1. 개요

동일한 List 인터페이스를 구현한 것이므로 상당부분 ArrayList와 동일하다. Cloneable과 Serializable 인터페이스를 구현했고, 순차적며 synchronized가 아니다. 

반면 Queue 인터페이스/Deque 인터페이스를 구현했으므로 그 인터페이스의 method들을 사용할 수 있다. 

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/12/LinkedListClass.png" alt="Java LinkedList">
</figure>


### 2. Deque 인터페이스의 계승 

Deque는 double ended queue를 의미한다. C++에도 유사한 deque 자료구조가 있다.

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/11/Deque.png" alt="Java Deque">
</figure>

Deque 인터페이스의 메소드는 first와 last로 삽입/삭제하는 것을 모두 지원하기 때문에 조금 복잡하다. 

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/11/HowDequeWorks.png" alt="Java Deque method">
</figure>

LinkedList는 ArrayList와 유사한 것 외에 Deque의 다양한 메소드를 지원하지만, 여기서는 Stack과 Queue 메소드에만 집중해서 설명하고자 한다. 

- 아래의 Queue와 Stack의 메소드를 쓸 경우, List 인터페이스가 제공하는 Random Access는 사용하지 않는 것이 좋다. 
- Deque의 인터페이스는 Stack과 Queue가 아닌 addLast(), getFirst(), removeFirst() 와 같이 First와 Last의 element를 추가/삭제/읽기 등을 하는 메소드를 제공한다. 이 메소드와 함께라면 index를 이용한 Random Access가 훨씬 용이하다. 

#### 2.1 Queue 

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

#### 2.2 Stack

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

- [Java LinkedList Programming Examples](http://javaconceptoftheday.com/java-linkedlist-programming-examples/)
- [Collection Framework – The Deque Interface](http://javaconceptoftheday.com/collection-framework-deque-interface/)
- [ArrayList Vs LinkedList In Java](http://javaconceptoftheday.com/arraylist-vs-linkedlist-java/)

