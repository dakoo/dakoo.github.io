---
layout: post
title: leetcode-valid anagram
description: leetcode
modified: 2016-04-19
tags: [leetcode]
comments: true
image:
  feature: abstract-11.png
---
[Leetcode - valid anagram](https://leetcode.com/problems/valid-anagram/)

### Problem

If you solve this problem as lowercase alphabet rearannge problem as described, it's too easy. Assume almost all kinds of characters.  

### Solution

#### Approach

Check how many times each character is used in each string. If the number is the same, it's anagram. Hashtable is the best data structure for this.  

#### Cpp

```cpp
#include <unordered_map>
#include <iostream>
#include <string>
#include <algorithm>

using namespace std;
class Solution {
public:
    bool isAnagram(string s, string t) {
        unordered_map<char, int> T;         
        for(auto c : s) {
                T[c]++;
        }
        for(auto c : t) {
            auto it = T.find(c);
            if(it == T.end())
                return false;   
            if(--T[c] == 0)
                T.erase(c); 
        }
        if(T.size() != 0)
            return false;
        return true;
    }
};

int main(){
	Solution so;
	string s[4] = {"hello", "", "", "h l"};
	string t[4] = {"hello", "", "o", "hel"};

	for(int i = 0; i<4; i++){
		if(so.isAnagram(s[i], t[i]))
			cout << s[i] << " and " << t[i] << " is anagram" << endl;
		else 
			cout << s[i] << " and " << t[i] << " is NOT anagram" << endl;
	}

	return 0;
}
```

#### Java

```java
import java.util.*;

public class Solution {
    public boolean isAnagram(String s, String t) {
        HashMap<Character, Integer> T =  new HashMap<Character, Integer>();
        for(char c : s.toCharArray()){
            int value = T.containsKey(c)? T.get(c) + 1 : 1;
            T.put(c, value);
        }
        for(char c : t.toCharArray()){
            if(T.containsKey(c) == false) return false;
            int val = T.get(c);
            if(val > 1)
                T.put(c, val - 1);
            else
                T.remove(c);
        }
        if(T.size() != 0)
            return false;
        return true;
    }
    public static void main(String[] argc){
        Solution so =  new Solution();
        String s[] = {"hello", "", "", "h l"};
        String t[] = {"hello", "", "o", "hel"};
        for(int i = 0; i< 4; i++){
            if(so.isAnagram(s[i], t[i]))
                System.out.println(s[i] + " and " + t[i] + " is anagram");
            else
                System.out.println(s[i] + " and " + t[i] + " is NOT anagram");
        }   
    } 
}
```

#### Python

```python
class Solution(object):
    def isAnagram(self, s, t):
        T = {}
        for c in s :
            T[c] = T.get(c, 0) + 1
        for c in t :
            val = T.get(c, 0)  
            if val > 1 : 
                T[c] = val - 1
            elif val == 1 :
                del T[c]
            else:
                return False
        if len(T) != 0: return False
        return True

if __name__ == "__main__":
        so = Solution()
        s = ["hello", "", "", "h l"]
        t = ["hello", "", "o", "hel"]
        for i in range(len(s)):
            if so.isAnagram(s[i], t[i]):
                print(s[i], "and" , t[i] , "is anagram")
            else:
                print(s[i], "and" , t[i] , "is NOT anagram")
```
