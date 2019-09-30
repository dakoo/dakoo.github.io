---
layout: post
title: Algorithm - quick sort
modified: 2015-08-05
tags: [algorithm]

---

### The performance of quicksort

There are a lot of implementations of quick sort algorithm. Even though the time complexity of the algorithm is **nlog(n)**, the real performance is highly depending on the partition algorithm. 

The pseudo code of quicksort algorithm is as follows. 

```
quickSort(A[], l, r)
      if l<r
      s <- partition (a, l, r)
      quickSort(A[], l, s-1)
      quickSort(A[], s+1, r)
```

### Two partition algorithms

There are two famous partition algorithms. As my experience, Lumuto-Partition is better for performance and readibility.

#### Hoare-Partition algorithm

Hoare-Partition algorithm is not so good to understand due to several duplicated loops.
The pseudo code of Hoare-partition algorithm:

```
partition(A[], l, r) 
    pivot <- A[l]  // select a value
    i = l-1, j = r + 1;
    loop true
        do  i++; while (A[i] < pivot);
        do  j--; while (A[j] > pivot);
        if  (i < j)
            swap(A[i], A[j])
        else
            return j // when i and j meets
```

#### Lumuto-Partition algorithm

The pseudo code of Lumuto-partition algorithm:

```
partition(A[], l, r) 
	pivot <- A[r]   // select value of the most right
	index <- l     // pointer the location to place the item smaller than pivot. The item will be placed by swap.
	for i <- low to high - 1 // loop
		if A[i] <= pivot      // when item is smaller than the pivot
			swap(A[i], A[index]) // swap with index
			index++ //move to the next
	swap(A[index], A[r]) // pivot value is located after the smaller values since the index++.
	return index // pivot index
```

```
1) 
     : 0 1 2 3 4 5
       - - - - - -
value: 1 4 5 2 7 3 
l: 0, r: 5, pivot value: 3, index: 0

2) 1 at 0 is smaller than 3. So swap and increase index. But index = 0 and i = 0 so no value change. 
index: 0, i = 0
-> index: 1, i = 1
     : 0 1 2 3 4 5
       - - - - - -
value: 1 4 5 2 7 3 

3) 4 at 1 is bigger than 3. No change
index: 1, i = 1
-> index: 1, i = 2
     : 0 1 2 3 4 5
       - - - - - -
value: 1 4 5 2 7 3 

4) 5 at 2 is bigger than 3. No change
index: 1, i = 2
-> index: 1, i = 3
     : 0 1 2 3 4 5
       - - - - - -
value: 1 4 5 2 7 3 

5) 2 at 3 is smaller than 3. So swap and increase index.
index: 1, i = 3
-> index: 2, i = 4
     : 0 1 2 3 4 5
       - - - - - -
value: 1 2 5 4 7 3 

5) 7 at 4 is bigger than 3. No change. Loop is finished.
index: 1, i = 4
-> index: 2, i = 5
     : 0 1 2 3 4 5
       - - - - - -
value: 1 2 5 4 7 3

6) Change the pivot item with the item the index
     : 0 1 2 3 4 5
       - - - - - -
value: 1 2 3 4 7 5

7) return index 2
```


The following source code presents the Lumuto-Partition implementation. This is very fast. Morover, the code is intuitive and easy to understand.

```c
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
```
