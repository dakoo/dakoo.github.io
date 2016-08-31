---
layout: post
title: Java - List 인터페이스
description: Java - List 인터페이스 
modified: 2016-8-30
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

**List 인터페이스**는 순차적인 객체 집합들을 다루기 위한 Java Collection Framework의 인터페이스이다. Java Collection Framework의 최상위 인터페이스인 **Collection 인터페이스**를 상속한다. 

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/11/ListInterface.png" alt="Java List 인터페이스">
</figure>

List 인터페이스에서 다루는 객체 집합의 속성은 다음과 같다. 

- 0부터 시작하는 index에 의해 순서가 매겨진다. 
- index를 통해 element를 접근할 수 있다. 
- index를 통해 특정 위치에 element를 삽입하거나 제거할 수 있다. 이 경우 이후 element들의 index가 시프트된다. 
- 중복 element가 존재할 수 있다. 
- null element가 존재할 수 있다. 

List 인터페이스에서 추가된 주요 메소드는 다음과 같다. 

- E get(int index)
- E set(int index, E e): 특정 index의 element를 대체
- void add(int index, E e)
- E remove(int index): 특정 index의 element를 제거하며 반환
- int indexOf(Object o): o와 일치하는 첫번째 element의 index를 반환
- int lastIndexOf(Object o): o와 일치하는 마지막 element의 index반환
- ListIterator<E> listIterator(): List interator 획득

### 2. 구현 클래스 비교

List 인터페이스를 구현한 Array, LinkedList 클래스에 대해서 알아보자. 
 
#### 2.1 ArrayList 클래스

ArrayList는 **크기가 가변 가능한 Array**로서 구현된 클래스이다. 크기가 가변되는 점을 제외하고는 일반 Array와 동일하게 사용되어 질 수 있고 성능도 마찬가지이다. random access가 가능하므로 get()/set()의 성능이 좋은 반면 add()/remove()의 성능은 나쁘다. 

#### 2.2 LinkedList 클래스

LinkedList는 **doubly linked list**로서 구현된 클래스이다. List이므로 get()/set()에서의 성능이 좋지 못하다. 

#### 2.3 Vector 클래스(Legacy)

java.util.Collection이 아닌 Legacy 클래스인 Vector는 ArrayList와 동일하다. 차이라면 Vector는 **synchronized**라는 것이다. 이로인해 ArrayList에 비해 성능적으로 불리하다. 많은 java 프로그래머들은 Vector대신 ArrayList를 사용하며, synchronized가 필요한 경우조차도 명시적으로 ArrayList를 synchronized하는 방법을 선호한다. 즉, Vector 클래스는 잘 사용되지 않는 클래스이다. 

