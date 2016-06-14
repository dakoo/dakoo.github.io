---
layout: post
title: leetcode-N Queens II
description: leetcode
modified: 2016-06-13
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[leetcode - N Queens II](https://leetcode.com/problems/n-queens-ii/)

### Problem

- Find the total number of distinct solutions.(Not the maximum number of queens!!) 
- Put queens at the position that queens can not kill each other. 
- A queen can move in the vertical, horizontal, and diagonal directions.

### Solution 

Very typical backtracking problem

#### Approach

1. Try to put a queen at all the possible position at the first row and store the position. Since the queen covers the row, we have to try to next row. 
2. From the second row to the last row, we need to check if we can put a queen at an each position as follows:

```python
def isPossibleToPut(row_index, column_index, previousQueens):
    for queen in previousQueens:
        #Case 1: vertical 
        if queen["column"] == column_index: 
            return False 

        # Case 2: The queen is located at the left diagonal position of the current position
        if (queen["row"] - queen["column"]) == (row_index - column_index): 
            return False

        # Case 3: The queen is located at the right diagonal position of the current position
        if (queen["row"] + queen["column"]) == (row_index + column_index): 
            return False
```

```
  0 1 2 3 4 
0 - q - - - 
1 x x x - -  
2
3
4
```

For example, let's assume a queen's coordinate is (0, 1)(row, column). 
At row 1, 

- We can't put a new queen at (1, 0) because of case 3 ( 0 + 1 == 1 + 0). 
- We can't put a new queen at (1, 1) because of case 1 ( Both are column 1). 
- We can't put a new queen at (1, 2) because of case 2 ( 0 - 1 == 1 - 2). 

3. If there is no available column at a row, don't go forward. 
4. If there is no more row, return 1

##### Complexity

- Time complexity: O(n**3) (At each position, we have to check if we can put a queen or not there)

### Implementation

#### C++

- Solution.hpp

```cpp
#include <vector>

class Solution {
private:
    int isPossibleToPut(int row_index,
                        int column_index, std::vector<std::pair<int, int>>& queens);
    int putQueens(int row_index,
                 std::vector<std::pair<int, int> >& queens, int n);
public:
    int totalNQueens(int n);
};

```

- SolutionTest.cpp

```cpp
#define CATCH_CONFIG_MAIN
#include <vector>
#include "catch.hpp"
#include "Solution.hpp"

TEST_CASE("N Queens II - normal", "[Solution]") {
    Solution so;
    SECTION("Normal 1") {
        int input = 4;
        int expected_result = 2;
        REQUIRE(so.totalNQueens(input) == expected_result);
    }
    SECTION("Normal 2") {
        int input = 5;
        int expected_result = 10;
        REQUIRE(so.totalNQueens(input) == expected_result);
    }
    SECTION("Normal 3") {
        int input = 6;
        int expected_result = 4;
        REQUIRE(so.totalNQueens(input) == expected_result);
    }
    SECTION("Normal 4") {
        int input = 8;
        int expected_result = 92;
        REQUIRE(so.totalNQueens(input) == expected_result);
    }
    SECTION("Normal 5") {
        int input = 10;
        int expected_result = 724;
        REQUIRE(so.totalNQueens(input) == expected_result);
    }
};
TEST_CASE("N Queens II - exception", "[Solution]") {
    Solution so;
    SECTION("If the board size is 0, it should be 0") {
        int input = 0;
        int expected_result = 0;
        REQUIRE(so.totalNQueens(input) == expected_result);
    }
    SECTION("If the board size is 1, it should be 1") {
        int input = 1;
        int expected_result = 1;
        REQUIRE(so.totalNQueens(input) == expected_result);
    }
    SECTION("If the board size is 2, it should be 0") {
        int input = 2;
        int expected_result = 0;
        REQUIRE(so.totalNQueens(input) == expected_result);
    }
    SECTION("If the board size is 3, it should be 0") {
        int input = 3;
        int expected_result = 0;
        REQUIRE(so.totalNQueens(input) == expected_result);
    }
};

```

- Solution.cpp

```cpp
#include "Solution.hpp"
#include <vector>

int Solution::isPossibleToPut(int row_index,
                              int column_index, std::vector<std::pair<int, int> >& queens) {
    for (auto queen : queens) {
        if (queen.second == column_index)
            return false;
        if ((queen.first + queen.second) == (row_index + column_index))
            return false;
        if ((queen.first - queen.second) == (row_index - column_index))
            return false;
    }
    return true;
}
int Solution::putQueens(int row_index,
                        std::vector<std::pair<int, int> >& queens, int n) {
    if (row_index == n) {
        return 1;
    }
    int ret = 0;
    for (int column_index = 0; column_index < n; column_index++) { 
        if (isPossibleToPut(row_index, column_index, queens)) {
            queens.emplace_back(row_index, column_index);
            ret += putQueens(row_index + 1, queens, n);
            queens.pop_back();
        }
    }
    return ret;
}
int Solution::totalNQueens(int n) {
    if (n == 0)
        return 0;
    std::vector<std::pair<int, int> > queens;
    return putQueens(0, queens, n);
}

```
