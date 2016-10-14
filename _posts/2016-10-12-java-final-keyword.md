---
layout: post
title: Java - final 키워드
description: Java - final 키워드
modified: 2016-10-12
tags: [java]
comments: true
image:
  feature: algorithm.jpeg
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

Java의 final 키워드에 대해 살펴보자. 

### 1. 클래스의 final 멤버 변수

#### 1.1 선언과 함께 정의한 경우 

상수이다. 주로 변수명은 모두 대문자로 구성되어야 한다. 아래와 같이 다시 값을 대입하려고 하면 컴파일 에러가 발생한다. 

```java
public class Solution {
	final int LIMIT = 10;
	void foo(){
		LIMIT = 11; //Error: cannot assign a value to final variable LIMIT
	}
}
```

#### 1.2 선언만 하고 정의하지 않은 경우 

반드시 constructor내에서 값이 초기화 되어야 한다. 

```java
public class Solution {
    final int limit;
    public Solution(final int limit) {
        this.limit = limit;	//OK
    }
    ...
}
```

constructor외의 메소드에서 값을 초기화하는 것은 안된다. 


```java
public class Solution {
    final int limit;
    public Solution(final int limit) {
		setLimit(limit);
    }
    public void setLimit(final int limit) {
        this.limit = limit; //Error: cannot assign a value to final variable LIMIT
    }
    ...
}
```

#### 2. static final 변수

#### 2.1 선언과 함께 정의한 경우 

상수이다. 주로 변수명은 모두 대문자로 구성되어야 한다.  

```java
public class Solution {
    final static int LIMIT = 10;

    public static void main(String[] args) {
        System.out.println(Solution.LIMIT);
    }
}
```

아래와 같이 다시 값을 대입하려고 하면 컴파일 에러가 발생한다.

```java
public class Solution {
    final static int LIMIT = 10;

    public static void main(String[] args) {
        System.out.println(Solution.LIMIT);
        Solution.LIMIT = 11; //Error: cannot assign a value to final variable LIMIT
    }
}
```

#### 2.2 선언만하고 정의하지 않은 경우 

반드시 **static block**에서 초기화 되어야 햔다. 

```java
public class Solution {
    final static int LIMIT;
    
    static {
    	LIMIT = 10;
    }

    public static void main(String[] args) {
        System.out.println(Solution.LIMIT);
    }
}
```

### 3. 메소드의 final 파라미터

**const**와 같은 의미이다. 값을 대입할 수 없다.

```java
public class Solution {
    void tryToChagne(final int count, int length) {
        count = 10;	//Error: cannot assign a value to final variable count
        length = 10; //OK
    }
}
```

### 4. final Collection 변수

Collection instance를 다시 정의 할 수는 없다. 

```java
import java.util.ArrayList;
import java.util.LinkedList;
import java.util.List;

public class Solution {
    public static void main(String[] args) {
        final List<Integer> list = new ArrayList<>();
        list = new LinkedList<>(); //Error: cannot assign a value to final variable list
    }
}
```

하지만, 삽입이나 삭제는 가능하다. 

```java
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public static void main(String[] args) {
         final List<Integer> list = new ArrayList<>();
         list.add(1);
         list.add(2);
         list.remove(0);
    }
}
```


### 5. final 메소드

#### 5.1 일반 

final 메소드는 오버라이드를 할 수 없다는 의미이다. 

```java
class Parent {
    final void thisIsFinal() {
    }
    void thisIsNotFinal() {
    }
}

public class Solution extends Parent {
    @Override
    void thisIsFinal() { //Error: thisIsFinal() in Solution cannot override thisIsFinal() in Parent
  overridden method is final
    }

    @Override
    void thisIsNotFinal() {	//OK    
    }
}
```

#### 5.2 final constructor

constructor는 final일수 없다!!!

```java
public class Solution {
    final public Solution() { //Error: modifier final not allowed here
    }
}
```

### 6. final 클래스

final class는 상속할 수 없다. 

```java
final class Parent {
}

public class Solution extends Parent { //Error: cannot inherit from final Parent
}
```

### 7. interface

#### 7.1 final로 선언되지 않은 변수들

interface의 변수의 값을 바꾸려 하면 아래와 같이 final 변수를 바꾸려는 시도이기 때문에 안된다고 한다. 즉, interface의 멤버 변수들은 모두 final이다. 


```java
interface Solvable {
    int LIMIT = 10;
}

public class Solution implements Solvable {
    void tryToChange(){
        LIMIT = 1; //Error: cannot assign a value to final variable LIMIT
    }
}
```

#### 7.2 final로 선언된 메소드 선언

interface의 메소드 선언은 final을 허용하지 않는다. 

```java
interface Solvable {
    public final void hey(); //Error: modifier final not allowed here
}

public class Solution implements Solvable {
    public void hey() {
    }
}
```

