---
layout: post
title: Java - Lambda patterns
description: Java - Lambda patterns
modified: 2017-03-13
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

Lambda를 이용한 디자인 패턴을 소개한 내가 좋아하는 Venka Subramaniam 교수의 강의가 있어 그 내용을 간략히 코드 중심으로 정리한다. 

### Light Strategy Pattern

조건에 맞는 데이터만 처리하도록 하는 것인데 그 조건을 외부에서 제공할 수 있도록 한 것이다. 

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;

public class LightStrategyPattern {
    public static int totalValues(List<Integer> values, Predicate<Integer> predicate) {
        return values.stream()
                .filter(predicate)
                .mapToInt(o -> o)
                .sum();
    }

    public static void main(String[] args) {
        int sum = totalValues(Arrays.asList(1, 2, 3, 4, 5, 6), o -> o % 2 == 0);
        System.out.println(sum);
    }
}

```


### Decorator Pattern

복수의 operation을 입력으로 받아서 하나의 입력 데이터에 적용하는 것이다. 입력 operation이 없는 경우 default operation을 하는 것도 가능하다. 이 경우 아래 Function.identity() 대신 default Function을 호출하면 된다. 

```java
import java.awt.*;
import java.util.function.Function;
import java.util.stream.Stream;

class Camera {
    private Function<Color, Color> filter;

    public Camera(Function<Color, Color>... filters) {
        setFilters(filters);
    }

    public void setFilters(Function<Color, Color>... filters) {
        filter = Stream.of(filters)
                .reduce(Function.identity(), Function::andThen);
    }

    public Color snap(Color input) {
        return filter.apply(input);
    }
}

public class DecoratorPattern {
    public static void main(String[] args) {
        printSnap(new Camera());
        printSnap(new Camera(Color::brighter));
        printSnap(new Camera(Color::darker));
        printSnap(new Camera(Color::brighter, Color::darker));
    }

    public static void printSnap(Camera camera) {
        System.out.println(camera.snap(new Color(125, 125, 125)));
    }
}
```


### Cascade 또는 Builder Pattern

리소스 획득/해제와 사용을 분리하며 순서에 상관없는 연속적인 operation을 처리하고 확실히 맺기 위한 패턴이다. 예를 들어 아래 send()도 from(), to()와 유사한 방식으로 정의되어 있다면, send()를 호출한 뒤에도 from()을 호출할 수 있는 것인지, 어떻게 이를 막을 것인지 고민해야 할 것이다. 
그런데 Cascade 패턴, 또는 builder 패턴을 사용하면 send()이후에는 Resource가 해제되고 호출할 방법이 없기 때문에 안전하다. 이것은 다음에 다룰 Loan 패턴과 유사하다.

```java
import java.util.function.Consumer;

class Mailer {
    public Mailer from(String source) {
        System.out.println(source);
        return this;
    }

    public Mailer to(String destination) {
        System.out.println(destination);
        return this;
    }

    public Mailer subject(String subject) {
        System.out.println(subject);
        return this;
    }

    public Mailer body(String body) {
        System.out.println(body);
        return this;
    }

    public static void send(Consumer<Mailer> block) {
        Mailer mailer = new Mailer();
        block.accept(mailer);
        System.out.println("Sending...");
    }
}

public class BuilderOrCascadePattern {
    public static void main(String[] args) {
        Mailer.send(mailer ->
                mailer.from("aaa@example.com")
                        .to("bbb@example.com")
                        .subject("Your code sucks")
                        .body("...here you go...")
        );
    }
}
```

### Loan Pattern

리소스의 사용과 획득/해제를 완전히 분리하는 패턴이다. 사용은 Consumer 인터페이스를 통해 외부에서 정의하고 리소스 획득/해제는 메소드 내부에서 담당하여 사용하는 관점에서 리소스에 대해 신경쓰지 않아도 되도록 한 것이다. 
리소스를 획득/해제하는 것을 반복적인 호출로 처리하면 실수할 수도 있고, 번거롭기도 한 경우 사용할만 한다. 

```java
import java.util.function.Consumer;

class Resource {
    public Resource() {
    }
    public Resource policy1() {
        System.out.println("policy 1");
        return this;
    }
    public Resource policy2() {
        System.out.println("policy 2");
        return this;
    }
    public void close() {
        System.out.println("Resource is returned");
    }
    public static void use(Consumer<Resource> block) {
        Resource resource = new Resource();
        try {
            block.accept(resource);
        } finally {
            resource.close();
        }
    }
}

public class LoanPattern {
    public static void main(String[] args) {
        Resource.use(resource ->
                resource.policy1()
                        .policy2());
    }
}
```
