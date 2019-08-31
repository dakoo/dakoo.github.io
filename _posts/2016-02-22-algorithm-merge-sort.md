---
layout: post
title: Algorithm - merge sort
modified: 2016-02-22
tags: [algorithm]
---

merge sort는 O(nlogn) 성능을 가진 대표적인 Divide & Conquer 패러다임 알고리즘이다. 그 구현에 대해 살펴보자. 

### 알고리즘

merge sort 알고리즘은 다음과 같다. divide & conquer 패러다임을 따르기 때문에 quick sort와 매우 유사하다. pivot을 얻어오는 partition 함수가 없고 대신 middle을 구하고, 맨 마지막에 merge하는 함수가 있다는 점이 다르다.  알고리즘은 다음과 같다. 

- 입력 배열을 왼쪽/오른쪽으로 반 나눈다. 
- recursive하게 왼쪽과 오른쪽을 호출한다. 
- 왼쪽과 오른쪽을 합친다. 잘 합치기 위해서 low, middle, high 인덱스가 필요하다. 

```cpp
merge_sort(Array, low, high) {
	if(low >= high) return;
	middle = (low + high)/2;
	merge_sort(Array, low, middle);
	merge_sort(Array, middle+1, high);
	merge(Array, low, middle, high);
}
```

주어진 배열의 정렬을 위해서는 다음과 같이 호출한다. 

```
merge_sort(Array, 0, Array.size()-1);
```

quick sort의 핵심이 partition함수라면 merge sort의 핵심은 merge알고리즘이다. 그 내용은 매우 단순하다. merge함수로 들어오는 배열은 middle을 중심으로 왼쪽 오른쪽이 각각 정렬되어 있는 것이다. 두개의 임시 배열을 만들어 왼쪽과 오른쪽 값을 복사해두고, 값을 비교하며 원래 배열에 하나씩 집어 넣는다. 이때 임시 배열 마지막에는 무한대 값을 두어 비교 시 길이 비교를 하지 않도록 하는 트릭을 쓴다. 

```cpp
merge(Array, low, middle, high) {
	l_len = middle - low + 1; 
	r_len = high - middle;
	create array L[l_len + 1] and array R[r_len + 1] //마지막에 무한대를 넣기 위해 추가
	for(i = 0; i< l_len; i++) 
		L[i] = A[low+i]
	for(i = 0; i< r_len; i++)
		R[i] = A[middle + 1 + i]
	L[l_len] = 무한대 //L 배열의 길이를 넘어가는 것을 비교하지 않기 위해 
	R[r_len] = 무한대 //R 배열의 길이를 넘어가는 것을 비교하지 않기 위해 
	i = 0, j = 0
	for(k = low; k <= high; k++){
		if(L[i] <= R[j]) {
			A[k] = L[i]
			i++
		} else {
			A[k] = R[j]
			j++;
		}
	} 
}
```

### 코드 

```cpp
#include <vector>
#include <limits>
#include <iostream>
using namespace std;
void merge(vector<int> &T, int low, int middle, int high){
	int l_len = middle - low + 1;
	int r_len = high - middle;
	vector<int> L(l_len + 1);
	vector<int> R(r_len + 1);
	for (int i = 0; i<l_len; i++){
		L[i] = T[low + i];
	}
	for (int i = 0; i<r_len; i++){
		R[i] = T[middle + 1 + i];
	}
	L[l_len] = R[r_len] = numeric_limits<int>::max();
	int i = 0;
	int j = 0;
	for (int k = low; k <= high; k++){
		if (L[i] > R[j]) {
			T[k] = R[j++];
		}
		else {
			T[k] = L[i++];
		}
	}
}
void merge_sort(vector<int> &T, int low, int high){
	if (low >= high) return;
	int middle = (low + high) / 2;
	merge_sort(T, low, middle);
	merge_sort(T, middle + 1, high);
	merge(T, low, middle, high);
}
int main(){
	vector<int> T = { 4, 1, 2, 7, 8, 5, 9, 3, 6, 10 };
	merge_sort(T, 0, 9);
	for (int i = 0; i<T.size(); i++){
		cout << T[i] << " "; //must be "1 2 3 4 5 6 7 8 9 10"
	}
	return 0;
}
```
