---
layout: post
title: Data Structure - B-tree
description: B-tree
modified: 2016-03-12
tags: [data structure]
comments: true
image:
  feature: algorithm.jpeg
---

B 트리는 자식을 두개만 가질 수 잇는 이진 트리를 확장하여 더 많은 자식을 가질 수 있게 고안한 것이다. 오라클과 같은 상용 DB에서 많이 사용하는 자료구조로 외부 검색(주 저장장치인 메모리 외의 저장장치에서의 검색)에 유용하다. 

### Balanced Tree란?

이진 트리는 O(NlogN)의 시간 복잡도를 가지지만, 좌우 균형이 맞지 않으면 비효율적인 성능을 낸다. 예를 들어 정렬이 되어 있는 자료를 순차적으로 삽입하면 한쪽으로만 치우치게 되고 O(N*N)의 시간 복잡도를 갖게된다. 이와 같은 이진트리의 문제를 해결하고 항상 삽입/삭제/검색이 O(NlogN)의 성능을 내도록 삽입과 삭제시에 스스로 좌우 균형을 유지하는 트리를 Balanced Tree라고 한다. 

#### Balanced Tree의 종류

Balanced 트리의 종류는 다음과 같다.

- AVL 트리
- 2-3 트리
- 2-3-4 트리 
- Red-Black 트리
- B 트리

위 중 AVL트리와 Red-Black트리는 유사하고 2-3/2-3-4트리와 B트리는 유사하다. 

### B 트리란?

이진 트리와 달리 하나의 노드가 여러 데이터를 가진다. 한 노드에 최대 M개의 자료가 배치될 수 있으면 M차 B트리라고 한다. 이 M이 짝수냐 홀수냐에 따라 알고리즘이 상당히 다르다. 2, 4, 6차 B트리와 3,5,7차 B트리는 상당히 다른 알고리즘을 사용한다. 홀수 B 트리가 짝수 B 트리에 비해 알고리즘이 많이 쉽다. 2-3 트리는 2차 B 트리와 같은 것이고, 2-3-4 트리는 3차 B 트리와 같다. 

<figure>
	<img src="/images/btree1.png" alt="">
</figure>

### B 트리의 정의와 규칙 

#### 간단한 규칙

B 트리는 다음과 같은 규칙을 갖는다. 

- 노드의 데이터 수가 N이면 자식의 수는 항상 N+1이여야 한다. 즉, 노드 2개의 데이터를 가진다면 그 노드의 자식은 반드시 3개여야 한다는 것이다. 
- 노드내의 데이터는 반드시 정렬된 상태여야 한다. 
- 노드의 데이터 D1의 왼쪽 서브 트리는 D1보다 작은 값들로 이루어져 있어야 하고, D1의 오른쪽 서브트리는 D1보다 큰 값들로 이루어져 있어야 한다. 이진 검색 트리의 성질과 유사하다. 

<figure>
	<img src="/images/b-tree5.png" alt="">
</figure>

#### 복잡한 규칙

- Root 노드는 자식이 있다면 적어도 2개 이상의 자식을 가져야 한다. 
- Root 노드를 제외한 모든 노드는 적어도 M/2개의 데이터를 가지고 있어야 한다. 예를 들어 5차 B트리라면 적어도 2개의 데이터를 가지고 있어야 한다. 
- Leaf 노드로 가는 경로의 길이는 모두 같다. 즉, leaf 노드는 모두 같은 레벨에 존재한다. 
- 입력 자료는 중복될 수 없다. 

### B 트리 구현

#### B 트리 노드

##### 노드의 구조

B 트리 노드는 N개의 데이터를 저장하는 배열, Child를 가리키는 포인터를 N+1개 저장하는 배열이 필요하다. 데이터 배열의 데이터 index i에 대해 Children 배열의 인덱스는 다음과 같은 관계를 가진다. 

- index i: i 데이터의 left child node를 가리키는 포인터가 저장된 index
- index i+1: i 데이터의 right child node를 가리키는 포인터가 저장된 index

```cpp
struct Datum {
    int key;
    int value;
    Datum (){ key = 0; value = 0;};
};
struct Node {
    vector<Datum> Data;
    vector<Node *> Children;
    int Dim;
    int count;
    Node(int d){
        Dim = d;
        Keys.resize(Dim, 0);
        Children.resize(Dim + 1, NULL);
        count = 0;
    };
};
```

##### 데이터의 값 반환

데이터 배열의 인덱스를 넣으면 그 키 값을 반환하거나 value를 반환한다. 

```cpp
int keyAt(int index){
    return Data[index].key;
};
int valueAt(int index){
    return Data[index].value;
};
```

##### 데이터의 child 반환

노드의 배열 구조를 이용한다. 데이터의 index를 받으면 left 혹은 right child의 포인터를 반환한다. 


```cpp
Node* leftChildOf(int index){
    return Children[index];
};
Node* rightChildOf(int index){
    return Children[index + 1];
};
```

##### 노드에서 데이터 삽입

노드의 데이터는 B 트리의 규칙에 따라 정렬되어 있어야 한다. 그러므로 정렬된 데이터들에게 데이터를 삽입하는 상황이므로 삽입 정렬과 같은 방식으로 삽입을 한다. 맨 뒤에서부터 하나씩 shift하면서 비교해서 적당한 위치에 놓으면 된다. 그리고 이때 Children 배열도 함께 Shift해야 한다. 쉽게 하기 위해 left child와 right child의 포인터를 이미 알고 있다고 하자. 그러면, 데이터 배열에서의 데이터의 index를 정하면 Children 배열의 index에는 left child를 넣고, index + 1에는 right child를 넣는다. 

