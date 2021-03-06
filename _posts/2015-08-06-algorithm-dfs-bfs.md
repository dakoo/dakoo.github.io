---
layout: post
title: Algorithm - DFS와 BFS
modified: 2016-02-06
tags: [algorithm]
---

BFS와 DFS의 구현 시 차이점은 BFS는 queue를 쓰고 DFS는 stack을 쓴다는 것이다. 물론 DFS는 recursion으로 구현하는 것이 쉽기도 하다.
BFS와 DFS는 구현 flow는 동일하며 visited라는 공통 자료구조를 사용한다.

### 간단 flow

```bash
function(g, s) //g는 graph, s는 시작 vertex
  필요한 자료 구조 초기화 //BFS는 queue, DFS는 stack, 그리고 visited[]를 false(미방문상태)로 세팅
  시작점 s를 방문한 것으로 표시 //visited[]에서 s를 true로 설정
  자료구조(queue나 stack)에 s를 삽입//push
  while(자료 구조가 비어있지 않으면)
     v <- 자료구조에서 vertex를 하나 뺌//pop
     for(u <- v와 인접한 vertex들)  //g에서 v의 인접 vertex를 찾아 그 수만큼 loop
        if(u가 방문되지 않았다면)  //visited[]에서 u가 false인지 확인
           방문으로 표시 //visited[]에서 u를 true로 설정
           u를 자료구조에 넣기 //push
```
