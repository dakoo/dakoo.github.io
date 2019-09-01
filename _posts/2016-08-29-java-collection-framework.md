---
layout: post
title: Java - Collection Framework
description: Java - Collection Framework
modified: 2016-8-30
tags: [java]
comments: true
image:
  feature: abstract-11.png
---

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

> Note: Collection에서는 ArrayList가 메모리 크기의 잇점과 Cache 친화적이라는 점으로 인해 ArrayList가 LinkedList에 비해 훨씬 많이 쓰인다. 굳이 LinkedList를 사용할 필요가 거의 없다


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
- Stream forEach

예는 다음과 같다. 

```java
     HashSet<String> set = new HashSet<String>();
     set.add("1");
     set.add("2");
     set.add("3");

     /*iterator*/
     Iterator<String> it = set.iterator();
     while(it.hasNext()){
        System.out.println(it.next());
     }
     /*foreach*/
     for(String s: set){
        System.out.println(s);     
     }
     /*Stream*/
     set.forEach(System.out::println); 
```

출력은 다음과 같다. 입력과 상관없이 정렬되어 출력된다는 것을 알 수 있다. 하지만, 이 정렬을 믿고 코딩을 하면 안된다. 정렬이 필요하면 TreeSet을 사용하는 것이 좋다. 

```
1
2
3
1
2
3
1
2
3
```

#### 3.3 Key/Value 쌍으로 저장하는 자료 구조 (Map)

Key를 이용해서 Value를 찾는 검색 기능을 제공하는 자료구조이다. Java는 크게 다음의 Map Class를 가지고 있다. 

- HashMap: 동기화를 보장하지 않는 Java Collection Framework의 대표 Map
- Hashtable: 동기화를 보장 (Collection Framework이 아닌 Legacy Directory abstract class를 구현)
- Map 인터페이스를 상속한 SortedMap 인터페이스를 구현한 TreeMap 

TreeMap은 정렬을 하는 특수한 자료구조이므로 이후 별도로 다룬다. Hashtable은 동기화를 지원하기 때문에 상대적으로 HashMap에 비해 느리다. 그리고 HashMap을 필요한 경우에 동기화를 걸 수 있으므로 Hashtable은 자주 사용하지 않게 되었다. 

##### 3.3.1 HashMap

HashMap은 Map자료구조이므로 key/value 쌍으로 데이터를 저장하고, 내부적으로 Hash기법을 구현하고 있다. 동기화가 필요한 경우 다음과 같이 사용할 수 있다. 

- Map<>map = Collections.synchronizedMap(new HashMap<>());

