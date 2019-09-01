---
layout: post
title: Data Structure - binary search tree
modified: 2016-02-13
tags: [datastructure]
parent: Coding Interview
nav_order: 2
---

직접 구현해 본 binary search tree. iterative binary tree travasal에 익숙해져야 한다. 

### 구조

tree를 design할때 가장 먼저 고민할 것은 tree를 구성하는 Node의 정의이다. 입력 데이터는 key와 value 조합으로 했으며, 삽입, 검색, 삭제의 기준은 key로 했다. array가 아닌 pointer로 tree를 구성하고자 다음과 같이 Node를 정의했다. 구현을 용이하게 하기 위해 constructor를 담은 struct로 정의했다. 

```cpp
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
```

public interface인 함수 프로토타입은 다음과 같이 선언했다.  

```cpp
class BST{
public:
    BST(); //자료구조(root) 초기화
    ~BST(); //할당된 resource 모두 해제
    void insertItem(int key, int value); //item 삽입
    void removeItem(int key); //item 삭제
    bool findItem(int key, int &value); //item을 key로 검색해서 value를 획득. 없으면 false를 리턴
    void print(); //tree의 모든 아이템을 inorder로 출력 
};
```

위의 프로토타입을 뒷바침하기 위한 private 멤버는 다음과 같이 도출했다. 

```cpp
class BST{
private:
    Node *root;   
    void removeTree(Node *r); //root r인 tree에 할당된 resource를 모두 해제 
    pair<int, int> getBiggest(Node **r); //root r인 tree 중 가장 큰 key를 가진 Node를 삭제하고 그 key와 value를 반환 
    pair<int, int> getSmallest(Node **r); //root r인 tree 중 가장 작은 key를 가진 Node를 삭제하고 그 key와 value를 반환 
    void printInorder(Node *r); ///root r인 tree를 inorder로 순회하며 출력 
};
```

getBiggest와 getSmallest의 경우 root가 삭제되면 NULL로 root가 변경되어야 하므로 더블포인터를 파라미터로 사용한다. 

binary tree의 iterative search는 기본적으로 아래와 같은 방식으로 한다.  

```cpp
bool find(int k, int &val){
    Node *tmp = root;
    while(tmp != NULL){
        if(tmp->key == k){
            val = tmp->value;
            return true;
        }
        if(tmp->key > k){
            tmp = tmp->l_child;
        } else {
            tmp = tmp->r_child;
        }
    }
    return false;
}
```

삽입이나 삭제를 위해서는 Parent Node를 기억하고 있어야 하므로 조금 변형이 필요하다. root node는 parent가 없는 node이므로 먼저 확인을 한다. tmp를 이동시키기 전에 parent를 가리키는 pointer로 tmp를 가리키게 하면 간단히 parent를 저장할 수 있다. 

```cpp
void insert(int k, int val){
    if(root == NULL){
        root = new Node(k, val);
        return;
    }
    Node *tmp = root;
    Node *p = tmp;
    while(tmp!= NULL){
        p = tmp;
        if(tmp->key > k){
            tmp = tmp->l_child;
        } else {
            tmp = tmp->r_child;
        }
    }
    if(p->key > k) {
        p->l_child = new Node(k, val);
    } else {
        p->r_child = new Node(k, val);
    }
}
```

tree의 resource해제나 print는 간단히 recursion을 이용해서 한다. 

```cpp
void removeTree(Node *tmp){
    if(tmp == NULL) return;
    if(tmp->l_child) removeTree(tmp->l_child);
    if(tmp->r_child) removeTree(tmp->r_child);
    delete tmp; 
}

void printInorder(Node *r){
    if(r == NULL) return;
    if(r->l_child) printInorder(r->l_child);
    cout << r->key << ", " << r->value ;
    if(r->r_child) printInorder(r->r_child);
}
```

### code

