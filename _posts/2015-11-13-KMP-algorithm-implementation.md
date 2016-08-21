---
layout: post
title: Algorithm - KMP algorithm to search a pattern in a string
modified: 2015-11-15
tags: [algorithm]
comments: true
image:
  feature: abstract-12.png
---

KMP algorithm implementation in C++.
KMP algorithm is very famous algorithm to search a pattern in a string.

### code

{% highlight c %}
#include <iostream>

using namespace std;

int mstrlen(char *s){
    int c = 0;
    while(*s++) c++;
    return c;
}
int Table[100];
void makeTable(char P[], int plen){
    int i = 0;
    int j = -1;
    Table[0] = -1;
    while(i < plen){
        if(j == -1 || P[i] == P[j]){
            i++;j++;
            Table[i] = j;
        }else{
            j = Table[j];
        }
    }
}
int search(char S[], int slen, int index, char P[], int plen){
    int i = index;
    int j = 0;
    while(i < slen){
        if(j == -1 || S[i] == P[j]){
            j++;i++;
        }else {
            j = Table[j];
        }
        if( j == plen) 
            return i - plen;
    }
    return -1;
}
int main(){
    char S[100];
    char P[100];
    cin >> S >> P;
    int slen = mstrlen(S);
    int plen = mstrlen(P);
    makeTable(P, plen);
    int found = search(S, slen, 0, P, plen);
    while(found!=-1){
        cout << found << " ";
        found = search(S, slen, found+plen, P, plen);
    }
    return 0;
}
{% endhighlight %}
