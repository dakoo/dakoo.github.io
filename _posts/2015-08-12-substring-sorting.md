---
layout: post
title: Algorithm - 부분 문자열의 사전순 정렬
description: 부분 문자열의 사전순 정렬
modified: 2016-03-01
tags: [algorithm]
comments: true
image:
  feature: abstract-16.png
---


<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


### 문제

문자열이 주어졌을때 모든 가능한 부분 문자열을 사전순으로 정렬할 때 k번째 문자열을 찾아보자. 이때 중복되는 부분 문자열은 없어야 한다. 
예를 들어 문자열 'food'의 모든 가능한 부분 문자열을 사전순으로 정렬하면 다음과 같다. 

> d, f, fo, foo, food, o, od, oo, ood

### 아이다어 

입력 문자열의 모든 접미어에 대해 모든 접두어를 추출하면 모든 가능한 부분 문자열이 된다. 이때 중복이 발생하긴 한다. 그리고, 문자열의 길이는 그 문자열의 모든 접미어의 수와 일치하며, 또한 모든 접두어의 길이와도 일치한다. 
예를 들어 'food'의 경우 길이가 4인 문자열인데 4개의 접미어와 4개의 접두어를 가진다. 
'food'의 접미어와 사전순으로 정렬한 순서는 다음과 같다. 

> food, ood, od, d --> d, food, od, ood

'food'의 접두어는 다음과 같다. 

> f, fo, foo, food

위의  사전순으로 정렬된 4개의 접미어별로 각각 접두어를 뽑으면 다음과 같다.

- d --> d
- food --> f, fo, foo, food
- od --> o, od
- ood --> o, oo, ood

위의 도출된 접두어를 모으면 아래와 같다. 

> d, f, fo, foo, food, o, od, o, oo, ood

 이것이 'food'의 모든 부분 문자열이다. 그런데, ood 접미어의 접두어에 'o'가 중복되어 있으므로 이것을 제거해야 한다. 

### 문제 해결 flow
 
 1. 자료 구조를 준비한다. 
 char S[]: 입력 문자열 
 int A[]: 접미어 배열 //접미어 배열은 따로 문자열을 저장하는 것이 아니라 S[]의 index를 저장하기 위한 것
 int LCP[]: 최장 공통 접두어 길이 배열 //문자열간 공통 접두어의 길이 저장하기 위한 것으로 LCP[0]은 0으로 초기화 한다. 
 
 2. 입력 문자열의 모든 접미어를 뽑아서 사전순으로 정렬하여 접미어 배열을 만든다. 
 S[]의 접미어를 사전순으로 비교하여 index 0, 1, 2 ... 를 A[]에 저장한다. 예를 들어, food의 경우 food와 d를 비교하면 이것은 S[0]과 S[3]의 비교이다. d가 사전순으로 더 우선이므로 A[]에서 3이 0보다 앞에 위치해야 한다. 
 food의 경우 A[]는 {4, 0, 3, 2}이다. (d, food, od, ood)
 
 3. 접미어 배열에서 최장 공통 접두어 길이 배열을 만든다. 
 LCP[i]는 S[A[i-1]]과 S[A[i]]간의 최장 공통 접두어 길이를 저장한다. 예를 들어 od와 ood의 최장 공통 접두어는 'o'이며 길이는 1이다. 
 food의 경우 LCP[]는 {0,0,0,1}이다. 
 
 4. 접미어 배열과 최장 공통 접두어 길이 배열을 이용해 k번째 문자열을 반환한다. 
 접미어의 가능한 접두어 수(접미어의 길이)에서 LCP[i]를 빼면 그 접미어에 대해 중복을 제외한 부분 문자열의 수를 구할 수 있다. 그러므로 접미어에 대해 누적을 해서 k번째까지 도달하도록 하면 k번째 문자열이 무엇인지 구할 수 있다. 한 접미어의 유효 부분 문자열 수는 접미어 길이 -LCP이다.  
 food는 다음과 같다.  
 
| 접미어  |   시작 index  |  접두어수 | LCP  |  누적 유효 부분 문자열 수  |
|---|---|---|---|---|
|  d | 3  | 1  |  0 | 1  |
|  food | 0  |  4 | 0  |  5 |
|  od |  2 |  2 |  0 |  7 |
|  ood | 1  |  3 |  1 |  9 |

