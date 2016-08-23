---
layout: post
title: Java - collections API
description: Java - collections API
modified: 2016-08-23
tags: [java]
comments: true
image:
  feature: abstract-18.png
---
Java의 Collections class는 static method로만 구성된 utility 클래스이다. 주로 List, Collection등과 같은 자료 구조를 위한 알고리즘을 제공하고 있다. 

### 주요 함수들 

#### Collections.sort(List myList)

List를 Ascending order로 sorting한다.

#### Collecions.sort(List myList, Collections.reverseOrder())

List를 Descending order로 sorting한다.

#### Collections.sort(List myList, comparator cmp)

comparator를 이용해 sorting한다. Java의 comparator는 다음에 자세히 알아보자. 

#### Collections.shuffle(List myList)

List 아이템을 무작위로 순서를 바꾼다.  

#### Collections.reverse(List myList)

List 아이템의 순서를 완전히 뒤집는다. 

#### Collections.binarySearch(List myList, T key)

myList의 아이템을 binary search로 검색한다. 

#### Collections.frequency(List myList, Object o)

myList의 아이템 중 o가 몇 개 존재하는지를 반환한다. 

### 예제

```
    List<Integer> myList = new ArrayList();
    myList.add(3);
    myList.add(4);
    myList.add(10);
    myList.add(-1);
    myList.add(2);
    myList.add(10);
    myList.add(101);
    myList.add(0);

    System.out.println("Original: " + myList);

    Collections.sort(myList);
    System.out.println("Sorted:" + myList);

    Collections.sort(myList, Collections.reverseOrder());
    System.out.println("Sorted in descending order:" + myList);

    Collections.reverse(myList);
    System.out.println("Re-Reversed: " + myList);

    Collections.shuffle(myList);
    System.out.println("Shuffled: " + myList);

    System.out.println("Occurance of 10: " + Collections.frequency(myList, 10));
```

결과

```
Original: [3, 4, 10, -1, 2, 10, 101, 0]
Sorted:[-1, 0, 2, 3, 4, 10, 10, 101]
Sorted in descending order:[101, 10, 10, 4, 3, 2, 0, -1]
Re-Reversed: [-1, 0, 2, 3, 4, 10, 10, 101]
Shuffled: [2, 4, 101, 3, 10, 10, 0, -1]
Occurance of 10: 2
```
