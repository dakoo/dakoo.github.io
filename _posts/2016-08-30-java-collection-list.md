---
layout: post
title: Java - List (ArrayList, LinkedList, Vector)
description: Java - List (ArrayList, LinkedList, Vector)
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

#### 1.1 Collection Framework의 클래스 계층 구조 

Java Collection Framework의 클래스 계층 구조는 다음과 같다. 

전체 Colleciton Framework은 4개의 interface로 구분된다. 

- List: 객체들의 순차적인 리스트. 구현은 ArrayList, Vector, LinkedList 클래스
- Queue: head에서만 제거되는 특별한 구조의 객체들의 리스트. 구현은 LinkedList와 PriorityQueue 클래스
- Set: 중복되지 않는 유니크한 아이템들만 다루는 리스트. HashSet, LinkedHashSet 클래스로 구현. SortedSet인터페이스에 의해 상속되고, 이는 TreeMap 클래스로 구현
- Map: Collection에 의해 상속되지 않는 인터페이스. Key/Value 쌍으로서 객체를 다룸. 구현은 HashMap과 HashTable. SortedMap 인터페이스에 의해 상속되고 이는 TreeMap에 의해 구현됨

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/11/CollectionHierarchy.png" alt="Java Collections 계층 구조">
</figure>


#### 1.2 Collection Framework을 사용하기 위한 인터페이스

##### 1.2.1 Collection 인터페이스

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

##### 1.2.2 List 인터페이스 

List 인터페이스를 구현한 클래스들 - ArrayList, Vector, LinkedList -을 다룰 때 List 인터페이스의 메소드들이 자주 사용된다. 

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/11/CollectionInterface.png" alt="Java Collection 인터페이스">
</figure>

List의 속성은 다음과 같다. 

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

### 2. 비교

List 인터페이스를 구현한 Array, LinkedList, Vector 클래스에 대해서 알아보자. 
 
#### 2.1 ArrayList 클래스

ArrayList는 **크기가 가변 가능한 Array**로서 구현된 클래스이다. 크기가 가변되는 점을 제외하고는 일반 Array와 동일하게 사용되어 질 수 있고 성능도 마찬가지이다. random access가 가능하므로 get()/set()의 성능이 좋은 반면 add()/remove()의 성능은 나쁘다. 

#### 2.2 LinkedList 클래스

LinkedList는 **doubly linked list**로서 구현된 클래스이다. List이므로 get()/set()에서의 성능이 좋지 못하다. 

#### 2.3 Vector 클래스

Vector는 ArrayList와 동일하다. 차이라면 Vector는 **synchronized**라는 것이다. 이로인해 ArrayList에 비해 성능적으로 불리하다. 많은 java 프로그래머들은 Vector대신 ArrayList를 사용하며, synchronized가 필요한 경우조차도 명시적으로 ArrayList를 synchronized하는 방법을 선호한다. 

### 3. ArrayList

Array와 비교하여 ArrayList의 장점이 무엇인지 살펴보자. 

- 동적으로 크기가 변경된다.

```java
        ArrayList<String> list = new ArrayList<String>();
        list.add("ONE");
        list.add("TWO");
        System.out.println(list.size());     //2
	list.remove("TWO");
	System.out.println(list.size());     //1
```

- 특정 위치에 element를 추가/삭제할 수 있다. Array는 이 경우 시프트를 해야 한다. 

```java
        ArrayList<String> list = new ArrayList<String>();
        list.add("ONE");
        list.add("TWO");
        System.out.println(list);     //[One, TWO]
	list.add(1, "1.5");
	System.out.println(list);     //[One, 1.5, TWO]
	list.remove(1);		
	System.out.println(list);     //[One, TWO]
```

- 다양한 메소드들을 지원한다. 
- generic이 사용되지 않는다면, 다양한 타입의 객체를 저장할 수 있다. 

```java
        ArrayList list = new ArrayList();
        list.add("ONE");
        list.add(2);
        list.add(new Float(3.0));
        System.out.println(list);     //[One, 2, 3.0]
```

- 크기 가변이 발생하는 삽입/삭제를 많이 할 경우 Array에 비해 성능이 나쁠 것이라고 생각하지만, 사실 그렇지 않다. 
- 하나의 ListIterator를 사용하여 forward/backward 양방향 순회가 가능하다. 

```java
        ArrayList<String> list = new ArrayList<String>();        
        list.add("ONE");
        list.add("TWO");
        list.add("THREE");
        ListIterator iterator = list.listIterator();
        while (iterator.hasNext()) {
            System.out.println(iterator.next()); //forward
        }         
        while (iterator.hasPrevious()) {
            System.out.println(iterator.previous()); //backward
        }
```

- 여러 개의 **null** element를 저장할 수 있다. (Array도 가능한 부분)

```java
        ArrayList<Integer> list = new ArrayList<Integer>();      
        list.add(100);
        list.add(null); 
        list.add(null);
        System.out.println(list);     //[100, null, null]
```

- 중복된 element를 저장할 수 있다. (Array도 가능한 부분)

```java
        ArrayList<Integer> list = new ArrayList<Integer>();      
        list.add(100);
        list.add(100); 
        list.add(100);
        System.out.println(list);     //[100, 100, 100]
```

### 4. LinkedList

동일한 List 인터페이스를 구현한 것이므로 사용법은 ArrayList와 동일하다. 대신 Queue 인터페이스를 역시 구현했으므로 그 인터페이스의 method들을 사용할 수 있다. 크게 두 가지 자료구조의 메소드를 지원한다. 하나는 Queue, 하나는 Stack이다. 

#### 4.1 Queue 

Queue는 알다시피 FIFO이다. offer()는 맨 뒤(가장 큰 index)에 저장, poll()은 맨 앞(0번 index)에서 element를 가져오면서 제거, peek()는 맨 앞(0번) element를 읽어만 온다. 

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

#### 4.2 Stack


##### 4.2.1 push, pop, peek
Stack은 LIFO이다. push()는 stack의 맨 위에 삽입. pop()은 stack의 맨 위에서 pop(), peek()는 삭제없이 맨 위의 element를 읽는 것이다. 그런데 실제 맨 위라고 하는 것은 List의 맨 앞(0번 index)를 가르킨다. 

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

##### 4.2.2 addLast, removeLast, getLast

반대로 맨 뒤의 element를 기준으로 push, pop을 하려면 addLast(), removeLast(), getLast()를 이용하면 된다. 

```java
        LinkedList<Integer> list = new LinkedList<Integer>();
        list.addLast(100);
        list.addLast(200);
        list.addLast(300);
        System.out.println(list);     //[100, 200, 300]
        System.out.println(list.removeLast());     //300
        System.out.println(list);     //[100, 200]
        System.out.println(list.getLast());     //200
        System.out.println(list);     //[100, 200]
        System.out.println(list.removeLast());     //200
        System.out.println(list);     //[100]
```

### Reference

- [Collection Framework – Class Hierarchy](http://javaconceptoftheday.com/collection-framework-class-hierarchy/)
- [Collection Framework – Collection Interface](http://javaconceptoftheday.com/collection-framework-collection-interface/)
- [Collection Framework – List Interface](http://javaconceptoftheday.com/collection-framework-list-interface/)
- [Advantages Of Using ArrayList Over Arrays](http://javaconceptoftheday.com/advantages-of-using-arraylist-over-arrays/)



