---
layout: post
title: Algorithm - 보이어-무어
description: 문자열 패턴 검색 - 보이어-무어 알고리즘에 대한 이해와 구현
modified: 2016-02-28
tags: [algorithm, string]
comments: true
image:
  feature: abstract-11.png
---

보이어-무어 알고리즘은 문자열(S[M])과 패턴 문자열(P[N])이 일치하는 지 검사하는 패턴 매칭 알고리즘으로, KMP 알고리즘과 함께 성능이 좋은 알고리즘으로 알려져 있다. 

아이디어는 간단하다. 문자열과 패턴 문자열을 비교할때 패턴 문자열의 맨 뒤 문자 위치를 비교하고, 다르면  문자열을 일정 길이만큼 skip해서 비교를 계속하는 것이다. 	

## pseudo code

{% highlight bash %}

SkipTable[256]; //256 ASCII charset 저장

generateSkipTable(P[], n) //P: pattern문자열, n: 패턴 문자열 길이
  loop i <- 0:n-1
     SkipTable[i] = n //패턴에 없는 문자일 경우 무조건 패턴 문자열 길이만큼 skip하도록
  loop i <- P[]  
     SkipTable[P[i]] = n - 1 - i  //패턴에 있는 문자일 경우 패턴의 끝과 그 문자와의 거리만큼을 skip 길이로 지정

search(S[], m, P[], n) //S: 문자열, m: S의 길이, P:패턴 문자열, n: 패턴 문자열 길이
  index = n - 1 // 패턴 문자열의 마지막부터 비교 
  loop (index < m)
	if S[index] == P[n-1] 
		if S[index]과 P[]을 뒤에서부터 비교해서 일치하는 경우
			return index - n + 1  //찾았다!
                 else //일치하지 않으면 
		        틀린 문자가 있는 index로 index를 update                     
        index = index + SkipTable[S[index]]; //틀린 문자에 해당되는 거리만큼 jump
  return -1 //not found
{% endhighlight %}

## c로 구현 

위의 pseudo 코드를 C로 구현해보자.

{% highlight c %}
#include <stdio.h>

int SkipTable[256];
void generate_SkipTable(char pattern[], int pattern_length){
	for (int i = 0; i < 256; i++)
		SkipTable[i] = pattern_length;
	for (int i = 0; i < pattern_length; i++)
		SkipTable[pattern[i]] = pattern_length - 1 - i;
}

// return: if matched -1, otherwise index in the pattern
int my_strcmp(char str[], int str_index, char pattern[], int pattern_length){
	int index = pattern_length - 1;
	while (str[str_index] == pattern[index] && index >= 0){
		str_index--;
		index--;
	}
	return index;
}

int search(char str[], int str_length, int start_index, char pattern[], int pattern_length){
	int index = start_index + pattern_length - 1; //starting point
	while (index < str_length){
		if (str[index] == pattern[pattern_length - 1]){
			int p_index = my_strcmp(str, index, pattern, pattern_length);
			if (p_index == -1){
				return index - pattern_length + 1; //found
			}
			index = index - pattern_length + 1 + p_index; //틀린 문자가 있는 위치
		}
		index = index + SkipTable[str[index]];	//틀린 문자에 해당되는 거리만큼 jump
	}
	return -1; //not found
}
{% endhighlight %}

위의 함수를 이제 테스트 해보자. 이때 문자열에 패턴의 여러개 반복되는 경우 이를 모두 찾아서 위치를 출력하는 형태로 테스트한다. 

{% highlight c %}
int main(){
	char str[37] = "I love yoe ve move. Plovse, love me.";
	int str_length = 36;
	char pattern[5] = "love";
	int pattern_length = 4;
	
	generate_SkipTable(pattern, pattern_length);

	int found = search(str, str_length, 0, pattern, pattern_length);
	while (found != -1 && found < str_length){
		printf("Found at %d\n", found);
		found = search(str, str_length, found + pattern_length, pattern, pattern_length);
	}
	return 0;
}
{% endhighlight %}
