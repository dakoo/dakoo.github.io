---
layout: post
title: Java - Comparable & Comparator
modified: 2016-08-23
tags: [java]
---

Java에서 비교를 위한 Comparable interface와 Comparator에 대해 살펴보자. 

### 1. 개요

Java에서 정렬들을 위한 목적으로 Object를 비교하기 위해 Comparable interface에서 정의한 CompareTo()함수를 구현하거나, Comparator를 구현해야 한다. 이 두가지를 살펴보자. 

- CompareTo()
- Comparator

### 2. 문제점

다음과 같은 Book class가 있고, 이를 가격 순으로 정렬하기 위한 코드를 짠다고 생각해보자. 

##### 2.1 Book class

```java
public class Book {
    private int price;
    public Book(int price){
        this.price = price;
    }
    public int getPrice(){
        return this.price;
    }
}
```

##### 2.2 Solution class

이제 Book class를 생성해 List에 넣고 정렬을 하는 코드를 짜보자. 

```java
import java.util.Collections;
import java.util.ArrayList;
import java.util.List;

public class Solution {

    public static void main(String[] args) {
        List<Book> myBookList = new ArrayList();
        myBookList.add(new Book(300));
        myBookList.add(new Book(310));
        myBookList.add(new Book(100));
        myBookList.add(new Book(200));

        System.out.println("Original: " + myBookList);

        Collections.sort(myBookList);
        for(Book book : myBookList){
            System.out.println(book.getPrice());
        }
    }
}
```

이렇게 하면 **Collections.sort**함수에서 sort를 적용할 수 있는 instance가 아니라는 에러가 뜬다. 왜냐하면 Book class가 sort에 쓰이는 비교 함수를 구현하지 않았기 때문이다. 

### 3. Comparable Interface

Book instance를 비교하기 위해서는 **Comparable interface**에서 정의하는 **public int compareTo(Object o)**을 구현해야 한다. Book class를 다음과 같이 수정해 보자. 여기서는 ascending order를 가정한다. 

```java
public class Book implements Comparable<Book>{
    private int price;
    public Book(int price){
        this.price = price;
    }
    public int getPrice(){
        return this.price;
    }

    @Override
    public int compareTo(Book b) {
        return this.price - b.price; //자신이 앞에 있는게 ascending order
    }
}
```

2.2 Solution class를 그대로 두고 실행하면 결과는 다음과 같다. 

```
100
200
300
310
```

**implements Comparable<Book>**형태에 주의하자. 


### 4. Comparator

Comparator는 static Comparator instance로 비교 대상인 object를 수정하지 않고 비교를 위한 함수를 만들 수 있다. 

#### 4.1 Syntax

Comparator의 syntax는 다음과 같다. 

```java
public static Comparator<Type> myComparator
                          = new Comparator<Type>() {

	    public int compare(Type o1, Type o2) {

	      //ascending order
	      return o1 - o2;
	    }

	};
```

위의 myComparator를 다음과 같이 사용하면 된다. 

```java
Collections.sort(myList, myComparator);
```

#### 4.2 예제 

2.1의 Book class를 그대로 두고 (Comparable Interface 구현 없이) sorting을 해보자. 

```java
public class Book{
    private int price;
    public Book(int price){
        this.price = price;
    }
    public int getPrice(){
        return this.price;
    }
}
```
2.2의  Solution class를 다음과 같이 변경해보자. 

##### 4.2.1 Comparator

```java
import java.util.Collections;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

public class Solution {
    public static Comparator<Book> myComparator
            = new Comparator<Book>() {
        @Override
        public int compare(Book b1, Book b2) {
            return b1.getPrice() - b2.getPrice();
        }
    };

    public static void main(String[] args) {
        List<Book> myBookList = new ArrayList();
        myBookList.add(new Book(300));
        myBookList.add(new Book(310));
        myBookList.add(new Book(100));
        myBookList.add(new Book(200));

        System.out.println("Original: " + myBookList);

        Collections.sort(myBookList, myComparator);
        for(Book book : myBookList){
            System.out.println(book.getPrice());
        }
    }
}
```


##### 4.2.2 Anonymous Comparator

다음과 같이 anonymous Comparator를 사용해 작성할 수도 있다. 

```java
import java.util.Collections;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

public class Solution {
    public static void main(String[] args) {
        List<Book> myBookList = new ArrayList();
        myBookList.add(new Book(300));
        myBookList.add(new Book(310));
        myBookList.add(new Book(100));
        myBookList.add(new Book(200));

        System.out.println("Original: " + myBookList);

        Collections.sort(myBookList, new Comparator<Book>() {
            @Override
            public int compare(Book b1, Book b2) {
                return b1.getPrice() - b2.getPrice();
            }
        });
        for(Book book : myBookList){
            System.out.println(book.getPrice());
        }
    }
}
```

##### 4.2.3 Lambda 

Anonymous function은 Java의 Lambda로 변환 가능하다. 이제 마지막 코드를 보자.

```java
import java.util.Collections;
import java.util.ArrayList;
import java.util.List;

public class Solution {
    public static void main(String[] args) {
        List<Book> myBookList = new ArrayList();
        myBookList.add(new Book(300));
        myBookList.add(new Book(310));
        myBookList.add(new Book(100));
        myBookList.add(new Book(200));

        System.out.println("Original: " + myBookList);

        Collections.sort(myBookList,
                (Book b1, Book b2)-> b1.getPrice() - b2.getPrice());
        for(Book book : myBookList){
            System.out.println(book.getPrice());
        }
    }
}
```

참고로 Java의 Lambda에서는


```java
 (a1, a2) -> { return a1 > a2; }
```

와 

```java
 (a1, a2) -> a1 > a2;
```

를 compiler가 동일하게 본다. 
