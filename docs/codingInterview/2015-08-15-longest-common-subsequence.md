---
layout: post
title: Algorithm - LCS(Longest Common Subsequence) 알고리즘
modified: 2015-08-15
tags: [algorithm]
parent: Coding Interview
nav_order: 2
---

최장 공통 부분 수열(Longest Common Subsequence, LCS) 문제는 두 수열의 가장 긴 공통 부분 수열을 찾아내는 문제이다. 그 문제를 푸는 LCS 알고리즘에 대해 살펴보자.

### 문제

최장 공통 부분 수열(Longest Common Subsequence) 문제는 두 수열의 가장 긴 공통 부분 수열을 구하는 문제이다. 어떤 수열의 일부를 순서를 그대로 유지하여 나열한 것이 바로 부분 수열이 된다. 
예를 들어 문자열 'abc'의 부분 수열은 빈 문자열, a, b, c, ab, bc, ca, abc이다. 둘 이상의 수열이 있을 때, 공통된 가장 긴 부분 수열을 찾는 것이 LCS 문제이다. 이렇게 설명하면 조금 복잡한데 쉽게 말해서 두 수열을 앞에서 뒤로 비교할 때 가장 많이 일치하는 게 뭔지, 그리고 그 길이는 얼마나 긴지를 찾는 것이다. 예를 들어, 'abcdefg'와 'cdeabfg'가 있다고 하면, LCS는 'cdef'가 된다. 이 LCS를 이용해 diff를 구현할 수 있다. 두 파일의 문자열을 비교해서 LCS가 아닌 부분을 보여주는 것으로 구현하면 된다. 아래는 그 예를 보여준다.
 
<figure>
<img src="/images/lcs_problem.jpg" alt="longest common subsequence problem">
</figure>
 
### 아이디어

LCS의 아이디어는 단순하다. 수열 A 'abcd'와 수열  B 'bcf'를 살펴보자. 수열 A의 맨 뒤를 하나 삭제해 보자. 'abc'가 되는데 삭제된 'd'는 LCS에 포함되지 않기 때문에 LCS의 길이는 변함이 없다. 다음 수열 A의 맨뒤를 하나 더 삭제해 보자. 'ab'가 되는데 'c'가 수열 A에서 제거됨에 따라 LCS의 길이도 하나 줄어들게 된다. 이처럼 수열의 맨뒤를 제거하면 LCS의 길이는 하나 줄거나 그대로인 경우로 나뉘게 된다. 이것을 다음의 경우로 나누어 살펴보자.
 
#### 경우 1. 수열 A와 B 모두의 마지막이 공통 부분 수열에 속하는 경우

- A와 B의 **문자가 같다(A[i] == B[j])**
- A와 B의 LCS의 길이는 마지막 문자를 하나 줄인 수열 A와 마지막을 하나 줄인 수열 B간의 LCS에 공통 문자의 길이 1을 추가한 것과 같다 .
- 일반화 하면, 마지막 index가 각각 i, j일 경우 **A[i] == B[j]이면 LCS[i][j] = LCS[i-1][j-1] + 1**이다. 
- 예를 들면, 'abcd'와 'ad'의 LCS 길이는 'abc'와 'a'의 LCS길이 + 1이다 

#### 경우 2. 수열 A의마지막이 공통 부분 수열에 속하지 않는 경우

- A와 B의 **문자가 다르다(A[i] != B[j])**
- A와 B의 LCS는 마지막 문자를 하나 줄인 수열 A와 수열 B간의 LCS와 동일하다. 
- 일반화하면, **LCS[i][j] = LCS[i-1][j]**이다.
- 예를 들면, 'abcd'와 'ac'의 LCS길이는 'abc'와 'ac'의 길이와 같다. 

#### 경우 3. 수열 B의마지막이 공통 부분 수열에 속하지 않는 경우

- A와 B의 **문자가 다르다(A[i] != B[j])**
- A와 B의 LCS는 수열 A와 마지막 문자를 하나 줄인 수열 B간의 LCS와 동일하다. 
- 일반화하면, **LCS[i][j] = LCS[i][j-1]**이다.
- 예를 들면, 'abxc'와 'acd'의 LCS길이는 'abxc'와 'ac'의 길이와 같다. 

