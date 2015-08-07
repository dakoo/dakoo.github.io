---
layout: post
title: Dijkstra's algorithm에 대한 이해
description: Dijkstra's algorithm에 대한 이해
modified: 2015-08-06
tags: [algorithm]
image:
  feature: abstract-17.png
link: http://trowind.tistory.com/80 
---

그래프의 최단 경로 알고리즘 중 하나인 Dijkstra 알고리즘은 다른 최단 경로 알고리즘 중에서는 그나마 쉽지만, 역시 이해가 어렵다. 다른 최단 경로 알고리즘을 이해하기 위해서라도 Dijkstra 알고리즘을 이해하는 것이 필요하다. 
Dijkstra 알고리즘은 Vertex A와 그래프 내의 다른 모든 Vertex와의 **최단 거리**를 찾아내는 알고리즘이다. 최단 경로를 찾아내기 위해서는 알고리즘의 변형이 필요하다는 것을 알아두자.

[알고리즘의 Pseudo 코드와 동작에 대해 설명을 한 글](http://trowind.tistory.com/80)과 [자세하고 친절한 알고리즘 해석을 한 글](http://adnoctum.tistory.com/165)을 참고하자. 

### 기본 자료 구조

  - Graph : n개의 vertex와 m개의 edge로 구성
  - Distance[n] : 시작점부터 각 vertex까지의 거리를 저장하는 자료. 시작 시엔 시작점(index i)은 0, 나머지는 무한대(987654321?)로 초기화 
  - Selected[n] : 시작점(index i)만 true, 나머지는 false로 초기화 하여 시작. 모두 true가 되면 종료

   참고로, vertex와 edge 정보가 따로 주어지는 Graph가 아니라 edge의 weight정보만 가진 2진 array로 Graph가 주어질 경우에는 Distance도 2진 배열, Selected도 2진 배열이 필요하다. 
  
### flow

dijkstra(G, s, n){ //G:Graph, s: 시작 정점의 index, n: 정점의 수
  loop 
    Distance[i] = 987654321
    Selected[i] = false
  Distance[s] = 0
  Selected[s] = true
  num_selected = 1
  
  loop condition(num_selected != n)
    i = extract_min(Distance[]) // Distance에서 최소인 index를 리턴. 
    Selected[i] = true
    num_selected++
    loop j <- i의 모든 인접 정점들       
      //인접 정점의 거리를 update. j가 이미 가지고 있던 값과 i의 시작정점으로부터의 거리+i와j간 거리를 비교하여 작은 것을 택함
      Distance[j] = Distance[j] > Distance[i] + G.e(i,j) ? Distance[i] + G.e(i,j): Distance[j]; 
  

}
 


