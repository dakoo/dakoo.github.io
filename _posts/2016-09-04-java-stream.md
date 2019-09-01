---
layout: post
title: Java - Stream
modified: 2016-09-04
tags: [java]
---

functional programming을 지원하기 위한 Java 8의 핵심인 Stream에 대해 알아보자. [java2s.com](http://www.java2s.com/Tutorials/Java/Java_Stream/index.htm)의 내용을 개인적으로 정리한 것이므로 자세한 내용은 해당 사이트를 참고하자. 

## 1. 개요

### 1.1 정의 

Java 8에서의 Stream의 정의는 다음과 같다 .

> “a sequence of elements from a source that supports aggregate operations.”

Stream은 Collection 객체, Array, I/O로부터의 sequence 입력을 집합적으로 처리하기 위한 명령을 지원하는 프레임웍이라고 생각하자. 


### 1.2 Stream과 Collection의 비교

아래의 Stream 특성이 Collection과 다른 점이다. 

- Stream은 element를 저장하지 않고 필요할 때에만 처리된다. 
- Stream operation들은 그 source 데이터를 바꾸지 않고, 결과를 저장한 새로운 stream을 반환할 뿐이다. 
- Collection은 유한한 크기를 가지고 있으나 Stream은 그렇지 않다. limit()나 findFirst()등의 operation을 이용해 유한한 시간내에 처리할 수 있도록 지원한다. 
- Stream은 한번 사용되고 버려진다. 

### 1.3 Java Stream API 

Stream을 다루는 API는  java.util.stream package 안에 있다. Stream은 AutoCloseable interface를 상속한다. 

```bash
AutoCloseable
 |
 +--BaseStream
     |
     +--IntStream
     |
     +--LongStream
     |
     +--DoubleStream
     |
     +--Stream<T>
```

#### 1.4 BaseStream

BaseStream의 method는 다음과 같다. 

- Iterator<T> iterator(): Terminal operation, 스트림의 element를 순회 
- sequential(): intermediate operation, sequential 스트림을 반환 
- parallel(): intermediate operation, parallel 스트림을 반환
- boolean isParallel(): parallel스트림 여부 반환
- unordered(): intermediate operation, unordered 버전의 스트림을 반환

#### 1.5 Optional 

##### 1.5.1 개요

Java8에서 소개된  java.util.Optional<T> 클래스는 **NullPointerException**을 우아하게 다룰 수 있다. 만약 스트림 operation이 null을 반환해야 하는 경우, null이 아닌 Optional을 반환한다. 

- isPresent(): null인지 아닌지 확인
- get(): null이 아니라면 null이 아닌 value를 반환하고 null인 경우 NoSuchElementException을 던짐

결과가 primitive인 경우 Optional을 상속한 OptionalInt, OptionalLong, OptionalDouble 클래스를 사용할 수 있으며, 값은 get()이 아닌 getAsInt(), getAsLong(), getAsDouble()을 통해 Optional 객체로부터 primitive 값을 획득할 수 있다. 

##### 1.5.2 Optional 예제 1


```java
Optional<String> str = Optional.of("hi");

if (str.isPresent()) {
    System.out.println(str.get());
} else {
    System.out.println("Optional is  empty.");
}
```

결과는 다음과 같다. 

```bash
hi
```


##### 1.5.3 Optional 예제 2 - primitive

```java
OptionalInt maxOdd = IntStream.of(10, 20, 30).filter(n -> n % 2 == 1).max();
if (maxOdd.isPresent()) {
    int value = maxOdd.getAsInt();
    System.out.println("Maximum odd  integer is " + value);
} else {
    System.out.println("Stream is  empty.");
}
```

결과는 다음과 같다. 

```bash
Stream is  empty.
```

## 2. Stream의 생성

Stream의 생성 방법은 다음과 같다. 

### 2.1 값(value)로 부터 직접

Stream interface 중 **of()**를 사용하여 value로 부터 직접 Stream을 생성할 수 있다. 

```java
<T> Stream<T> of(T  t) //하나의 value
<T> Stream<T> of(T...values) // multiple value
```

하나의 value로 부터 생성하는 것은 다음과 같다. 

```java
Stream<String> stream  = Stream.of("java2s.com");
stream.forEach(System.out::println);
```

multiple value로부터 생성하는 예는 다음과 같다. 

```java
Stream<String> stream  = Stream.of("XML", "Java",  "CSS", "SQL");
stream.forEach(System.out::println);
```


### 2.2 Empty stream

빈 Stream을 생성한 후 값을 추가하여 Stream을 만들 수 있다. 

#### 2.2.1 Stream Builder 사용

Stream builder를 만든 후 값들을 추가 하는 것은 다음과 같다. 

```java
    Stream<String> stream  = Stream.<String>builder()
        .add("XML")
        .add("Java")
        .add("CSS")
        .add("SQL")
        .build();
    stream.forEach(System.out::println); //
}
```

#### 2.2.2 IntStream의 range() 사용

정수 스트림을 생성하는 IntStream 인터페이스의 range() method를 이용하여 Stream을 만들 수 있다. 그 예는 다음과 같다. 

```java
        IntStream oneToFive  = IntStream.range(1, 6);
        // = IntStream oneToFive  = IntStream.rangeClosed(1, 5);
        oneToFive.forEach(System.out::println);
}
```

InStream외에도 LongStream, DoubleStream 인터페이스가 있다. 

#### 2.2.3 Stream의 empty() method

```java
   Stream<String> stream  = Stream.empty();
    stream.forEach(System.out::println);  
}
```


### 2.3 function을 이용한 생성


필요할 때 값들을 생성하는 function들을 가지고 있다. 이 function들을 이용하여 **무한한 길이의 Stream** 생성할 수 있다. 

```java
<T> Stream<T> iterate(T  seed, UnaryOperator<T>  f) //sequential ordered stream를 생성
<T> Stream<T> generate(Supplier<T> s) //sequential unordered stream을 생성
```

IntStream, LongStream, DoubleStream도 동일하게 iterate와 generate() function을 가지고 있다.

또한 Random class에서 제공하는 ints(), longs(), doubles()로 무한한 IntStream, LongStream, DoubleStream을 반환한다. 

#### 2.3.1 Stream.iterate() 

iterate()는 seed와 function을 인자로 가진다. seed는 스트림의 첫번째 element이다. 첫번째 element를 두번째  인자인 function에 집어넣어 스트림의 두번째 element를 만들어 낸다. 예를 들어 다음은 무한한 자연수의 스트림을 만드는 코드이다. 

```java
Stream<Long> naturalNumbers = Stream.iterate(1L, n -> n + 1);
```

 무한한 길이이므로 주로 Intermediate operation 중 하나인 숫자 제한을 하는 `limit(long maxSize)`와 결합하여 사용한다. 그 예는 다음과 같다. 

```java
Stream<Long> tenNaturalNumbers = Stream.iterate(1L, n  ->  n  + 1)
		.limit(10);	//1 2 3 4 5 6 7 8 9 10
```

filter(), skip()과 결합하여 사용한 예는 다음과 같다. skip은 몇개를 건너 뛰느냐를 지정한다. 

```java
Stream.iterate(2L, n  ->  n  + 1)
       .filter(x->x%2!=0)
       .skip(100)
       .limit(4)
       .forEach(System.out::println);//203 205 207 209
```

#### 2.3.2 Stream.generate()

'generate(Supplier<T> s)'는 supplier를 이용하여 무한한 sequential unordered 스트림을 생성한다. 예를 들어 다음과 같다. 

```java
Stream.generate(Math::random)
       .limit(5)
       .forEach(System.out::println);
```

출력은 다음과 같다. 

```bash
0.28717330520043527
0.5642316709475632
0.24729530988007464
0.5199892875599263
0.9355928642452513
```

supplier는 다음과 같이 static method로 정의할 수 있다. 


```java
public class Solution {
    public static void main(String[] args) {
        Stream.generate(Solution::next)
                .limit(5)
                .forEach(System.out::println);	//1 2 3 4 5
    }
    static int i = 0;
    private static int next(){
        return ++i;
    }
}
```

Stream.generate()의 supplier로 Random::nextInt()를 사용할 수도 있다. 


```java
    Stream.generate(new Random()::nextInt)
    .limit(5)
    .forEach(System.out::println);
```

이때의 출력값은 다음과 같이 random값이다. 


```bash
755239976
768110885
220701942
210236989
-649010462
```

#### 2.3.3 Random class 이용

Random 클래스의 ints(), longs(), doubles()도 무한한 수의 sequence를 반환한다. 단, 그 값을 정의할 수는 없다. 
그 예는 다음과 같다. 

```java
new Random().ints()
            .limit(5)
            .forEach(System.out::println);
```

결과는 다음과 같이 random이다. 

```bash
755239976
768110885
220701942
210236989
-649010462
```


### 2.4 Array로부터의 생성

다음과 같이 **Arrays** 클래스를 사용하여 Array에서 Stream을 생성할 수 있다. 

```java
Stream<String> names = Arrays.stream(new String[] {"XML",   "Java"});
//or
IntStream numbers = Arrays.stream(new int[]{1, 2, 3});
```

### 2.5 Collection으로부터 생성 

Collection 인터페이스는 **stream()**메소드와 **parallelStream()**메소드를 제공하고 있다. 이 메소드들을 이용해 스트림을 바로 생성할 수 있다. 그 예는 다음과 같다. 

```java
Set<String> names = new HashSet<>();
names.add("XML");
names.add("Java");

names.stream().forEach(System.out::println);
names.parallelStream().forEach(System.out::println);
```

### 2.6 String으로부터 생성 

#### 2.6.1 문자열 sequence로부터

String, StringBuffer, StringBuilder는 문자열 sequence를 반환하는 **chars()**를 가지고 있다. 이를 통해 스트림을 생성한다. 그 예는 다음과 같다. 

```java
String str = "5 123,123,qwe,1,123, 25";
str.chars()
     .filter(n ->  !Character.isDigit((char)n) &&   !Character.isWhitespace((char)n))
     .forEach(n ->  System.out.print((char)n));
```

출력결과는 다음과 같다. 

```bash
,,qwe,,,
```

#### 2.6.1 정규표현식(Regular expression)으로부터 

**java.util.regex.Pattern**의 **splitAsStream(CharSequence input)** method는 String에서 패턴에 일치하는 것을 기준으로 자른 element들을 스트림으로 반환한다. 아래의 예는 다음과 같다. 

```java
String str = "XML,CSS,HTML"; 
Pattern.compile(",")
    .splitAsStream(str)
    .forEach(System.out::println);
```

출력 예는 다음과 같다. 

```bash
XML
CSS
HTML
```

### 2.7 File로부터의 입력

Java 8은 File IO에서 Stream을 지원한다. 그리고, 파일을 close()함과 동시에 스트림도 멈추게 된다. 다음과 같다. 

```java
Path path = Paths.get("./Solution.java");
try (Stream<String> lines = Files.lines(path)) {
      lines.forEach(System.out::println);
} catch (IOException e) {
      System.out.println("Can't open the file");
}
```

다음음 directory로부터 stream을 가져오는 예이다. 

```java
Path dir = Paths.get(".");
System.out.printf("%nThe file tree for %s%n",
      dir.toAbsolutePath());
try (Stream<Path> fileTree = Files.walk(dir)) {
      fileTree.forEach(System.out::println);
} catch (IOException e) {
      e.printStackTrace();
}
```

## 3 예제 설명을 위한 예제 코드 

Intermediate 및 terminal operation을 설명하기 이전에 그 예제로 사용하기 위한 Employee class는 다음과 같다. 
id, name, gender, date, income을 attribute로 가지며 persons() 메소드를 이용해 persons List를 반환한다. 


```java
import java.time.LocalDate;
import java.time.Month;
import java.util.Arrays;
import java.util.List;

class Employee {
    public static enum Gender {
        MALE, FEMALE
    }
    private long id;
    private String name;
    private Gender gender;
    private LocalDate dob;
    private double income;
    public Employee(long id, String name, Gender gender, LocalDate dob,
                    double income) {
        this.id = id;
        this.name = name;
        this.gender = gender;
        this.dob = dob;
        this.income = income;
    }
    public long getId(){
        return this.id;
    }
    public String getName() {
        return name;
    }
    public Gender getGender() {
        return gender;
    }
    public boolean isMale() {
        return this.gender == Gender.MALE;
    }
    public boolean isFemale() {
        return this.gender == Gender.FEMALE;
    }
    public double getIncome() {
        return income;
    }
    public void setIncome(double income) {
        this.income = income;
    }

    public static List<Employee> persons() {
        Employee p1 = new Employee(1, "Jake", Gender.MALE, LocalDate.of(1971,
                Month.JANUARY, 1), 2343.0);
        Employee p2 = new Employee(2, "Jack", Gender.MALE, LocalDate.of(1972,
                Month.JULY, 21), 7100.0);
        Employee p3 = new Employee(3, "Jane", Gender.FEMALE, LocalDate.of(1973,
                Month.MAY, 29), 5455.0);
        Employee p4 = new Employee(4, "Jode", Gender.MALE, LocalDate.of(1974,
                Month.OCTOBER, 16), 1800.0);
        Employee p5 = new Employee(5, "Jeny", Gender.FEMALE, LocalDate.of(1975,
                Month.DECEMBER, 13), 1234.0);
        Employee p6 = new Employee(6, "Jason", Gender.MALE, LocalDate.of(1976,
                Month.JUNE, 9), 3211.0);

        List<Employee> persons = Arrays.asList(p1, p2, p3, p4, p5, p6);

        return persons;
    }

    @Override
    public String toString() {
        String str = String.format("(%s, %s,  %s,  %s,  %.2f)\n", id, name, gender,
                dob, income);
        return str;
    }
}
```

## 4 Intermediate(중간) operation

중간에서 스트림 입력을 받아 스트림을 출력으로 내는 operation들을 intermediate operation들이라고 한다. 그것들은 다음과 같다. 

- peek(): debugging을 위한 액션 수행
- filter(): 조건에 일치하는 element로만 구성된 스트림 반환
- map(): 1 대 1 맵핑을 수행한 스트림 반환(예를 들어 element에서 특정 attribute만 뽑아서 스트림으로 만들기)
- distinct(): equals() 메소드를 확인하여 동일하지 않은 element로 구성된 스트림 반환
- sorted(): 자연스러운 순서 또는 Comparator에 의해 정해진 순서에 따라 스트림을 정렬
- skip(): 앞에서 n개까지를 제외한 스트림 반환
- limit(): 앞에서부터 n개까지의 스트림 반환
- flatMap(): 평탄화된 스트림 반환


### 4.1 peek() 

#### 4.1.1 개요 

peek()는 debug 용으로 사용되며 스트림 파이프라인을 거쳐가는 element들을 프린트하는 목적으로 주로 사용된다. 

> peek(Consumer<? super T> action)

IntStream, LongStream, DoubleStream 모두 peek() 메소드를 제공한다. 

#### 4.1.2 예제 

```java
Employee.persons().stream()
        .peek(e -> System.out.println("Original: " + e.getName()))
        .filter(Employee::isFemale)
        .peek(e -> System.out.println("Filtered: " + e.getName()))
        .forEach(System.out::println);
```

이렇게 하면 출력은 다음과 같다. 

```bash
Original: Jake
Original: Jack
Original: Jane
Filtered: Jane
(3, Jane,  FEMALE,  1973-05-29,  5455.00)

Original: Jode
Original: Jeny
Filtered: Jeny
(5, Jeny,  FEMALE,  1975-12-13,  1234.00)

Original: Jason
```

그런데 intermediate operation인 peek을 맨뒤 두면 어떤 일이 생길까? 

```java
Employee.persons().stream()
        .peek(e -> System.out.println("Original: " + e.getName()))
        .filter(Employee::isFemale)
        .peek(e -> System.out.println("Filtered: " + e.getName()));
```

이렇게 하면 출력에 아무것도 찍히지 않게 된다. 중간의 "Original" ... " 도 나타나지 않는다. 

### 4.2 filter()

filter는 조건에 맞는 element로만 구성된 스트림을 반환한다. 그 예는 다음과 같다. 

```java
Employee.persons()
        .stream()
        .filter(p->p.getIncome() > 4000.0)
	.map(Employee::getName)
        .forEach(System.out::println);
```

위에서는 java lambda를 이용해 조건을 입력했고 결과는 다음과 같다. 

```bash
Jack
Jane
```

### 4.3 map() 

#### 4.3.1 개요 

map은 Stream의 element 각각에 함수를 적용한 결과로 구성된 스트림을 반환한다. 그러므로 입력 스트림과 출력 스트림의 element의 수는 같다. map은 다음과 같은 interface를 사용할 수 있다. 

```java
<R> Stream<R> map(Function<? super T,? extends R> mapper)
DoubleStream  mapToDouble(ToDoubleFunction<? super T> mapper)
IntStream     mapToInt(ToIntFunction<? super T> mapper)
LongStream    mapToLong(ToLongFunction<? super T> mapper)
```

위의 메소드들은 IntStream, LongStream, DoubleStream도 모두 적용된다. 스트림에 sum(), max(), min()등을 수행하기 위해서는 숫자 type의 스트림인 경우 그대로 하고, 만약 숫자 type이 아닌 경우에는 mapToInt(), mapToLong(), mapToDouble()을 이용해 변환한 뒤에 해당 operation을 수행한다. 

#### 4.3.2 map() 예제

```java
Employee.persons()
        .stream()
        .filter(p->p.getIncome() < 4000.0)
	.map(p->p.getName())
        .forEach(System.out::println);
```

출력은 다음과 같다. 

```bash
Jake
Jode
Jeny
Jason
```

#### 4.3.3 mapToDouble() 예제

```java
double totalIncome = Employee.persons()
        .stream()
        .mapToDouble(Employee::getIncome)
        .sum();
System.out.println("Total Income:  "  + totalIncome);
```

출력은 다음과 같다. 만약 그냥 map()으로 하면 compile error이다. 

```bash
Total Income:  21143.0
```

### 4.4 flatMap() 

#### 4.3.1 개요 

map() operation은 1 대 1 mapping을 만드는데 반해 flatMap()은 1 대 n mapping을 지원한다. 즉, 한 개의 element입력을 받아 n개의 element를 만들 수 있다는 것이다. 

#### 4.3.2 예제 1

다음과 같이 

```java
    Stream.of(1, 2, 3)
    .flatMap(n -> Stream.of(n, n+1))
    .forEach(System.out::println);
```

출력은 다음과 같다. 

```bash
1
2
2
3
3
4
```

#### 4.3.3 예제 2

다음과 같이 하는 것도 가능하다. 3개의 element를 받아서 각 element마다 0부터 끝까지의 길이를 Stream으로 만든 뒤에 이를 각각 charAt()을 이용해 각 위치의 character를 반환한 후 이를 mapToObj로 Typed primitive로 변환 후 출력한 것이다. 각 단계 별로 하나씩 진행하는 것이 아니라 **각 element마다 병렬로 처리**되는 것을 머리속에 그릴 수 있어야 할 것이다. 


```java
Stream.of("XML", "Java",  "CSS")
        .flatMap(str ->  IntStream.range(0, str.length())
        .mapToObj(str::charAt))
        .forEach(System.out::println);
```

출력은 다음과 같다. 

```bash
X
M
L
J
a
v
a
C
S
S
``` 

### 4.5 sorted() 

#### 4.3.1 개요 

unordered 스트림을 ordered 스트림으로 변환한다. 

#### 4.3.2 예제 1

다음과 같이 

```java
List<Integer> numbers = Arrays.asList(3,7,9,3,1,2,1, 2, 3, 4, 5);
numbers.stream()
	.filter(n -> n % 2  == 1)
	.sorted()
	.forEach(System.out::println);
```

결과는 

```bash
1
1
3
3
3
5
7
9
```


### 5. Terminal operation

stream을 입력으로 받아 결과를 내는 operation들을 terminal operation들이라고 한다. 그것들은 다음과 같다. 

- forEach: 스트밍의 각 element에 액션 수행
- toArray: 스트림을 Array로 변환하여 반환
- collect: 스트림을 Collection으로 변환하여 반환
- reduce: 스트림으로부터 하나의 값을 얻기 위한 reduction operation을 수행
- count: element의 갯수 반환
- max: 가장 큰 element 반환
- min: 가장 작은 element 반환
- findFirst: 스트림의 첫번째 element 반환
- findAny: 스트림 중 하나의 element 반환(empty이면 Optional object 반환)
- anyMatch: 만약 하나의 element라도 정해진 조건을 만족하면 true반환(empty이면 false 반환)
- allMatch: 만약 모든 element가 정해진 조건을 만족하면 true 반환(empty라도 true)
- noneMatch: 만약 모든 element가 정해진 조건을 만족하지 않으면 false 반환(empty이면 true)


### 5.1 forEach()

#### 5.1.1 개요 

forEach는 각 element에 대해 액션을 수행하는 operation이다. 
forEach()는 스트밍의 입력에 대한 처리 순서를 약속하지 않는다. 

> void  forEach(Consumer<? super  T> action)

처리 순서에 대한 확신을 갖기 위해서는 forEachOrdered를 사용해야 한다. 대신 parallel stream에서는 성능이 떨어진다. 

> void  forEachOrdered(Consumer<? super  T> action)

IntStream, LongStream, DoubleStream 모두 동일한 메소드를 제공한다. 

#### 5.1.2 예제 

아래와 같이 forEach이용해 하나씩 출력할 수 있다.  

```java
Employee.persons()
       .stream()
       .filter(Employee::isFemale)
       .forEach(System.out::println);
```

결과는 다음과 같다. 

```bash
(3, Jane,  FEMALE,  1973-05-29,  5455.00)

(5, Jeny,  FEMALE,  1975-12-13,  1234.00)
```

### 5.2 reduce()

#### 5.2.1 개요 

reduce는 스트림의 모든 element들을 하나의 값으로 변환하는 것이다. 이것은 두 개의 인자를 가지는데 첫번째는 seed이고 두번째는 accumulator function이다. 만약 스트림이 empty이면 seed가 결과가 된다. accumulator는 2개의 인자를 가진다. seed와 첫번째 element가 accumlator의 입력으로 들어가고, 결과가 반환되면 다시 그 결과와 다음 element가 accumulator의 입력으로 다시 들어가고 이런 식으로 마지막 element까지 반복한다. reduce method는 다음과 같이 정의된다. 

```
T  reduce(T identity, BinaryOperator<T> accumulator)
```

reduce() operation이 병렬로 처리될 때 combiner를 이용해서 중간 값들을 묶을 수 있다. 즉, 병렬 처리시를 위한 특별한 reduce()는 다음과 같다. 자세한 내용은 예제(5.2.4) 를 살펴보자.

```
<U> U reduce(U identity, BiFunction<U,? super T,U> accumulator, BinaryOperator<U> combiner)
```

#### 5.2.2 간단한 예 

아래와 같이 reduce 이용해 총합을 만들어 낼 수 있다.  

```java
List<Integer> numbers  = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream()
	        .reduce(0, Integer::sum);
System.out.println(sum); //15
```

#### 5.2.3 예제

위의 예를 참고해서 모든 employee의 income의 합을 계산해 보자. 

```java
double sum = Employee.persons()
        .stream()
        .map(Employee::getIncome)
        .reduce(0.0, Double::sum);
System.out.println(sum);	//21143.0
```

#### 5.2.4 병렬 연산 예

모든 employee의 income의 합을 병렬로 계산하는 예는 다음과 같다.

```java
double sum = Employee.persons()
        .stream()
        .reduce(0.0, (partialSum, person) -> partialSum + person.getIncome(), Double::sum);
System.out.println(sum);	//21143.0
```

#### 5.2.4 복잡한 병렬 연산의 예 - thread

이제 매우 복잡한 병렬 reduce()의 예를 살펴보자.   stream()의 경우 main thread 하나를 이용함을 알 수 있다. 
하지만, parallelStream()를 이용해 stream을 만드는 경우 Thread.currentThread().getName()로 출력해보면 thread가 여러개 사용되고 있음을 알 수 있다. 

```java
double sum = Employee.persons().stream()
        .reduce(
                0.0,
                (Double partialSum, Employee p) -> {
                    double accumulated = partialSum + p.getIncome();
                    System.out.println(Thread.currentThread().getName()
                        + "  - Accumulator: partialSum  = " + partialSum
                        + ",  person = " + p + ", accumulated = " + accumulated);
                    return accumulated;
                },
                (a, b) -> {
                    double combined = a + b;
                    System.out.println(Thread.currentThread().getName()
                        + "  - Combiner:  a  = " + a + ", b  = " + b
                        + ", combined  = " + combined);
                    return combined;
                });

System.out.println("--------------------------------------");
System.out.println(sum);

sum = Employee.persons().parallelStream()
        .reduce(
                0.0,
                (Double partialSum, Employee p) -> {
                    double accumulated = partialSum + p.getIncome();
                    System.out.println(Thread.currentThread().getName()
                        + "  - Accumulator: partialSum  = " + partialSum
                        + ",  person = " + p + ", accumulated = " + accumulated);
                    return accumulated;
                },
                (a, b) -> {
                    double combined = a + b;
                    System.out.println(Thread.currentThread().getName()
                        + "  - Combiner:  a  = " + a + ", b  = " + b
                        + ", combined  = " + combined);
                    return combined;
            });
System.out.println(sum);
```

출력은 다음과 같다. 

```bash
main  - Accumulator: partialSum  = 0.0,  person = (1, Jake,  MALE,  1971-01-01,  2343.00)
, accumulated = 2343.0
main  - Accumulator: partialSum  = 2343.0,  person = (2, Jack,  MALE,  1972-07-21,  7100.00)
, accumulated = 9443.0
main  - Accumulator: partialSum  = 9443.0,  person = (3, Jane,  FEMALE,  1973-05-29,  5455.00)
, accumulated = 14898.0
main  - Accumulator: partialSum  = 14898.0,  person = (4, Jode,  MALE,  1974-10-16,  1800.00)
, accumulated = 16698.0
main  - Accumulator: partialSum  = 16698.0,  person = (5, Jeny,  FEMALE,  1975-12-13,  1234.00)
, accumulated = 17932.0
main  - Accumulator: partialSum  = 17932.0,  person = (6, Jason,  MALE,  1976-06-09,  3211.00)
, accumulated = 21143.0
--------------------------------------
21143.0
main  - Accumulator: partialSum  = 0.0,  person = (4, Jode,  MALE,  1974-10-16,  1800.00)
, accumulated = 1800.0
ForkJoinPool.commonPool-worker-3  - Accumulator: partialSum  = 0.0,  person = (1, Jake,  MALE,  1971-01-01,  2343.00)
, accumulated = 2343.0
main  - Accumulator: partialSum  = 0.0,  person = (5, Jeny,  FEMALE,  1975-12-13,  1234.00)
, accumulated = 1234.0
ForkJoinPool.commonPool-worker-2  - Accumulator: partialSum  = 0.0,  person = (6, Jason,  MALE,  1976-06-09,  3211.00)
, accumulated = 3211.0
ForkJoinPool.commonPool-worker-1  - Accumulator: partialSum  = 0.0,  person = (2, Jack,  MALE,  1972-07-21,  7100.00)
, accumulated = 7100.0
ForkJoinPool.commonPool-worker-2  - Combiner:  a  = 1234.0, b  = 3211.0, combined  = 4445.0
ForkJoinPool.commonPool-worker-2  - Combiner:  a  = 1800.0, b  = 4445.0, combined  = 6245.0
ForkJoinPool.commonPool-worker-3  - Accumulator: partialSum  = 0.0,  person = (3, Jane,  FEMALE,  1973-05-29,  5455.00)
, accumulated = 5455.0
ForkJoinPool.commonPool-worker-3  - Combiner:  a  = 7100.0, b  = 5455.0, combined  = 12555.0
ForkJoinPool.commonPool-worker-3  - Combiner:  a  = 2343.0, b  = 12555.0, combined  = 14898.0
ForkJoinPool.commonPool-worker-3  - Combiner:  a  = 14898.0, b  = 6245.0, combined  = 21143.0
21143.0
```

### 5.3 seed 값 없는 reduce()

#### 5.3.1 개요 

seed가 없이 reduce()를 수행하는 것은 위에서 본 sum과 같은 accumulator가 아닌 max나 min값을 찾는 경우에는 가능하다. 

> reduce(BinaryOperator<T> accumulator)

결과값이 없을 수도 있으므로 반환은 `Optional<T>` type으로 받아서 isPresent()를 이용해 확인한다. 자세한 내용은 아래 예제를 참고로 하자. 

#### 5.3.2 간단 예제 

```java
Optional<Integer> max = Stream.of(1, 2, 3, 4, 5).reduce(Integer::max);

if (max.isPresent()) {
    System.out.println("max = " + max.get());
} else {
    System.out.println("max is not  defined.");
}

max = Stream.<Integer> empty().reduce(Integer::max);
if (max.isPresent()) {
    System.out.println("max = " + max.get());
} else {
    System.out.println("max is not  defined.");
}
```

출력은 다음과 같다. 

```bash
max = 5
max is not  defined.
```

#### 5.3.3 예제 

Employee 중에 가장 많은 income인 것을 찾는 것은 다음과 같다. 


```java
Optional<Employee> person = Employee
    .persons()
    .stream()
    .reduce((p1, p2) -> p1.getIncome() > p2.getIncome() ? p1 : p2);
if (person.isPresent()) {
    System.out.println("Highest earner: " + person.get());
} else {
    System.out.println("Could not  get   the   highest earner.");
}
```

출력은 다음과 같다. 

```bash
Highest earner: (2, Jack,  MALE,  1972-07-21,  7100.00)
```

### 5.4 sum()

#### 5.4.1 개요 

sum()을 적용하기 위해서는 숫자 type의 스트림인 경우 그대로 하고, 만약 숫자 type이 아닌 경우에는 mapToInt(), mapToLong(), mapToDouble()을 이용해 변환한 뒤에 sum()을 한다. 

#### 5.4.2 예제  

```java
double totalIncome = Employee.persons()
        .stream()
        .mapToDouble(Employee::getIncome)
        .sum();
System.out.println("Total Income:  "  + totalIncome);
```

출력은 다음과 같다. 만약 그냥 map()으로 하면 compile error이다. 

```bash
Total Income:  21143.0
```

### 5.5 max()와 min()

#### 5.5.1 개요 

max()와 min()이 없을 수도 있으므로 seed 값 없는 reduce()와 동일하게 반환은 `Optional<T>` type으로 받아서 isPresent()를 이용해 확인한다. max()와 min()의 인자는 Comparator이거나, 숫자 스트림의 경우는 인자가 없어도 된다. 예를 들어 다음 두 가지는 동일하게 유효하다. 

```java
OptionalInt optionalInt = Stream.of(1, 1, 1, 1, 1).mapToInt(i->i).max();
if (optionalInt.isPresent()) {
    System.out.println(optionalInt.getAsInt());
} else {
    System.out.println("Could not  get the max.");
}
```

```java
OptionalInt optionalInt = IntStream.of(1, 1, 1, 1, 1).max();
if (optionalInt.isPresent()) {
    System.out.println(optionalInt.getAsInt());
} else {
    System.out.println("Could not  get the max.");
}
```

위의 두 결과 모두 `1`이다. 

#### 5.5.2 max() 예제 

다음과 같이 Optional<T>로 반환받은 객체에 접근할 때는 get() method를 통해 접근한다. 

```java
Optional<Employee> maxIncomeEmployee = Employee.persons().stream()
        .max(Comparator.comparingDouble(Employee::getIncome));

if (maxIncomeEmployee.isPresent()) {
    System.out.println("Highest earner: " + maxIncomeEmployee.get().getIncome());
} else {
    System.out.println("Could not  get   the   highest earner.");
}
```

출력은 다음과 같다. 

```bash
Highest earner: 7100.0
```

max() operation이전에 numeric으로 변환하는 것도 가능하다. 다음 코드를 사용하면 동일한 결과를 얻는다. 


```java
OptionalDouble income = Employee.persons()
                        .stream()
                        .mapToDouble(Employee::getIncome).max();

if (income.isPresent()) {
    System.out.println("Highest income:   " + income.getAsDouble());
} else {
    System.out.println("Could not  get   the   highest income.");
}
```
 
### 5.6 count()

#### 5.6.1 개요 

count()는 스트림의 element 숫자를 반환한다. type은 long이다. 

#### 5.6.2 예제  

```java
long personCount = Employee.persons().stream().count();
System.out.println("Person count: " + personCount);
```

출력은 다음과 같다. 

```bash
Person count: 6
```

### 5.7 collect()

#### 5.7.1 개요 

collect()는 스트림의 데이터를 그룹화 할 수 있다. collect() 정의는 다음과 같다. 

```java
<R> R collect(Supplier<R> supplier, BiConsumer<R,? super T> accumulator, BiConsumer<R,R> combiner)
<R,A> R collect(Collector<?  super T,A,R> collector)
```
- supplier: 결과를 저장할 mutable container(Collection)
- accumulator: mutable container에 결과를 쌓을 함수
- combiner: 병렬 연산시 부분 결과를 모으는 함수

설명이 어려운데 예를 들어 다음의 예를 생각해보자. 우리가 다루고 있는 예제의 Employee의 이름만 따로 저장한 ArrayList 자료구조를 Stream에서 반환하고자 한다. map은 결과물이 역시 Stream이기 때문에 적당하지 않고, 이럴때 사용할만한 것이 collect()이다. 첫번째 인자가 바로 ArrayList, 두번째가 ArrayList에 Employee의 이름을 추가하는 동작, 세번째 인자가 병렬 연산시 통째로 ArrayList를 다른 ArrayList 뒤에 붙이는 연산이다. 

```java
List<String> names = Employee.persons()
        .stream()
        .map(Employee::getName)
        .collect(ArrayList::new,  ArrayList::add, ArrayList::addAll);
System.out.println(names);
```

첫번째 `ArrayList::new 대신 `() -> new ArrayList<>()`로 해도 된다. 

```java
List<String> names = Employee.persons()
        .stream()
        .map(Employee::getName)
        .collect(()->new ArrayList<>(),  ArrayList::add, ArrayList::addAll);
System.out.println(names);
```

결과는 다음과 같다. 

```bash
[Jake, Jack, Jane, Jode, Jeny, Jason]
```

#### 5.7.2 Collectors interface

collect()는 Collectors interface를 argument로 받을 수 있다. 이렇게 하면 위와 같이 복잡하게 supplier, accumulator를 정하는 것이 아니라 쉽게 Collection으로 변경 가능다. Collectors interface는 다음과 같다. 

- Collectors.toList()
- Collectors.toSet()
- Collectors.toCollection()

사용 예는 다음과 같다. 

```java
Set<String> uniqueNames  = Person.persons()
                        .stream()
                        .map(Person::getName)
                        .collect(Collectors.toSet());
System.out.println(uniqueNames); 
```

#### 5.7.3 예제  

Employee의 name을 기준으로 sorting한 결과를 출력하는 것을 구현해보자. 만약 같은 이름이 여러 개 있는 경우에는 한번만 출력하도록 한다. 

```java
SortedSet<String> uniqueSortedNames=   Employee.persons()
        .stream()
        .map(Employee::getName)
        .collect(Collectors.toCollection(TreeSet::new));
System.out.println(uniqueSortedNames);
```

출력은 다음과 같다. 

```bash
[Jack, Jake, Jane, Jason, Jeny, Jode]
```

### 5.8 collect()와 Statistics 

#### 5.8.1 java.util의 Statistics class들  

숫자 데이터의 통계를 낼때 java.util에서 제공하는 다음의 class들을 사용할 수 있다. Statistics 클래스들은 마치 Collection과 같은 역할을 한다. 

- DoubleSummaryStatistics
- LongSummaryStatistics
- IntSummaryStatistics

사용방법은 Statistics 객체를 생성 후 accept() method로 값을 추가한 후, getCount(), getSum(), getMin(), getAverage(), getMax() 메소드를 이용해 원하는 값을 얻는 것이다. 다음은 그 예이다. 

```java
DoubleSummaryStatistics stats = new DoubleSummaryStatistics();
stats.accept(100.0);
stats.accept(300.0);

long count = stats.getCount();
double sum = stats.getSum();
double min = stats.getMin();
double avg = stats.getAverage();
double max = stats.getMax();

System.out.printf(
        "count=%d, sum=%.2f,  min=%.2f,  average=%.2f, max=%.2f%n", 
        count, sum, min, max, avg);
```

결과는 다음과 같다. 

```bash
count=2, sum=400.00,  min=100.00,  average=300.00, max=200.00
```

#### 5.8.1 스트림에서의 Statistics 사용

##### 5.8.1.1 collect()와 supplier, accumulator, combiner

위에서 확인했다시피 Statistics는 Collection과 유사하기에 collect()에서 Collection의 supplier, accumulator를 등록해서 사용하는 것과 거의 동일하게 쓸 수 있다. 그리고, 병렬 연산을 위해 Statistics는 combine() 메소드를 제공한다. 다음은 그 사용 예이다. 


```java
DoubleSummaryStatistics incomeStats = Employee.persons()
        .stream()
        .map(Employee::getIncome)
        .collect(DoubleSummaryStatistics::new,
                DoubleSummaryStatistics::accept,
                DoubleSummaryStatistics::combine);
System.out.println(incomeStats);
```

결과는 다음과 같다. 

```bash
DoubleSummaryStatistics{count=6, sum=21143.000000, min=1234.000000, average=3523.833333, max=7100.000000}
```

##### 5.8.1.2 Collectors interface

Collectors는 숫자 연산을 위해 다음과 같은 method들을 제공하고 있다. 이를 이용해 Stream에서 바로 값을 획득할 수 있다. 

- Collectors.counting()
- Collectors.maxBy()
- Collectors.minBy()
- Collectors.summarizingInt() 
- Collectors.summingInt()
- Collectors.averagingInt()
- Collectors.summarizingLong() 
- Collectors.summingLong()
- Collectors.averagingLong()
- Collectors.summarizingDouble() 
- Collectors.summingDouble()
- Collectors.averagingDouble()

다음은 그 사용 예이다. 


```java
DoubleSummaryStatistics incomeStats = Employee.persons()
        .stream()
        .collect(Collectors.summarizingDouble(Employee::getIncome));
System.out.println(incomeStats);
```

결과는 다음과 같다. 

```bash
DoubleSummaryStatistics{count=6, sum=21143.000000, min=1234.000000, average=3523.833333, max=7100.000000}
```

### 5.9 collect() 사용한 스트림의 Map 변환 

#### 5.9.1 개요 

collect()를 이용해 Collection으로 변경하는 것의 한 종류는 Map을 변경하는 것이다. 스트림의 element에서 key와 value를 추출하는 function을 등록하여 Map으로 변환한다. 만약 중복된 key값이 있다면 `IllegalStateException` exception이 발생된다. 아래에서 keyMapper는 key를 추출하는 function, valuemapper는 value를 추출하는 function이다. 

```
toMap(Function<? super T,? extends K> keyMapper, Function<? super T,? extends U> valueMapper)
```

추가적으로 merge function을 제공할 수 있다. 이것은 중복된 key가 있는 경우 어떻게 처리할 지를 결정하는 function으로 argument는 old value와 new value이고 반환 값은 merge된 value이다. 

```
toMap(Function<? super T,? extends K> keyMapper, Function<? super T,? extends U> valueMapper, BinaryOperator<U> mergeFunction)
```

또한 Supplier를 통해 Map

#### 5.9.2 예제 1

```java
Map<Long,String>  idToNameMap  = Employee.persons()
        .stream()
        .collect(Collectors.toMap(Employee::getId,  Employee::getName));
System.out.println(idToNameMap);
```

결과는

```bash
{1=Jake, 2=Jack, 3=Jane, 4=Jode, 5=Jeny, 6=Jason}
```

#### 5.9.3 예제 2 - merge function

일부러 중복된 key를 택해보자. gender를 key로 하고, 각 gender의 해당하는 이름을 ,를 경계로 출력하는 것을 해보자. 

```java
Map<Employee.Gender,String> genderToNamesMap  =
        Employee.persons()
                .stream()
                .collect(Collectors.toMap(Employee::getGender,
                        Employee::getName,
                        (oldValue, newValue)  ->  String.join(", ", oldValue,  newValue)));
System.out.println(genderToNamesMap);
```

결과는

```bash
{FEMALE=Jane, Jeny, MALE=Jake, Jack, Jode, Jason}
```

#### 5.9.4 예제 3 - merge function

gender를 key로 하고, 각 gender의 해당하는 Employee의 숫자를 출력하는 것을 해보자. 

```java
Map<Employee.Gender, Long> countByGender  = Employee.persons()
        .stream()
        .collect(Collectors.toMap(Employee::getGender, p  ->  1L,
                                (oldCount, newCount)  ->  newCount+oldCount));

System.out.println(countByGender);  
```

결과는

```bash
{FEMALE=2, MALE=4}
```

#### 5.9.5 예제 4 - merge function

gender를 key로 하고, 각 gender별로 가장 수입이 놓은 사람의 정보를 출력하도록 해보자.

```java
Map<Employee.Gender, Employee>  highestEarnerByGender = Employee.persons()
        .stream()
        .collect(Collectors.toMap(Employee::getGender, Function.identity(),
                (oldPerson, newPerson)  -> newPerson.getIncome() >
			 oldPerson.getIncome() ? newPerson : oldPerson));
System.out.println(highestEarnerByGender);
```

결과는

```bash
{FEMALE=(3, Jane,  FEMALE,  1973-05-29,  5455.00)
, MALE=(2, Jack,  MALE,  1972-07-21,  7100.00)
}
```

### 5.10 collect()와 joining() 

#### 5.10.1 개요

Collectors.joining()은 CharSequence 스트림에서 character들을 모아서 String으로 반환한다. 

- Collectors.joining(): 모든 element를 모음
- Collectors.joining(CharSequence delimiter): delimiter를 element 사이에 넣어서 모음
- Collectors.joining(CharSequence delimiter, CharSequence prefix, CharSequence suffix): prefix와 suffix를 String의 맨 앞과 뒤에 추가

#### 5.10.2 예제

```java
        List<Employee> persons  = Employee.persons();
        String names = persons.stream()
                .map(Employee::getName)
                .collect(Collectors.joining());
        System.out.println(names);	//JakeJackJaneJodeJenyJason

        String  delimitedNames = persons.stream()
                .map(Employee::getName)
                .collect(Collectors.joining(", "));
        System.out.println(delimitedNames); //Jake, Jack, Jane, Jode, Jeny, Jason

        String  prefixedNames = persons.stream()
                .map(Employee::getName)
                .collect(Collectors.joining(", ", "Hello ",  ".  Goodbye."));
        System.out.println(prefixedNames); //Hello Jake, Jack, Jane, Jode, Jeny, Jason.  Goodbye.
```

### 5.11 collect()와 groupingBy() 

#### 5.11.1 개요

Collectors.groupingBy()는 같은 속성을 가진 group으로 Map을 만든다. 함수의 정의는 다음과 같다. 

```java
groupingBy(Function<? super  T,?  extends K>  classifier)
groupingBy(Function<? super  T,?  extends K>  classifier,  Collector<? super T,A,D> downstream)
```

- classifier function: map의 key를 만드는 function
- downstream function: 모여진 group에 대해 수행할 function으로 value를 만든다.

#### 5.11.2 예제 1 

gender를 key로 하고, 각 gender별로 숫자 출력하자. toMap()을 이용한 5.9.4 예제 3과 동일하다. 

```java
Map<Employee.Gender, Long> countByGender  = Employee.persons()
        .stream()
        .collect(Collectors.groupingBy(Employee::getGender, Collectors.counting()));
System.out.println(countByGender);
```

결과는

```bash
{MALE=4, FEMALE=2}
```

#### 5.11.3 예제 2

gender를 key로 하고, 각 gender별로 사람 이름을 ,로 구분하여 출력하자. toMap()을 이용한 5.9.3 예제 2와 동일하다. 

```java
Map<Employee.Gender, String>  namesByGender = Employee.persons()
        .stream()
        .collect(Collectors.groupingBy(Employee::getGender,
                Collectors.mapping(Employee::getName, Collectors.joining(", "))));
System.out.println(namesByGender);
```

결과는

```bash
{MALE=Jake, Jack, Jode, Jason, FEMALE=Jane, Jeny}
```

#### 5.11.4 예제 3

gender를 key로 하고, 각 gender별로 사람이름을 List에 저장하자. 

```java
Map<Employee.Gender, List<String>>  namesByGender =
        Employee.persons()
                .stream()
                .collect(Collectors.groupingBy(Employee::getGender,
                        Collectors.mapping(Employee::getName, Collectors.toList())));

System.out.println(namesByGender);
```

결과는

```bash
{FEMALE=[Jane, Jeny], MALE=[Jake, Jack, Jode, Jason]}
```

### 5.12 collect()와 partitioningBy() 

#### 5.12.1 개요

Collectors.partitioningBy()는 특별한 경우의 grouping이다. groupingBy()가 키를 기준으로 여러개의 group을 가질 수 있다면 partitioningBy()는 조건문을 기준으로 만족하는 것과 아닌 것, 두 개의 group만 가진다. 생성되는 Map의 키는 이로 인해 boolean 타입만이 가능하다. 

```java
partitioningBy(Predicate<? super T> predicate)
partitioningBy(Predicate<? super T> predicate,  Collector<? super T,A,D> downstream)
```

#### 5.12.2 예제 1 

gender를 기준으로 나누는 것의 예는 다음과 같다. 

```java
Map<Boolean,  List<Employee>>  partionedByMaleGender =
        Employee.persons()
                .stream()
                .collect(Collectors.partitioningBy(Employee::isMale));
System.out.println(partionedByMaleGender);
```

결과는

```bash
{false=[(3, Jane,  FEMALE,  1973-05-29,  5455.00)
, (5, Jeny,  FEMALE,  1975-12-13,  1234.00)
], true=[(1, Jake,  MALE,  1971-01-01,  2343.00)
, (2, Jack,  MALE,  1972-07-21,  7100.00)
, (4, Jode,  MALE,  1974-10-16,  1800.00)
, (6, Jason,  MALE,  1976-06-09,  3211.00)
]}
```

#### 5.12.3 예제 2

gender를 기준으로 나누고  downstream function을 이용해 reduction operation을 수행하는 예이다. 

```java
Map<Boolean,String> partionedByMaleGender = Employee.persons()
    .stream()
    .collect(Collectors.partitioningBy(Employee::isMale, 
               Collectors.mapping(Employee::getName, Collectors.joining(", "))));
System.out.println(partionedByMaleGender);
```

결과는

```bash
{false=Jane, Jeny, true=Jake, Jack, Jode, Jason}
```

### 5.13 collect()와 collectingAndThen() 

#### 5.13.1 개요

Collector의 결과를 다른 type으로 변환할 때 Collectors.collectingAndThen()를 사용한다. Collectors.toList(), Collectors.toSet(), Collectors.toCollection()와의 차이는 finisher function을 이용해 결과를 다시 한번 변환할 수 있다는 것이다. 


```java
collectingAndThen(Collector<T,A,R> downstream, Function<R,RR>  finisher)
```


#### 5.13.2 예제

다음은 List로 변환된 스트림을 다시 unmodifiableList로 변환한 예이다. 

```java
List<String> names = Employee.persons()
        .stream()
        .map(Employee::getName)
        .collect(Collectors.collectingAndThen(Collectors.toList(),
                result ->  Collections.unmodifiableList(result)));
System.out.println(names);
```

결과는 다음과 같다. 

```bash
[Jake, Jack, Jane, Jode, Jeny, Jason]
```

### 5.14 findAny()와 findFirst()

#### 5.14.1 개요

findAny()는 스트림의 element가 있으면 반환하는 것으로 주로 filter()와 함께 사용된다. findFirst()는 스트림에서 첫번째로 일치하는 element를 반환한다. 이 두 operation 모두 short-circuiting operation으로 전체 스트림에 대해 처리할 필요없이 결과를 바로 반환한다. 

```java
Optional<T>   findAny()
Optional<T>   findFirst()
```

반환 type은 `Optional<T>`이다. primitive type 스트림인 IntStream, LongStream, DoubleStream에서도 동일한 operation을 제공한다. 

#### 5.14.2 findAny() 예제

```java
List<Employee> persons = Employee.persons();
Optional<Employee> anyMale = persons.stream().filter(Employee::isMale).findAny();
if (anyMale.isPresent()) {
    System.out.println(anyMale.get());
} else {
    System.out.println("No male  found.");
}
```

결과는 다음과 같다. 

```bash
(1, Jake,  MALE,  1971-01-01,  2343.00)
```

#### 5.14.3 findFirst() 예제

```java
List<Employee> persons = Employee.persons();
Optional<Employee> firstMale = persons.stream().filter(Employee::isMale).findFirst();
if (firstMale.isPresent()) {
    System.out.println(firstMale.get());
} else {
    System.out.println("No male  found.");
}
```

결과는 다음과 같다. 

```bash
(1, Jake,  MALE,  1971-01-01,  2343.00)
```

### 5.15 allMatch(), anyMatch(), noneMatch()

#### 5.15.1 개요

allMatch()는 스트림의 element가 모두 조건을 만족하면 반환 true를 반환한다. anyMatch()는 스트림에서 하나라도 조건을 만족하면 true를 반환한다. noneMatch()은 모든 element가 조건을 만족하지 않으면 true를 반환한다. 이 세 operation 모두 short-circuiting operation으로 전체 스트림에 대해 처리할 필요없이 결과를 바로 반환할 수 있다. 

```java
boolean allMatch(Predicate<? super T> predicate)
boolean anyMatch(Predicate<? super T> predicate)
boolean noneMatch(Predicate<? super  T> predicate)
```

primitive type 스트림인 IntStream, LongStream, DoubleStream에서도 동일한 operation을 제공한다. 

#### 5.15.2 allMatch() 예제

```java
List<Employee> persons = Employee.persons();

boolean allMales = persons.stream().allMatch(Employee::isMale);
System.out.println("All  males: " + allMales); System.out.println("No male  found.");
}
```

결과는 다음과 같다. 

```bash
All  males: false
```

#### 5.15.3 anyMatch() 예제

```java
List<Employee> persons = Employee.persons();

