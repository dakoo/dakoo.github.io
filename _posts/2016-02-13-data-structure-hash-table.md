---
layout: post
title: Data Structure - hash table
modified: 2016-02-13
tags: [data structure]
---

아주 간단히 구현한 C++ Hash table. 물론 unordered_multimap을 쓰는 것이 편하지만 hash table의 구조에 대해 이해하기 위해 구현해 보았다.

### 구조

Hash table의 entry는 key와 value로 구성되고, key는 주로 string이 많이 쓰이고, value는 다양하다. 코딩이 용이하도록 class가 아닌 struct로 하되 constructor는 추가해 놓았다. 

```cpp
struct Item{
    string key;
    int val;
    Item(string p_key, int p_val){
        key = p_key;
        val = p_val;
    };
};    
```

hash function은 key값을 받아서 각 char의 ascii값을 더해서 전체 entry 숫자로 나눈 나머지를 index로써 반환하도록 구현했다.

```cpp
int hash(string key){
    int sum = 0;
    for(auto &it : key){
        sum += it;
    }
    return sum % entryNum;
};
```

Hash table의 구조는 Separate Chaining이다. 각 Entry마다 linkedlist가 있어서 collision없이 아이템을 쉽게 제거, 추가할 수 있지만, worsecase O(n)인 구조이다.  

```cpp
vector<list<Item> > HT;
```

초기화시에 entry의 숫자를 정하는데 2, 4, 6, 8의 배수로 할 경우 중복의 가능성이 커서 소수로 정하는 것이 좋다. 
그리고 이때 반드시 vector는 초기화 해야 한다. 

```cpp
HashTable(int num){
    if(num <= 0) num = 17;
    entryNum = num;
    HT.resize(num);
}
```

### code

```cpp
#include <iostream>
#include <vector>
#include <list>
#include <string>
using namespace std;
struct Item{
    string key;
    int val;
    Item(string p_key, int p_val){
        key = p_key;
        val = p_val;
    };
};
class HashTable {
private:
    int entryNum;
    vector<list<Item> > HT;
    int hash(string key){
        int sum = 0;
        for(auto &it : key){
            sum += it;
        }
        return sum % entryNum;
    };
public:
    HashTable(int num){
        if(num <= 0) num = 17;
        entryNum = num;
        HT.resize(num);
    }
    void addItem(string key, int value){
        int index = hash(key);
        HT[index].push_back(Item(key, value));
    }
    void removeItem(string key){
        int index = hash(key);
        for(auto it = HT[index].begin(); it!= HT[index].end(); it++){
            if((*it).key == key){
                HT[index].erase(it);
                return;
            }
        }
    }
    bool lookup(string key, int &value){
        int index = hash(key);
        for(auto it = HT[index].begin(); it!= HT[index].end(); it++){
            if((*it).key == key){
                value = (*it).val;
                return true;
            }
        }
        return false;
    }
    void print(){
        int index = 0;
        for(auto it = HT.begin(); it!= HT.end(); it++){
            cout << "INDEX: " << index++ << " || ";
            for(auto lt = (*it).begin(); lt != (*it).end(); lt++){
                cout << "(" << (*lt).key << "," << (*lt).val << ") ";
            }
            cout << endl;
        }
    }
};

int main()
{
    HashTable table(11);
    table.addItem("AAA", 10);
    table.addItem("BBB", 20);
    table.addItem("CCC", 30);
    table.addItem("DDD", 40);
    table.addItem("EEE", 50);
    table.addItem("FFF", 60);
    table.addItem("AAB", 10);
    table.addItem("BBC", 20);
    table.addItem("CCD", 30);
    table.addItem("DDE", 40);
    table.addItem("EEF", 50);
    table.addItem("FFA", 60);
    table.addItem("BAB", 10);
    table.addItem("CBC", 20);
    table.addItem("DCD", 30);
    table.addItem("EDE", 40);
    table.addItem("FEF", 50);
    table.addItem("AFA", 60);
    int val;
    if(table.lookup("EEE", val)){
        cout << val << endl;
    } else {
        cout << "NOT FOUND" << endl;
    }
    table.removeItem("EEE");
    if(table.lookup("EEE", val)){
        cout << val << endl;
    } else {
        cout << "NOT FOUND" << endl;
    }
    table.print();
    return 0;
}
```

### 참고

vector와 list를 사용하지 않은 좀 더 순수(?)한 구현은 보고 싶다면 [여기](http://pumpkinprogrammer.com/2014/06/21/c-tutorial-intro-to-hash-tables/)를 참조하자. 
