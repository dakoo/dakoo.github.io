---
layout: post
title: leetcode-Implement Queue using Stacks
description: leetcode
modified: 2016-05-18
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)

### Problem

Understanding about queue and stack. Implement queue using stacks

### Solution 

#### Approach

Let's use two stacks. One is "front" stack and the other is "back" stack.

- Push() in queue: Push the new item to the back stack. 
- int peek() in queue: If front stack is empty, pop all items in the back stack and push to the front stack one by one. Then peek the item at the top of the front stack. 
- pop() in queue: invoke peek() and then pop an item from the front stack. 
- bool empty() in queue: if both of front and back stack are empty, return true

#### Complexity

Time complexity: O(1) (for each operation) 

#### Cpp

To reverse a vector, do "reverse(begin(v), end(v));" 

- Solution.hpp

```cpp
#include <vector>

using namespace std;

class Queue {
private:
	vector<int> front;
	vector<int> back;
public:
    // Push element x to the back of queue.
    void push(int x);

    // Removes the element from in front of queue.
    void pop(void);

    // Get the front element.
    int peek(void);

    // Return whether the queue is empty.
    bool empty(void);
};
```

- Solution.cpp

```cpp
#include "Solution.hpp"

void Queue::push(int x) {
    back.push_back(x);
}

void Queue::pop(void) {
    peek();
    front.pop_back();
}

int Queue::peek(void) {
    if(front.empty()){
        while(!back.empty()){
            front.push_back(back.back());
            back.pop_back();
        }
    }
    return front.back();
}

bool Queue::empty(void) {
    return front.empty() && back.empty(); 
}
```
