---
layout: post
title: Algorithm - Minimum Spanning Tree - Kruskal
description: Minimum Spanning Tree - Kruskal
modified: 2016-02-16
tags: [algorithm]
comments: true
image:
  feature: abstract-19.png
---

Minimum Spanning Tree(한국어로 최소 신장 트리) 알고리즘은 쉽게 말해 가장 비용을 적게 사용해서 네트웍을 구성하기 위한 그래프 알고리즘이다. 
indirected(무방향) 그래프에만 해당된다. 
Kruskal알고리즘은 Prim 알고리즘과 더불어 유명한 MST 알고리즘이다. 그런데 성능 측면에서는 두 알고리즘이 유사하지만, 이해하기 쉽고 개발이 용이한 것은 Kruskal알고리즘이다. 


## 알고리즘 이해 

간단한 flow는 다음과 같다. 

{% highlight bash %}
kruskal(G) //G: graph - 가중치가 다른 edge로 구성 
  for vertex v in G //graph의 vertex들에 대해
    makeset(v)      //각각을 set으로 초기화 
  for edge e in G //graph의 모든 edge들에 대해 
    insert_minheap(e) //MINHeap에 넣어서 가중치가 작은 순서로 불러올 수 있게 초기화
  int selected_edges = 0
  while ( minheap is not empty && selected_edges != n - 1 )
    edge <- delete_minheap //가중치가 가장 낮은 edge
    if(findset(edge.v) != findset(edge.u)) //edge를 이루는 vertex u와 v가 같은 set에 있지 않으면-- 즉, 서클을 형성하지 않으면!
      do something(edge) --> edge가 MST에 포함되므로 edge의 거리를 더하거나, 아니면 MST자료 구조를 따로 정의해 저장
      selected_edges++;
      union(u,v)
{% endhighlight %}

더 자세한 내용은 [네이버 글](http://blog.naver.com/ryutuna/100123829840)을 참조하자.

## c언어를 이용한 구현 

[minheap구현에 대한 글](http://dakoo.github.io/priority-queue/)과 [disjoints-set 구현에 대한 글](http://dakoo.github.io/disjoints-set/)에 나온 코드를 사용하여 다음 kruskal 코드를 작성했다. 

{% highlight c %}

int mst_kruskal(int num_items){
  for (int i = 1; i <= num_items; i++){
    makeset(i);
  }
  init_priorityqueue();

  for (int i = 1; i <= num_items - 1; i++){
    for (int j = i + 1; j <= num_items; j++){
      Item new_item;
      new_item.val = Edges[i][j];
      new_item.v = i;
      new_item.u = j;
      insert_priorityqueue(new_item);
    }
  }
  int num_selected = 0;
  int total_distance = 0;
  while (!isEmpty_priorityqueue() && num_selected != num_items-1){
    Item it = delete_priority();
    if(findset(it.v) != findset(it.u)){
      num_selected++;
      total_distance += it.val;
      unionset(it.v, it.u);
    }
  }
  return total_distance;
}
{% endhighlight %}




