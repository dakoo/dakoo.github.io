---
layout: post
title: Java - Lambda
description: Java - Lambda 
modified: 2016-08-23
tags: [java]
comments: true
image:
  feature: abstract-22.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

Java 8에서 지원하는 Lambda를 살펴보자. 이 글은 [Jakob Jenkov의 Java Lambda tutorial](http://tutorials.jenkov.com/java/lambda-expressions.html)의 많은 부분을 참고하여 작성하였다. 

### 1. 개요

Labmda는 Java가 functional programming을 지원하기 시작한다는 신호이다. **Lambda를 통해 어떤 class에도 속하지 않은 함수**를 만들 수 있게 된 것이다. 함수를 마치 object처럼 argument로 사용하거나, 필요할 때에만 실행되도록 할 수 있게 되었다. 


### 2. Lambda의 사용

Anonymous function은 event listener에 많이 사용되어지고, 이는 lambda로 변환하기 매우 적절한 부분이다. 

#### 2.1 Lambda없이 Publisher class에 Event listener 등록하기 

Lambda없이 작성한 Java 7의 event listener를 등록하는 코드를 살펴보자. 


##### 2.1.1 Event Listener interface와 Publisher Class

Event Listener interface는 다음과 같다.  

```java
public interface StateChangeListener {

    public void onStateChange(State oldState, State newState);

}
```

위의 event Listener를 argument로 가지는 Publisher Class는 다음과 같다. 

```java
public class Publisher {

    public void addEventListener(StateChangeListener listener) { ... }

}
```

##### 2.1.2 나의 Event Listener 구현하고 등록하기 

이제 Publisher instance에 내가 작성한 event listener를 등록하려면 다음과 같이 해야 할 것이다. 


```java
Publisher publisher = new Publisher();

publisher.addStateListener(new StateChangeListener() {

    public void onStateChange(State oldState, State newState) {
	System.out.println("State changed");
    }
});
```

#### 2.2 Lambda 코드 


##### 2.2.1 코드 

위에서 작성한 event listener 코드 대신 Lambda를 이용하면 다음과 같이 간결하게 작성할 수 있다. 

```java
StateOwner stateOwner = new StateOwner();

stateOwner.addStateListener(
    (oldState, newState) -> System.out.println("State changed") //여기가 lambda 코드
);
```

##### 2.2.2 매칭

StateChangeListener 객체를 새로 생성할 필요 없고, onStateChange와 같이 이름을 명시할 필요도 없다. 자동적으로 컴파일러가 StateChangeListener 타입을 매칭할 것이다. StateChangeListener interface가 하나의 method만 가지고 있으므로 바로 매칭된다. 다음의 단계를 거쳐 매칭이 성공/실패가 결정된다.  

1. interface가 단지 하나의 method만 가지고 있나?
2. 하나의 method의 parameter가 일치하는가?
3. return type이 일치하는 가?

### 3. Lambda에 대해 좀 더 자세한 이야기 

#### 3.1 Lambda Parameter

##### 3.1.1 Zero Parameter

```java
publisher.addStateListener(new StateChangeListener() {
    public void onStateChange() {
	System.out.println("State changed");
    }
});
```

위와 같은 파라미터 없는 method는 다음과 같이 표현가능하다. ()안에 아무것도 없다. 

```java
() -> System.out.println("State changed");
```

##### 3.1.2 One Parameter

```java
publisher.addStateListener(new StateChangeListener() {
    public void onStateChange(State newState) {
	System.out.println("State changed :" + newState);
    }
});
```

위와 같은 파라미터 없는 method는 다음과 같이 표현가능하다. type을 표기하지 않아도 된다. 

```java
(newState) -> System.out.println("State changed :" + newState);
```

Parameter가 하나만 있는 경우 ()를 생략할 수 있다. 

```java
newState -> System.out.println("State changed :" + newState);
```

##### 3.1.3 Multiple Parameter

(oldState, newState) -> System.out.println("State changed" + oldState + "," + newState);

#### 3.2 Lambda Parameter Type

컴파일러가 Lambda Parameter Type을 헷갈려하는 경우 type을 추가하면 된다. 

```java
(State newState) -> System.out.println("State changed :" + newState.getName());
```

#### 3.3 Lambda 함수의 body

##### 3.3.1 body 부분

Lambda함수는 '->' 뒷 부분이다. 예를 들어 다음 Lambda함수에서 **System.out.println("State changed")**이 body이다. 

```java
(oldState, newState) -> System.out.println("State changed")
```

##### 3.3.2 여러 줄인 경우 

Lambda 함수가 여러 줄로 구성된 경우 {}를 사용하면 된다. 

```
(oldState, newState) -> {
    System.out.println("Old state: " + oldState);
    System.out.println("New state: " + newState);
}
```

#### 3.3 Lambda 함수의 return

##### 3.3.1 명시적인 return

다음과 같이 명시적으로 return을 사용할 수 있다. 

```java
(param) -> return "return value";
```

##### 3.3.2 비명시적 return 

Lambda 내부에서 계산된 값 또는 새로 만들어진 값을 return하는 경우 'return'을 생략할 수 있다. 

```java
(param) -> "return value";
```

### 4. Object로서의 Lambda

기본적으로 Lambda expression은 object라서 다른 변수에 대입 가능하고, 함수의 argument로 사용가능하다. 

#### 4.1 문제 

다음과 같이 Book Class의 page를 비교하는 interface가 있는 경우를 생각해보자. 

```java
public class Book {
    private int page;
    public Book(int page){
        this.page = page;
    }
    public int getPage(){
        return this.page;
    }
}


public interface MyComparator {
    public boolean isTheSame(Book b1, Book b2);
}
```

#### 4.2 구현 

위의 MyComparator을 어떻게 구현하는게 좋을까? Lambda를 이용하여 빠르게 구현할 있고, 변수에 대입하여 사용할 수 있다. 

```java
MyComparator comparator = (b1, b2) -> b1.getPage() == b2.getPage();

Book b1 = new Book(10);
Book b2 = new Book(10);
boolean result = comparator.isTheSame(b1, b2);
System.out.println(result);
```
