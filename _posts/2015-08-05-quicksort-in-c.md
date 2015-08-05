---
layout: post
title: quick sort algorithm in C
description: quick sort algorithm in C
modified: 2015-08-05
tags: [algorithm, english]
image:
  feature: abstract-15.png
---

### The performance of quicksort

There are a lot of implementations of quick sort algorithm. Even the time complexity of the algorithm is **nlog(n)**, the real performance is highly depending on the implementation. 

### Fastest quick sort implmentation I've ever used

The following source code presents the fastest quick sort implementation that I have ever used in common cases. Morover, the code is intuitive and easy to understand.

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

