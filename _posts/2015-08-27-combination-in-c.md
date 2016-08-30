---
layout: post
title: Algorithm - combination
description: combination
modified: 2016-02-16
tags: [algorithm, c]
comments: true
image:
  feature: abstract-10.png
---

알고스팟의 문제 중 [CLOCKSYNC](https://algospot.com/judge/problem/read/CLOCKSYNC) 라는 것이 있다. 이 문제의 해결 방법은 조합도 있지만, Greedy로 문제를 푸는 방식도 있는데 조합의 경우를 살펴보자. 


<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


### 간단한 조합 문제

그림과 같이 4 x 4 개의 격자 형태로 배치된 16개의 시계가 있다. 이 시계들은 모두 12시, 3시, 6시, 혹은 9시를 가리키고 있다. 이 시계들이 모두 12시를 가리키도록 바꾸고 싶다.

시계의 시간을 조작하는 유일한 방법은 모두 10개 있는 스위치들을 조작하는 것으로, 각 스위치들은 모두 적게는 3개에서 많게는 5개의 시계에 연결되어 있다. 한 스위치를 누를 때마다, 해당 스위치와 연결된 시계들의 시간은 3시간씩 앞으로 움직인다. 스위치들과 그들이 연결된 시계들의 목록은 다음과 같다.

{% highlight bash %}
0   0, 1, 2
1   3, 7, 9, 11
2   4, 10, 14, 15
3   0, 4, 5, 6, 7
4   6, 7, 8, 10, 12
5   0, 2, 14, 15
6   3, 14, 15
7   4, 5, 7, 14, 15
8   1, 2, 3, 4, 5
9   3, 4, 5, 9, 13
{% endhighlight %}

시계들은 맨 윗줄부터, 왼쪽에서 오른쪽으로 순서대로 번호가 매겨졌다고 가정하자. 시계들이 현재 가리키는 시간들이 주어졌을 때, 모든 시계를 12시로 돌리기 위해 최소한 눌러야 할 스위치의 수를 계산하는 프로그램을 작성하시오.

### 아이디어
문제를 푸는 방법을 생각해보자. 각 버튼마다 4가지의 가능성이 있다. 누르지 않거나, 한번, 두번, 세번 누르는 경우이다. 버튼을 누르는 순서는 문제의 풀이와 상관이 없다라는 것도 중요하다. 이렇게 보면 조합을 이용해 문제를 풀 수 있음을 알 수 있다. 모든 조합에 대해 테스트해보는 것이다. 

#### Pseudo code

n개의 아이템과 각 아이템별 x개의 경우의 수가 있다면 총 n*x개의 조합을 만들 수 있다. 위의 문제의 경우는 4*10의 경우의 수가 있다. 그리고, 버튼을 누를 수 있는 최대의 수는 30회가 된다. 순서가 상관없으므로 아이템 1개를 가지고 x개의 경우의 수를 테스트하고, 각 경우의 수 마다 다음으로 이동해서 테스트 하는 방식으로 수행한다. 
이런 방식으로 간단히 조합 문제를 풀 수 있다. 

{% highlight c %}
answer = 31;
push_count = 0;
cal(button_index){
    if(Button_index < 10){
        for 버튼을 누른 횟수(1->4){
            해당된 Clock들을 조작
            push_count++;
            Clock이 모두 12시를 가리키는지 검사해서 맞으면 
                answer = push_count < answer? push_count: answer;
            cal(button_index + 1);
        }
        push_count -= 4; //한바퀴 돌아서 제자리가 된 것이므로 push_count를 1 줄인다.  
    }
}
{% endhighlight %}




