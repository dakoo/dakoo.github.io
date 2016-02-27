---
layout: post
title: 데이터구조 - disjoints-set
description: 데이터구조 - disjoints-set
modified: 2016-02-16
tags: [algorithm]
image:
  feature: abstract-11.png
---

집합을 자료구조 중 array를 이용해 만드는 disjoints-set을 이해해 보자. 

### 자료 구조

{% highlight bash %}
int parent[MAX_LENGTH];
{% endhighlight %}

### makeset(x)

유일한 멤버 x를 포함하는 집합 만드는 함수이다. 초기화 함수로 모든 멤버들을 각기 독립적인 set으로 만든다. 

{% highlight bash %}
makeset(x)
  parent[x] = x; 
{% endhighlight %}

### findset(x)

x를 포함하는 집합(정확히 말하면 집합의 root parent index)를 찾는 함수이다. 

{% highlight bash %}
findset(x)
   if x == parent[x] //root parent
      return x;
    else 
      return findset(parent[x]); //recursion
{% endhighlight %}

skewed tree가 되는 것을 막기위해 찾으면서 그 결과를 이용해 tree를 평탄화시키는 것은 아래와 같다. 

{% highlight bash %}
findset(x)
   if x == parent[x] //root parent
      return parent[x];
    else 
      return parent[x] = findset(parent[x]); //recursion 하며 결과로 자신의 parent를 갱신
{% endhighlight %}

### unionset(x, y)

x와 y를 포함하는 두 집합을 합치는 함수이다. 

{% highlight bash %}
union(x)
   parent[findset(y)] = findset(x);
{% endhighlight %}

### 두 개의 원소가 같은 집합에 속해 있는지 판단하기 

findset(x)와 findset(y)가 같은 결과가 아니면 다른 집합이다. 

{% highlight bash %}
isUnion(x, y)
  if findset(x) != findset(y)
    return false;
  return true;
{% endhighlight %}

### 기타

성능을 최적화하는 방법으로 집합을 union할때 높이가 낮은 쪽으로 연결되도록 rank를 사용하는 경우도 있다. 