```java
#include <iostream>
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
        if(tmp == NULL) return;
        if(tmp->l_child) removeTree(tmp->l_child);
        if(tmp->r_child) removeTree(tmp->r_child);
        delete tmp;
    }
    pair<int, int> getBiggest(Node **r){ //root의 pointer를 NULL로 변경할 수 있도록 이중 pointer로 입력받음
        Node *tmp = *r;
        Node *p = *r;
        while(tmp->r_child != NULL){ //r_child가 없을때까지 이동
            p = tmp;
            tmp = tmp->r_child;
        }
        pair<int, int> ret(tmp->key, tmp->value);
        if(tmp->l_child){ //만약 l_child가 있을 경우 l_child tree의 가장 큰 아이템으로 대체
            pair<int, int> t = getBiggest(&(tmp->l_child));
            tmp->key = t.first;
            tmp->value = t.second;
        } else { //l_child와 r_child가 모두 없는 node는 그 node를 가리키는 포인터를 NULL로!
            if(tmp != p){ //tree의 root가 아닌 경우
                p->r_child = NULL;
            } else { //root인 경우
                *r = NULL; //!!!!
            }
            delete(tmp);
        }
        return ret;
    }
    pair<int, int> getSmallest(Node **r){ //root의 pointer를 NULL로 변경할 수 있도록 이중 pointer로 입력 받음
        Node *tmp = *r;
        Node *p = *r;
        while(tmp->l_child != NULL){ //l_child가 없을때까지 이동
            p = tmp;
            tmp = tmp->l_child;
        }
        pair<int, int> ret(tmp->key, tmp->value);
        if(tmp->r_child){
            pair<int, int> t = getSmallest(&(tmp->r_child));
            tmp->key = t.first;
            tmp->value = t.second;
        } else {
            if(tmp != p){
                p->l_child = NULL;
            } else {
                *r = NULL; //root인 경우 NULL로 변경
            }
            delete(tmp);
        }
        return ret;
    }
    void printInorder(Node *r){
        if(r == NULL) return;
        if(r->l_child) printInorder(r->l_child);
        cout << "(" << r->key << ", " << r->value << ") ";
        if(r->r_child) printInorder(r->r_child);
    }
public:
    BST(){
        root = NULL;
    }
    virtual ~BST(){
        removeTree(root);
    }
    void insertItem(int k, int val){
        if(root == NULL){
            root = new Node(k, val);
            return;
        }
        Node *tmp = root;
        Node *p = tmp;
        while(tmp!= NULL){
            p = tmp;
            if(tmp->key > k){
                tmp = tmp->l_child;
            } else {
                tmp = tmp->r_child;
            }
        }
        if(p->key > k) {
            p->l_child = new Node(k, val);
        } else {
            p->r_child = new Node(k, val);
        }
    }
    bool findItem(int k, int &val){
        Node *tmp = root;
        while(tmp != NULL){
            if(tmp->key == k){
                val = tmp->value;
                return true;
            }
            if(tmp->key > k){
                tmp = tmp->l_child;
            } else {
                tmp = tmp->r_child;
            }
        }
        return false;
    }
    void removeItem(int k){
        if(root == NULL) return; //빈 tree
        Node *tmp = root;
        Node *p = tmp;
        while(tmp != NULL){
            if(tmp->key == k){
                if(tmp->r_child){ //r_child가 있는 경우 r_child tree의 가장 작은 item으로 Node를 대체
                    pair<int, int> t= getSmallest(&(tmp->r_child));
                    tmp->key = t.first;
                    tmp->value = t.second;
                } else if (tmp->l_child){ //r_child가 없고 l_child만 있는 경우 l_child의 가장 큰 item으로 Node를 대체
                    pair<int, int> t= getBiggest(&(tmp->l_child));
                    tmp->key = t.first;
                    tmp->value = t.second;
                } else { //leaf node인 경우 그냥 지운다. 이때 그 node를 가리키던 pointer를 NULL로 만든다.
                    if(tmp == root){ //root일 경우
                        root = NULL;
                    } else {
                        if(p->key > k){
                            p->l_child = NULL;
                        } else {
                            p->r_child = NULL;
                        }
                    }
                    delete tmp;
                }
                return;
            }
            p = tmp;
            if(tmp->key > k){
                tmp = tmp->l_child;
            } else {
                tmp = tmp->r_child;
            }
        }
    }
    void print(){
        cout << endl;
        printInorder(root);
        cout << endl;
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
    b.print();
    
    int val;
    //remove middle node
    if(b.findItem(5, val)) cout << "Key 5 : "<< val << endl;
    else cout << "Key 5 : Not found" <<endl;
    b.removeItem(5);
    if(b.findItem(5, val)) cout << "Key 5 : "<< val << endl;
    else cout << "Key 5 : Not found" <<endl;
    b.print();
    
    //remove leaf node
    if(b.findItem(0, val)) cout << "Key 0 : "<< val << endl;
    else cout << "Key 0 : Not found" <<endl;
    b.removeItem(0);
    if(b.findItem(0, val)) cout << "Key 0 : "<< val << endl;
    else cout << "Key 0 : Not found" <<endl;
    b.print();
    
    //remove root node
    if(b.findItem(10, val)) cout << "Key 10 : "<< val << endl;
    else cout << "Key 10 : Not found" <<endl;
    b.removeItem(10);
    if(b.findItem(10, val)) cout << "Key 10 : "<< val << endl;
    else cout << "Key 10 : Not found" <<endl;
    b.print();
    
    return 0;
}
```