boolean anyoneEarnsOver5000 = persons.stream().anyMatch(p -> p.getIncome() > 5000);
System.out.println("Anyone earns over 5000: " + anyoneEarnsOver5000);
```

결과는 다음과 같다. 

```bash
Anyone earns over 5000: true
```

## 6. Parallel Stream

### 6.1 개요

스트림은 sequential과 parallel 2종류가 있다. sequential 스트림은 하나의 thread에 의해 순차적으로 처리되며 parallel 스트림은 다수의 thread에 의해 병렬로 처리되어 진다. 스트림의 모든 API들은 기본적으로 sequential로 동작하며, 이를 parallel로 바꾸기 위해서는 parallel 스트림을 명시적으로 만들어야 한다. 

- parallelStream(): Collection(List 또는 Set)에서 Parallel 스트림 생성
- parallel(): sequential 스트림을 Parallel 스트림으로 변환
- sequential(): Parallel 스트림을 sequential 스트림으로 변환

### 6.2 parallelStream() 

Collection(List 또는 Set)에서 Parallel 스트림 생성한다. 

```java
String names = Employee.persons()
        .parallelStream()
        .filter(Employee::isMale)
        .map(Employee::getName)
        .collect(Collectors.joining(", "));
System.out.println(names);
```

결과는 

```bash
Jake, Jack, Jode, Jason
```

### 6.3 parallel()

squential을 parallel 스트림으로 변환한다. 

```java
String names = Employee.persons()                // The data source
        .stream()                  // Produces a  sequential  stream
        .filter(Employee::isMale)   // Processed in serial
        .parallel()               // Produces a  parallel  stream
        .map(Employee::getName)       // Processed in parallel
        .collect(Collectors.joining(", "));  // Processed in parallel
