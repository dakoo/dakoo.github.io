---
layout: post
title: C++ - How to figure out the unknown number of inputs
modified: 2015-10-28
tags: [c++]
comments: true
image:
  feature: abstract-10.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


### Simple example

{% highlight bash %}
#include <stdio.h> 

char input[1000];
int Arr[10];

int main(){
    int n;
    fgets(input, 1000, stdin);
    sscanf(input, "%d", &n);
    fgets(input, 1000, stdin);
    int index = 0;
    int a_i = 0;    
    int pt = 0;
    while(input[index] != 0){
        if(input[index] == ' ' || input[index] ==10){
            input[index] = 0;
            sscanf(input + pt, "%d", &Arr[a_i++] );
            pt = index+1;
        } 
        index++;
    }
    for(int i = 0; i<a_i;i++){
        printf("%d ", Arr[i]);
    }
    return 0;
}
{% endhighlight %}
