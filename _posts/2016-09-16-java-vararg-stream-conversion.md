---
layout: post
title: Java - vararg을 stream으로 변환
modified: 2016-09-16
tags: [java]
---

Java method의 가변 길이 argument(vararg)를 stream으로 변환하는 것을 해보자. 

### 1. 올바른 방법

vararg는 일종이 array이다. 그러므로 다음과 같이 `Stream.of()`나 `Arrays.stream()`을 사용해야 한다. 

#### 1.1 numeric type의 vararg인 경우 

##### 1.1.1 Stream.of() 사용

예제를 통해 살펴보자. 적당한 type의 Stream을 선택하여 사용한다. 

```java
import java.util.stream.IntStream;

class Adder {
	static int add(int ... ints) {	//vararg
		return IntStream.of(ints).sum();
	}
}

public class Solution {

	public static void main(String[] args) {
		System.out.println(Adder.add(1, 2, 1, 4)); //8
	}

}

```

##### 1.1.2 Arrays.stream() 사용

Array에서 stream을 직접 생성해보자. 

```java
import java.util.Arrays;

class Adder {
	static int add(int ... ints) {	//vararg
		return Arrays.stream(ints).sum();
	}
}
```


#### 1.2 비 numeric type의 vararg인 경우 

##### 1.2.1 Stream.of() 사용


예제를 통해 살펴보자. 

```java
import java.util.stream.Stream;

class Car {
	private int price;

	public Car(int price) {
		this.price = price;
	}

	public int getPrice() {
		return price;
	}
}

class CarPriceAdder {
	static int add(Car ... cars) { 		//vararg
		return Stream.of(cars).mapToInt(car->car.getPrice()).sum();
	}
}

public class Solution {

	public static void main(String[] args) {
		Car accent = new Car(100);
		Car outback = new Car(200);
		System.out.println(CarPriceAdder.add(accent, outback)); //300
	}

}
```

##### 1.2.2 Arrays.stream() 사용

Array에서 stream을 직접 생성해보자. 

```java
import java.util.Arrays;

class CarPriceAdder {
	static int add(Car ... cars) { 		//vararg
		return Arrays.stream(cars).mapToInt(car->car.getPrice()).sum();
	}
}
```

### 2. 올바르지 않은 방법 

#### 2.1 Collection처럼 접근하는 경우 

vararg는 Collection이 아니므로 직접 stream을 생성할 수 없다. 그러므로 아래 코드는 **컴파일이 안된다**. 

```java
class CarPriceAdder {
	static int add(Car ... cars) { 		//vararg
		return cars.stream().mapToInt(car->car.getPrice()).sum();
	}
}
```
