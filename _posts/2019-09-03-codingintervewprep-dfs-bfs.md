---
layout: post
title: coding Interview Prep - DFS && BFS
modified: 2019-09-03
tags: [codingIntervewPrep]
---

# DFS

DFS는 간단힌 recursion과 stack을 이용한 iteration으로 구현할 수 있다. 
 
## Recursion Flow

```java
function(g, s) //g는 graph, s는 시작 vertex
	visited를 false(미방문상태)로 초기화 
	시작점 s를 방문한 것으로 표시 //visited에서 s를 true로 설정
	process(g, visited, s)

process(g, visited, s) //g는 graph, s는 시작 vertex
  skip 조건 확인 // outOfBound, 이미 방문 했는지, 기타 skip 조건에 해당하는지
	종료 조건 확인 
	인접 vertex loop
		v <- next v
		visited[v] = true
		process(g, visited, v)
		visited[v] = false // 이것은 모든 경우의 수를 테스트 하는 경우 사용
```

## Iteration Flow

```java
function(g, s) //g는 graph, s는 시작 vertex
  stack 초기화 
  visited[]를 false(미방문상태)로 초기화
  시작점 s를 방문한 것으로 표시 //visited[]에서 s를 true로 설정

  stack에 s를 삽입//push
  while(stack이 비어있지 않으면)
     v <- stack에서 pop
     for(u <- v와 인접한 vertex들)  
        if(skip 조건 확인) {   // outOfBound, 이미 방문 했는지, 기타 skip 조건에 해당하는지
          continue;
        }
        종료 조건 확인
        방문으로 표시 //visited[]에서 u를 true로 설정
        u를 stack에 push
```

# BFS

BFS는 queue를 이용해 구현할 수 있다. 

## Iteration Flow

```java
function(g, s) //g는 graph, s는 시작 vertex
  queue 초기화 
  visited[]를 false(미방문상태)로 초기화
  시작점 s를 방문한 것으로 표시 //visited[]에서 s를 true로 설정

  stack에 s를 삽입//offer
  while(queue가 비어있지 않으면)
		size <- q.size() 
    while size-- > 0 // 현재 queue에 있는 아이템에 대해서 모두 (한 레벨씩)
	     v <- queue에서 poll
	     for(u <- v와 인접한 vertex들)  
	        if(skip 조건 확인) {   // outOfBound, 이미 방문 했는지, 기타 skip 조건에 해당하는지
	          continue;
	        }
	        종료 조건 확인
	        방문으로 표시 //visited[]에서 u를 true로 설정
	        u를 queue에 offer
```


# Java

## Stack

- 초기화: Stack<Integer> stack = new Stack<Integer>(); 
- Object push(Object element) 
- Object peek() : Returns the element on the top of the stack, but does not remove it.
- Object pop() : Removes and returns the top element of the stack. An ‘EmptyStackException’ exception is thrown if we call pop() when the invoking stack is empty.
- boolean isEmpty() : It returns true if nothing is on the top of the stack. Else, returns false.

## Queue

- 초기화: Queue<Integer> q = new LinkedList<>();
- offer(): This method is used to add elements at the tail of queue. = add() 
- peek(): This method is used to view the head of queue without removing it. It returns Null if the queue is empty.
- poll(): This method removes and returns the head of the queue. It throws NoSuchElementException when the queue is empty. = remove()
- boolean isEmpty() : It returns true if nothing is on the top of the stack. Else, returns false.

## Min and Max

```java
Math.min(), Min.max()  
```

## Max and Min value

```java
Interger.Min_Value, Interger.Max_Value
```

## 간단히 x, y 좌표를 Stack에 넣고 빼기

```java
Stack<int[]> stack = new Stack<>();
stack.push(new int[]{i, j});
int[] s = stack.pop();
int y = s[0];
int x = s[1];
```

## 간단히 x, y 좌표를 Queue에 넣고 빼기

```java
Queue<int[]> queue = new LinkedList<>();
queue.offer(new int[]{i, j});
int[] s = queue.poll();
int y = s[0];
int x = s[1];
```

## 간단히 4방향 순회하기

```java
int[][] dirs = new int[][]{ {1, 0}, {-1, 0}, {0, 1}, {0, -1} };

for (int[] dir : dirs) {
	int nextY = dir[0];
	int nextX = dir[1];
}
```

