---
layout: post
title: 데이터구조 - LRU(Least Recently Used) 
description: LRU Cache
modified: 2016-02-14
tags: [data structure]
comments: true
image:
  feature: algorithm.jpeg
---
직접 구현해 본 LRU Cache. O(1) 시간 복잡도로 lookup과 put을 만족시키기 위해서는 Entry에 access하는 것도 O(1)이고 LRU인지 판별하는 것도 O(1)으로 해야 한다. 이를 위해 ks(C++은 unordered_map)과 doubly linked list를 사용했다. Cache Replacement가 실행되는 것은  Cache가 full인 것은 unordered_map의 size()가 미리 정한 entry의 갯수에 도달했을 경우이다.  

### 구조

입력 데이터는 key와 value 조합으로 했으며, 삽입, 검색, 삭제의 기준은 key로 했다. 삽입, 삭제를 O(1)만에 하기 위해 singly linked list가 아닌 doubly linked list로 했다. 이를 Item의 member variable로 가지고 있으며, hashtable에 그대로 저장되도록 했다. 구현을 용이하게 하기 위해 constructor를 담은 struct로 정의했다. 

```javascript
struct Item {
    string key;
    int value;
    Item *prev;
    Item *next;
    Item(string k, int v): key(k), value(v){
        prev = NULL;
        next = NULL;
    };
};  
```

public interface인 함수 프로토타입은 Cache라는 base class를 사용하여 다음과 같이 선언했다. Cache는 interface로만 동작하므로 pure virtual class이다. 

```javascript
class Cache {
public:
    virtual bool lookup(string key, int &val) = 0;
    virtual void put(string key, int val) = 0;
};
```

LRUCache class는 실제 구현을 포함하고 있으며 그 private 멤버는 다음과 같이 도출했으며 그 구현은 통상적은 doubly linked list의 operation과 같다. 

```javascript
class LRUCache: public Cache{
private:
    /* Linked list 관련 */
    Item *head;  //가장 최근에 참조된 아이템을 가리킴 (head->next)
    Item *tail;  //가장 오래전에 참조된 아이템을 가리킴 (tail->prev)
    void detach(Item *item); //linked list에서 item을 제거
    void push_front(Item *item); //linked list의 head에 item 추가(newest)
    void pop_back(); //linked list의 tail이 가리키는 아이템(oldest) 제거
    bool back(string &key); //가장 오래전에 참조된 아이템의 key 획득, 없으면 false

    /* Hashtable 관련 */
    int MaxNumEntries;
    unordered_map<string,Item*> CacheEntries;
};
```

LRUCache의 publc member의 구현을 살펴보자. 먼저 Constructor와 destructor는 linked list를 초기화하거나 제거한다. 

```javascript
LRUCache(int num):MaxNumEntries(num){
    //head <-> tail
    head = new Item("0", 0);
    tail = new Item("0", 0);
    head->next = tail;
    tail->prev = head;
};
~LRUCache(){
    while(head->next != tail){
        pop_back();
    }
    delete head;
    delete tail;
}
```

Lookup method는 Hashtable에서 해당하는 key가 존재하는지 확인하고 존재하면 그것의 value를 반환한다. 이때, Linked List에서 해당 아이템을 찾아서 그것을 맨 앞으로 이동시킨다. 

```javascript
bool lookup(string k, int &val){
    /* find a matching item */
    auto it = CacheEntries.find(k);
    if(it == CacheEntries.end()){ //not found
        return false;
    }
    val = (*it).second->value; //found
    /* update linked list */
    detach((*it).second); //detach
    push_front((*it).second); //add to the head because this is the latest
    return true;
}
```

Put method는 Hashtable에서 해당하는 key가 존재하는지 먼저 찾는다. 
존재한다면 value를 update하고 Lookup method처럼 Linked List에서 해당 아이템을 찾아서 맨 앞으로 이동시킨다. 
존재하지 않는다면 다음의 두가지 경우를 고려해야 한다. 

- Hashtable의 아이템 숫자가 미리 정한 숫자에 도달하지 않은 경우: 아이템을 Hashtable에 추가하고, LinkedList의 맨 앞에도 추가한다. 
- Hashtable의 아이템 숫자가 미리 정한 숫자에 도달한 경우(Full): Linked List의 맨 뒤에서 가장 오래전에 참조된 아이템의 key를 획득하고, 그 아이템을 Linked List와 Hashtable에서 제거한다. 
그 뒤 새로운 아이템을 Hashtable에 추가하고 Linked List의 맨 앞에 추가한다. 


