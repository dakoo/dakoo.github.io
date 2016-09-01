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

계층 구조가 아닌 자료 구조 관점에서 설명을 해보자. Collection Framework이 제공하는 자료구조는 크게 다음과 같은 4가지로 구분할 수 있다. 

- 순서가 있는 자료구조(List)
- 순서가 없고 중복이 안되는 집합 자료구조(Set)
- 검색이 용이하도록 key/value 쌍으로 저장하는 자료구조(Map)
- 정렬이 되어서 저장하는 특수한 자료구조(Sorted)

각 자료구조에 대해 살펴보자. 

#### 3.1 순서가 있는 자료 구조 (List)

순서가 있는 자료구조는 element의 중복을 허용하며 주로 선형 구조를 가진다. Collection Framework은 크게 다음 4가지의 Class를 가지고 있다. 

- ArrayList: resizable array, synchronized 보장하지 않음
- Vector: resizable array, synchronized 보장
- LinkedList: linked list
- Stack: stack 

##### 3.1.1 ArrayList

ArrayList의 특성은 다음과 같다. 

- 동기화를 보장하지 않으며 동기화가 필요할 때는 `Collections.synchronizeList()` 메소드를 통해 동기화가 보장되는 List를 반환받아 사용한다. 
- resizable하다. 

