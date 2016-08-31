---
layout: post
title: Java - Collection Framework
description: Java - Collection Framework
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

### 1. 용어

**Java Collection Framework**은 Java가 제공하는 잘 정의된 객체의 그룹을 다루기 위한 framework의 이름이고 java.util 패키지안에 있다.  **Collection**은 Java collection framework의 최상위 레벨 인터페이스이고 **Collections**는 static 메소드만으로 구성된 유틸리티 클래스이다. 그런데 때로는 Collection과 Collections를 추상 명사로도 사용하므로 혼란스러울 수 있다. 예를 들어 Collections를 유틸리티 클래스가 아닌 객체 집합이라는 의미로도 많은 인터넷 상의 글들에서 사용되곤 한다. 

### 2. Collection Framework 계층 구조 

Java Collection Framework의 클래스 계층 구조는 다음과 같다. 

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/11/CollectionHierarchy.png" alt="Java Collections 계층 구조">
</figure>

전체 Colleciton Framework은 4개의 인터페이스로 구분된다. 

- **List 인터페이스**: 객체들의 순차적인 리스트. 구현은 **ArrayList**, **Vector**, **LinkedList** 클래스
- **Queue 인터페이스**: head에서만 제거되는 특별한 구조의 객체들의 리스트. 구현은 **LinkedList**와 **PriorityQueue** 클래스
- **Set 인터페이스**: 중복되지 않는 유니크한 아이템들만 다루는 리스트. **HashSet**, **LinkedHashSet** 클래스로 구현. **SortedSet 인터페이스**에 의해 상속되고, 이는 **TreeMap** 클래스로 구현
- **Map 인터페이스**: Collection에 의해 상속되지 않는 인터페이스. Key/Value 쌍으로서 객체를 다룸. 구현은 **HashMap**과 **HashTable**. **SortedMap 인터페이스**에 의해 상속되고 이는 **TreeMap**에 의해 구현됨


#### 3. Collection 인터페이스

Collection Framework의 root level 인터페이스. 

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/11/CollectionInterface.png" alt="Java Collection 인터페이스">
</figure>

주요 메소드는 다음과 같다. 

- int size()
- boolean isEmpty()
- boolean contains(Object o)
- Interator<E> iterator(): iterator를 획득
- Object[] toArray(): collection의 모든 element들이 포함된 array 반환
- boolean add(E e)
- boolean remove(Object o)
- void clear(): 모든 element를 제거

### Reference

- [Collection Framework – Class Hierarchy](http://javaconceptoftheday.com/collection-framework-class-hierarchy/)
- [Collection Interface](http://javaconceptoftheday.com/collection-framework-collection-interface/)
- [Difference Between Collection And Collections In Java](http://javaconceptoftheday.com/difference-between-collection-and-collections-in-java/)

