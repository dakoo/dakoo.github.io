---
layout: post
title: unit test framework들 - C++, java, python
description: unit test framework들 - C++, java, python
modified: 2016-05-07
tags: [blog]
comments: true
image:
  feature: abstract-11.png
---
쉽게 사용할 수 있는 C++, java, python test framework에 대한 설명

### C++ Test Framework

사용하기 편리한 C++ test framework은 [CATCH](https://github.com/philsquared/Catch)이다. Google Test framework과 비교하면 압도적인 단순함을 자랑한다. 별도의 복잡한 설정없이 단지 catch.hpp 파일을 include하는 것만으로 사용할 수 있다. 
사용예는 다음과 같다. 

```cpp
#define CATCH_CONFIG_MAIN  // This tells Catch to provide a main() - only do this in one cpp file
#include "catch.hpp"

unsigned int Factorial( unsigned int number ) {
    return number <= 1 ? number : Factorial(number-1)*number;
}

TEST_CASE( "Factorials are computed", "[factorial]" ) {
    REQUIRE( Factorial(1) == 1 );
    REQUIRE( Factorial(2) == 2 );
    REQUIRE( Factorial(3) == 6 );
    REQUIRE( Factorial(10) == 3628800 );
}
```

### python

python test framework 중 기본은 builtin되어있는 [unittest](http://pythontesting.net/framework/unittest/unittest-introduction/) 모듈이다. builtin되어 있으므로 아주 쉽게 test case를 작성하고 실행할 수 있다. 
사용예는 다음과 같다. 

```python
import unittest
from unnecessary_math import multiply
 
class TestUM(unittest.TestCase):
 
    def setUp(self):
        pass
 
    def test_numbers_3_4(self):
        self.assertEqual( multiply(3,4), 12)
 
    def test_strings_a_3(self):
        self.assertEqual( multiply('a',3), 'aaa')
 
if __name__ == '__main__':
    unittest.main()
```

### Java

Java의 test framework 중 가장 유명하면서도 많이 사용되는 것은 junit이다. junit을 그냥 사용하는 것이 아니라 eclipse에 연동하여 사용하는 것이 일반적이다. 
[nextree의 junit 사용법](http://www.nextree.co.kr/p11104/)을 참조하면 쉽게 test case를 작성하고 실행할 수 있다. 작성된 test case의 예는 다음과 같다. 

```java
package com.powerofthree.test;
import static org.junit.Assert.*;
import org.junit.Before;
import org.junit.Test;

import com.powerofthree.Solution;

public class SolutionTest {
	private Solution so;
	@Before
	public void setUp() throws Exception {
		so = new Solution();
	}

	@Test
	public void test1IsPowerOfThree() {
		assertEquals(so.isPowerOfThree(1), true);
	}
  /* ... */
}

```
