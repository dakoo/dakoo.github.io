---
layout: post
title: 알고리즘 - Topological Sort
description: Topological Sort
modified: 2016-02-25
tags: [algorithm]
comments: true
image:
  feature: algorithm.jpeg
---

Topological Sort는 dependency를 가진 것들을 처리할 때 어떤 순서로 처리해야 하는지를 알아내기 위한 그래프 알고리즘이다. 예를 들어, `a -> b, b -> c, a -> c`라는 의존 관계가 있다면 `a -> b -> c`라는 순서로 처리되어야만 한다. 
보통 의존성이 있는 작업들이 있을 때 순서를 구하거나, 소프트웨어의 각 모듈간 의존성을 고려한 설치 순서등을 정할 때 사용된다. 

### 알고리즘 

Topological sort는 의존성을 가진 것들의 처리 순서를 다루는 것이므로 방향성 그래프에 적용된다. 다음과 같은 어프로치를 활용한다. 

- 다른 vertex들이 의존하지 않는 vertex부터 하나 씩 지워나간다.   

예를 들어  `a -> b, b -> c, a -> c`라는 의존 관계에서 c를 지우고, b를 지우고, a를 지우는 식으로 진행하는 것이다. 이 어프로치를 구현하기 위한 알고리즘은 다음과 같다. 


- 그래프내의 모든 vertex들을 방문할 때까지 
- DFS로 vertex를 순회한다. 
- 한 vertex를 기준으로 볼 때 자신의 adjacent vertex들을 모두 방문하거나, 혹은 adjacent vertex가 없어서 더 이상 자신에게 의존하는 vertex가 남아 있지 않으면 이때 자신을 스택에 넣는다. 
- 모든 vertex를 방문했다면 스택에서 하나씩 pop을 한다. pop된 아이템들의 순서가 바로 topological_sort된 순서이다. 

간단히 pseudo 코드로 표현하면 다음과 같다. 

```javascript
dfs(graph, vertex id, visited[], stack){
	해당 vertex를 방문한 것으로 check
	모든 인접 vertex를 순회하며 {
		if(인접 vertext가 방문한 적이 없다면){
			dfs(graph, 인접 vertex의 id, visited, stack);
		}
	}
	stack에 vertex를 푸쉬한다.
}
topological_sort(graph){
	자료 구조 초기화: graph, visited[], stack
	graph내의 모든 vertex에 대해 {
		dfs(graph, vertex id, visited, stack)
		방문한 vertex 숫자가 graph의 vertex 숫자와 일치하면 loop을 멈춤
	}
	stack에서 하나씩 push하면서 출력한다. 
}
```

### 코드

위의 pseudo code를 실제 코드로 작성한 것은 다음과 같다. set 자료 구조를 사용해 visited 여부를 체크하고, vector를 stack로 사용했다. 


```javascript
#include <iostream>
#include <set>
#include <vector>
using namespace std;
void dfs(vector<vector<bool> > &G, int index, set<int> &Visited, vector<int> &Stack){
	Visited.insert(index); //방문한 것으로 Check
	for (int i = 0; i<G.size(); i++){
		if (i == index) continue;	//자기 자신은 Skip
		if (G[index][i]){			//연결된 Vertex가 있다면 (edge가 true)
			auto it = Visited.find(i);
			if (it == Visited.end()){		//방문했던 Vertex인지 확인해서 하지 않았다면 
				dfs(G, i, Visited, Stack);	//방문한다. 
			}
		}
	}
	Stack.push_back(index);				//더 이상 방문할 것이 없다면 V에 넣는다.(즉, 자신에게 의존적인 Vertex들을 모두 처리한 후에 자신을 처리)
}
vector<int> topological_sort(vector<vector<bool> > &G){
	set<int> Visited;
	vector<int> Stack;
	for (int i = 0; i<G.size(); i++){	//각 Vertex에서 시작해서 Graph를 dfs로 travesal한다. 
		dfs(G, i, Visited, Stack);
		if (Visited.size() == G.size()){
			return Stack;
		}
	}
	return Stack;
}
int main(){
	/* create graph */
	vector<vector<bool> > G(5, vector<bool>(5, false));
	G[0][4]  = true;
	G[0][2]  = true;
	G[2][3]  = true;
	G[1][4]  = true;
	G[2][1]  = true;
	vector<int> stack = topological_sort(G);
	while (stack.size()){
		cout << stack.back() << " ";
		stack.pop_back();
	}
	return 0;
}
```

