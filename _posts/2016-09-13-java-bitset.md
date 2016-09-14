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

### 1. 예제 코드 

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



### 2. 결과 

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