System.out.println(names);
```

결과는 

```bash
Jake, Jack, Jode, Jason
```

## 참고

- [java2s.com](http://www.java2s.com/Tutorials/Java/Java_Stream/index.htm) 
- [java8 tutorial](http://www.tutorialspoint.com/java8/java8_streams.htm)
- [programcreek의 simple java 8](http://www.programcreek.com/simple-java-8-lambdas/)
- [Java Optional](http://www.java2s.com/Tutorials/Java/Java_Stream/0035__Java_Optional.htm)
- [java.util.stream Package Reference -  Stream](http://www.java2s.com/Tutorials/Java/java.util.stream/Stream/index.htm)
- [java.util.stream Package Reference - Collectors](http://www.java2s.com/Tutorials/Java/java.util.stream/Collectors/index.htm)
- [java.util.stream Package Reference -  Stream.Builder](http://www.java2s.com/Tutorials/Java/java.util.stream/Stream.Builder/index.htm)
- [java.util.stream Package Reference - InStream](http://www.java2s.com/Tutorials/Java/java.util.stream/IntStream/index.htm)
- [java.util.stream Package Reference - LongStream](http://www.java2s.com/Tutorials/Java/java.util.stream/LongStream/index.htm)
- [java.util.stream Package Reference - DoubleStream](http://www.java2s.com/Tutorials/Java/java.util.stream/DoubleStream/index.htm)
