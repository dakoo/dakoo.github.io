---
layout: post
title: 문자열을 다루기 위한 기본 함수들
description: 문자열을 다루기 위한 기본 함수들 
modified: 2015-08-11
tags: [algorithm]
image:
  feature: abstract-15.png
---

C언어에서 문자열을 다루기 위한 기본 함수를 구현해보자. string.h에 포함되어 있는 함수이지만 stdio.h만을 이용해 구현한다. 

### 문자열 복사

{% highlight c %}
#include <stdio.h>
void m_strcpy(char *s, char *d){
    while(*s!='\0'){
        *d = *s;
        s++;
        d++;
    }
    *d = '\0';
}
{% endhighlight %}

### 문자열 비교

{% highlight c %}
int m_strcmp(char *a, char *b){
    int i = 0;
    while (a[i]!='\0') {
        if(a[i]!=b[i])
            break;
        i++;
    }
    return (a[i]-b[i]); //같으면 0, 문자열 a가 사전순으로 다 빠르면 음수, b가 더 빠르면 양수를 반환
}
{% endhighlight %}

### 문자열을 숫자로 변환

{% highlight c %}
int m_atoi(const char* str){
    int value = 0, digit, c;
    while((c=*str++)!='\0'){
        if(c>='0'&&c<='9')
            digit = c-'0';
        else
            break;
        value = (value*10)+digit;
    }
    return value;
}
{% endhighlight %}

### 테스트 코드 

{% highlight c %}
int main(){
    char a[5] = "1234";
    char b[5];
    m_strcpy(a, b);
    printf("%s\n", b); // '1234'
    int output = m_strcmp(a, b);
    if(output == 0)
        printf("a and b have the same value\n");
    else
        printf("a and b don't have the same value\n");
    printf("%d\n", m_atoi(b));
    return 0;
}
{% endhighlight %}