# 예제 문제

https://leetcode.com/problems/shortest-bridge/

두개의 island를 잊는 가장 짧은 다리를 구하는 것이다. 1이 육지이고 0이 바다이다. 
1을 찾은 후 그것의 DFS를 그려서 모두 visited로 마크하고 queue에 집어 넣는다. 이제 queue에서 하나씩 빼내어 주변을 탐색한다.
만약 visited가 아니면 하나씩 돌면서 queue에 다시 넣고, 방문한 것으로 처리한다. 이것은 한 레벨씩 증가하면서 탐색을 하고 
만약 값이 1인데 visited가 아닌 것을 만나면 이때의 step의 숫자를 출력한다. 

아래는 BFS와 DFS를 사용한 것인데, DFS를 recursion으로 한것과 iteration으로 한것을 각각 구현하였다.

```java
class Solution {
    public int shortestBridge(int[][] A) {
        int m = A.length, n = A[0].length;
        boolean[][] visited = new boolean[m][n];
        int[][] dirs = new int[][]{ {1, 0}, {-1, 0}, {0, 1}, {0, -1} };
        Queue<int[]> q = new LinkedList<>();
        boolean found = false;
        // 1. dfs to find an island, mark it in `visited`
        for (int i = 0; i < m; i++) {
            if (found) {
                break;
            }
            for (int j = 0; j < n; j++) {
                if (A[i][j] == 1) {
                    dfs(A, visited, q, i, j, dirs);
                    found = true;
                    break;
                }
            }
        }
        // 2. bfs to expand this island
        int step = 0;
        while (!q.isEmpty()) {
            int size = q.size();
            while (size-- > 0) {
                int[] cur = q.poll();
                for (int[] dir : dirs) {
                    int i = cur[0] + dir[0];
                    int j = cur[1] + dir[1];
                    if (i >= 0 && j >= 0 && i < m && j < n && !visited[i][j]) {
                        if (A[i][j] == 1) {
                            return step;
                        }
                        q.offer(new int[]{i, j});
                        visited[i][j] = true;
                    }
                }
            }
            step++;
        }
        return -1;
    }

    // DFS by recursion
  private void dfs(int[][] A, boolean[][] visited, Queue<int[]> q, int i, int j, int[][] dirs) {
        Stack<int[]> stack = new Stack<>();
        visited[i][j] = true;
        q.offer(new int[]{i, j}); //Insert all the 1s of island to the queue 
        stack.push(new int[]{i, j});
        while (!stack.isEmpty()) {
            int[] s = stack.pop();
            int y = s[0];
            int x = s[1];
            for (int[] dir : dirs) {
                int nextY = y + dir[0];
                int nextX = x + dir[1];
                if (nextY < 0 || nextX < 0 || nextY >= A.length || nextX >= A[0].length || visited[nextY][nextX] || A[nextY][nextX] == 0) {
                    continue;
                }
                visited[nextY][nextX] = true;
                q.offer(new int[]{nextY, nextX}); //Insert all the 1s of island to the queue 
                stack.push(new int[]{nextY, nextX});
            }
        }
    }

	// DFS by recursion
	private void dfs(int[][] A, boolean[][] visited, Queue<int[]> q, int i, int j, int[][] dirs) {
        if (i < 0 || j < 0 || i >= A.length || j >= A[0].length || visited[i][j] || A[i][j] == 0) {
            return;
        }
        visited[i][j] = true;
        q.offer(new int[]{i, j}); //Insert all the 1s of island to the queue 
        for (int[] dir : dirs) {
            dfs(A, visited, q, i + dir[0], j + dir[1], dirs);
        }
    }
}
```

### 기타 Leetcode 문제들

leetcode에 있는 DFS관련한 문제를 풀어보자. 모두 hard difficulty의 문제들이다. 

- Unique Paths III: https://leetcode.com/problems/unique-paths-iii/
- https://leetcode.com/problems/recover-a-tree-from-preorder-traversal/
- https://leetcode.com/problems/making-a-large-island/
- https://leetcode.com/problems/contain-virus/
- https://leetcode.com/problems/minimize-malware-spread/
- https://leetcode.com/problems/sum-of-distances-in-tree/
- https://leetcode.com/problems/remove-boxes/
- https://leetcode.com/problems/binary-tree-cameras/
- https://leetcode.com/problems/similar-string-groups/
