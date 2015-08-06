---
layout: post
title: Priority Queue의 동작에 대한 이해 
description: Priority Queue의 동작에 대한 이해 
modified: 2015-08-06
tags: [algorithm]
image:
  feature: abstract-10.png
link: http://blog.naver.com/wpdls6012/220247604017
---

전체적인 동작 원리는 [네이버 글](http://blog.naver.com/wpdls6012/220247604017)을 참조한다. 
아래 내용은 Max Heap을 기준으로 설명한다. 

### 자료 구조 

Array를 이용해 binary tree를 구현한다. 이때 index는 1부터 시작한다. 다음은 기초적인 알고리즘이다. 

{% highlight bash %}
parent_index = index/2;
left_child_index = index*2;
right_child_index = index*2 + 1;
{% endhighlight %}

Array의 관리를 위해 원소의 수를 가리키는 변수(마지막 index이기도 함)를 하나 사용한다. 

### 삽입 

1. 원소의 수를 1 증가한다. 
2. 새로운 원소는 마지막 위치에 저장한다.  
3. 부모 노드와 값을 비교해서 값이 더 크면 위치를 서로 바꾼다(swap). 더 작으면 삽입과정을 종료한다. 
4. root(1)에 도달할 때까지 2를 반복한다. 

### 삭제

1. root(1) 노드를 삭제한다. 
2. 마지막 노드를 root 노드(1)로 이동시키고 원소의 수를 1 줄인다. 
3. root 노드부터 시작해서 parent와 left 또는 right child와 비교하여 parent가 더 값이 작으면 swap한다. 더 크면 삭제 과정을 종료한다. 
4. leaf에 도달할 때 까지 위의 과정을 반복한다. 