주요 메소드는 다음과 같으며 자세한 API 사용법은 [여기](https://docs.oracle.com/javase/8/docs/api/?java/util/ArrayList.html)를 참고하자.  

- boolean add(E e)
- void add(int index, E e)
- boolean remove(Object o)
- E remove(int index)
- E get(int index)
- E set(int index, E e)
- Object[] toArray()

##### 3.1.2 Vector

Vector의 특성은 다음과 같다. 

- **동기화를 보장**하는 resizable Array이다.  

ArrayList와 지원 메소드와 속성이 거의 동일하다. 동기화를 보장(Thread-safe)할 필요가 없는 경우 ArrayList를 추천하며, 심지어 동기화가 필요한 경우 조차도 ArrayList를 동기화 시켜 사용되기 때문에 이제 Vector는 자주 사용되지 않는 자료구조이다. 

주요 메소드는 다음과 같으며 자세한 API 사용법은 [여기](http://docs.oracle.com/javase/8/docs/api/?java/util/Vector.html)를 참고하자.  

- boolean add(E e)
- void add(int index, E e)
- boolean remove(Object o)
- E remove(int index)
- E get(int index)
- E set(int index, E e)
- Object[] toArray()

ArrayList의 메소드와 동일함을 알 수 있다. 

##### 3.1.3 LinkedList

LinkedList의 특성은 다음과 같다. 

- 동기화를 보장하지 않으며 동기화가 필요할 때는 `Collections.synchronizeList()` 메소드를 통해 동기화가 보장되는 List를 반환받아 사용한다. 
- ArrayList와 유사하지만, 선입 선출인 **Queue**와 양쪽 끝에서의 처리를 하는 **Deque**의 속성과 메소드를 가지고 있다. 

주요 메소드는 다음과 같으며 자세한 API 사용법은 [여기](http://docs.oracle.com/javase/8/docs/api/?java/util/LinkedList.html)를 참고하자.  

- boolean add(E e)
- void add(int index, E e)
- E get(int index)
- boolean remove(Object o)
- E remove(int index)
- E set(int index, E e)
- Object[] toArray()

위의 메소드는 ArrayList와 동일하며, 다음은 LinkedList만의 메소드이다. Queue 자료구조의 메소드는 기본적으로 **tail에 넣고 front에서 뺀다**. 그리고, offer()가 삽입하는 것, poll()이 빼는 것, peek()이 읽는 것이다. Deque 자료구조를 지원하기 위해 addFirst/Last, getFirst/Last, removeFirst/Last를 지원한다. LinkedList를 이용해서 deque로 사용할지, queue로 사용할지에 따라 적당한 메소드를 사용하면 된다.  

- void addFirst(E e): front에 삽입
- void addLast(E e): tail에 삽입
- E getFirst(): front에서 읽기
- E getLast(): tail에서 읽기
- E removeFirst(): front에서 삭제
- E removeLast(): tail에서 삭제
- boolean offerFirst(E e): == addFirst()
- boolean offerLast(E e): == addLast()
- E pollFirst(): = removeFirst()
- E pollLast(): = removeLast()
- E peekFirst(): = getFirst()
- E peekLast():  = getLast()
- boolean offer(E e): = offerLast()
- E poll(): = pollFirst()
- E peek(): = peekFirst()

##### 3.1.3 Stack

Deque 인터페이스의 속성을 물려받아 메소드만 LIFO에 맞게 **정의**한 것이다. Stack의 특성은 다음과 같다. 

- LIFO(후입 선출)을 지원

주요 메소드는 다음과 같으며 자세한 API 사용법은 [여기](http://docs.oracle.com/javase/8/docs/api/?java/util/Stack.html)를 참고하자.  

- boolean empty()
- E push(E item): 삽입. E를 그대로 반환
- E pop(): 제거
- E peek(): 읽기
- int search(Object o): 반환하는 값은 top부터의 거리를 의미. 만약 1을 반환하면 top. -1을 반환하면 없다는 것

#### 3.2 순서가 없는 집합 자료 구조 (Set)

순서가 없는 자료구조는 element의 중복을 허용하지 않는 특성이 있다. 순서의 의미가 없으므로 index를 통해 접근하지 않는다. Collection Framework은 크게 다음의 Set Class를 가지고 있다. 

- HashSet: Java Collection의 대표 Set 자료구조
- Set 인터페이스를 상속한 SortedSet 인터페이스를 구현한 TreeSet 

TreeSet은 정렬을 하는 특수한 자료구조이므로 이후 별도로 다룬다. 

##### 3.2.1 HashSet

HashSet은 Set자료구조이므로 가장 중요한 것은 자료구조안에 아이템이 있는 지 확인하는 것이다. 이는 contains() 메소드를 이용한다. 

주요 메소드는 다음과 같으며 자세한 API 사용법은 [여기](http://docs.oracle.com/javase/8/docs/api/?java/util/HashSet.html)를 참고하자.  

- boolean empty()
- boolean add(E e)
- boolean contains(Object o): 존재하면 true
- boolean remove(Object o)

HashSet을 순회하는 방법은 다음과 같다. 

- iterator
- foreach
- Stream

예는 다음과 같다. 

```java
     HashSet<String> set = new HashSet<String>();
     set.add("1");
     set.add("2");
     set.add("3");

     Iterator<String> it = set.iterator();
     while(it.hasNext()){
        System.out.println(it.next());//1 2 3
     }
     for(String s: set){
        System.out.println(s); //1 2 3     
     }
     set.stream().forEach(System.out::println); //1 2 3
```

#### 3.3 Key/Value 쌍으로 저장하는 자료 구조 (Map)

Key를 이용해서 Value를 찾는 검색 기능을 제공하는 자료구조이다. Collection Framework은 크게 다음의 Map Class를 가지고 있다. 

- HashMap
- Hashtable
- Map 인터페이스를 상속한 SortedMap 인터페이스를 구현한 TreeMap 

TreeMap은 정렬을 하는 특수한 자료구조이므로 이후 별도로 다룬다. 

#### 3.4 정렬된 자료 구조 (Sorted)

기존 데이터 구조에 정렬 속성을 추가한 자료 구조이다. 

### Reference

- [Java 8 API](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)
- [Java Collection Tutorial](http://www.tutorialspoint.com/java/java_collections.htm)
- [Collections Framework Overview](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/overview.html)
- [Collection Framework – Class Hierarchy](http://javaconceptoftheday.com/collection-framework-class-hierarchy/)
- [Collection Interface](http://javaconceptoftheday.com/collection-framework-collection-interface/)
- [Difference Between Collection And Collections In Java](http://javaconceptoftheday.com/difference-between-collection-and-collections-in-java/)

