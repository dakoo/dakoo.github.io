---
layout: post
title: 알고리즘 - Longest Increasing Subsequence
description: LIS 
modified: 2016-02-09
tags: [algorithm, Dynamic Programming, LIS]
comments: true
image:
  feature: algorithm.jpeg
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

Longest Increasing Sequence는 "순서" 또는 "아이템들을 연속해서 조합할 때의 최대"  등을 구할 때 매우 많이 사용되는 방법이다. 
그런데 이때 보통 i번째 아이템에 대해 0부터 i-1까지의 아이템을 비교해서 최대값을 갱신하는 O(n*n) 알고리즘이 흔히 사용된다. 

{% highlight c %}
for(int i = 1; i<n; i++){
  for(int j = i - 1; j< i; j++){
    T[i] = max(T[i], T[j] + In[i]);
  }
}
{% endhighlight %}

그런데 이런 식으로 하면 n이 100000이 넘어가면 계산이 불가능해진다. 그러므로 O(NlogN)알고리즘을 사용할 수 있어야 한다. 
입력이 10000이하이면 구현이 용이한 O(n*n) 알고리즘을, 그 이상이면 O(NlogN) 알고리즘을 사용하는 것을 추천한다. 

### logN LIS

#### 필요한 자료구조 

- 입력 저장: int In[]
- 계산 중의 LIS 저장: int T[], 초기값 T[0] = 0
- 각 입력값 별 LIS시 이전 아이템 저장: int R[], 초기값 -1
- 현재의 LIS 길이 저장: int lastIdx, 초기값 0

{% highlight c %}
    vector<int> T(In.size(), 0);
    vector<int> R(In.size(), -1);
    int lastIdx = 0;
    T[0] = 0;
{% endhighlight %}

#### 계산 

 In[1]부터 마지막 아이템까지 진행하면서 

 In[T[lastIdx]] < In[i]이면,  (마지막 아이템과 비교해서 더 크면)
 R[i] = T[lastIdx], lastIdx++, T[lastIdx] = i

 In[T[lastIdx]] < In[i]가 아니고, In[T[0]] > In[T[i]]이면 (가장 작은 아이템과 비교해서 더 작으면)
 T[0] = i

 위의 조건에 만족하지 않으면 T[1]부터 T[lastIdx]중의 하나에 i값을 넣는다. 그 기준은 "In[i]의 같거나 큰 값 중 가장 작은 값"에 넣는 것이다. 이로 인해 1번에서는 넣지 않은 마지막 위치(lastIdx)에 넣어 질 수 있게 된다. 
 이렇게 하는 이유는 **항상 가장 작은 값으로 구성된 T를 유지**함으로써 LIS를 찾아낼 수 있도록 함이다. 

 항상 T[0]부터 T[lastIdx]이 가리키는 In[]값은 정렬되어 있으므로 logN탐색을 위해 [이분 탐색](http://hochulshin.com/algorithm-bisectional-search/)을 이용한다. 그렇게 T[j]가 찾아지면 R[i] = T[j-1], T[j] = i

{% highlight c %}
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
{% endhighlight %}
 
#### 결과 처리 

마지막 아이템까지 진행이 끝났을 때 lastIdx + 1가 LIS의 길이가 되며, R[]은 각 index보다 앞의 것을 가리키게 된다. 그래서 순서를 구할 때는 R을 이용해서 역순으로 찾는다.

{% highlight c %}
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
{% endhighlight %}

### Code

{% highlight c %}
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
{% endhighlight %}

### Reference

[Javacode](https://github.com/mission-peace/interview/blob/master/src/com/interview/array/LongestIncreasingSubSequenceOlogNMethod.java)와 [동영상 강의](https://www.youtube.com/watch?v=S9oUiVYEq7E)를 참고하자.

### 실전 문제들 

#### easy

##### [꼬인 전깃줄](https://www.acmicpc.net/source/1617745)

- **최대 100000개의 입력**이 들어오므로 O(n*n)으로 하면 timeout이 나므로 nlogn알고리즘을 이용해야 한다.  
- LIS의 구성을 묻지 않고 단순히 잘라야할 전기줄의 수를 묻는 문제이므로 R[]을 저장하지 않고 T[]만 이용해서 계산한다.

{% highlight c %}
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
{% endhighlight %}
