---
layout: post
title: 데이터 구조 - Binary tree 문제들
description: binary tree 및 binary search tree 문제들 
modified: 2016-02-15
tags: [data structure]
comments: true
image:
  feature: algorithm.jpeg
---

Binary Tree와 [Binary Search Tree](http://hochulshin.com/data-structure-binary-search-tree/)에 관한 문제들은 recursion에 대한 이해가 있어야만 풀 수 있다. 그 문제들, 접근법, 해법을 간단히 살펴본다. 

### Binary Tree 문제와 해법들

#### Binary Tree의 크기 구하기 

Binary tree의 크기는 구성하는 노드의 숫자를 말한다. 간단히 Node의 왼쪽 자식이 구성하는 트리의 노드 숫자와 오른쪽 자식이 구성하는 노드 숫자, 그리고 자기 자신을 더해서 반환하면 된다. 물론 NULL check를 잊으면 안된다. 

```javascript
    int size(Node *r){
        if (r == NULL) return 0;
        return size(r->l_child) + size(r->r_child) + 1;
    }
```

#### Binary Tree 최대 깊이 구하기 

Binary tree의 최대 depth는 자신이 NULL이면 0, 아니면 왼쪽 자식 트리의 최대 depth와 오른쪽 자식 트리의 최대 depth중 큰 것을 택하고 거기에 자신의 깊이 1을 더한 것이다. 

```javascript
    int maxDepth(Node *r){
        if (r == NULL) return 0;
        int l_depth = maxDepth(r->l_child);
        int r_depth = maxDepth(r->r_child);
        return max(l_depth, r_depth) + 1;
    }
```

#### Binary Tree의 최대 직경 구하기 

Binary tree의 최대 직경은 가장 멀리 떨어져 있는 노드의 거리(edge 수)를 말한다. 주의할 것은 root 노드가 포함되지 않는 최대 직경도 가능하다는 것이다. 하지만 높이는 관련이 깊다. 한 노드에서 자신의 왼쪽 자식 트리의 최대 깊이와 오른쪽 자식 트리의 최대 깊이를 더한 것이 그 노드가 구성하는 트리의 직경이 된다. 각 노드 마다 직경을 구해서 그것 중 가장 큰 것을 택하면 트리의 최대 직경이 된다. 

```javascript
	int m_dia;
    int maxDiameter(Node *r){
        if(r == NULL)
            return 0;
        int l_depth = maxDiameter(r->l_child); //max depth of l_child tree
        int r_depth = maxDiameter(r->r_child); //max depth of r_child tree
        if(m_dia < l_depth + r_depth) //l_depth + r_depth : diameter of this node
            m_dia = l_depth + r_depth; //update the max diameter
        return max(l_depth, r_depth) + 1; //max depth at this node
    }
    int main(){
    	m_dia = 0;
    	int maxDiameter(root);
    	cout << m_dia; //최대 직경 
    	return 0;
    }
```

#### Binary Tree의 root부터 모든 leaf 노드까지의 경로 출력하기 

함수의 파라미터로 vector와 depth를 넘겨주고 leaf 노드에 도달하면 0부터 depth까지 출력하게 한다.  
leaf 노드는 왼쪽 오른쪽 자식이 없고 NULL이 아닌 노드이다. 

```javascript
    void printPath(Node *r, vector<int> &T, int len){
        T[len] = r->key;
        if (!r->l_child && !r->r_child){
            for (int i = 0; i <= len; i++)
                cout << T[i] << " ";
            cout << endl;
            return;
        }
        if (r->l_child) printPath(r->l_child, T, len+1);
        if (r->r_child) printPath(r->r_child, T, len+1);
    };
```

#### Binary Tree의 모든 노드 제거 

마치 post order 순회처럼 하면 된다. 

```javascript
    void removeTree(Node *tmp){
        if (tmp == NULL) return;
        if (tmp->l_child) removeTree(tmp->l_child);
        if (tmp->r_child) removeTree(tmp->r_child);
        delete tmp;
    }
```

### Binary Search Tree의 문제와 해법

#### isBST 문제 

Binary Tree가 BST인지 확인하는 문제이다. 5가지 경우를 처리하면 된다. 

- 노드가 NULL : BST 속성을 해치지 않는다. 
- 노드의 자식 노드가 모두 없음 : BST 속성을 해치지 않는다. 
- 노드의 오른쪽 자식이 있고 왼쪽 자식이 없음: 왼쪽 자식에 대한 속성(왼쪽 자식보다 노드의 값이 더 커야 한다. 그리고 자식 트리도 그 속성을 만족해야 한다.)을 유지하는 지 확인해야 한다. 
- 노드의 왼쪽 자식이 있고 오른쪽 자식이 없음: 오른쪽 자식에 대한 속성(오른쪽 자식보다 노드의 값이 더 작아야 하고 자식 트리도 만족해야 한다.)을 유지하는 지 확인해야 한다. 
- 노드의 양 자식이 모두 존재: 두 자식에 대한 속성을 모두 확인해야 한다. 그리고 양 자식 트리도 그 속성을 만족해야 한다.  


```javascript
    bool isbst(Node *r){
        if (r == NULL)
            return true;
        if (r->l_child && r->r_child){
            if (r->l_child->key >= r->key || r->r_child->key <= r->key) return false;
            return isbst(r->l_child) && isbst(r->r_child);
        } else if (r->l_child){
            if (r->l_child->key >= r->key) return false;
            return isbst(r->l_child);
        } else if (r->r_child){
            if (r->r_child->key <= r->key) return false;
            return isbst(r->r_child);
        }
        return true;
    }
```

### 코드  

```javascript

#include <iostream>
#include <algorithm>
#include <vector>
using namespace std;
struct Node {
    int key;
    int value;
    Node *l_child;
    Node *r_child;
    Node(int k, int val){
        key = k;
        value = val;
        l_child = NULL;
        r_child = NULL;
    };
};
class BST {
private:
    Node *root;
    void removeTree(Node *tmp){
        if (tmp == NULL) return;
        if (tmp->l_child) removeTree(tmp->l_child);
        if (tmp->r_child) removeTree(tmp->r_child);
        delete tmp;
    }
    bool isbst(Node *r){
        if (r == NULL)
            return true;
        if (r->l_child && r->r_child){
            if (r->l_child->key >= r->key || r->r_child->key <= r->key) return false;
            return isbst(r->l_child) && isbst(r->r_child);
        } else if (r->l_child){
            if (r->l_child->key >= r->key) return false;
            return isbst(r->l_child);
        } else if (r->r_child){
            if (r->r_child->key <= r->key) return false;
            return isbst(r->r_child);
        }
        return true;
    }
    int size(Node *r){
        if (r == NULL) return 0;
        return size(r->l_child) + size(r->r_child) + 1;
    }
    int maxDepth(Node *r){
        if (r == NULL) return 0;
        int l_depth = maxDepth(r->l_child);
        int r_depth = maxDepth(r->r_child);
        return max(l_depth, r_depth) + 1;
    }
    void printPath(Node *r, vector<int> &T, int len){
        T[len] = r->key;
        if (!r->l_child && !r->r_child){
            for (int i = 0; i <= len; i++)
                cout << T[i] << " ";
            cout << endl;
            return;
        }
        if (r->l_child) printPath(r->l_child, T, len+1);
        if (r->r_child) printPath(r->r_child, T, len+1);
    };
    int m_dia;
    int maxDiameter(Node *r){
        if(r == NULL)
            return 0;
        int l_depth = maxDiameter(r->l_child); //max depth of l_child tree
        int r_depth = maxDiameter(r->r_child); //max depth of r_child tree
        if(m_dia < l_depth + r_depth) //l_depth + r_depth : diameter of this node
            m_dia = l_depth + r_depth; //update the max diameter
        return max(l_depth, r_depth) + 1; //max depth at this node
    }
    void inorder_traversal(Node *r, vector<int> &T){
        if(r == NULL) return;
        inorder_traversal(r->l_child, T);
        T.push_back(r->key);
        inorder_traversal(r->r_child, T);
    }
    void postorder_traversal(Node *r, vector<int> &T){
        if(r == NULL) return;
        postorder_traversal(r->l_child, T);
        postorder_traversal(r->r_child, T);
        T.push_back(r->key);
    }
public:
    BST(){
        root = NULL;
    }
    virtual ~BST(){
        removeTree(root);
    }
    void insertItem(int k, int val){
        if (root == NULL){
            root = new Node(k, val);
            return;
        }
        Node *tmp = root;
        Node *p = tmp;
        while (tmp != NULL){
            p = tmp;
            if (tmp->key > k){
                tmp = tmp->l_child;
            }
            else {
                tmp = tmp->r_child;
            }
        }
        if (p->key > k) {
            p->l_child = new Node(k, val);
        }
        else {
            p->r_child = new Node(k, val);
        }
    }
    bool isBST(){
        return isbst(root);
    }
    int size(){
        return size(root);
    }
    int maxDepth(){
        return maxDepth(root);
    }
    int minVal(){
        if (!root) return -1; //empty
        Node *temp = root;
        Node *p = root;
        while (temp != NULL){
            p = temp;
            temp = temp->l_child;
        }
        return p->key;
    }
    int maxVal(){
        if (!root) return -1; //empty
        Node *temp = root;
        Node *p = root;
        while (temp != NULL){
            p = temp;
            temp = temp->r_child;
        }
        return p->key;
    }
    void printAllPath(){
        vector<int> T(100, 0);
        int len = 0;
        printPath(root, T, len);
    }
    int maxDiameter(){
        m_dia = 0;
        maxDiameter(root);
        return m_dia;
    }

    int findTheLowestCommonAncenstor(int keyA, int keyB){
        //in-order traversal의 속성으로 keyA부터 key B까지의 영역이 하나의 subtree의 일부분
        vector<int> I;
        inorder_traversal(root, I);
        //post-order traversal의 속성으로 한 subtree의 root는 맨 뒤에 위치
        vector<int> P;
        postorder_traversal(root, P);
        //keyA와 keyB 영역을 in-order 결과에서 찾는다. index를 찾고 작은 것을 앞으로 위치 시킴
        int first_index = -1;
        int last_index = -1;
        for(int i = 0; i<I.size(); i++){
            if(I[i] == keyA) first_index = i;
            if(I[i] == keyB) last_index = i;
        }
        if(first_index > last_index){
            int tmp = first_index;
            first_index = last_index;
            last_index = tmp;
        }
        //in-order의 keyA와 keyB의 아이템 중 post-order로 순회된 아이템 중 가장 뒤의 것을 찾는다.
        int last_mark = -1;
        for(int i = first_index; i<= last_index; i++){
            for(int j = 0; j<P.size(); j++){
                if(P[j] == I[i]){
                    if(last_mark < j){
                        last_mark = j;
                    }
                }
            }
        }
        return P[last_mark]; //post-order로 찾은 것중 마지막 것이 root
    }
};
int main(){
    BST b;
    b.insertItem(10, 100);
    b.insertItem(1, 200);
    b.insertItem(5, 300);
    b.insertItem(7, 100);
    b.insertItem(20, 400);
    b.insertItem(15, 500);
    b.insertItem(17, 600);
    b.insertItem(0, 700);
    /*
              10
         1         20
       0   5    15
            7     17
     */
    /* check if the tree is a BST */
    if (b.isBST()) cout << "BST" << endl;	//must be "BST"
    else cout << "NOT BST" << endl;
    /* get the size of binary tree */
    cout << b.size() << endl; // must be 8
    /* get the maximum depth of binary tree*/
    cout << b.maxDepth() << endl; //msut be 4
    /* get the min & max value of binary search tree */
    cout << b.minVal() << endl; //must be 0
    cout << b.maxVal() << endl; //must be 20
    /* print all the possible paths in a binary tree*/
    b.printAllPath(); //must be 10 1 0, 10 1 5 7, 10 20 15 17
    /* find the max diameter of the binary tree */
    cout << b.maxDiameter() << endl; //must be 6 (num of edges)
    /* Find the lowest common ancestor from two nodes in binary tree */
    cout << b.findTheLowestCommonAncenstor(7, 0) << endl; //must be 1
    cout << b.findTheLowestCommonAncenstor(17, 1) << endl; //must be 10
    cout << b.findTheLowestCommonAncenstor(10, 17) << endl; //must be 10
    return 0;
}
```


