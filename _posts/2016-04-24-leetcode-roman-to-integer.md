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

class RomanToIntConverter {
private:
    unordered_map<char, int> Table;
public:
    RomanToIntConverter(){
        Table.insert(make_pair<char, int>('I', 1));
        Table.insert(make_pair<char, int>('V', 5));
        Table.insert(make_pair<char, int>('X', 10));
        Table.insert(make_pair<char, int>('L', 50));
        Table.insert(make_pair<char, int>('C', 100));
        Table.insert(make_pair<char, int>('D', 500));
        Table.insert(make_pair<char, int>('M', 1000));
    }
    int convert(string s) {
        int next = Table[s.back()];
        int sum = next;
        for(int i = s.length() - 2; i>=0; i--){
            int current = Table[s[i]];
            if(current >= next)
                sum += current;
            else 
                sum -= current;
            next = current;
        }
        return sum;
    }
};

struct Pair {
    int val;
    string roman;
    Pair(int v, string r): val(v), roman(r){};
};
class IntToRomanConverter {
private:
    vector<Pair> Table;
public:
    IntToRomanConverter(){
        Table.push_back(Pair(1, "I"));
        Table.push_back(Pair(4, "IV"));
        Table.push_back(Pair(5, "V"));
        Table.push_back(Pair(9, "IX"));
        Table.push_back(Pair(10, "X"));
        Table.push_back(Pair(40, "XL"));
        Table.push_back(Pair(50, "L"));
        Table.push_back(Pair(90, "XC"));
        Table.push_back(Pair(100, "C"));
        Table.push_back(Pair(400, "CD"));
        Table.push_back(Pair(500, "D"));
        Table.push_back(Pair(900, "CM"));
        Table.push_back(Pair(1000, "M"));
    }
    string convert(int n){
        string ret;
        for(auto rit = Table.rbegin() ; rit != Table.rend(); rit++){
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
    RomanToIntConverter ric; 
    for(auto t: testcases1){
        cout << t << " -> ";        
        cout << ric.convert(t) << endl;
    }
    cout <<"---------------"<< endl;
    vector<int> testcases2 = {36, 2012, 1996};
    IntToRomanConverter irc; 
    for(auto i: testcases2){
        cout << i << " -> ";        
        cout << irc.convert(i) << endl;
    }
    return 0;
}
```

#### Java

RomanToIntConverter.java 

```java
import java.util.*;

public class RomanToIntConverter {
    private HashMap<Character, Integer> Table;
    public RomanToIntConverter(){
        Table = new HashMap<Character, Integer>();
        Table.put(new Character('I'), new Integer(1));
        Table.put(new Character('V'), new Integer(5));
        Table.put(new Character('X'), new Integer(10));
        Table.put(new Character('L'), new Integer(50));
        Table.put(new Character('C'), new Integer(100));
        Table.put(new Character('D'), new Integer(500));
        Table.put(new Character('M'), new Integer(1000));
    }
    public int convert(String s) {
        char[] s_str = s.toCharArray();
        int len = s.length();
        int next = Table.get(s_str[len - 1]);
        int sum = next;
        for(int i = len - 2;  i >= 0; i--){
            int current = Table.get(s_str[i]);
            if(current >= next) 
                sum += current;
            else 
                sum -= current;
            next = current;
        }
        return sum;
    }
    public static void main(String[] argc){
        String[] testcase = {"XXXVI", "MMXII", "MCMXCVI"};
        RomanToIntConverter ric = new RomanToIntConverter();
        for(String s : testcase){
            System.out.print(s + " -> ");
            System.out.println(ric.convert(s)); 
        }
    }
}
```

IntToRomanConverter.java

```java
import java.util.*;

public class IntToRomanConverter {
	private class Pair {
		public int val;
		public String roman;
		public Pair(int v, String r){
			val = v;
			roman = r;
		};
	}
	private ArrayList<Pair> Table;
	public IntToRomanConverter(){
		Table = new ArrayList<Pair>();
		Table.add(new Pair(1, "I"));
		Table.add(new Pair(4, "IV"));
		Table.add(new Pair(5, "V"));
		Table.add(new Pair(9, "IX"));
		Table.add(new Pair(10, "X"));
		Table.add(new Pair(40, "XL"));
		Table.add(new Pair(50, "L"));
		Table.add(new Pair(90, "XC"));
		Table.add(new Pair(100, "C"));
		Table.add(new Pair(400, "CD"));
		Table.add(new Pair(500, "D"));
		Table.add(new Pair(900, "CM"));
		Table.add(new Pair(1000, "M"));
	}
	public String convert(int n){
		String ret = "";		
		for(int i = Table.size() - 1; i>=0; i--){
			if(Table.get(i).val > n) continue;
			int temp = n / Table.get(i).val;	
			for(int j = 0; j<temp; j++){
				ret += Table.get(i).roman;
			}
			n = n % Table.get(i).val;
			if(n == 0) break;
		}
		return ret;
	}
	public static void main(String[] argc){
		int[] testcases = {36, 2012, 1996};
		IntToRomanConverter irc = new IntToRomanConverter();
		for(int i : testcases){
            System.out.print(i);
            System.out.println(" -> " + irc.convert(i)); 			
		}
	}
}

```

#### Python

```python
class RomanToIntConverter(object):
	def __init__(self):
		self.Table = {"I": 1, "V": 5, "X": 10, "L": 50, "C": 100, "D": 500, "M": 1000}
	def convert(self, s):
		next = self.Table[s[len(s) - 1]]
		sum = next
		for i in reversed(range(len(s) - 1)):
			current = self.Table[s[i]]
			if current >= next : 
				sum += current	
			else : 
				sum -= current
			next = current
		return sum

class IntToRomanConverter(object):
	def __init__(self):
		self.Table = [(1, "I"), (4, "IV"), (5, "V"), (9, "IX"), (10, "X"), (40, "XL"), (50, "L"), (90, "XC"), (100, "C"), (400, "CD"), (500, "D"), (900, "CM"), (1000, "M")]
	def convert(self, n):
		ret = ""
		for i, r in reversed(self.Table): 	
			if i > n : continue	
			ret += r*int(n/i)
			n %= i
			if n == 0: break
		return ret

if __name__ == "__main__":
	testcases1 = ["XXXVI", "MMXII", "MCMXCVI"]
	ric = RomanToIntConverter()
	for t in testcases1:
		print(t, end="")
		print("->", ric.convert(t))
	print("------------------")
	irc = IntToRomanConverter()
	testcases2 = [36, 2012, 1996]
	for t in testcases2:
		print(t, end="")
		print("->", irc.convert(t))
```
