---
layout: post
title: Java - Priority Queue
description: OSX - Priority Queue
modified: 2016-09-13
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

Java의 Priority Queue의 method에 대해 살펴보자. 주의할 것은 Java priority queue는 null element를 허용하지 않는다는 것이다. 

### 1. 생성자

#### 1.1 PriorityQueue()

default capacity가 11이며 natural ordering을 하는 priority queue를 생성한다. 

#### 1.2 	PriorityQueue(int initialCapacity)

주어진 capacity로 natural ordering을 하는 priority queue를 생성한다. 

#### 1.3 	PriorityQueue(Collection<? extends E> c)

Collection으로부터 Priority queue를 생성한다. 

#### 1.4 PriorityQueue(int initialCapacity, Comparator<? super E> comparator)

주어진 capacity로 comparator가 설정한 ordering을 하는 priority queue를 생성한다. 

#### 1.5 	PriorityQueue(PriorityQueue<? extends E> c)

Priority queue로부터 Priority queue를 생성한다. 

#### 1.6 	PriorityQueue(SortedSet<? extends E> c)

Sorted Set으로부터 Priority queue를 생성한다. 

### 2. 주요 메소드 

#### 2.1 	boolean add(E e) = boolean offer(E e)

element를 추가한다. 

#### 2.2 	E poll()

head에서 element를 제거하며 읽는다. 만약 queue가 empty이면 null을 반환한다. 

#### 2.3 	E peek()

head에서 element를 제거하지 않고 읽는다. 만약 queue가 empty이면 null을 반환한다. 

#### 2.5 	boolean contains(Object o)

특정 object가 저장되어 있는지 확인

#### 2.6 Iterator<E> iterator()

queue의 element를 iteration하기 위한 iterator획득

#### 2.7 int size()

element의 수 

#### 2.8 toArray()

Array 변환

### 3. 예제 

```java
import java.util.PriorityQueue;
import java.util.Comparator;
public class Solution {

	public static void main(String[] args) {
		PriorityQueue<Integer> priorityQueue = new PriorityQueue<>(new Comparator<Integer>() {
			public int compare(Integer w1, Integer w2) {
				return w2.compareTo(w1); //descendant order
			}
		});
		priorityQueue.add(12);
		priorityQueue.add(1);
		priorityQueue.add(5);
		priorityQueue.add(22);
		priorityQueue.add(3);
		priorityQueue.add(30);

		while(priorityQueue.size() > 0) {
			System.out.println(priorityQueue.poll());
		}
	}
}
```

결과는 다음과 같다. 

```bash
30
22
12
5
3
1
```
