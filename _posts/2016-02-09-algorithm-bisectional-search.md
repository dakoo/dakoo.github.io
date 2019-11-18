---
layout: post
title: Algorithm - 이분 탐색 
description: 이분 탐색 
modified: 2016-02-09
tags: [algorithm]
comments: true
image:
  feature: algorithm.jpeg
---

O(logN) 시간내에 정렬된 값을 탐색하는 방법으로 조건을 만족하는 다양한 값들 중 최적 값을 찾아내는데 사용된다. 

### Pseudo code

중복된 값들이 포함되어 있을 수 있으므로 while문 내에서 바로 return하지 않고 탐색을 계속해서 
중복 값 중 가장 크거나 작은 인덱스를 반환한다. 

```
int findIndex(int array[], int low, int high, int target_value){
    int answer = -1; // 값을 못 찾는 경우 -1 반환
    while(low <= high){
        int middle = (low + high)/2;
        if(array[middle]와 target_value를 비교해서 조건일 일치하면 ){
            anwser = middle; //값을 갱신 
            high = middle - 1 또는 low = middle + 1로 갱신 
        } else { //조건에 일치하지 못할 경우 
            low = middle + 1 또는 high = middle - 1로 갱신 
        }
    }
    return answer;
}
```

### code

```cpp
#include <iostream>
#include <vector>
using namespace std;

// 중복된 값을 허용하는 array에서 target값과 같은 값 중 가장 작은 index 찾기 
int findSame(int arr[], int low, int high, int target){
    int ans = -1; 
    while(low <= high){
        int m = low + (high-low)/2;
        if(arr[m] == target){ //같은 경우 그보다 더 작은 index에도 같은 값이 있는지 계속 확인하기 위해 ans를 갱신하며 loop이 깨질때까지 진행
            high = m-1;
            ans = m;
        } else if(arr[m] < target){
            low = m+1;
        }
        else { // target < arr[m]
            high = m-1;
        }
    }
    return ans; 
}

// target보다 큰 값 중 가장 최소 값 찾기
int findBig(int arr[], int low, int high, int target){
    int ans = -1; 
    while(low <= high){
       int m = low + (high-low)/2;
        if(arr[m] > target){  // target보다 큰 값을 만날때마다 계속 ans를 갱신하면서 loop이 깨질때 까지 진행
            high = m - 1;
            ans = m;
        } else {
            low = m+1;
        }
    }
    return ans; 
}

// target보다 작은 값중 가장 최대값 찾기 
int findSmall(int arr[], int low, int high, int target){
    int ans = -1; //못찾으면 -1
    while(low <= high){
        int m = low + (high-low)/2;
        if(arr[m] < target){ // target보다 작은 값을 만날때마다 계속 ans를 갱신하면서 loop이 깨질때 까지 진행
            low = m + 1;            
            ans = m;
        } else { 
            high = m-1;
        }
    }
    return ans;
}

// target과 같거나 큰 값 중 최소 값 (target이 존재하지 않을 수 있음)
int findSameorBig(int arr[], int low, int high, int target){
    int ans = -1;
    while(low <= high){
        int m = low + (high-low)/2;
        if(arr[m] >= target){ //target과 같거나 큰 값을 만나면 ans를 갱신하면서 loop이 깨질때 까지 계속 진행
            ans = m;
            high = m - 1;
        } else {
            low = m+1;
        }
    }
    return ans; 
}

// target과 같거나 작은 값 중 최대값 (target이 존재하지 않을 수 있음)
int findSameorSmall(int arr[], int low, int high, int target){
    int ans = -1; //못찾으면 -1
    while(low <= high){
       int m = low + (high-low)/2;
        if(arr[m] <= target){  //target과 같거나 작은 값을 만나면 ans를 갱신하면서 loop이 깨질때 까지 계속 진행
            ans = m;
            low = m + 1;
        } else {
            high = m-1;
        }
    }
    return ans;
}

int main(){
    int T[7] = {1, 2, 3, 3, 4, 5, 6};
    //목표값과 같은 것 중 가장 빠른 index
    cout << findSame(T, 0, 6, 3)<< endl; //must be 2
    cout << findSame(T, 0, 6, 1)<< endl; //must be 0
    cout << findSame(T, 0, 6, 6)<< endl; //must be 6
    cout << findSame(T, 0, 6, 7)<< endl; //must be -1
    
    //목표값보다 큰 값 중 가장 빠른 index(가장 작은)
    cout << findBig(T, 0, 6, 2)<<endl; //must be 2
    cout << findBig(T, 0, 6, 0)<<endl; //must be 0
    cout << findBig(T, 0, 6, 1)<< endl; //must be 1
    cout << findBig(T, 0, 6, 6)<<endl; //must be -1
    
    //목표값보다 작은 값 중 가장 큰 index(가장 큰)
    cout << findSmall(T, 0, 6, 4)<<endl; //must be 3
    cout << findSmall(T, 0, 6, 7)<<endl; //must be 6
    cout << findSmall(T, 0, 6, 0)<<endl; //must be -1
    cout << findSmall(T, 0, 6, 1)<<endl; //must be -1
    
    //목표값과 같거나 큰 값 중 가장 빠른 index
    cout << findSameorBig(T, 0, 6, 2)<<endl; //must be 1
    cout << findSameorBig(T, 0, 6, 3)<<endl; //must be 2
    cout << findSameorBig(T, 0, 6, 6)<<endl; //must be 6
    cout << findSameorBig(T, 0, 6, -1)<<endl; //must be 0
    cout << findSameorBig(T, 0, 6, 7)<<endl; //must be -1
    
    
    //목표값과 같거나 작은 값 중 가장 큰 index
    cout << findSameorSmall(T, 0, 6, 4)<<endl; //must be 4
    cout << findSameorSmall(T, 0, 6, 3)<<endl; //must be 3
    cout << findSameorSmall(T, 0, 6, -1)<<endl; //must be -1
    cout << findSameorSmall(T, 0, 6, 7)<<endl; //must be 6
    
    return 0;
}
```
