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

**Java Collection Framework**은 Java가 제공하는 잘 정의된 객체의 그룹을 다루기 위한 framework의 이름이고 java.util 패키지안에 있다.  **Collection**은 Java collection framework을 구성하는 2개의 그룹 중 한 그룹의 최상위 레벨 인터페이스이고 **Collections**는 static 메소드만으로 구성된 유틸리티 클래스이다. 그런데 때로는 Collection과 Collections를 추상 명사로도 사용하므로 혼란스러울 수 있다. 예를 들어 Collections를 유틸리티 클래스가 아닌 객체 집합이라는 의미로도 많은 인터넷 상의 글들에서 사용되곤 한다. 

### 2. Collection Framework 계층 구조 

#### 2.1 인터페이스들

전체 Colleciton Framework은 크게 2개의 그룹으로 구분된다. (그림 출처: 소설같은자바 Third Edition)

<figure>
	<img src="http://cfile25.uf.tistory.com/image/175466144CC11C27552BBC" alt="Java Collection">
</figure>

- java.util.Collection
- java.util.Map

##### 2.1.1 java.util.Collection 인터페이스 그룹

아래의 것들이 java.util.Collection의 구현체가 아닌 인터페이스들이다. 

- java.util.List : List 자료 구조 (ordered, sequential)
- java.util.Set : Set 자료 구조 (unique element)
- java.util.SortedSet : 정렬된 set
- java.util.NavigableSet 
- java.util.Queue : Queue 자료 구조 (한쪽에서 삽입, 반대에서 추출)
- java.util.Deque : Deque 자료 구조 (FIFO와 FILO 모두 지원)

##### 2.1.2 java.util.Map 인터페이스 그룹

 java.util.Map은 key/value pair를 다루는데 사용된다. 다음과 같은 인터페이스들이 java.util.Map 인터페이스를 상속한다.

- java.util.SortedMap: key가 ascending order로 정렬된 map 
- java.util.NavigableMap

#### 2.2 Collection의 클래스들 

Collection Framework에서 인터페이스들을 구현한 주요 클래스들은 다음과 같다. 위 그림 중 leaf node에 해당하는 것들이다. 아래 클래스 말고도 아래 설명할 Legacy 클래스들,  Abstract 클래스들, Concurrent를 위한 클래스 들이 있다. 

- ArrayList: Dynamic Array
- LinkedList: Linked List
- ArrayDeque: Dynamic Array를 이용한 Deque구현 
- TreeSet: Tree 구조로 set을 구현
- HashSet: Hash table 
- LinkedHashSet: 삽입 순서로 iteration될 수 있는 HashSet
- TreeMap: Tree를 사용하여 Map 구현
- HashMap: Hash table을 이용해 Map 구현
- LinkedHashMap: 삽입 순서로 iteration될 수 있는 HashMap

위의 클래스들의 구현 스타일(가로)과 인터페이스(세로)를 정리하면 다음과 같다. 

|Interface|Hash Table|Resizable Array|Balanced Tree|Linked List|Hash Table + Linked List|
|---|---|---|---|---|---|
|Set|HashSet| |TreeSet|	|LinkedHashSet|
|List|	|ArrayList| |LinkedList| | 
|Deque|	|ArrayDeque| |LinkedList| |
|Map|HashMap| |TreeMap|	|LinkedHashMap|

##### Legacy 클래스들

Java Collection Framework의 Legacy 클래스들이 있다. 그것들은 다음과 같다. 

- Vector: ArrayList과 유사. synchronized
- Stack: Vector를 이용한 LIFO 구현
- Dictionary: key/value 저장(Map과 유사)하는 abstract class
- Hashtable: Dictionary의 구현
- Properties: Hashtable의 subclass로 key/value 모두 String인 경우에 리스트를 관리
- BitSet: Bit 값을 저장하는 특별한 유형의 array. 필요시 사이즈 증가 가능

#### 2.3 Collection 알고리즘 

java.util.Collection의 알고리즘을 담당하는 것이 [**Collections 클래스**](http://hochulshin.com/java-collections-api/)이다. 자세한 메소드는 [여기](http://www.tutorialspoint.com/java/java_collection_algorithms.htm)를 참고하자. 

#### 2.4 Concurrent Collection들

##### 2.4.1 인터페이스

Concurrent programming을 지원하기 위한 Concurrent Collection 인터페이스들은 별도로 정의되어 있다. 

- BlockingQueue
- TransferQueue
- BlockingDeque
- ConcurrentMap
- ConcurrentNavigableMap

##### 2.4.1 클래스

- LinkedBlockingQueue
- ArrayBlockingQueue
- PriorityBlockingQueue
- DelayQueue
- SynchronousQueue
- LinkedBlockingDeque
- LinkedTransferQueue
- CopyOnWriteArrayList
- CopyOnWriteArraySet
- ConcurrentSkipListSet
- ConcurrentHashMap
- ConcurrentSkipListMap

### 3. 자료 구조 관점 

계층 구조가 아닌 자료 구조 관점에서 설명을 해보자. 

#### 3.1 순서가 있는 자료 구조

순서가 있는 자료구조는 element의 중복을 허용한다.  

##### 3.1.1 ArrayList

ArrayList의 특성은 다음과 같다. 

- 동기화를 보장하지 않는다. 
- resizable한 자료구조이다. 
- 


#### 3.2 순서가 없는 자료 구조 

순서가 없는 자료구조는 element의 중복을 허용하지 않는 특성이 있다.

#### 3.3 Key/Value 쌍으로 저장하는 자료 구조

Key를 이용해서 Value를 찾는 검색 기능을 제공하는 자료구조이다. 

#### 3.4 정렬된 자료 구조

기존 데이터 구조에 정렬 속성을 추가한 자료 구조이다. 

### Reference

- [Java 8 API](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)
- [Java Collection Tutorial](http://www.tutorialspoint.com/java/java_collections.htm)
- [Collections Framework Overview](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/overview.html)
- [Collection Framework – Class Hierarchy](http://javaconceptoftheday.com/collection-framework-class-hierarchy/)
- [Collection Interface](http://javaconceptoftheday.com/collection-framework-collection-interface/)
- [Difference Between Collection And Collections In Java](http://javaconceptoftheday.com/difference-between-collection-and-collections-in-java/)