A[i]!=B[j]이면 **반드시** 두번째 또는 세번째 경우에 포함되며, LCS 길이는 **최대** 수열의 길이이므로 두 경우 중 길이가 긴 것과 같다. 

### 아이디어의 구현

위의 알고리즘을 배열을 이용해 용이하게 구현할 수 있다. String A는 "abcd", B는 "bca"라고 가정하자. LCS 배열을 다음과 같이 초기화 된다. 아래와 같이 LCS[0][]과 LCS[][0]을 모두 0으로 초기화 한다. 각 문자에 대응되는 LCS가 0이라고 초기화한 것이다.  

<figure>
<img src="/images/lcs1.jpg" alt="longest common subsequence table">
</figure>


- LCS[1][1]부터 계산을 시작한다. 
- A와 B의 첫번째 문자끼리 비교하는 것이다. 'a'와 'b'이므로 첫번째 경우가 아닌 두번째 혹은 세번째 경우에 속하는 것이다. 
- A[0]과 B[0]을 비교한 것이고, 서로 같지 않으므로 그 문자를 제거한 문자열간 LCS인 두번째와 세번째 경우에 해당되는 것이 된다. 물론 여기서는 맨 첫번째 문자끼리의 비교이므로 두번째와 세번째 경우 모두 0이 될 것이다. 
- 일반화 하자면, 이미 살펴본 아이디어처럼 A[i]과 B[j]가 같지 않은 경우 LCS[i][j]는 LCS[i-1][j] 또는 LCS[i][j-1] 중 큰 것의 값을 택하는 것이다. 
- 배열에서는 위 또는 좌 측의 값 중 큰 값을 택하는 것이 된다. 

<figure>
<img src="/images/lcs2.jpg" alt="longest common subsequence table">
</figure>

- 다음 A[1]과 B[0]를 비교한다. 
- 'b'와 'b'이므로 첫번째 경우에 속하게 되고, 그러면 A와 B에서 'b'를 제외했을때의 LCS 길이에 1을 더한 값이 LCS 길이가 된다. 물론, 이경우는 이전 값이 0이였으므로 이 연산의 결과가 1이 된다. 
- 일반화 한다면, A[i]와 B[j]가 같은 경우에는 LCS[i][j]는 LCS[i-1][j-1]에 1을 증가시킨 것이다. 
- 배열에서는 좌측 상단의 값 + 1과 같다.   

<figure>
<img src="/images/lcs3.jpg" alt="longest common subsequence table">
</figure>

- 이런 식으로 각 문자들을 모두 비교한다. A의 길이가 n이라고 하고 B의 길이를 m이라고 하면 n*m 번을 계산한다. 
- String A의 끝(n)과 String B의 끝(m)까지 비교하면 종료된다. 이때 LCS[n][m]의 값이 A와 B의 LCS 길이가 된다. 

<figure>
<img src="/images/lcs4.jpg" alt="longest common subsequence table">
</figure>

### 코드 

내용을 보면 알겠지만 위의 설명을 그대로 옮겨 놓은 것이다. 
간단히 지극히 c스럽게  c++로 구현 했다.

```cpp
#include <iostream>
#include <string>

#define MAX_SIZE 501

int main(void){
    //자료 구조
    int LCS[MAX_SIZE][MAX_SIZE];
    char first_string[MAX_SIZE];
    char second_string[MAX_SIZE];
    
    //두 문자열을 입력으로 받고 길이 확인
    std::cin >> first_string >> second_string;
    size_t length_first_string = strlen(first_string);
    size_t length_second_string = strlen(second_string);
    
    //알고리즘 구현
    for(int i = 1; i <= length_first_string; i++){
        for(int j = 1; j <= length_second_string; j++){
            if(first_string[i-1] == second_string[j-1]) //문자가 일치하는 경우 (1번 경우)
                LCS[i][j] = LCS[i-1][j-1]+1;
            else //일치하지 않는 경우 (2, 3번 경우)
                LCS[i][j] = LCS[i-1][j]> LCS[i][j-1]? LCS[i-1][j]: LCS[i][j-1];
        }
    }
    
    //결과 출력
    std::cout << "LCS: " << LCS[length_first_string][length_second_string];
    
    return 0;
}
```
