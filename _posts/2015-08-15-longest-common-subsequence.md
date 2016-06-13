---
layout: post
title: LCS(Longest Common Subsequence)알고리즘 
description: LCS(Longest Common Subsequence)알고리즘
modified: 2015-08-15
tags: [algorithm]
comments: true
image:
  feature: abstract-17.png
---

최장 공통 부분 수열(Longest Common Subsequence, LCS) 문제는 두 수열의 가장 긴 공토 부분 수열을 찾아내는 문제이다. 그 문제를 푸는 LCS 알고리즘에 대해 살펴보자.

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

### 문제

- 최장 공통 부분 수열(Longest Common Subsequence) 문제는 두 수열의 가장 긴 공통 부분 수열을 구하는 문제이다. 어떤 수열의 일부를 순서를 그대로 유지하여 나열한 것이 바로 부분 수열이 된다. 예를 들어, 문자열 'abc'이 있을때 그 부분 수열은 빈 문자열, a, b, c, ab, bc, ca, abc이다. 
- 둘 이상의 수열이 있을 때, 공통된 가장 긴 부분 수열을 찾는 것이 LCS 문제이다. 예를 들어, 'abcdefg'와 'cdeabfg'가 있다고 하면, LCS는 'cdef'가 된다. 
- LCS를 이용해 diff를 구현할 수 있다. 아래는 두 문자열을 비교해서 LCS가 아닌 부분을 보여주는 것으로 diff의 예이다. 공통으로 나타나는 부분 수열(LCS)인 'AAAATTCA'를 제외한 나머지가 다른 부분이 되는 것이다. 
 
<figure>
<img src="/images/lcs_problem.jpg" alt="longest common subsequence problem">
</figure>
 
### 아이디어

길이 3인 수열 A 'bca'와 길이 4인 수열  B 'abcd'를 살펴보자. LCS는 'bc'이라는 것을 알고 다음 내용을 살펴보자. 

- 수열 A의 맨 뒤 문자인 'a'를 삭제해 보자. 맨 뒷 문자가 제거된 A는 'bc'가 된다. 이때, 삭제된 맨 뒤의 'a'는 LCS에 포함되지 않기 때문에, 'a'가 삭제된 수열 A와 B를 비교해도 LCS의 길이는 변함이 없다. 
- 다음 'a'가 삭제된 수열 A의 맨 뒤 문자인 'c'를 삭제해 보자. 수열 A는 'b'가 되는데 'c'가 수열 A에서 제거됨에 따라 LCS의 길이도 하나 줄어들게 된다.
- 이처럼 수열의 맨뒤를 제거하면 LCS의 길이는 하나 줄거나 그대로인 경우로 나뉘게 된다. 

위의 아이디어를 이용하여 다음을 도출할 수 있다 .
 
#### 경우 1: 수열 A와 B 모두의 마지막 문자가 공통 부분 수열에 속하는 경우

- A와 B의 **마지막 문자가 같다(A[i] == B[j])**
- 수열 A와 B의 LCS 길이는 마지막 문자를 하나 삭제한 수열 A와 마지막 문자를 하나 삭제한 수열 B간의 LCS에 길이 1을 더한 것이다. 
- 즉, 마지막 index가 각각 i, j일 경우 **A[i] == B[j]이면 LCS[i][j] = LCS[i-1][j-1] + 1**이다. 
- 예를 들어 수열 A 'abcd'와 수열 B 'axd'인 경우 A와 B의 LCS길이는 'abc'와 'ax'간의 LCS길이에 'd'의 길이 1을 더한 것과 같다. 


#### 경우 2: 수열 A의  마지막이 공통 부분 수열에 속하지 않는 경우

- A와 B의 **마지막 문자가 다르다(A[i] != B[j])**
- 수열 A와 B의 LCS 길이는 마지막 문자를 하나 삭제한 수열 A와 수열 B간의 LCS와 같다. 
- 이 경우의 **LCS[i][j] = LCS[i-1][j]**이다. 
- 예를 들어 수열 A 'abce'와 수열 B 'ac'인 경우 A와 B의 LCS길이는 'abc'와 'ac'간의 LCS길이와 같다. 