```cpp
    void insertKey(int k, int v, Node* leftChild, Node* rightChild){
        if(count >= Dim) return;
        int i = count-1;  //last index prior to insertion
        while(i>=0 && Data[i].key > k){
            Data[i + 1].key = Data[i].key;
            Data[i + 1].value = Data[i].value;
            Children[i + 1] = Children[i];
            i--;
        }
        i++;
        Data[i].key = k;
        Data[i].value = v;
        Children[i] = leftChild;
        Children[i+1] = rightChild;
        count++;
    };
```

##### 노드에서 데이터 검색

노드는 정렬된 값을 가지고 있다. 차수가 작은 경우엔 순차적으로 검색하고, 차수가 큰 경우엔 이분 검색등을 이용한다. 찾으면 그 index를 반환하고, 못찾으면 -1을 반환한다. 

```cpp
    int findKey(const int k){
        for(int i = 0; i<count; i++){
            if(Data[i].key == k) return i;
        }
        return -1;
    }
```

##### 노드에서 데이터 삭제

데이터 삽입과 달리 데이터의 index를 이미 알고 있는 상황이라고 생각하자. 그러면 그 index만 지우면 되는데 이때 Children 배열에서는 Left child의 값을 지우는 것으로 하자. 

```cpp
    void deleteKey(int index){
        if(index >= count) return;
        int i = index + 1;
        for(; i<count; i++){
            Data[i - 1].key = Data[i].key;
            Data[i - 1].value = Data[i].value;
            Children[i - 1] = Children[i];
        }
        Children[i - 1] = Children[i];
        count--;
    };
```

##### 노드 구현

key가 int인 경우로 구현한 노드는 다음과 같다. 

```cpp
#include <vector>
#include <iostream>
using namespace std;
struct Datum {
    int key;
    int value;
    Datum (){ key = 0; value = 0;};
};
struct Node {
    vector<Datum> Data;
    vector<Node *> Children;
    int Dim;
    int count;
    Node(int d){
        Dim = d;
        Data.resize(Dim);
        Children.resize(Dim + 1, NULL);
        count = 0;
    };
    ~Node(){};
    int keyAt(int index){
        return Data[index].key;
    };
    int valueAt(int index){
        return Data[index].value;
    };
    Node* leftChildOf(int index){
        return Children[index];
    };
    Node* rightChildOf(int index){
        return Children[index + 1];
    };
    void insertKey(int k, int v, Node* leftChild, Node* rightChild){
        if(count >= Dim) return;
        int i = count-1;  //last index prior to insertion
        while(i>=0 && Data[i].key > k){
            Data[i + 1].key = Data[i].key;
            Data[i + 1].value = Data[i].value;
            Children[i + 1] = Children[i];
            i--;
        }
        i++;
        Data[i].key = k;
        Data[i].value = v;
        Children[i] = leftChild;
        Children[i+1] = rightChild;
        count++;
    };
    void deleteKey(int index){
        if(index >= count) return;
        int i = index + 1;
        for(; i<count; i++){
            Data[i - 1].key = Data[i].key;
            Data[i - 1].value = Data[i].value;
            Children[i - 1] = Children[i];
        }
        Children[i - 1] = Children[i];
        count--;
    };
    int findKey(const int k){
        for(int i = 0; i<count; i++){
            if(Data[i].key == k) return i;
        }
        return -1;
    }
};
```

#### B 트리의 구조

Root Node를 가리키는 HeadNode를 만들어 둔다. key가 하나도 없어도 HeadNode는 존재하는 것이다. 그리고 HeadNode의 Children[0]이 Root Node를 가리킨다. key가 없을 때는 `HeadNode->Children[0]`이 NULL이다. 

```cpp
class BTree {
	private:
		Node* head;
	public:
		BTree(){
			head = new Node(5); //5차 Btree
		};
		~BTree(){
			delete head;	
		};
};
```

#### B 트리의 검색

다음과 같은 순서에 따라 iteration하며 검색한다. 

```
1. Root Node에서부터 시작하며 입력된 key값을 찾는다. 
2. Node가 NULL이 아니고 자신의 노드에서 key를 찾지 못하면 
3. Keys[]를 index 0부터 마지막 index까지 돌면서 입력 key보다 큰 key를 가진 index의 왼쪽 Child 노드를 현재 Node로 갱신해서 
4. 2-3을 반복한다. 
5. 만약 Node가 NULL이기 때문에 2-3 반복이 종료된 것이면 찾지 못한 것이다.
6. 만약 Node가 NULL이 아니면서 종료된 것이면 해당 index를 이용해 그 값을 반환한다.  
```

<figure>
	<img src="/images/btree-search.gif" alt="">
</figure>

##### 검색의 구현

위의 검색 알고리즘을 코드로 구현하면 다음과 같다. 

```cpp
bool find(const int &key, int &value){
	Node *temp = head->Children[0];	
	while( temp != NULL){
		/* Node의 key값 확인 */
		int index = temp->findKey(key);
		if(index >= 0) {	//Found!
			value = temp->valueAt(index);
			return true;
		}
		/* Children 확인*/
		int c_index = 0;
		for(; c_index< temp->count; c_index++){
			if(key < temp->keyAt(c_index)) break;
		}	
		temp = temp->leftChildOf(c_index);
	}
	return false;
}
```
