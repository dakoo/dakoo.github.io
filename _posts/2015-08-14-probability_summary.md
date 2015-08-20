---
layout: post
title: 확률에 대한 간단 정리
description: 확률에 대한 간단 정리 
modified: 2015-08-14
tags: [algorithm]
image:
  feature: abstract-19.png
---

### 확률의 덧셈 정리

두 사건 A와 B가 일어날 확률은 다음과 같다. 

{% highlight bash %}
P(AUB) = P(A) + P(B) - P(A와 B의 교집합: A와 B가 동시에 일어날 확률)
{% endhighlight %}

두 사건 A와 B가 동시에 일어나는 일이 아닌 경우엔 

{% highlight bash %}
P(AUB) = P(A) + P(B) 
{% endhighlight %}

### 조건부 확률

A안에서 A와 B가 동시에 일어날 확률은 A가 일어났을때의 B의 조건부 확률이라고 한다. 그것은 A와 B가 동시에 발생한 수를 A의 발생 수로 나눈 것과 같다.  

{% highlight bash %}
P(B|A) = n(A와 B의 교집합)/n(A) = P(A와 B의 교집합)/P(A)
{% endhighlight %}

### 독립 사건, 종속 사건

A와 B가 서로 상관없이 발생하는 경우 독립사건이라 하고, 수식화 하면 다음과 같다. 

{% highlight bash %}
P(B|A) = P(B) = P(B|A의 여집합) 
{% endhighlight %}

A와 B가 서로 상관있는 경우, 종속사건이라고 한다. B의 발생이 A의 발생에 의해 영향을 받는 경우를 수식화 하면 다음과 같다. 

{% highlight bash %}
P(B|A) != P(B)
{% endhighlight %}

#### 독립 사건의 곱셈 정리

{% highlight bash %}
P(A와 B의 교집합) = P(A)*P(B)
{% endhighlight %}

### 독립 시행

동전이나 주사위를 여러번 던지는 것과 같이 어떤 시행을 되풀이 해도 각 사건이 서로 독립적인 경우 즉, 시행의 결과가 이전 시행에 의해 영향을 받지 않는 시행을 독립 시행이라고 한다. 

#### 독립 시행의 확률 구하기

어떤 시행에서 사건 A가 발생할 확률이 p이고 그 여사건이 발생할 확률이 q(q = 1-p)일때 n번의 독립 시행에서 사건 A가 r번 이러날 확률은 다음과 같다. 

{% highlight bash %}
Pr = nCr * p의 r제곱승 * q의 (n-r)제곱승
{% endhighlight %}