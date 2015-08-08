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
struct Item {
  int val;
};

Item PriorityQueue[1000001];
int num_items; 
int getParentIndex(int i){
  return i / 2;
}
int getLChildIndex(int i){
  return i * 2;
}
int getRChildIndex(int i){
  return i * 2 + 1;
}
void init(){
  num_items = 0;
}
int isEmpty(){
  if(num_items == 0)
    return true;
  return false;
}

{% endhighlight %}

Array의 관리를 위해 원소의 수를 가리키는 변수(마지막 index이기도 함)를 하나 사용한다. 

### 삽입 

1. 원소의 수를 1 증가한다. 
2. 새로운 원소는 마지막 위치에 저장한다.  
3. 부모 노드와 값을 비교해서 부모 노드의 값이 더 작으면 위치를 서로 바꾼다(swap). 부모 노드가 더 크면 삽입과정을 종료한다. 
4. root(1)에 도달할 때까지 2를 반복한다. 

{% highlight bash %}
void swap(int i, int j){
  Item temp;
  temp.val = PriorityQueue[j].val;
  PriorityQueue[j].val = PriorityQueue[i].val;
  PriorityQueue[i].val = temp.val;
}
void insert(Item b){
  num_items++;
  int index = num_items;
  PriorityQueue[index].val = b.val;
  int p;
  while ((p = getParentIndex(index)) > 0){ //root parent에 도달할 때 까지 
    if (PriorityQueue[p].val > PriorityQueue[index].val) //부모 노드가 더 큰 값이면 stop
      break;
    swap(p, index);
    index = p;
  }
}
{% endhighlight %}

### 삭제

1. root(1) 노드를 삭제한다. 
2. 마지막 노드를 root 노드(1)로 이동시키고 원소의 수를 1 줄인다. 
3. root 노드부터 시작해서 left 또는 right child와 더 큰 것을 선택해 부모 노드가 더 값이 작으면 swap한다. 부모 노드의 값이 더 크면 삭제 과정을 종료한다. 
4. leaf에 도달할 때 까지 위의 과정을 반복한다. 

{% highlight c %}
Item delete(){
  Item temp;
  temp.val = PriorityQueue[1].val;
  swap(num_items, 1);
  num_items--;
  int index = 1;
  int cr = getRChildIndex(index);
  int cl = getLChildIndex(index);
  int target_index;
  while (cl <= num_items ){
    if (cl == num_items){ //left child가 마지막이면
      target_index = cl;
    }
    else {  //child 두개를 비교하여 더 큰 것을 고른다. 
      if (PriorityQueue[cr].val < PriorityQueue[cl].val) 
        target_index = cl;
      else
        target_index = cr;
    }
    if (PriorityQueue[index].val >= PriorityQueue[target_index].val) //부모 노드가 더 크면 stop
      break;
    swap(target_index, index);
    index = target_index;
    cr = getRChildIndex(target_index);
    cl = getLChildIndex(target_index);
  }
  return temp;
}
{% endhighlight %} 




