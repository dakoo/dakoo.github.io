---
layout: post
title: Algorithm - quick sort
description: quick sort 
modified: 2015-08-05
tags: [algorithm]
comments: true
image:
  feature: abstract-15.png
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

### The performance of quicksort

There are a lot of implementations of quick sort algorithm. Even though the time complexity of the algorithm is **nlog(n)**, the real performance is highly depending on the partition algorithm. 

The pseudo code of quicksort algorithm is as follows. 

{% highlight javascript %}
quickSort(A[], l, r)
      if l<r
      s <- partition (a, l, r)
      quickSort(A[], l, s-1)
      quickSort(A[], s+1, r)
{% endhighlight %}

### Two partition algorithms

There are two famous partition algorithms. As my experience, Lumuto-Partition is better for performance and readibility.

#### Hoare-Partition algorithm

Hoare-Partition algorithm is not so good to understand due to several duplicated loops.
The pseudo code of Hoare-partition algorithm:

{% highlight javascript %}
partition(A[], l, r)
      p <- A[l]
      i <-l, j<- r
      while i <= j
	 while A[i] <= p: i++
	 while A[j] >= p: j--
	 if i<j : swap(A[i], A[j])
      swap(A[l], A[j])
      return j
{% endhighlight %}

#### Lumuto-Partition algorithm

The pseudo code of Lumuto-partition algorithm:

{% highlight javascript %}
partition(A[], l, r)
      x <- A[r]
      i <- l - 1
      for j in l->r-1
      	if A[j] <= x
      	   i++, swap(A[i], A[j])
      swap(A[i+1], A[r])
      return i + 1
{% endhighlight %}

The following source code presents the Lumuto-Partition implementation. This is very fast. Morover, the code is intuitive and easy to understand.

{% highlight c %}
#include <stdio.h>

void swap(int array[], int a, int b){
	int tmp = array[a];
	array[a] = array[b];
	array[b] = tmp;
}

int partition(int array[], int low, int high){
	int last = array[high];
	int index = low;
	for (int i = low; i < high; i++){
		if (array[i] <= last){
			swap(array, i, index);
			index++;
		}
	}
	swap(array, index, high);
	return index;
}
/* quick sort function*/
void qsort(int array[], int low, int high){
	if (high <= low)
		return;
	int pivot = partition(array, low, high);
	qsort(array, low, pivot - 1);
	qsort(array, pivot + 1, high);
}
/* Test function*/
int main(){
	int sample[10] = { 3, 7, 8, 5, 2, 1, 9, 0, 4, 6 };
	qsort(sample, 0, 9);
	for (int i = 0; i <= 9; i++){
		printf("%d ", sample[i]);
	}
	return 0;
}
{% endhighlight %}

