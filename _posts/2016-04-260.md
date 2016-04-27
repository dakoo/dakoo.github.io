/*
problem

Convert a decimal number into a binary number. Then count '1's.

approach

Use bit operations to implement a function to convert decimal to binary as below.

1. Repeat 2 to 4 until the decimal number, N is not 0
2. Divide the N by 2. 
3. Then, the remainder is 1, increase the counter.
4. N = the quotient

```python
    [Slow version: Normal operations]
    int hammingWeight(uint32_t n) {
        int counter = 0;
        while(n != 0){
            if(n%2) counter++;
            n = n/2;
        } 
        return counter;
    }
```
complexity
- O(LogN) : Because N is divided by 2 repeatedly. 
*/
#include <iostream>

using namespace std;

class Solution {
public:
    int hammingWeight(uint32_t n) {
        int counter = 0;
        while(n != 0){
            if(n & 0x1) counter++;
            n = n >> 0x1;
        } 
        return counter;
    }
};

int main(){
    uint32_t testcase = 11;
    Solution so;
    cout << so.hammingWeight(testcase) << endl;
    return 0;
}
