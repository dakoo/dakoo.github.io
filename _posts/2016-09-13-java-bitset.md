---
layout: post
title: Java - BitSet class 예제
description: Java - BitSet class 예제
modified: 2016-09-13
tags: [java]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

Java의 BitSet class의 사용방법을 알아보자. 

### 0. Methods

API는 [여기](https://www.tutorialspoint.com/java/util/java_util_bitset.htm)를 참조하자. 반드시 이해해야 하는 것은 BitSet은 일종의 **boolean element로 이루어진 Array**라는 것이다. BitSet instance를 출력하면 **true인 bit의 index가 Array처럼 출력**된다. 

### 1. set

#### 1.1 void set(int bitIndex)

bitIndex를 true로 set

#### 1.2 void set(int bitIndex, boolean value)

bitIndex를 value로 set

#### 1.3 void set(int fromIndex, int toIndex)

fromIndex부터 toIndex이전까지(즉, toIndex는 exclusive) true로 set

#### 1.4 void set(int fromIndex, int toIndex, boolean value)

fromIndex부터 toIndex이전까지(즉, toIndex는 exclusive) value로 set

#### 1.5 예제 

```java
import java.util.*;

public class Solution {

	public static void main(String[] args) {

		BitSet bitset = new BitSet(8);

		// assign values to bitset1
		bitset.set(0);
		bitset.set(1);
		bitset.set(2);
		bitset.set(3);
		bitset.set(4);
		bitset.set(5);

		// print the sets
		System.out.println("Bitset:" + bitset);

		// set more values
		bitset.set(1, 10, false);

		// print the sets
		System.out.println("Bitset:" + bitset);
	}
}
```

출력은 다음과 같다. 

```bash
Bitset:{0, 1, 2, 3, 4, 5}
Bitset1:{0}

```

### 2. get

#### 2.1 boolean get(int bitIndex)

bitIndex의 value를 획득

#### 2.2 BitSet get(int fromIndex, int toIndex)

fromIndex부터 toIndex이전까지(즉, toIndex는 exclusive)를 BitSet으로 반환

#### 2.3 예제 

```java
import java.util.*;

public class Solution {

	public static void main(String[] args) {

		BitSet bitset = new BitSet(8);

		// assign values to bitset
		bitset.set(0);
		bitset.set(1);
		bitset.set(2);
		bitset.set(3);
		bitset.set(4);
		bitset.set(5);

		// print the sets
		System.out.println("Bitset:" + bitset);

		// get index 1 to 4 of bitset
		System.out.println("" + bitset.get(1,4));
	}
}
```

출력은 다음과 같다. 

```bash
Bitset:{0, 1, 2, 3, 4, 5}
Bitset:{0}
```

### 3. flip

#### 3.1 void flip(int bitIndex)

bitIndex의 value를 toggle

#### 3.2 void flip(int fromIndex, int toIndex)

fromIndex부터 toIndex이전까지(즉, toIndex는 exclusive) value들을 각각 toggle

#### 3.3 예제 

```java
import java.util.*;

public class Solution {

   public static void main(String[] args) {

   BitSet bitset = new BitSet(8);

   // assign values to bitset
   bitset.set(0);
   bitset.set(1);
   bitset.set(2);
   bitset.set(3);
   bitset.set(4);
   bitset.set(5);

   // print the sets
   System.out.println("Bitset:" + bitset);

   // flip from index 2 to index 5 of bitset and print it
   bitset.flip(2,5);
   System.out.println("" + bitset);
   }
}
```

출력은 다음과 같다. 

```bash
Bitset:{0, 1, 2, 3, 4, 5}
{0, 1, 5}
```

### 4. Bitwise Operation 

#### 4.1 void and(BitSet set)

set과 logical AND한 것으로 update

####4.2 void andNot(BitSet set)

set에서 true인 것을 모두 false로 update

#### 4.3 boolean intersects(BitSet set)

두개의 BitSet instance에서 하나의 bit index라도 모두 true인 경우 true를 반환

#### 4.4 void or(BitSet set)

Logical OR operation

#### 4.5 void xor(BitSet set)

Logical XOR operation. 두개의 set에서 각 Bit의 값이 다른 경우만 true

#### 4.6 예제 

Bitwise Operation의 예는 다음과 같다.

```java
import java.util.BitSet;

class BitSetDemo {
	public static void main(String args[]) {
		BitSet bits1 = new BitSet(16);
		BitSet bits2 = new BitSet(16);
		// set some bits
		for(int i=0; i<16; i++) {
			if((i%2) == 0) bits1.set(i);
			if((i%5) != 0) bits2.set(i);
		}
		System.out.println("Initial pattern in bits1: ");
		System.out.println(bits1);
		System.out.println("\\nInitial pattern in bits2: ");
		System.out.println(bits2);
		// AND bits
		bits2.and(bits1);
		System.out.println("\\nbits2 AND bits1: ");
		System.out.println(bits2);
		// OR bits
		bits2.or(bits1);
		System.out.println("\\nbits2 OR bits1: ");
		System.out.println(bits2);
		// XOR bits
		bits2.xor(bits1);
		System.out.println("\\nbits2 XOR bits1: ");
		System.out.println(bits2);
	}
}
```

결과는 다음과 같다. 

```bash
Initial pattern in bits1: 
{0, 2, 4, 6, 8, 10, 12, 14} 
Initial pattern in bits2: 
{1, 2, 3, 4, 6, 7, 8, 9, 11, 12, 13, 14} 
bits2 AND bits1: 
{2, 4, 6, 8, 12, 14} 
bits2 OR bits1: 
{0, 2, 4, 6, 8, 10, 12, 14} 
bits2 XOR bits1: 
{}
```

### 5. Bitwise Operation 

Bitwise Operation의 예는 다음과 같다.

```java
import java.util.BitSet;

class BitSetDemo {
	public static void main(String args[]) {
		BitSet bits1 = new BitSet(16);
		BitSet bits2 = new BitSet(16);
		// set some bits
		for(int i=0; i<16; i++) {
			if((i%2) == 0) bits1.set(i);
			if((i%5) != 0) bits2.set(i);
		}
		System.out.println("Initial pattern in bits1: ");
		System.out.println(bits1);
		System.out.println("\\nInitial pattern in bits2: ");
		System.out.println(bits2);
		// AND bits
		bits2.and(bits1);
		System.out.println("\\nbits2 AND bits1: ");
		System.out.println(bits2);
		// OR bits
		bits2.or(bits1);
		System.out.println("\\nbits2 OR bits1: ");
		System.out.println(bits2);
		// XOR bits
		bits2.xor(bits1);
		System.out.println("\\nbits2 XOR bits1: ");
		System.out.println(bits2);
	}
}
```

결과는 다음과 같다. 

```bash
Initial pattern in bits1: 
{0, 2, 4, 6, 8, 10, 12, 14} 
Initial pattern in bits2: 
{1, 2, 3, 4, 6, 7, 8, 9, 11, 12, 13, 14} 
bits2 AND bits1: 
{2, 4, 6, 8, 12, 14} 
bits2 OR bits1: 
{0, 2, 4, 6, 8, 10, 12, 14} 
bits2 XOR bits1: 
{}
```

### 5. 기타 

#### 5.1 int cardinality()

true인 bit의 숫자 반환

#### 5.2 void clear()

모든 bit를 false로 

#### 5.3 void clear(int bitIndex)

bitIndex를 false로 

#### 5.4 void clear(int fromIndex, int toIndex)

fromIndex부터 toIndex이전까지(즉, toIndex는 exclusive)를 false로 