#### 경우 3: 수열 B의 마지막이 공통 부분 수열에 속하지 않는 경우

- A와 B의 **마지막 내용이 다르고(A[i] != B[j])**
- 수열 A와 B의 LCS 길이는 마지막 문자를 하나 삭제한 수열 B와 수열 A간의 LCS와 같다. 
- 이 경우 **LCS[i][j] = LCS[i][j-1]**이다. 
- 예를 들어 수열 A 'abce'와 수열 B 'aex'인 경우 A와 B의 LCS길이는 'abce'와 'ae'간의 LCS길이와 같다. 

A[i]!=B[j]이면 반드시 두번째 또는 세번째 경우에 포함된다. 그리고, LCS 길이는 최대 수열의 길이이므로 두 경우 중 길이가 긴 것과 같다. 

### 아이디어의 구현

위의 아이디어는 맨 뒤부터 비교하는 것처럼 되어 있지만, 사실 그럴 필요는 없고, 맨 처음 문자부터 하나씩 비교하면 된다. 두 문자열의 문자들을 앞에서부터 비교하여 서로 같으면 첫번째 경우이고, 서로 다르면 두번째와 세번째 경우 중 하나에 속한다고 보는 것이다.  

#### 2차원 배열을 이용한 구현

String A는 "abcd", B는 "bca"라고 가정하자. LCS 배열을 다음과 같이 초기화 된다. 아래와 같이 LCS[0][]과 LCS[][0]을 모두 0으로 초기화 한다. 

<figure>
<img src="/images/lcs1.jpg" alt="longest common subsequence table">
</figure>


LCS[1][1]부터 계산을 시작하는데 A[i]과 B[j]가 같지 않은 경우 LCS[i][j]는 LCS[i-1][j] 또는 LCS[i][j-1] 중 큰 것의 값과 같다. 즉, 배열에서는 위 또는 좌 측의 값 중 큰 값을 택한다. 

<figure>
<img src="/images/lcs2.jpg" alt="longest common subsequence table">
</figure>


A[i]와 B[j]가 같은 경우에는 LCS[i][j]는 LCS[i-1][j-1]에 1을 증가시킨 것이다. 배열에서는 좌측 상단의 값 + 1과 같다.   

<figure>
<img src="/images/lcs3.jpg" alt="longest common subsequence table">
</figure>


String A의 끝(n)과 String B의 끝(m)까지 비교하면 종료된다. 이때 LCS[n][m]의 값이 A와 B의 LCS 길이가 된다. 

<figure>
<img src="/images/lcs4.jpg" alt="longest common subsequence table">
</figure>

### C를 이용한 구현

{% highlight c %}
#include <stdio.h>

#define MAX_SIZE 501
int LCS[MAX_SIZE][MAX_SIZE];
int cal(char x[], char y[], int len_x, int len_y){
    for(int i = 1; i <= len_y; i++){
        for(int j = 1; j <= len_y; j++){
            if(x[i-1] == y[j-1])
                LCS[i][j] = LCS[i-1][j-1]+1;
            else
                LCS[i][j] = LCS[i-1][j]> LCS[i][j-1]? LCS[i-1][j]: LCS[i][j-1];
        }
    }
    return LCS[len_y][len_x];
}

int main(void){
    int test_case;
    int T;
    setbuf(stdout, NULL);
    scanf("%d", &T);
    for(test_case = 1; test_case <= T; ++test_case){
        int len;
        char x[MAX_SIZE];
        char y[MAX_SIZE];
        scanf("%s", x);
        scanf("%s", y);
        for(int i = 0; i<= len; i++){
            for(int j = 0; j<= len; j++){
                LCS[i][j] = 0;
            }
        }
        double l = len;
        double d = cal(x, y, len, len);
        double res = d/l*100;
        printf("#%d %.2f", test_case, res);
    }
    return 0;
}
{% endhighlight %}
