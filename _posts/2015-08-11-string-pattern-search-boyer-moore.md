---
layout: post
title: String pattern search algorithm - Boyer-Moore 
description: String pattern search algorithm - Boyer-Moore
modified: 2015-08-11
tags: [algorithm, english]
image:
  feature: abstract-8.png
---

Boyer-Moore alogirithm is a pattern search algorithm in string. The performance of the algorithm is excellent as well as KMP algorithm. The idea is simple. Comparison between target string and pattern is performed from the end of the pattern. If mismatch happens at a character in string, comparison can be skipped as the distance that calculated for the character in advance. 

## pseudo code

{% highlight bash %}

SkipTable[256]; //To store the preprocessed distance for 256 ASCII charset

generateSkipTable(P[], n) //P: pattern string, n: pattern length
  loop i <- 0:n-1
     SkipTable[i] = n //If the character is not in pattern, the skip distance is n.
  loop i <- P[]  
     SkipTable[P[i]] = n - 1 - i  //If the character is in pattern, the skip distance is the distance between the character and the end of the pattern.

search(S[], m, P[], n) //S: target string, m: S length, P: pattern string, n: pattern length
  index = n - 1 // Comparsion starts from the end of pattern string 
  loop (index < m)
	if S[index] == P[n-1] //The character of target string is the same as the last character of pattern 
		if the all characters in pattern are found in the string 
			return index - n + 1  //Return the index that pattern string starts at.
        else //mismatch happens at a specific position  
		     index = the position that mismatch happens //Update index                     
        index = index + SkipTable[S[index]]; //Skip
  return -1 //not found
{% endhighlight %}

## C implementation 

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
			index = index - pattern_length + 1 + p_index; //Update index to point out the position hat mismatch happened.    
		}
		index = index + SkipTable[str[index]];	//Skip
	}
	return -1; //not found
}
{% endhighlight %}

Let's test the functions above. The following example is to search a repeated pattern in a string.

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
