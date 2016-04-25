---
layout: post
title: leetcode-roman to integer
description: leetcode
modified: 2016-04-23
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - roman to integer](https://leetcode.com/problems/roman-to-integer/)

### Problem

Roman numerals and the corresponding decimal integers are as follows:

```
Roman numeral (s)	Decimal value (n)
I	1
IV	4
V	5
IX	9
X	10
XL	40
L	50
XC	90
C	100
CD	400
D	500
CM	900
M	1000
```

The examples are as below. 

- XXXVI : 36
- MMXII : 2012 
- MCMXCVI : 1996

In addition, I will implement conversion from integer to roman numerals.

### Solution 

#### Approach

#### Roman to Integer

```
Roman numeral (s)	Decimal value (n)
I	1
V	5
X	10
L	50
C	100
D	500
M	1000
```

1. Let's store the table above using Hashmap. 
2. Repeat 3 and 4 iteratively from the end to the first character in a roman string.
3. Read one character and find them in the Hashmap. 
4. If ith item's value is smaller than i+1th item's value, subtract the value from the sum. Else then, add the value to the sum.   
5. Return the sum.

#### Integer to Roman 

```
Decimal value (n) Roman numeral (s)
1	I
4	IV
5	V
9	IX
10	X
40	XL
50	L
90	XC
100	C
400	CD
500	D
900	CM
1000	M
```

1. Let's store pairs of decimal and roman in a array. 
2. Find highest decimal value and convert it to roman character(s). - Divide the value by the decimal. If the result is not 0, add the corresponding character(s) several times as the result.
3. Then subtract the decimal value from the value. 
4. Repeat 2 and 3 until the value become 0 from the largest decimal value to the smallest.
5. Return the roman string. 

#### Complexity

- Roman to Integer: O(N) thanks to Hashmap
- Integer to Roman: O(M) - M: size of table

#### Cpp

```
#include <iostream>
#include <unordered_map>
#include <map>
#include <string>
#include <vector>

using namespace std;
struct Table {
	int val;
	string roman;
	Table(int v, string r): val(v), roman(r){};
};

class Solution {
private:
	unordered_map<char, int> RomanToIntTable;
	vector<Table> intToRomanTable;
public:
	Solution(){
		RomanToIntTable.insert(make_pair<char, int>('I', 1));
		RomanToIntTable.insert(make_pair<char, int>('V', 5));
		RomanToIntTable.insert(make_pair<char, int>('X', 10));
		RomanToIntTable.insert(make_pair<char, int>('L', 50));
		RomanToIntTable.insert(make_pair<char, int>('C', 100));
		RomanToIntTable.insert(make_pair<char, int>('D', 500));
		RomanToIntTable.insert(make_pair<char, int>('M', 1000));

		intToRomanTable.push_back(Table(1, "I"));
		intToRomanTable.push_back(Table(4, "IV"));
		intToRomanTable.push_back(Table(5, "V"));
		intToRomanTable.push_back(Table(9, "IX"));
		intToRomanTable.push_back(Table(10, "X"));
		intToRomanTable.push_back(Table(40, "XL"));
		intToRomanTable.push_back(Table(50, "L"));
		intToRomanTable.push_back(Table(90, "XC"));
		intToRomanTable.push_back(Table(100, "C"));
		intToRomanTable.push_back(Table(400, "CD"));
		intToRomanTable.push_back(Table(500, "D"));
		intToRomanTable.push_back(Table(900, "DM"));
		intToRomanTable.push_back(Table(1000, "M"));
	}
    int romanToInt(string s) {
    	int next = RomanToIntTable[s.back()];
    	int sum = next;
    	for(int i = s.length() - 2; i>=0; i--){
    		int current = RomanToIntTable[s[i]];
    		if(current >= next)
    			sum += current;
    		else 
    			sum -= current;
    		next = current;
    	}
    	return sum;
    }
    string intToRoman(int n){
    	string ret;
    	for(auto rit = intToRomanTable.rbegin() ; rit != intToRomanTable.rend(); rit++){
    		if((*rit).val > n) continue; 				
    		int temp = n / (*rit).val;
    		for(int i = 0; i< temp; i++){
    			ret += (*rit).roman;
    		}	
    		n = n % (*rit).val;
    		if(n == 0) break;
    	}
    	return ret;
    }
};
int main(){	
	vector<string> testcases1 = {"XXXVI", "MMXII", "MCMXCVI"};
	vector<int> testcases2 = {36, 2012, 1996};
	Solution so; 
	for(auto t: testcases1){
		cout << t << " -> ";		
		cout << so.romanToInt(t) << endl;
	}
	cout <<"---------------"<< endl;
	for(auto i: testcases2){
		cout << i << " -> ";		
		cout << so.intToRoman(i) << endl;
	}
	return 0;
}
```
#### Java

```java

}
```

#### Python

```python

		print("------------------")
```
