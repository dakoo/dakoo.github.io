---
layout: post
title: 순열과 조합
description: 순열과 조합 
modified: 2015-08-15
tags: [algorithm]
comments: true
image:
  feature: abstract-18.png
---

순열, 중복 순열, 조합, 중복 조합에 대한 이해를 해보자. 

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

### 순열, 중복 순열, 조합, 중복 조합의 비교 

1. 순열: 중복없이 n개 중에서 r개를 뽑아 순서를 정해 나열하는 경우. 예를 들어 다섯 개의 문자 a, a, a, b, b를 일렬로 배열하는 방법의 수
2. 중복 순열: n개 중에서 r개를 뽑아 순서를 정해 나열하는 경우,같은 것(종류)을 다시 뽑을 수 있다. 예를 들어, 숫자 1,2,3을 이용해 만들수 있는 4자리 정수의 개수를 구하는데 같은 숫자를 반복할 수 있는 경우. 
3. 조합: 중복없이 n개 중에서 r개를 뽑는 경우. 뽑히는 순서는 상관없다. 예를 들어 서로 다른 5통의 편지를 우체통 3개에 넣는 방법의 수. 
4. 중복 조합: n개 중에서 r개를 뽑는데 같은 것(종류)를 뽑을 수 있으며 뽑히는 순서는 상관없다. 예를 들어 6명의 선거인이 2명의 후보자에게 무기명으로 투표하는 방법의 수

### 순열 

세개의 문자 a,b,c 중 2개를 택해 일렬로 배열하는 방법의 수는 첫 번째 자리의 문자를 택하는 가짓수(3)와 남은 문자 중에서 두번째 자리에 놓을 가짓수(2)를 곱한 것과 같다. 이처럼 서로 다른 n가지에서 r가지를 택하는 경우의 수는 nPr로 표시할 수 있다.

> nPr = n!/(n-r)!, nP0는 1, nPn은 1(모두 선택하거나, 아무것도 선택하지 않을 경우의 수는 1)

#### 구현

nPr을 이루는 각 경우를 뽑아내기 위한 구현의 예제는 다음과 같다. 1, 2, 3, 4 숫자 4개가 주어지고 이 중 3개를 뽑는 순열이다.  

{% highlight c %}
#include <stdio.h>

int T[10]; //nPr을 이루는 각각의 경우를 저장
int data[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 0};

void swap(int *i, int *j){
    int temp = *i;
    *i = *j;
    *j = temp;
}
void process(int q){
    for(int i = q-1; i>= 0; i--){
            printf("%d ", T[i]);
    }
    printf("\n");
}
void Perm(int n, int r, int q){
    if(r == 0){
        process(q);
        return;
    }
    for(int i = n-1; i>=0; i--){
        swap(&data[i], &data[n-1]); //n-1을 모든 index와 swap해서 다양한 순서를 만든다.
        T[r-1] = data[n-1];		  //T의 뒤에서부터 결과값 저장	
        Perm(n-1, r-1, q);		  //다음  index로 진행 	
        swap(&data[i], &data[n-1]);
    }
}
int main(void){
    Perm(4, 3, 3);
    return 0;
}
{% endhighlight %}

### 중복 순열
 
서로 다른 n개의 중복을 허용하여 r개를 택하여 나열하는 방법을 n개에서 r개를 택하는 중복 순열이라고 한다. 
 
> 중복 순열 nㅠr = n의 r제곱승
 
#### 구현

중복 순열의 각 경우를 출력하는 코드를 순열 구현 코드와 비교하면서 확인해보자. 1, 2, 3, 4 숫자 4개가 주어지고 이 중 3개를 뽑는데 같은 숫자가 반복되어도 가능하다.  

{% highlight c %}
#include <stdio.h>

int T[10]; //nPr을 이루는 각각의 경우를 저장
int data[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 0};

void swap(int *i, int *j){
    int temp = *i;
    *i = *j;
    *j = temp;
}

void process(int q){
    for(int i = q-1; i>= 0; i--){
            printf("%d ", T[i]);
    }
    printf("\n");
}

void PI(int n, int r, int q){
    if(r == 0){
        process(q);
        return;
    }
    for(int i = n-1; i>=0; i--){
        swap(&data[i], &data[n-1]);
        T[r-1] = data[n-1];
        PI(n, r-1, q);	//유일하게 다른 부분. 1가지를 선택한 후 선택할 수 있는 종류를 줄이지 않음.(n)
        swap(&data[i], &data[n-1]);
    }
}

int main(void){
    PI(4, 3, 3);
    return 0;
}
{% endhighlight %}

### 조합
 
서로 다른 n개에서 순서를 생각하지 않고 r개(0<=r<=n)를 택하는 것을 조합이라고 한다. 
  
> nCr = nPr을 r!로 나눈 것 = n!/(r!*(n-r)!), nC0과 nCn은 1이다. 
 
nCr은 다음과 같은 관계를 가진다. 
 
> nCr = n-1Cr-1 + n-1Cr
 
참고로, 서로 다른 n개의 물건을 p개, q개, r개의 3개의 그룹으로 나누는 방법은 다음과 같다. 
 
> nCp * n-pCq * rCr
 
예를 들어, 서로 다른 종류의 꽃 15송이를 다섯 송이씩 세묶음으로 나누는 방법의 수는 15C10 * 10C5 * 5C5이다.  
 
#### 구현
 
조합을 한 각 경우를 출력하는 것을 생각해보자. `nCr = n-1Cr-1 + n-1Cr, nC0 = 1` 관계를 이용한다. 1, 2, 3, 4 숫자 4개가 주어졌을때 순서 상관없이 3개씩 묶어 그룹을 만들 때 그 멤버를 출력한다. 
 
{% highlight c %}
#include <stdio.h>

int T[10]; //nPr을 이루는 각각의 경우를 저장
int data[10] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 0};

void swap(int *i, int *j){
    int temp = *i;
    *i = *j;
    *j = temp;
}

void process(int q){
    for(int i = q-1; i>= 0; i--){
            printf("%d ", T[i]);
    }
    printf("\n");
}

void Comb(int n, int r, int q){
    if(r == 0){
        process(q);
        return;
    }else if(n<r){
        return;
    }
    else {  //loop이 아님
        T[r-1] = data[n-1];
        Comb(n-1, r-1, q);  //n-1Cr-1
        Comb(n-1, r, q);    //n-1Cr
    }
}

int main(void){
    Comb(4, 3, 3);
    return 0;
}
{% endhighlight %}

### 중복 조합
 
서로 다른 n개에서 순서를 생각하지 않고 중복을 허용하여 r개(0<=r<=n)를 택하는 것을 중복 조합이라고 한다.  예를 들어, 숫자 1, 2에서 중복을 허용하여 3개를 선택하는 조합은 (1,1,1)(1,1,2)(1,2,2)(2,2,2)이다 
  
> nHr = n+r-1Cr 
 
nHr은 다음과 같은 관계를 가진다. 
 
> nHr = nHr-1 + n-1Hr
 
