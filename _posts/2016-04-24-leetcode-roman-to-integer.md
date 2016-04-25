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
Roman numeral (n)	Decimal value (v)
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

1. Let's store the table above using Hashmap. 
2. Repeat 3 and 4 until the end.
3. Read two characters and find them in the Hashmap. 
4. If found, just sum. If not found, try it again with one character.  
5. Return the sum.

#### Integer to Roman 

1. Let's store the table which has keys with decimal and values with roman. 
2. Find highest decimal value and convert it to roman character(s) using Hashmap. 
3. Then subtract the decimal value from the value. Repeat  
4. Repeat 2 and 3 until the value become 0.
5. Return the roman string. 

#### Complexity

O(N) thanks to Hashmap

#### Cpp

```
--
```
#### Java

```java

}
```

#### Python

```python

		print("------------------")
```
