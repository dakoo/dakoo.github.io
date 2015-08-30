---
layout: post
title: 실수를 반올림하여 정수로 변환하기 
description: 실수를 반올림하여 정수로 변환하기 
modified: 2015-08-07
tags: [algorithm]
comments: true
image:
  feature: abstract-17.png
---

실수에서 정수로 변환하면서 소수점에서 반올림 하는 방법을 간단히 알아보자.
간단히 말하면, 1.4인 실수는 1로, 1.6인 실수는 2로 출력하는 방법이다. 소수 첫번째 자리에서 0.5를 더한후 실수(double, float)형 data type을 정수형(long long, long, int)으로 캐스팅하면 된다. 

{% highlight c %}
double result = 1.4;
result += 0.5;
long long i_result = result;
printf("%d\n", i_result); //1이 출력
result = 1.6;
result += 0.5;
i_result = result;
printf("%d\n", i_result); //2가 출력
{% endhighlight %}
