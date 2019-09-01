---
layout: post
title: Algorithm - LIS(Longest Increasing Subsequence) O(N*N) 알고리즘
description: LIS(Longest Increasing Subsequence)알고리즘
modified: 2016-03-03
tags: [algorithm]
comments: true
image:
  feature: abstract-13.png
---

Longest Increasing Subsequence(LIS)알고리즘은 나열된 수열에서 배열 순서를 유히자면서 크기가 점진적으로 커지는 가장 긴 부분 수열을 구하는 알고리즘이다. 예를 들어, 3, 2, 7, 5, 6, 1 에서 2, 5, 6이 LIS이며 그 길이는 3이다. 

### 아이디어

입력 수열이 a1, a2, ..., an일때 LIS[i]는 a1~ai의 최장 부분 수열의 **길이**라고 정의하자.  LIS[i]를 LIS[1]~LIS[i-1]과의 관계로 표현하면 다음과 같다. 
aj < ai (이때, j<i)인 aj를 모두 찾는다. 즉, ai보다 앞에 위치해 있으면서 ai보다 작은 값을 모두 찾는다. 3, 2, 7, 5, 6, 1이 있을 때 ai가 5(index는 4)라면 aj후보는 3, 2(index는 1과 2)이다. 
aj후보의 LIS 중 가장 큰 값을 찾아 1을 증가시킨 것이 LIS[i]이다. 예를 들어, LIS[4]는 LIS[1]과 LIS[2]의 최대값+1이다. LIS[1]과 LIS[2]가 모두 1이므로 LIS[4]는 2이다.

### Pseudo 코드

```bash
function(A[],n){
	for i in 1 -> n {
		LIS[i] = 1 //자기 자신만의 길이로 초기화 
		for j in 1 -> i - 1 { //자신 앞을 검색
			if (A[j] < A[i] && 1 + LIS[j] > LIS[i]) //크기가 더 작고 LIS를 증가시킬 수 있으면
				LIS[i] = 1 + LIS[j] 
		}
	}
	return max LIS[i] //최대값을 찾아서 반환
}
```
