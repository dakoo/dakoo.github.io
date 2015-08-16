---
layout: post
title: LCS(Longest Common Subsequence)알고리즘 
description: LCS(Longest Common Subsequence)알고리즘
modified: 2015-08-15
tags: [algorithm]
image:
  feature: abstract-17.png
---

### 문제

최장 공통 부분 수열(Longest Common Subsequence) 문제는 두 수열의 가장 긴 공통 부분 수열을 구하는 문제이다. 어떤 수열의 일부를 순서를 그대로 유지하여 나열한 것이 바로 부분 수열이 된다. 예를 들어 문자열 'abc'의 부분 수열은 빈 문자열, a, b, c, ab, bc, ca, abc이다. 둘 이상의 수열이 있을 때, 공통된 가장 긴 부분 수열을 찾는 것이 LCS 문제이다. 예를 들어, 'abcdefg'와 'cdeabfg'가 있다고 하면, LCS는 'cdef'가 된다. 이 LCS를 이용해 diff를 구현할 수 있다. 두 파일의 문자열을 비교해서 LCS가 아닌 부분을 보여주는 것으로 구현하면 된다. 
 
<figure>
<img src="/images/lcs_problem.jpg" alt="longest common subsequence problem">
</figure>
 
### 아이디어

위의 길이 n(7)인 수열 A 'abcdefg'와 길이 m(8)인 수열  B 'cdeabfgh'를 살펴보자. 수열 A의 맨 뒤를 하나 삭제해 보자. 'abcdef'가 되는데 수열 맨 뒤의 'g'는 LCS에 포함되지 않기 때문에 LCS의 길이는 변함이 없다. 다음 수열 A의 맨뒤를 하나 더 삭제해 보자. 'abcde'가 되는데 'f'가 수열 A에서 제거됨에 따라 LCS의 길이도 하나 줄어들게 된다. 이처럼 수열의 맨뒤를 제거하면 LCS의 길이는 하나 줄거나 그대로인 경우로 나뉘게 된다. 이것을 다음의 경우로 나누어 살펴보자.
 
1. 수열 A와 B 모두의 마지막이 공통 부분 수열에 속하는 경우: A와 B의 **마지막 내용이 같고(A[i] == B[j])**, LCS의 길이는 수열 A의 마지막을 하나 줄인 수열 A[1~i-1]과 B의 마지막을 하나 줄인 수열 B[1~j-1]의 LCS에 길이 1이 추가 된 것과 같다. 즉, 마지막 index가 각각 i, j일 경우 **A[i] == B[j]이면 LCS[i][j] = LCS[i-1][j-1]**이다. 
2. 수열 A의  마지막이 공통 부분 수열에 속하지 않는 경우: A와 B의 **마지막 내용이 다르고(A[i] != B[j])**, A와 B의 LCS는 A의 길이를 하나 줄인 수열 A[1~i-1]과 수열 B[1~j]의 LCS와 동일하다. 이 경우의 **LCS[i][j] = LCS[i-1][j]**이다. 
3. 수열 B의 마지막이 공통 부분 수열에 속하지 않는 경우: A와 B의 **마지막 내용이 다르고(A[i] != B[j])**, A와 B의 LCS는 B의 길이를 하나 줄인 수열 B[1~j-1]과 수열 A[1~i]의 LCS와 동일하다. 이 경우 **LCS[i][j] = LCS[i][j-1]**이다. 

**A[i]!=B[j]이면 반드시 두번째 또는 세번째 경우에 포함되며, LCS 길이는 최대 수열의 길이이므로 두 경우 중 길이가 긴 것과 같다. 

### 배열을 이용한 위 아이디어의 구현

위의 알고리즘을 배열을 이용해 용이하게 구현할 수 있다. 
String A는 "abcd", B는 "bca"라고 가정하자. LCS 배열을 다음과 같이 초기화 된다. LCS[0][]과 LCS[][0]을 모두 0으로 초기화 한다. 

<figure>
<img src="/images/lcs1.jpg" alt="longest common subsequence table">
</figure>

LCS[1][1]부터 계산을 시작하는데 A[i]과 B[j]가 같지 않은 경우 LCS[i][j]는 LCS[i-1][j] 또는 LCS[i][j-1] 중 큰 것의 값과 같다. 즉, 배열에서는 위와 좌측의 값 중 큰 값을 택한다. 

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
