---
layout: post
title: disjoints-set에 대한 이해 
description: disjoints-set에 대한 이해 
modified: 2015-08-06
tags: [algorithm]
image:
  feature: abstract-11.png
---

집합을 자료구조 중 array를 이용해 만드는 disjoints-set을 이해해 보자. 

### 자료 구조

{% highlight bash %}
int parent[MAX_LENGTH];
{% endhighlight %}

### makeset(x)

유일한 멤버 x를 포함하는 집합 만드는 함수이다. 초기화 함수로 모든 멤버들을 각기 독립적인 set으로 만든다. 

{% highlight bash %}
makeset(x)
  parent[x] = x; 
{% endhighlight %}

### findset(x)

x를 포함하는 집합(정확히 말하면 집합의 root parent index)를 찾는 함수이다. 

{% highlight bash %}
findset(x)
   if x == parent[x] //root parent
      return x;
    else 
      return findset(parent[x]); //recursion
{% endhighlight %}

skewed tree가 되는 것을 막기위해 찾으면서 그 결과를 이용해 tree를 평탄화시키는 것은 아래와 같다. 

{% highlight bash %}
findset(x)
   if x == parent[x] //root parent
      return parent[x];
    else 
      return parent[x] = findset(parent[x]); //recursion 하며 결과로 자신의 parent를 갱신
{% endhighlight %}

### unionset(x, y)

x와 y를 포함하는 두 집합을 합치는 함수이다. 

{% highlight bash %}
union(x)
   parent[findset(y)] = findset(x);
{% endhighlight %}

### 두 개의 원소가 같은 집합에 속해 있는지 판단하기 

findset(x)와 findset(y)가 같은 결과가 아니면 다른 집합이다. 

{% highlight bash %}
isUnion(x, y)
  if findset(x) != findset(y)
    return false;
  return true;
{% endhighlight %}

### 기타

성능을 최적화하는 방법으로 집합을 union할때 높이가 낮은 쪽으로 연결되도록 rank를 사용하는 경우도 있다. 

Priority Queue (MinHeap)
{% highlight c %}
isUnion(x, y)
//Bridge 정보
struct Bridge {
	int v1;
	int v2; 
	double tax;
};

Bridge PriorityQueue[1000001];
int items_PQ; 
int getParentIndex(int i){
	return i / 2;
}
int getLChildIndex(int i){
	return i * 2;
}
int getRChildIndex(int i){
	return i * 2 + 1;
}
void initPQ(){
	items_PQ = 0;
}
void swapPQ(int i, int j){
	Bridge temp;
	temp.v1 = PriorityQueue[j].v1;
	temp.v2 = PriorityQueue[j].v2;
	temp.tax = PriorityQueue[j].tax;
	PriorityQueue[j].v1 = PriorityQueue[i].v1;
	PriorityQueue[j].v2 = PriorityQueue[i].v2;
	PriorityQueue[j].tax = PriorityQueue[i].tax;
	PriorityQueue[i].v1 = temp.v1;
	PriorityQueue[i].v2 = temp.v2;
	PriorityQueue[i].tax = temp.tax;
}
void insertPQ(Bridge b){
	items_PQ++;
	int index = items_PQ;
	PriorityQueue[index].tax = b.tax;
	PriorityQueue[index].v1 = b.v1;
	PriorityQueue[index].v2 = b.v2;
	int p;
	while ((p = getParentIndex(index)) > 0){
		if (PriorityQueue[p].tax <= PriorityQueue[index].tax)
			break;
		swapPQ(p, index);
		index = p;
	}	
}
Bridge deletePQ(){
	Bridge temp;
	temp.v1 = PriorityQueue[1].v1;
	temp.v2 = PriorityQueue[1].v2;
	temp.tax = PriorityQueue[1].tax;
	swapPQ(items_PQ, 1);
	items_PQ--;
	int index = 1;
	int cr = getRChildIndex(index);
	int cl = getLChildIndex(index);
	int target_index;
	while (cl <= items_PQ){
		if (cl == items_PQ){
			target_index = cl;
		}
		else {
			if (PriorityQueue[cr].tax < PriorityQueue[cl].tax)
				target_index = cr;
			else
				target_index = cl;
		}
		if (PriorityQueue[target_index].tax >= PriorityQueue[index].tax)
			break;
		swapPQ(target_index, index);
		index = target_index;
		cr = getRChildIndex(target_index);
		cl = getLChildIndex(target_index);
	}
	return temp;
}
int isEmptyPQ(){
	if (items_PQ == 0)
		return 1;
	return 0;
}
{% endhighlight %}


Kruskal 알고리즘의 간단 예
{% highlight c %}
double mst_kruskal(int num_Island, double taxrate){
	for (int i = 1; i <= num_Island; i++){
		makeset(i);
	}
	initPQ();

	for (int i = 1; i <= num_Island - 1; i++){
		for (int j = i + 1; j <= num_Island; j++){
			Bridge b;
			b.tax = cal_tax(X[i], Y[i], X[j], Y[j], taxrate);		
			b.v1 = i;
			b.v2 = j;
			insertPQ(b);
		}
	}
	int num_selected = 0;
	double total_sum = 0;
	while (!isEmptyPQ() && num_selected != num_Island-1){
		Bridge tempb = deletePQ();
		if (!isInSameSet(tempb.v1, tempb.v2)){
			num_selected++;
			total_sum += tempb.tax;
			unionset(tempb.v1, tempb.v2);
		}
	}
	return total_sum;
}

{% endhighlight %}

실수에서 정수로 변환하면서 소수점에서 반올림 하는 방법
{% highlight c %}
	double result = 1.4;
	result += 0.5;
	long long i_result = result;
	printf("%d\n", i_result);
	result = 1.6;
	result += 0.5;
	i_result = result;
	printf("%d\n", i_result);
{% endhighlight %}
