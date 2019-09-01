---
layout: post
title: Algorithm - 2D Matrix 회전
modified: 2016-02-29
tags: [algorithm]
parent: Coding Interview
nav_order: 2
---

#### Problem

NxN 크기의 2D Matrix의 시계 방향, 그리고 반 시계 방향 회전을 하는 방법을 알아보자. Matix를 얼마나 쉽게 다룰 수 있는가를 알 수 있는 간단한 알고리즘이다. 
안타깝게도 전혀 제대로 하지 못해서 큰 인터뷰에서 문제가 된 적이 있기에 간단한 접근 방법과 코드를 남긴다. 
예를 들어 다음과 같은 3x3 2d Matrix가 있다면,

```
1 2 3
4 5 6
7 8 9
```

시계 방향 90 회전은 다음과 같다. 

```
7 4 1
8 5 2
9 6 3
```

반시계 방향 90 회전은 다음과 같다. 

```
3 6 9
2 5 8
1 4 7
```

#### 접근법

##### 시계 방향 회전

NxN의 2D matrix를 4등분을 한다고 생각해보자. 시계 방향으로 회전한다는 것은 1사분면부터 시작한다면 1사분면의 것은 잠시 저장하고, 4사분면을 1사분면으로, 3사분면을 4사분면으로, 2사분면의 것을 3사분면으로, 1사분면의 것을 2사분면으로 옮긴다는 것이다. 
1사분면의 좌표를 (y, x)이고 높이와 넓이가 N이라고 할 때 각 사분면에서의 위치를 고민해보자. 각 예를 보면 다음과 같다. 

- 1사분면 : (0, 0),    (0, 1),    (0, 2)
- 2사분면 : (0, N-1),  (1, N-1),  (2, N-1)
- 3사분면 : (N-1, N-1),(N-1, N-2),(N-1, N-3)
- 4사분면 : (N-1, 0),  (N-2, 0),  (n-3, 0)

위의 것을 일반화 시키면 다음과 같다. 

- 1사분면: (y, x)
- 2사분면: (x, N-1-y)
- 3사분면: (N-1-y, N-1-x)
- 4사분면: (N-1-x, y)

각 사분면간 이동은 다음과 같다.

```
1사분면의 각 좌표에 대해 
temp에 저장
4사분면의 것을 1사분면으로 복사
3사분면의 것을 4사분면으로 복사
2사분면의 것을 3사분면으로 복사
temp에 저장된 1사분의 것을 2사분면으로 복사
```

##### 시계 반대 방향 회전 

기본적으로 시계 방향 회전과 동일한데 순서만 다르다. 

```
1사분면의 각 좌표에 대해 
temp에 저장
2사분면의 것을 1사분면으로 복사
3사분면의 것을 2사분면으로 복사
4사분면의 것을 2사분면으로 복사
temp에 저장된 1사분의 것을 4사분면으로 복사
```

##### 주의 할 점 

N이 짝수인 경우 1사분면의 범위는 다음과 같다. 

- 세로: 0~N/2 (N/2는 미포함)
- 가로: 0~N/2 (N/2는 미포함)

N이 홀수인 경우는 가로, 세로 중 하나는 가운데 줄을 포함해야 한다. 즉, 약간 비대칭적으로 해야한다는 것이다. 당연한 것이 그렇지 않고 가로/세로 모두 가운데 줄을 미포함하면 1사분면이 아니라 약간 작은 사각형이 된다. 

- 세로: 0~N/2 (N/2는 포함)
- 가로: 0~N/2 (N/2는 미포함)

또는 

- 세로: 0~N/2 (N/2는 미포함)
- 가로: 0~N/2 (N/2는 포함)

#### 코드 

```cpp
#include <vector>
#include <iostream>
using namespace std;
void print(vector<vector<int> > &T){
	cout << endl;
	for (int i = 0; i<T.size(); i++){
		for (int j = 0; j<T.size(); j++){
			cout << T[i][j] << " ";
		}
		cout << endl;
	}
	cout << endl;
}
void rotate(vector<vector<int> > &T, bool isClockwiseDirection, int times){
	int N = T.size();
	int H = T.size() / 2;
	if (N % 2) {// 홀수 인경우 Height를 1 증가 시켜서 1사분면을 잡는다. 
		H++;
	}
	for (int t = 1; t <= times; t++){	//90 회전 횟수 
		for (int i = 0; i<H; i++) {	
			for (int j = 0; j<N / 2; j++){
				if (isClockwiseDirection) {	//시계 방향
					int temp = T[i][j];
					T[i][j] = T[N - 1 - j][i];
					T[N - 1 - j][i] = T[N - 1 - i][N - 1 - j];
					T[N - 1 - i][N - 1 - j] = T[j][N - 1 - i];
					T[j][N - 1 - i] = temp;
				}
				else {	//반시계 방향
					int temp = T[i][j];
					T[i][j] = T[j][N - 1 - i];
					T[j][N - 1 - i] = T[N - 1 - i][N - 1 - j];
					T[N - 1 - i][N - 1 - j] = T[N - 1 - j][i];
					T[N - 1 - j][i] = temp;
				}
			}
		}
	}
}
int main(){
	vector<vector<int> > T_even = { 
		{ 1, 2, 3, 4 }, { 5, 6, 7, 8 }, { 9, 10, 11, 12 }, { 13, 14, 15, 16 } 
	};
	vector<vector<int> > T_odd = { 
		{ 1, 2, 3 }, { 4, 5, 6 }, { 7, 8, 9 } 
	};

	/*짝수크기의 2D matrix를 시계 방향으로 회전 */
	print(T_even);
	rotate(T_even, true, 1);
	print(T_even);
	rotate(T_even, true, 2);
	print(T_even);
	/*홀수크기의 2D matrix를 시계 반대방향으로 회전 */
	print(T_odd);
	rotate(T_odd, false, 1);
	print(T_odd);
	rotate(T_odd, false, 1);
	print(T_odd);
	rotate(T_odd, false, 1);
	print(T_odd);
	rotate(T_odd, false, 1);
	return 0;
}
```
