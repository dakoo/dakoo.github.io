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

Java의 BitSet class의 사용방법을 알아보자. API보다는 예제를 참조하는 것이 좋겠다. 아래 예제는 [java-samples.com](http://www.java-samples.com/showtutorial.php?tutorialid=378)을 참조했다. 

### 1. 기본 예제

```java
import java.util.*;

public class Main {

   public static void main(String[] args) {

      
      BitSet bitset1 = new BitSet(8);
      BitSet bitset2 = new BitSet(8);

      // assign values to bitset1
      bitset1.set(0);
      bitset1.set(1);
      bitset1.set(2);

      // assign values to bitset2
      bitset2.set(2);
      bitset2.set(4);

      // print the sets
      System.out.println("Bitset1:" + bitset1);
      System.out.println("Bitset2:" + bitset2);

      // flip from index 2 to index 5 of bitset1 and print it
      bitset1.flip(2,5);
      System.out.println(bitset1);
      
      // flip from index 1 to index 5 of bitset2 and print it
      bitset2.flip(1,5);
      System.out.println(bitset2);

   }
}
```

결과는 다음과 같다. 

```bash
Bitset1:{0,1,2}
Bitset2:{2,4}
{0,1,3,4}
{1,3}
```

### 2. set/get 에제 

```java
import java.util.BitSet;

public class Program {
    public static void main(String[] args) {
	BitSet b = new BitSet();

	// Set bits in this range to true.
	b.set(2, 5);

	// Display first five bits.
	for (int i = 0; i < 5; i++) {
	    System.out.println(b.get(i));
	}
	
	// Get range of bits from original set.
	BitSet b2 = b.get(2, 5);

	// Display first five bits.
	for (int i = 0; i < 5; i++) {
	    System.out.println(b2.get(i));
	}
	
    }
}
```

결과는 다음과 같다. 

```bash
false
false
true
true
true
true
true
true
false
false
```


### 3. ToByteArray 에제 

```java
import java.util.BitSet;

public class Program {
    public static void main(String[] args) {
	BitSet b = new BitSet();

	// Set bit 0 and bit 8 to true.
	b.set(0);
	b.set(8);

	// Convert to byte array.
	for (byte value : b.toByteArray()) {
	    System.out.println(value);
	}
    }
}
```

결과는 다음과 같다. 

```bash
1
1
```

### 4. Clear 에제 

```java
import java.util.BitSet;

public class Program {
    public static void main(String[] args) {

	BitSet b = new BitSet();

	// Set first four bits to true.
	b.set(0, 4);

	// Clear first two bits.
	b.clear(0, 2);

	// Display first four bits.
	System.out.println(b.get(0));
	System.out.println(b.get(1));
	System.out.println(b.get(2));
	System.out.println(b.get(3));
    }
}
```

결과는 다음과 같다. 

```bash
false
false
true
true
```

### 5. Bitwise Operation

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
