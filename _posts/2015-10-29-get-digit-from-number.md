---
layout: post
title: How to get digits from a number
modified: 2015-10-28
tags: [algorithm]
comments: true
image:
  feature: abstract-11.png
---

Let's figure out how to extract a digit from a number

### Pseudo code


{% highlight bash %}
getDigit(n){
  i = 1; //the most left position
  int bottomRemoved=n/i;
  while(bottomRemoved!=0){
     int pos = bottomRemoved%10; //get digit
     process (pos);
     i *= 10;
     bottomRemoved=n/i;
  }

}
{% endhighlight %}

### Simple example

The following example is a program to count digits fo a number that can divide the number except 0.

{% highlight c %}
#include <cmath>
#include <cstdio>
#include <vector>
#include <iostream>
#include <algorithm>
using namespace std;


/*
    integer N:
    Find the digits in this numberthat exactly divide N
*/

int main() {
    int T;
    cin >> T;
    while(T--){
        int N;
        cin >> N;
        int count = 0;
        for(int i=1; i<10000000000; i*=10 ){
            int bottomRemoved = N/i;
            if(bottomRemoved == 0)
                break;
            int pos = bottomRemoved%10;
            if(pos == 0)
                continue;
            if(N%pos ==0)
                count++;
        }
        cout << count <<endl;
    }
    return 0;
}

{% endhighlight %}
