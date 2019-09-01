---
layout: post
title: Algorithm - k번째 큰(작은) 아이템 찾기
modified: 2016-02-22
tags: [algorithm]
parent: Coding Interview
nav_order: 2
---

정렬이 되어 있지 않은 배열에서 k번째로 작은 알고리즘을 찾는 방법을 생각해보자. 예를 들어 다음의 vector에서 3번째로 작은 값은 3이다. 

```cpp
	vector<int> T = { 3, 1, 6, 4, 2, 7, 8, 10, 5, 8, 9 };
```

### 몇가지 접근법

#### Selection Sort

Selection Sort는 다음의 알고리즘으로 수행되는 아주 간단한 정렬이다. 

- 주어진 배열 중에 최솟값을 찾는다.
- 그 값을 맨 앞에 위치한 값과 교체한다(패스(pass)).
- 맨 처음 위치를 뺀 나머지 배열을 같은 방법으로 교체한다.

위를 k번 반복하면 된다. 그런데 이 알고리즘은 O(N*k)성능으로 k가 큰 경우 성능이 나쁘다.  

#### Quick Sort

전체 배열을 O(NlogN)알고리즘인 Quick Sort로 정렬한 뒤 k번째를 찾는 것이다. k에 따라서 Selection Sort를 이용하는 것보다 성능이 좋을 수 있으나 불필요하게 모든 배열을 정렬하게 된다. 

#### Quick Sort의 변형

Quick Sort의 partition 함수는 pivot을 반환하게 된다. pivot index를 기준으로 더 작은 것과 큰 것으로 나뉘게 되는데 이 성질을 이용하여 k번째를 구할 수 있다. 

- pivot이 k와 같으면, 즉, pivot이 k번째 index이면 pivot에 해당하는 값을 반환
- pivot이 k보다 작으면 k는 pivot 이후에 있는 것
- pivot이 k보다 크면 k는 pivot이전에 있는 것

#### 코드 


Quick Sort를 변형하여 k번째 값을 찾는 코드는 다음과 같다. Partition함수는 기존의 Quick Sort와 동일하고, Partition함수를 통해 얻은 pivot을 처리하는 것은 위에서 설명한 바와 같다. 

```cpp
#include <iostream>
#include <vector>
using namespace std;
void swap(vector<int> &T, int a, int b){
	int temp = T[a];
	T[a] = T[b];
	T[b] = temp;
}
int partition(vector<int> &T, int low, int high){
	int index = low;
	for (int i = low; i<high; i++){
		if (T[i] <= T[high]){
			swap(T, i, index);
			index++;
		}
	}
	swap(T, index, high);
	return index;
}
/* Quick Sort를 변형 */
int findKth(vector<int> &T, int k, int low, int high){
	int pivot = partition(T, low, high);
	int ret;
	if (pivot == k){
		ret = T[pivot];
	}
	else if (pivot < k) {
		ret = findKth(T, k, pivot + 1, high);
	}
	else {
		ret = findKth(T, k, low, pivot - 1);
	}
	return ret;
}
int main(){
	vector<int> T = { 3, 1, 6, 4, 2, 7, 8, 10, 5, 8, 9 };
	int k = 3;
	/* 배열 index는 0부터 시작하므로 k를 1을 줄여서 findKth를 호출한다.*/
	cout << findKth(T, k - 1, 0, 9) << endl; //must be 3
	return 0;
}
```