주요 메소드는 다음과 같으며 자세한 API 사용법은 [여기](http://docs.oracle.com/javase/8/docs/api/?java/util/HashMap.html)를 참고하자.  

- HashMap(int) : 초기 capacity 설정하며 생성
- boolean isEmpty()
- boolean containsKey(Object key)
- boolean containsValue(Object value)
- V get(Object key): 만약 data가 없으면 null
- V getOrDefault(Object Key, V defaultValue): 만약 data가 없으면 default value를 반환
- V put(K key, V value): 삽입 - 만약 이전에 존재하던 key가 있으면 덮어쓰고 이전 값을 반환. null을 반환하면 key가 없었던 것.
- V putIfAbsent(K key, V value): 만약 key가 존재하지 않으면 삽입. 만약 존재하지 않았다면 null 반환하고 아니면 이전 value 반환
- V replace(K key, V value): 현재 key의 value를 대체. 만약 이전에 없었다면 null 반환(이전 value가 null일수도 있다)
- boolean replace(K key, V oldValue, V newValue): 만약 대체되면 true 반환
- V remove(Object key)
- boolean remove(Object key, Object value)
- Set<Map.Entry<K,V>> entrySet() : Set 자료구조로 반환
- Set<K> keySet() : key들만 모아서 Set 자료구조로 반환
- Collection<V>	values(): value들만 Collection으로 반환

HashMap의 element를 순회하기 위해서는 Set으로 변환하거나 stream을 이용한다. Set으로 변환시 필요하다면 Key나 Value로만 Set을 구성할 수도 있다. 위의 entrySet(), KeySet(), values()을 참조하자. 

- Map을 Set으로 변환 후 iterator로 순회 
- Map을 Set으로 변화하며 forEach로 순회
- stream forEach

예는 다음과 같다. 

```java
        Map<String, String> map = new HashMap<String, String>();
        map.put("k1", "haha");
        map.put("k3", "kaka");
        map.put("k2", "nana");

        //Map --> Set and iterator
        Iterator<Map.Entry<String, String>> it = map.entrySet().iterator();
        while(it.hasNext()){
            Map.Entry<String, String> elem = it.next();
            System.out.println(elem.getKey() + "," + elem.getValue());
        }
        //Map --> Set and foreach
        for(Map.Entry<String, String> elem: map.entrySet()){
            System.out.println(elem.getKey() + "," + elem.getValue()); 
        }
        //stream
        map.forEach((k, v)-> System.out.println(k +","+v));
```

출력은 다음과 같다. key를 기준으로 정렬되어 순회가 된다는 것을 알 수 있다. 하지만, 이 정렬을 믿고 코딩을 하면 안된다. 정렬이 필요하면 TreeMap을 사용하는 것이 좋다. 

```bash
k1,haha
k2,nana
k3,kaka
k1,haha
k2,nana
k3,kaka
k1,haha
k2,nana
k3,kaka
```

##### 3.3.2 Hashtable

Hashtable은 HashMap과 거의 동일하다. 그리고 Hashtable은 Collection framework의 Map 인터페이스가 아닌 Directory 추상 클래스를 구현하고 있다. 
동기화를 기본으로 지원한다는 점이 다르다. 하지만, 이로 인해 일반적인 경우 성능 문제가 있어서 잘 사용하지 않는다. 

주요 메소드는 다음과 같으며 자세한 API 사용법은 [여기](http://docs.oracle.com/javase/8/docs/api/?java/util/Hashtable.html)를 참고하자.  

#### 3.4 정렬된 자료 구조 (Sorted)

기존 데이터 구조에 정렬 속성을 추가한 자료 구조이다. 인터페이스로는 Set 인터페이스를 상속받은 SortedSet과 Map 인터페이스를 상속받은 SortedMap 인터페이스가 있다. 각각 TreeSet 클래스와 TreeMap 클래스로 구현되어 있다. 

##### 3.4.1 TreeSet

data들이 자동으로 오름차순으로 정렬된다. 
주요 메소드는 다음과 같으며 자세한 API 사용법은 [여기](http://docs.oracle.com/javase/8/docs/api/?java/util/TreeSet.html)를 참고하자.  

HashSet의 메소드와 동일한 메소드를 제공하며, 특별히 정렬된 순으로 순회가 유리한 메소드를 제공한다. 

- Iterator<E> iterator(): 오름순
- Iterator<E> descendingIterator(): 내림순
- last(): 가장 큰 element 
- first(): 가장 작은 element

사용예는 다음과 같다. 

```java
TreeSet <Integer> set = new TreeSet<Integer>();
set.add(1);
set.add(3);
set.add(2);

Iterator iterator = set.descendingIterator();
while (iterator.hasNext()){
     System.out.println(iterator.next()); //3 2 1
}
System.out.println(set.first()); //1
System.out.println(set.last());  //3
```

###### Custom Comparator

TreeSet에 custom comparator를 추가하는 것은 TreeSet 생성시 constructor에 comparator를 추가하면 된다. 

```java
//Cup.java
public class Cup {
    private final int size;
    public Cup(int size) {
        this.size = size;
    }
    int getSize(){
        return this.size;
    }
}
```

위와 같은 Cup Class의 객체들을 size에 따라 오름차순으로 정렬하고자 할 때 아래와 같이 하면 컴파일이 실패한다. 

```java
Set<Cup> set = new TreeSet<Cup>();
set.add(new Cup(1));
set.add(new Cup(3));
set.add(new Cup(2));
set.forEach((c)->System.out.println(c.getSize()));
```

다음과 같이 constructor에 comparator를 Lambda로 추가하면 `3 2 1`로 출력이 된다. 

```java
Set<Cup> set = new TreeSet<Cup>((c1, c2) -> c2.getSize() - c1.getSize() );
```

###### HashSet -> TreeSet

HashSet을 TreeSet으로 변환하는 것은 다음과 같이 `addAll()`을 사용한다. 

```java
Set<String> set = new HashSet<String>();
...
TreeSet<String> ts = new TreeSet<String>();
ts.addAll(set);
```

##### 3.4.2 TreeMap

data들이 Key를 기준으로 default로 오름차순으로 정렬된다. 동기화를 지원하지 않으면 동기화가 필요한 경우 다음과 같이 한다. 
- SortedMap m = Collections.synchronizedSortedMap(new TreeMap(...));

주요 메소드는 다음과 같으며 자세한 API 사용법은 [여기](http://docs.oracle.com/javase/8/docs/api/?java/util/TreeMap.html)를 참고하자. 

HashMap의 메소드와 동일한 메소드를 제공한다. HashMap에서 살펴봤다시피 Map을 순회 하기 위해서는 Set으로 변환하는데, TreeMap은 특별히 정렬된 순서의 Set을 반환하는 메소드를 제공한다. 

- Map.Entry<K,V> firstEntry(): 정렬된 entry중 가장 처음 것 반환. empty이면 null
- Map.Entry<K,V> lastEntry(): 정렬된 entry중 가장 마지막 것 반환. empty이면 null
- Map.Entry<K,V> pollFirstEntry():정렬된 entry중 가장 처음 entry 제거
- Map.Entry<K,V> pollLastEntry():  정렬된 entry중 가장 마지막 entry 제거
- NavigableSet<K> entrySet(): Set 반환

사용 예는 다음과 같다. 

```java
        TreeMap map = new TreeMap();
        map.put("Zara", new Double(3434.34));
        map.put("Mahnaz", new Double(123.22));
        map.put("Ayan", new Double(1378.00));

        Set set = map.entrySet();
        Iterator i = set.iterator();
        while(i.hasNext()) {
            Map.Entry entry = (Map.Entry)i.next();
            System.out.println(entry.getKey() + "," + entry.getValue());
        }
        System.out.println(map.firstEntry().getKey());
        System.out.println(map.lastEntry().getKey());
```

결과는 다음과 같이 key순으로 순회할 수 있다. 

```bash
Ayan,1378.0
Mahnaz,123.22
Zara,3434.34
Ayan
Zara
```

###### Custom Comparator

TreeMap에 custom comparator를 추가하는 것은 TreeMap 생성시 constructor에 comparator를 추가하면 된다. Cup Class의 객체를 Key로 하고 그 size에 따라 **내림차순**으로 정렬해보자. 
```java
//Cup.java
public class Cup {
    private final int size;
    public Cup(int size) {
        this.size = size;
    }
    int getSize(){
        return this.size;
    }
}
```

constructor에 lambda로 comparator를 입력으로 준다. 

```java
        TreeMap<Cup, Double> map = new TreeMap<Cup, Double>((c1, c2)-> c2.getSize()-c1.getSize());
        map.put(new Cup(3), new Double(0.34));
        map.put(new Cup(1), new Double(123.22));
        map.put(new Cup(2), new Double(178.00));

        map.forEach((k, v)-> System.out.println(k.getSize() + "," +v));
        System.out.println(map.firstEntry().getKey().getSize());
        System.out.println(map.lastEntry().getKey().getSize());
```

결과는 다음과 같다. key를 내림차순으로 정렬했고, 이에 따라 firstEntry와 lastEntry도 역시 결정되었다. 

```
3,0.34
2,178.0
1,123.22
3
1
```

###### key가 아닌 value 순으로 순회하기 

**Map 자료구조는 value순으로 정렬을 지원하지 않는다.** 만약 Value 순 정렬이 반드시 필요하다면 key와 value의 쌍을 저장하고 정렬하는 TreeSet을 먼저 만든 뒤, map의 entry들을 TreeSet에 복수한 후 순회하는 방법을 사용한다 .

```java
        TreeMap<String, Double> map = new TreeMap<>();
        map.put("Zara", new Double(3434.34));
        map.put("Mahnaz", new Double(123.22));
        map.put("Ayan", new Double(1378.00));

        Set <Map.Entry<String, Double>> set = new TreeSet<Map.Entry<String, Double>>(
                (e1, e2)->(e1.getValue().compareTo(e2.getValue()))
        );
        set.addAll(map.entrySet());
        for(Map.Entry<String, Double> entry: set){
            System.out.println(entry.getKey() + "," + entry.getValue());
        }
```

결과는 다음과 같이 key가 아닌 value순으로 순회할 수 있다. 

```bash
Mahnaz,123.22
Ayan,1378.0
Zara,3434.34
```

### Reference

- [Java 8 API](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)
- [Java Collection Tutorial](http://www.tutorialspoint.com/java/java_collections.htm)
- [Collections Framework Overview](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/overview.html)
- [Collection Framework – Class Hierarchy](http://javaconceptoftheday.com/collection-framework-class-hierarchy/)
- [Collection Interface](http://javaconceptoftheday.com/collection-framework-collection-interface/)
- [Difference Between Collection And Collections In Java](http://javaconceptoftheday.com/difference-between-collection-and-collections-in-java/)
