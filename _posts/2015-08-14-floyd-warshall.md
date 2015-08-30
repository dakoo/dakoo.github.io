---
layout: post
title: 간단한 최단 거리 알고리즘 - Floyd-Warshall 
description: 간단한 최단 거리 알고리즘 - Floyd-Warshall 
modified: 2015-08-14
tags: [algorithm]
comments: true
image:
  feature: abstract-12.png
---

Floyd-Warshall 알고리즘은 Dijkstra 알고리즘과 같은 최단 경로를 찾는 알고리즘이다. Dijkstra 알고리즘은 하나의 시작점을 기준으로 최단 경로를 찾는데 비해 Floyd-Warshall 알고리즘은 모든 쌍의 최단 경로를 찾을 수 있다. 무엇보다도 알고리즘이 훨씬 간단하다. 그 이유는 Dynamic Programming 기반의 알고리즘이기 때문이다. 

### 아이디어 

정점 i에서 j로 갈 때 i에서 j로 직접 가는 경로와 i에서 k를 거쳐 j로 가는 경로 중 **짧은 경로**를 선택한다. 그 뒤 i에서 l로 직접 가는 경로와 i로 부터 j를 거쳐 l까지 가능 경로 중 짧은 경로를 선택하는 것으로 확장한다. 

입력 그래프의 정점을 각각 1, 2, 3, n이라고 한다. 
Dijk: 정점 1, 2, ..., k만을 경유 가능한 정점으로 고려했을 때 가장 최단 경로의 거리이다.  i와 j사이에 아무 정점도 거치지 않을 수도 있고, 특정 정점을 거칠 수도 있는데 그 중 최단 경로의 거리가 Dijk인 것이다. 정점 1부터 k까지를 모두 거치야 하는 것은 아니란 것을 명심하자. 
Dij0: 어느 정점도 거치지 않고 i에서 j까지의 직접가는 경로의 거리를 의미한다. 즉, i와 j간 선분의 가중치(거리)와 같다. 
Dij1: i에서 j까지 경로 중 정점 1을 고려할 떄의 최단 경로를 의미한다. i에서 j에 직접 가는 거리와  1을 거쳐서 j에 가는 거리 중 최단 거리이며, 모든 쌍 i와 j에 대해 Dij1를 계산하는 것이 가장 작은 부분 문제이다. (이때 i와 j는 정점 1이 아니다) 


모든 쌍 i와 j에 대해 Dij1을 구한다. Dij1은 i에서 j로 직접 가는 거리와 정점 1을 반드시 경유해서 가는 거리 중 더 짧은 경로의 거리이다. 
<figure>
<img src="/images/floyd_warshall1.jpg" alt="floyd-warshall">
</figure>

모든 쌍 i,j에 대해 Dij2를 구한다. Dij2는 정점 1까지를 고려햔 최소 경로 길이와 정점 2를 반드시 경유하는 거리 중 짧은 거리를 Dij2로 정한다. 
<figure>
<img src="/images/floyd_warshall2.jpg" alt="floyd-warshall">
</figure>

i에서 출발하여 정점 k를 경유하여 j로 가는 경로의  거리와 Dij(k-1)를 비교하여 더 짧은 거리를 Dijk로 정한다. 
<figure>
<img src="/images/floyd_warshall3.jpg" alt="floyd-warshall">
</figure>

일반화 하면 다음과 같다. 
<figure>
<img src="/images/floyd_warshall4.jpg" alt="floyd-warshall">
</figure>

### Pseudo 코드

{% highlight bash %}
D[i][j] 초기화 //선분 (i, j)의 가중치. 선분이 없으면 무한대, 자기자신(i=j)이면 0으로 초기화 
function(D[i][j]){
	for k in 1->n { //k는 1부터 범위를 확장해 나가는 것 
		for i in 1->n { //단, i!= k 
			for j in 1->n { //단, j!= i, j!=k 
				D[i][j]=min(D[i][k] + D[j][k], D[i][j])
			}
		}
	}
}
{% endhighlight %}