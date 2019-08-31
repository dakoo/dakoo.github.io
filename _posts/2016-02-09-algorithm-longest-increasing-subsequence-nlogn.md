---
layout: post
title: Algorithm - Longest Increasing Subsequence
modified: 2016-02-09
tags: [algorithm]
---

Longest Increasing Sequence는 "순서" 또는 "아이템들을 연속해서 조합할 때의 최대"  등을 구할 때 매우 많이 사용되는 방법이다. 
그런데 이때 보통 i번째 아이템에 대해 0부터 i-1까지의 아이템을 비교해서 최대값을 갱신하는 [O(n*n) 알고리즘](http://hochulshin.com/Longest-Increasing-Subsequence/)이 흔히 사용된다. 

```java
for(int i = 1; i<n; i++){
  for(int j = i - 1; j< i; j++){
    T[i] = max(T[i], T[j] + In[i]);
  }
}
```

그런데 이런 식으로 하면 n이 100000이 넘어가면 계산이 너무 오래 걸린다. 그러므로 O(NlogN)알고리즘을 사용할 수 있어야 한다. 
입력이 10000이하이면 구현이 용이한 O(n*n) 알고리즘을, 그 이상이면 O(NlogN) 알고리즘을 사용하는 것을 추천한다. 

### logN LIS

#### 필요한 자료구조 

- 입력 저장: int In[]
- 계산 중의 LIS 저장: int T[], 초기값 T[0] = 0
- 각 입력값 별 LIS시 이전 아이템 저장: int R[], 초기값 -1
- 현재의 LIS 길이 저장: int lastIdx, 초기값 0

```java
    vector<int> T(In.size(), 0);
    vector<int> R(In.size(), -1);
    int lastIdx = 0;
    T[0] = 0;
```

#### 계산 

항상 가장 작은 값으로 구성된 T를 유지함으로써 LIS를 찾아낼 수 있도록 In[1]부터 마지막 아이템까지 진행하면서 다음을 수행한다.

- In[T[lastIdx]] < In[i]이면,  (마지막 아이템과 비교해서 더 크면): R[i] = T[lastIdx], lastIdx++, T[lastIdx] = i
- In[T[lastIdx]] < In[i]가 아니고, In[T[0]] > In[T[i]]이면 (가장 작은 아이템과 비교해서 더 작으면): T[0] = i
- 위의 조건에 만족하지 않으면 T[1]부터 T[lastIdx]중의 하나에 i값을 넣는다. 그 기준은 "In[i]의 같거나 큰 값 중 가장 작은 값"에 넣는 것이다. 이를 통해 마지막 위치(lastIdx)도 갱신될 수 있다. 항상 T[0]부터 T[lastIdx]이 가리키는 In[]값은 정렬되어 있으므로 logN탐색을 위해 [이분 탐색](http://hochulshin.com/algorithm-bisectional-search/)을 이용한다. 그렇게 T[j]가 찾아지면: R[i] = T[j-1], T[j] = i

위의 내용을 코드로 표현하면 다음과 같다. 

```java
    for(int i = 1; i< In.size(); i++){
        if(In[T[lastIdx]] < In[i]){
            R[i] = T[lastIdx];
            T[++lastIdx] = i;
        } else if(In[T[0]] > In[i]){
            T[0] = i;
        } else {
            int idx = findBigorSame(In, T, 0, lastIdx, In[i]);
            R[i] =  T[idx - 1];
            T[idx] = i;
        }
    }
```
그런데, R[]은 계산이 끝나고 최대 값을 구성하는 아이템이 무엇인지 추적하기 위한 자료구조이고, T[]에 값이 아닌 index를 넣는 이유는 R[]을 쉽게 갱신하기 위한 것이다. 그러므로, 만약 풀고자 하는 문제가 단순한 최대 값이라면 R[]을 사용하지 않아도 되며, 또한 T[]도 인덱스가 아닌 값을 저장하면 된다. 그 코드의 예는 다음과 같다. 

```java
    for(int i = 1; i< In.size(); i++){
        if(T[lastIdx] < In[i]){
            T[++lastIdx] = In[i];
        } else if(T[0] > In[i]){
            T[0] = In[i];
        } else {
            int idx = findBigorSame(T, 0, lastIdx, In[i]); //T를 이분탐색 
            T[idx] = In[i];
        }
    }
```

#### 결과 처리 

마지막 아이템까지 진행이 끝났을 때 lastIdx + 1가 LIS의 길이가 되며, R[]은 각 index보다 앞의 것을 가리키게 된다. 그래서 순서를 구할 때는 R을 이용해서 역순으로 찾는다.

```java
    vector<int> Out(lastIdx + 1);  //In의 index를 저장한다. 
    int oIdx = lastIdx;
    Out[oIdx--] = T[lastIdx];
    int prvIdx = R[T[lastIdx]];
    while(prvIdx != -1){
        Out[oIdx--] = prvIdx;
        prvIdx = R[prvIdx];
    }
    ...
    for(int i = 0; i<Out.size(); i++){
        cout << In[Out[i]] << " ";
    }
```

### Code

```java
#include <iostream>
#include <vector>
using namespace std;

int findBigorSame(vector<int> & In, vector<int> & T, int low, int high, int tar_val){
    int answer = -1;
    while(low <= high){
        int middle = (low + high)/2;
        if(In[T[middle]] >= tar_val){
            answer = middle;
            high = middle - 1;
        } else {
            low = middle + 1;
        }
    }
    return answer;
}
vector<int> logNLIS(vector<int> & In){
    //초기화
    vector<int> T(In.size(), 0);
    vector<int> R(In.size(), -1);
    int lastIdx = 0;
    T[0] = 0;
    //계산
    for(int i = 1; i< In.size(); i++){
        if(In[T[lastIdx]] < In[i]){
            R[i] = T[lastIdx];
            T[++lastIdx] = i;
        } else if(In[T[0]] > In[i]){
            T[0] = i;
        } else {
            int idx = findBigorSame(In, T, 0, lastIdx, In[i]);
            R[i] =  T[idx - 1];
            T[idx] = i;
        }
    }
    //LIS를 첫순서부터 저장
    vector<int> Out(lastIdx + 1);
    int oIdx = lastIdx;
    Out[oIdx--] = T[lastIdx];
    int prvIdx = R[T[lastIdx]];
    while(prvIdx != -1){
        Out[oIdx--] = prvIdx;
        prvIdx = R[prvIdx];
    }
    return Out;
}
int main(){
    vector<int> In = {3, 4, -1, 5, 8, 2, 3, 12, 7, 9, 10 };
    
    vector<int> Out = logNLIS(In);
    
    cout << "SIZE : " << Out.size() << endl; //must be 6
    cout << "SEQUENCE : ";
    for(int i = 0; i<Out.size(); i++){
        cout << In[Out[i]] << " "; //must be -1 2 3 7 9 10
    }
    cout << endl;
    return 0;
}
```

### Reference

[Javacode](https://github.com/mission-peace/interview/blob/master/src/com/interview/array/LongestIncreasingSubSequenceOlogNMethod.java)와 [동영상 강의](https://www.youtube.com/watch?v=S9oUiVYEq7E)를 참고하자.

### 실전 문제

#### 쉬운 문제들

##### [꼬인 전깃줄](https://www.acmicpc.net/problem/1365) 문제

- **최대 100000개의 입력**이 들어오므로 O(n*n)으로 하면 timeout이 나므로 nlogn알고리즘을 이용해야 한다.  
- LIS의 구성을 묻지 않고 단순히 잘라야할 전기줄의 수를 묻는 문제이므로 R[]을 저장하지 않고 T[]만 이용해서 계산한다.

```java
int lis(vector<int> &In){
    vector<int> T(In.size());
    int lastIdx = 0;
    T[0] = 0;
    int size = (int)In.size();
    for(int i = 1; i<size; i++){
        if(In[T[lastIdx]] < In[i]){ //In[T의 마지막]보다 더 크면 하나 증가
            lastIdx++;
            T[lastIdx] = i;
        } else if(In[T[0]] > In[i]){ //In[T[0]]보다 작으면 T[0] 갱신
            T[0] = i;
        } else { // In[T[0]]~In[T[lastIdx]] 중 In[i]보다 같거나 큰 최초의 수를 갱신
            int idx = findSameorBig(In, T, 0, lastIdx, In[i]);
            T[idx] = i;
        }
    }
    return lastIdx + 1;
}
```

##### [반도체 설계](https://www.acmicpc.net/problem/2352) 문제

- [꼬인 전깃줄](https://www.acmicpc.net/problem/1365)과 동일한 방식으로 풀면 되는 문제이다. 
- **최대 40000개의 입력**이며, LIS의 구성을 묻지 않고 단순히 갯수를 묻는 문제이다. 
- **입력을 저장하지 않고 바로 처리하도록 풀었으며 이로 인해 T[]는 *index를 저장하는 것이 아니라 값을 직접 저장*하도록 수정했다. 

```java
int findSameorBig(vector<int> &T, int low, int high, int target){
    int ans = -1;
    while(low <= high){
        int m = (low + high)/2;
        if(T[m] > target){
            ans = m;
            high = m - 1;
        } else {
            low = m + 1;
        }
    }
    return ans;
}
int main(){
    int N;
    cin >> N;
    int t;
    vector<int> T(N);
    int lastIdx = 0;
    cin >> t;
    T[lastIdx] = t;
    for(int i = 1; i<N; i++){
        cin >> t;
        if(T[lastIdx] < t){
            lastIdx++;
            T[lastIdx] = t;
        } else if (T[0] > t){
            T[0] = t;
        } else {
            int idx = findSameorBig(T, 0, lastIdx, t);
            T[idx] = t;
        }
    }
    cout << lastIdx + 1;
    return 0;
}
```
