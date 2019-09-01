---
layout: post
title: Algorithm - 독립 시행 확률
description: 독립 시행 확률
modified: 2015-08-16
tags: [algorithm]
parent: Coding Interview
nav_order: 2
---

예제를 통해 독립 시행 확률을 계산하는 방법을 알아보자.

### 문제

가구를 만드는 공장이 있다. 숙련된 가구 장인은 혼자서 하나의 가구를 5분만에 만들어 낸다. 그러나 가구를 만들어도 결함이 있을 수 있다. 결함이 없는 가구를 완제품이라고 한다. 사장은 두 명의 가구 장인에게 90분동안 가구를 만드는 시함을 시켰다. 그런데 사장은 두 가구 장인이 만들어 내는 완제품의 수가 최소한 한 명이라도 소 수 일 확률이 궁금해 졌다. 

가구 장인 A, B가 있을 때 평소 각 장인의 5분안에 완제품을 만들 확률이 주어진다. pA는 A장인이 5분안에 완제품을 만들 확률이며, pB는 B장인이 5분안에 완제품을 만들 확률이다. 각 가구 장인이 만든 완제품의 수가 최소 한 명이라도 소수일 확률을 구하는 프로그램을 작성하라. 예를 들어 pA가 100%, pB가 100%일 경우 최종적으로 A는 18개, B도 18개를 만들어 내고 18은 소수가 아니기 때문에 소수일 확률은 0.00이다. 

#### 제약 사항 ####

0<= pA, pB <= 100

#### 입력 ####

맨 위줄에 테스트 케이스 수가 주어지고, 그 뒤 한 줄씩 테스트 케이스의 입력 pA와 pB가 주어진다. 

```
2
0 0
80 90
```

#### 출력 ####

완제품이 최소 한명이라도 소수일 확률을 각 테스트 케이스 별로 출력한다. 이때 확률의 출력은 소수점 6자리를 넘어가면 소수점 7번 자리에서 반올림한다. 

```
#1 0.000000
#2 0.503683
```

### 아이디어 

18번의 시도를 통해 2개, 3개, 5개... 17개 등 소수 개의 완성품을 확률을 각기 계산하여 합산하면 된다. 그렇게 하기 위해서는 n번의 서로 독립적인 사건 중 r번이 발생할 확률이 얼마인지 계산하는 독립 시행 확률을 계산할 수 있어야 한다. 그리고, A와 B 장인의 확률의 합쳐서 계산해야 하므로 확률의 덧셈정리에 대한 이해도 필요하다. 

#### 독립 시행 확률

독립 시행이란 동전이나 주사위를 여러 번 던질 때와 같이 어떤 시행을 계속해서 되풀이 할 때, 매번 일어나는 사건이 서로 독립적인 경우 즉, 각 시행의 결과가 다른 시행의 결과에 영향을 미치지 않는 시행을 말한다. 독립 시행의 확률은 어떤 사건 A가 일어날 확률이 p이고, 그 여사건이 일어날 확률이 q(q=1-p)일때, n번의 독립 시행에서 사건 A가 r번 일어날 확률은 다음과 같다. 

> P(r) = nCr * p의 r제곱 승 * q의 n-r 제곱 승 (r=0, 1, 2, 3, ..., n)

#### 조합 

서로 다른 n개에서 순서를 생각하지 않고 r개(0<=r<=n)를 택하는 것을 조합이라 하며 그 조합의 수는 다음과 같이 계산한다. 

> nCr = n factorial / (r factorial * n-r factorial) 

참고로 순서를 감안한 경우의 수인 순열은 다음과 같다. 서로 다른 n개 중 r개를 택해 순서대로 나열하는 방법의 수

> nPr =  n factorial / n-r factorial

#### 확률의 덧셈 정리

두 사건 A, B에 대해 사건 A *또는* B가 일어날 확률은 다음과 같다. 

> P(AUB) = P(A) + P(B) - P(A와 B의 교집합)

즉, A의 확률과 B의 확률을 더하고, A와 B가 동시에 일어날 확률을 빼면된다. 
 A와 B가 서로 독립적인 것이라면 A와 B가 동시에 발생할 확률은 두 확률을 곱한 확률과 같다. 

>P(A와 B의 교집합) =  P(A)*P(B)

### C 구현 

```c
#include <stdio.h>

#define SIZE 19 //총 19 케이스 저장(0:18번 중 0개 완성품, 1:18번중 1개 완성품, ... 18:18번중 18개 완성품)

/* Composition을 구하기 위한 factorial 값 저장*/
long long Factorial[SIZE];
long long factorial(int n){
    if(n==1){
        Factorial[0] = 0;
        Factorial[1] = 1;
        return 1;
    }
    long long res = n*factorial(n-1);
    Factorial[n]=res;
    return res;
}
/* n개 중 r개를 선택하는 경우의 수 계산: nCr
   nCr = nfactorial/(rfactorial * (n-r)fractorial) */
int composition(int n, int r){
    if(r == 0 || r==n)
        return 1;
    long long res = Factorial[n]/Factorial[r];
    res /= Factorial[n-r];
    return res;
}

/* 18번 시도하여 소수개의 완성품을 얻을 확률을 그 갯수별로 각각 계산. - 독립 시행 수행 확률 계산 */
double cal(int i_p){
    if(i_p == 0){
        return 0;
    }
    int prime_numbers[7] = {2, 3, 5, 7, 11, 13, 17}; //19의 소수들
    double t_p = i_p;
    double p = t_p/100;//정수 확률을 소수 확률로 전환

    //독립 시행 확률 계산을 위해 제곱승을 각기 계산
    double P[19];   //독립 시행 확률 계산을 위한 확률 p의 r제곱승을 저장하기 위한 배열
    double Q[19];   //독립 시행 확률 계산을 위한 확률 p의 여확률(1-p)의 r제곱승을 저장하기 위한 배열
    P[1] = p;
    Q[1] = 1-p;
    for(int i = 2; i<18; i++){
        P[i] = P[i-1]*p;
        Q[i] = Q[i-1]*(1-p);
    }
    double res = 0;
    //소수 개를 완성품으로 만들 확률
    for(int i = 0; i<7; i++){
        //독립 시행 확률 계산
        //발생할 확률 p를 가진 독립적인 사건을 n번 수행했을 때 r번 그 사건이 발생할 확률 = nCr * p의 r제곱승 * p의 여확률의 n-r 제곱승
        //독립적인 사건이란 한번의 수행 결과가 다음 수행에 영향을 주지 않느 사건: 예- 동전 던져서 앞면이 나올 확률
        res += composition(18, prime_numbers[i])*P[prime_numbers[i]]*Q[18-prime_numbers[i]];
    }
    return res;
}

int main(){
    int testcase;
    int T;
    scanf("%d", &T);
    factorial(18);
    for(testcase = 1; testcase<=T; ++testcase){
        int p_a, p_b;
        scanf("%d %d", &p_a, &p_b);
        double res_a = cal(p_a);
        double res_b = cal(p_b);
        double res = res_a + res_b - res_a*res_b; //동시에 소수가 나올 확률을 제외해야 함
        printf("#%d ", testcase);
        printf("%.6f ", res);
    }
    return 0;
}
```
