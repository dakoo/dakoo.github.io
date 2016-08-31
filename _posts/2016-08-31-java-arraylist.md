---
layout: post
title: Java - ArrayList 클래스
description: Java -  ArrayList 클래스
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

ArrayList 클래스의 기본 초기 크기는 10이고, 10보다 더 커져야 하면 자동으로 증가된다. 또한 생성 시점에 크기를 명시할 수 있다. List 인터페이스 외에도 RandomAccess, Cloneable, Serializable 인터페이스를 구현한다. 

<figure>
	<img src="http://javaconceptoftheday.com/wp-content/uploads/2014/12/ArrayListClass.png" alt="Java ArrayList">
</figure>

### 2. ArrayList 클래스의 장점과 메소드들

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

### 3. ArrayList와 Array간 변환

Array와 ArrayList는 Java에서 매우 많이 사용되어지는 자료구조이다. 이름은 유사하지만, 자료 구조의 특성이 다르기 때문에 변환하는 작업이 직관적이지는 않다. 

#### 3.1 Array를 ArrayList로 변환

많은 개발자가 `Arrays.asList(array)` 메소드를 사용해 array를 ArrayList로 변환한다. 하지만, 이 메소드는 가변적인 사이즈를 지원하는 ArrayList와 다른 클래스를 반환한다. 그러므로 다른 방법이 필요하다.

##### 3.1.1 ArrayList 생성시 Arrays.asList() 메소드 사용

가장 많이 선호되는 방법이다. 

- List<> list = new ArrayList<>(Arrays.asList(array));

예를 들어 **Class의 instance를 저장한 array의 경우**는 다음과 같이 쉽게 변환 가능하다. 

```java
String [] array = new String[] {"one", "two", "three"};
List<String> list = new ArrayList<>(Arrays.asList(array));
System.out.println(list); //[one, two, three]
```
int와 같은 **primitive type의 경우**는 안타깝게도 위와 같이 할 수 없다. 아래와 같이 하나씩 옮겨야 한다. 

```java
int[] array = {1, 2, 3};
List<Integer> list = new ArrayList<>();
for(int i : array) {
    list.add(i);
}
System.out.println(list); //[1, 2, 3]
```

##### 3.1.2 Collections.addAll() 메소드 사용

source와 destination을 일종의 복사를 하는 방식이다. 이 역시 안타깝데도 primitive type의 경우는 사용할 수 없다.

- Collections.addAll(list, array)

```java
String [] array = new String[] {"one", "two", "three"};
List<String> list = new ArrayList<>();
Collections.addAll(list, array);
System.out.println(list); //[one, two, three]
```

##### 3.1.3 List 인터페이스의 addAll() 메소드 사용

List의 addAll() 메소드를 사용하는 방식이다. 이 역시 안타깝데도 primitive type의 경우는 사용할 수 없다.

- list.addAll(Arrays.asList(array))

```java
String [] array = new String[] {"one", "two", "three"};
List<String> list = new ArrayList<>();
list.addAll(Arrays.asList(array));
System.out.println(list); //[one, two, three]
```

###### 3.1.4 Java 8의 Stream 사용하기 

Java 8의 Stream에 대해서는 따로 정리된 내용을 확인하자. 

- List<Object> list = Arrays.stream(array).collect(Collectors.toList())

이 역시 안타깝데도 primitive type의 경우는 사용할 수 없다.

```java
String [] array = new String[] {"one", "two", "three"};
List<String> list = Arrays.stream(array).collect(Collectors.toList());
System.out.println(list); //[one, two, three]
```

#### 3.2 ArrayList를 Array로 변환

아주 간단하다. Array를 크기에 맞게 생성 후 List 인터페이스의 toArray() 메소드를 사용해서 복사하면 된다. 

- list.toArray(array)

```java
List<String> list = new ArrayList<String>();
list.add("one");
list.add("two");
list.add("three");
String [] array = new String[list.size()];
list.toArray(array);
for(String s: array){
    System.out.println(s); 
}
```

결과는 다음과 같다. 

```
one
two
three
```

### 4. Reference

- [Collection Framework – List Interface](http://javaconceptoftheday.com/collection-framework-list-interface/)
- [Advantages Of Using ArrayList Over Arrays](http://javaconceptoftheday.com/advantages-of-using-arraylist-over-arrays/)
- [Collection Framework – The ArrayList Class](http://javaconceptoftheday.com/collection-framework-arraylist-class/)
- [How To Remove Duplicate Elements From ArrayList In Java?](http://javaconceptoftheday.com/how-to-remove-duplicate-elements-from-arraylist-in-java/)
- [Array To ArrayList And ArrayList To Array In Java With Examples](http://javaconceptoftheday.com/array-to-arraylist-in-java-with-examples/)