출력되어야 하는 부분 문자열은 해당 접미어의 index에서부터  LCP+(k-그이전까지의 누적 유효 부분 문자열 수)만큼의 거리만큼 출력하면 된다. 그런데 그 범위가 문자열의 유효 길이를 넘는다면 다음 접미어로 넘어간다. 그것을 일반화 하면 다음과 같다.
 
> 출력할 lastindex =  접미어의 시작 index + LCP + (k- 이전까지의 누적 유효 부분 문자열 수) - 1

'food'의 문자열 마지막 index는 3이고 k는 8이다.
`'od'에서 출력할 lastindex = 2(시작 index) + 0(LCP) + (8-5) - 1 = 4`. 
'food'의 마지막 index인 3보다 크므로 다음 접미어로 처리가 넘어간다. 
`'ood'에서 출력할 lastindex = 1(시작 index) + 1(LCP) + (8 - 7) - 1 = 2`. 
'food'의 마지막 index인 3보다 작으므로 여기서 출력을 한다. 시작 index인 1부터 2까지 출력하므로 'oo'를 출력하면 된다. 

### C 구현 

위의 아이디어와 문제 해결 flow를 C로 구현한 것은 아래와 같다. 

{% highlight bash %}
#include <stdio.h>
int length;
int Suffix[10];
int LCP[10];
char str[5] = "food";
char result[10];
/* 기본적인 스트링 처리 함수들 */
int m_strlen(char *s){
    int len = 0;
    while(s[len]!='\0'){
        len++;
    }
    return len;
}
int m_strcmp(char *a, char *b){
    int i = 0;
    while (a[i]!='\0') {
        if(a[i]!=b[i])
            break;
        i++;
    }
    return (a[i]-b[i]); //같으면 0, 문자열 a가 사전순으로 다 빠르면 음수, b가 더 빠르면 양수를 반환
}
void swap(int *a, int *b){
    int temp = *a;
    *a = *b;
    *b = temp;
}
/* 문자열을 사전순으로 비교하여 sorting*/
int partition(int low, int high){
    int index = low;
    for(int i = low; i<=high-1; i++){
        if(m_strcmp(&str[Suffix[i]], &str[Suffix[high]])<0){ //음수이면 사전순으로 정렬시 더 앞에 있다는 의미
            swap(&Suffix[index], &Suffix[i]);
            index++;
        }
    }
    swap(&Suffix[index], &Suffix[high]);
    return index;
}
void quicksort(int low, int high){
    if(low >= high)
        return;
    int pivot = partition(low, high);
    quicksort(low, pivot-1);
    quicksort(pivot+1, high);
}
/* 접미어 배열 생성*/
void generate_Suffix_array(void){
    length = m_strlen(str);
    str[length] = '\0';
    for(int i = 0; i<length; i++)   //set as index
        Suffix[i] = i;
    quicksort(0, length-1);
}
/*최장 공통 접두어 길이 배열*/
int get_lcp(char *a, char *b){
    int i = 0;
    while(a[i] == b[i])
        i++;
    return i;
}
void generate_LCP_array(void){
    LCP[0] = 0;
    for(int i = 1; i<length; i++){
        LCP[i] =get_lcp(&str[Suffix[i-1]], &str[Suffix[i]]);
    }
}
/* k번째 문자열 계산 및 출력 */
int get_kth_word(int k){
    int accummulated_num_words = 0;
    int index = 0;
    while(accummulated_num_words< k && index<length){
        int startIndex = Suffix[index];
        // 출력할 lastindex =  접미어의 시작 index + LCP + (k- 이전까지의 누적 유효 부분 문자열 수) - 1
        int lastIndex = startIndex + LCP[index] + (k-accummulated_num_words)-1;
        if(lastIndex < length) {//접미어내에 해당 문자열 존재하는 경우 
            int j = 0;
            for(int i = startIndex; i<= lastIndex; i++){
                result[j] = str[i];
                j++;
            }
            result[j] = '\0';
            return 1;
        }
        else   { //다음 접미어로 이동
            accummulated_num_words += m_strlen(&str[Suffix[index]]) - LCP[index];
            index++;
        }
    }
    return 0;
}
/* 테스트 코드: food문자열의 부분 문자열을 사전순으로 정렬했을때 8번째 문자열 출력하시오. 이때 중복 문자열은 제외해야 함*/
int main(){
    generate_Suffix_array();
    generate_LCP_array();
    int res = get_kth_word(8);
    if(res)
        printf("%s\n", result);
    else
        printf("none\n");
    return 0;
}
{% endhighlight %}