```javascript
void put(string k, int val){
    /* find a matching item: O(1) operation */
    auto it = CacheEntries.find(k);
    if(it != CacheEntries.end()){ //found
        /* update data */
        (*it).second->value = val;
        /* update linked list */
        detach((*it).second); //detach
        push_front((*it).second); //add to the head because this is the latest
        return;
    }
    /* Add new one: O(1) operation */
    if(CacheEntries.size() != MaxNumEntries){ //Cache is NOT full
        Item *t = new Item(k, val);
        pair<string, Item *> entry(k, t);
        CacheEntries.insert(entry);
        push_front(t); //Latest one
    } else { //Cache is full
        /* Select Victim & delete */
        string key;
        back(key); //get the key of the oldest one
        pop_back();       //remove the oldest one
        auto rt = CacheEntries.find(key);
        if(rt != CacheEntries.end()){ //found
            CacheEntries.erase(rt); //delete
        }
        Item *t = new Item(k, val);
        pair<string, Item *> entry(k, t);
        CacheEntries.insert(entry);
        push_front(t); //Latest one
    }
}
```

### code

```javascript
#include <iostream>
#include <unordered_map>
#include <string>
using namespace std;
struct Item {
    string key;
    int value;
    Item *prev;
    Item *next;
    Item(string k, int v): key(k), value(v){
        prev = NULL;
        next = NULL;
    }
};
class Cache {
public:
    virtual bool lookup(string key, int &val) = 0;
    virtual void put(string key, int val) = 0;
};
class LRUCache : public Cache {
private:
    /* Doubly Linked List: Head - The most recent, Tail - The least recent */
    Item *head;
    Item *tail;
    void detach(Item *item){ //Detach item from linked list : O(1)
        item->next->prev = item->prev;
        item->prev->next = item->next;
    };
    void push_front(Item *item){ //Add the new item at the front : O(1)
        item->next = head->next;
        head->next->prev = item;
        head->next = item;
        item->prev = head;
    };
    void pop_back(){ //delete item from the tail : O(1)
        Item *item = tail->prev;
        if(item != head){
            tail->prev = item->prev;
            delete item;
        }
        else {
            tail->prev = head;
        }
        tail->prev->next = tail;
    };
    bool back(string & key){ //Get the item from the tail: O(1)
        if(tail->prev == head){
            return false;
        }
        key = tail->prev->key;
        return true;
    };
    /* Hash Table */
    int MaxNumEntries;
    unordered_map<string,Item*> CacheEntries;
public:
    LRUCache(int num):MaxNumEntries(num){
        //head <-> tail
        head = new Item("0", 0);
        tail = new Item("0", 0);
        head->next = tail;
        tail->prev = head;
    };
    ~LRUCache(){
        while(head->next != tail){
            pop_back();
        }
        delete head;
        delete tail;
    }
    bool lookup(string k, int &val){
        /* find a matching item */
        auto it = CacheEntries.find(k);
        if(it == CacheEntries.end()){ //not found
            return false;
        }
        val = (*it).second->value; //found
        /* update linked list */
        detach((*it).second); //detach
        push_front((*it).second); //add to the head because this is the latest
        return true;
    }
    void put(string k, int val){
        /* find a matching item: O(1) operation */
        auto it = CacheEntries.find(k);
        if(it != CacheEntries.end()){ //found
            /* update data */
            (*it).second->value = val;
            /* update linked list */
            detach((*it).second); //detach
            push_front((*it).second); //add to the head because this is the latest
            return;
        }
        /* Add new one: O(1) operation */
        if(CacheEntries.size() != MaxNumEntries){ //Cache is NOT full
            Item *t = new Item(k, val);
            pair<string, Item *> entry(k, t);
            CacheEntries.insert(entry);
            push_front(t); //Latest one
        } else { //Cache is full
            /* Select Victim & delete */
            string key;
            back(key); //get the key of the oldest one
            pop_back();       //remove the oldest one
            auto rt = CacheEntries.find(key);
            if(rt != CacheEntries.end()){ //found
                CacheEntries.erase(rt); //delete
            }
            Item *t = new Item(k, val);
            pair<string, Item *> entry(k, t);
            CacheEntries.insert(entry);
            push_front(t); //Latest one
        }
    }
};

int main(){
    LRUCache cache(5);
    cache.put("A", 1);
    cache.put("B", 2);
    cache.put("C", 3);
    cache.put("D", 4);
    cache.put("E", 5);
    int val;
    if(cache.lookup("A", val)){ //'A' is updated. So 'B' becomes the oldest.
        cout << "A's value is " << val << endl; //expected result
    } else {
        cout << "A: Not found" << endl;
    }
    cache.put("F", 6); //Cache replacement. 'B' is removed and 'C' becomes the oldest
    if(cache.lookup("B", val)){
        cout << "B's value is " << val << endl;
    } else {
        cout << "B: Not found" << endl; //expected
    }
    cache.put("C", 7); //'C' is updated. Now 'D' becomes the oldest
    if(cache.lookup("C", val)){
        cout << "C's value is " << val << endl; //expected
    } else {
        cout << "C: Not found" << endl;
    }
    cache.put("G", 8); //Cache replacement. 'D' is removed and 'E' becomes the oldest
    if(cache.lookup("D", val)){
        cout << "D's value is " << val << endl;
    } else {
        cout << "D: Not found" << endl; //expected result
    }
    return 0;
}
```
