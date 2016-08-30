---
layout: post
title: Java - pattern matching with regular expression
description: java - pattern matching with regular expression
modified: 2016-08-20
tags: [java, regularexpression, string]
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
- group() method: 패턴과 일치된 텍스트를 반환한다. group()을 하면 모두 반환하고, group(1)은 첫번째 sub group, group(2)는 두번째 subgroup을 반환한다. 

### 예제 

#### 예제 1

##### 입력 

"12345678901234567890"에서 "123" 찾기 

##### 구현 

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

##### 출력

```bash
0: 123
10: 123
```

#### 예제 2

주어진 IP address가 valid인지 검사하기 : valid IP 주소의 조건은 다음과 같다.

- A.B.C.D form의 string
- A, B, C, D의 값은 0부터 255까지
- A, B, C, D 값은 0으로 시작해되 된다.
- A, B, C, D의 길이는 3보다 클 수 없다.

##### 입력 

```bash
000.12.12.034
```

##### 구현 

```java
import java.util.regex.PatternSyntaxException;
import java.util.Scanner;

class Solution {
    public static void main(String[] args){
        Scanner scan = new Scanner(System.in);
        String s = scan.nextLine();
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

##### 출력

```bash
Valid
```

#### 예제 3

text에서 연속으로 반복되는 단어 제거하자. 이때 대소문자는 상관없이 제거하자. 예를 들어 'Goodbye bye Bye bye world World WORLD'가 입력이라면 'Goodbye bye world'가 출력이여야 한다. 

##### 입력 

```bash
Goodbye bye Bye bye world World WORLD
```

##### 구현 

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;
import java.util.Scanner;

class Solution {
    public static void main(String[] args){
        Scanner scan = new Scanner(System.in);
        String input = scan.nextLine();
        scan.close();
        String pattern = "\\b(\\w+)(\\b\\W+\\b\\1\\b)*";
        Pattern r = Pattern.compile(pattern, Pattern.MULTILINE+Pattern.CASE_INSENSITIVE);
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
- '(\\b\\W+\\b\\1\\b)*': 앞의 단어와 구분되며(\\b), 알파벳이 아닌 것이 - 예를 들어 줄바꿈이 나타날 수 있고(\\W+), 이전 단어와 일치(\\1)하는 것. 이것이 '*' 0번 또는 한 번 이상 출현 --> 즉, 이것은 앞의 w+로 찾아지는 단어의 반복을 찾아내는 것이다. 

위와 같은 패턴으로 하면 m.find()를 통해 다음이 찾아진다. 

- 'Goodbye'
- 'bye Bye bye'
-  'world World WORLD'


##### 출력

```bash
Goodbye bye world
```

#### 예제 4

xml tag를 parsing하자. <tag>contents</tag> 형태의 tag이며, 이때 가운데 컨텐츠만 출력한다. 

- tag는 시작과 끝이 같아야 한다. <h1>Hello World</h2>는 invalid이다. 
- tag는 중첩될 수 있으나 중첩된 tag사이의 컨텐츠는 invalid이다. 예를 들어, <h1><a>contents</a></h1>의 contents는 valid이다. 그리고,  <h1><a>contents</a>invalid</h1>에서 contents는 valid이나 invalid는 invalid이다. 

##### 입력 

```bash
<h1><h1>Sanjay has no watch</h1></h1><par>So wait for a while</par>
```

##### 코드 

```java
import java.util.Scanner;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class Solution {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        String inputString = scan.nextLine();
        scan.close();
        Pattern pattern = Pattern.compile("<(.+)>([^<>]+)</\\1>");
        Matcher matcher = pattern.matcher(inputString);
        boolean found = false;
        while (matcher.find()) {
            System.out.println(matcher.group(2));
            found = true;
        }
        if (found == false) {
            System.out.println("None");
        }
    }
}
```

- group(2)를 이용해서 content를 뽑아낸다. group(1)이면 tag가 나올 것이다. 

##### 출력

```bash
Sanjay has no watch
So wait for a while
```
