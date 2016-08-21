---
layout: post
title: Java - pattern matching with regular expression
description: java - pattern matching with regular expression
modified: 2016-08-20
tags: [java]
comments: true
image:
  feature: abstract-11.png
---
Java에서 regular expression을 이용해 pattern matching하는 방법을 살펴보자. Pattern class와 Matcher class가 사용된다. 

### String

`inputString.matches(pattern)`형태로 inputString이 regular expression pattern을 만족하는지 쉽게 파악 가능하다. 
true를 return하면 만족하는 것이다. 

### Pattern class

Pattern class에 대해 자세한 내용은 [여기](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Pattern.html)를 참조하자.

- static **compile(String s)** method: regular expression string을 입력으로 받아 컴파일된 Pattern instance를 반환. 
- static **matcher(String s)** method: regular expression과 비교할 입력 string을 받아 Matcher instance를 반환한다. 

### Matcher class

Matcher class에 대해 자세한 내용은 [여기](https://docs.oracle.com/javase/8/docs/api/java/util/regex/Matcher.html)를 참조하자.

- find() method: 패턴과 일치하는 부분을 찾으면 true를 반환하며, 이때 match instance의 start() method를 통해 index를 얻을 수 있다. 
- matches() method: 패턴과 대상 입력이 일치하면 true를 반환한다. 대상 입력이 원하는 조건에 맞는지를 확인하기 위한 것이다. 
- group() method: 패턴과 일치된 텍스트를 반환한다. 

### 예제 

#### 예제 1

"12345678901234567890"에서 "123" 찾기 

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

class Solution {
    public static void main(String[] args){
        Pattern p = Pattern.compile("123");
        Matcher m = p.matcher("12345678901234567890");
        while(m.find()){
            System.out.println(m.start() + ": " + m.group());
        }
    }
}
```

출력

```
0: 123
10: 123
```

#### 예제 2

주어진 IP address가 valid인지 검사하기 : valid IP 주소의 조건은 다음과 같다.

- A.B.C.D form의 string
- A, B, C, D의 값은 0부터 255까지
- A, B, C, D 값은 0으로 시작해되 된다.
- A, B, C, D의 길이는 3보다 클 수 없다.

```java
import java.util.regex.PatternSyntaxException;

class Solution {
    public static void main(String[] args){
        String s = "000.12.12.034";
        String pattern = "((\\d{1,2}|(0|1)\\d{2}|2[0-4]\\d|25[0-5])\\.){3}(\\d{1,2}|(0|1)\\d{2}|2[0-4]\\d|25[0-5])";
        try{
            if(s.matches(pattern) == true){
                System.out.println("Valid");
            } else {
                System.out.println("Invalid");
            }
        } catch (PatternSyntaxException e){
            System.out.println("Invalid regular expression");
        }
    }
}
```

결과 

```
Valid
```

#### 예제 3

##### 입력 

text에서 연속으로 반복되는 단어 제거하자. 이때 대소문자는 상관없이 제거하자. 예를 들어 'Goodbye bye Bye bye world World WORLD'가 입력이라면 'Goodbye bye world'가 출력이여야 한다. 

```
import java.util.regex.Matcher;
import java.util.regex.Pattern;

class Solution {
    public static void main(String[] args){
        String pattern = "\\b(\\w+)(\\b\\W+\\b\\1\\b)*";
        Pattern r = Pattern.compile(pattern, Pattern.MULTILINE+Pattern.CASE_INSENSITIVE);
        String input = "Goodbye bye Bye bye world World WORLD";
        Matcher m = r.matcher(input);
        while(m.find( )){
            input =input.replaceAll(m.group(),m.group(1));
        }
        System.out.println(input);
    }
}
```

위의 pattern을 설명하면 다음과 같다. 

- '\\b~~~\\b'를 이용해 공백으로 구분된 단어만 잡아냄 
- '(w+)'는 단어
- '\\b\\W+\\b\\1\\b': 앞의 단어와 구분되며(\\b), 알파벳이 아닌 것이 - 예를 들어 줄바꿈이 나타날 수 있고(\\W+), 이전 단어와 일치(\\1)하는 것
- '*': 연속으로 출현 

위와 같은 패턴으로 하면 m.find()를 통해 다음이 찾아진다. 

- 'Goodbye'
- 'bye Bye bye'
-  'world World WORLD'


##### 출력

```
Goodbye bye world
```
