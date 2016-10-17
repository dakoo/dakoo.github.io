---
layout: post
title: Java - Lombok
description: Java - Lombok
modified: 2016-10-14
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


[Lombok](http://projectlombok.org)은 아주 유용한 Java annotation 라이브러리이다. 
이것은 지저분한 boilerplate 코드를 줄이고 이에 따라 코딩을 더 빠르고 실수없이 할 수 있도록 도와준다. Lombok은 getter, setter, toString, constructor, equals, hashCode 메소드들을 자동적으로 생성한다. 개발자는 단지 클래스를 만들고 필드를 추가하면 된다. 

자세한 내용은 [여기](https://projectlombok.org/features/index.html)를 참조하고 annotation이 어떤 것을 지원하는지 간단히 알아보자

### 1. [val](https://projectlombok.org/features/val.html)

lombok.val을 임포트하면 final 키워드를 추가하지 않아도 필요한 변수에는 final을 추가한다. 

```java
import lombok.val;
```

### 2. [@NonNull](https://projectlombok.org/features/NonNull.html)

Null이여서는 안되는 메소드 parameter에 @NoneNull 키워드를 붙여주면 Null인 경우 NullPointerException을 throw한다. 

```java
public Shape(@NonNull Person person) {
    ...
}    
```


### 3. [@Cleanup](https://projectlombok.org/features/Cleanup.html)

InputStream이나 OutputStream 선언시 @Cleanup 키워드를 붙여 주면 자동으로 close()를 호출해서 리소스를 반환해 준다. 

```java
@Cleanup InputStream in = new FileInputStream(args[0]);
```


### 4. [@Getter / @Setter](https://projectlombok.org/features/GetterSetter.html)

클래스의 필드에 키워드를 추가하면 자동으로 setter와 getter를 만들어 준다. 접근 레벨도 설정할 수 있다. 

```java
@Getter @Setter 
private int age = 10;

@Setter(AccessLevel.PROTECTED) private String name;
```


### 5. [@ToString](https://projectlombok.org/features/ToString.html)

클래스의 toString() 메소드를 만들어준다. 이때 어떤 필드를 추가할 지 설정할 수 있다. 

```java
@ToString(exclude="id") 
public class Shape { 
    private String name;
    private double score;
    private String[] tags;
    private int id;
    ...
}    

@ToString(callSuper=true, includeFieldNames=true) 
public static class Square extends Shape { 
    ...
}    
```

### 6. [@EqualsAndHashCode](https://projectlombok.org/features/EqualsAndHashCode.html)

클래스의 필드를 기반으로 자동으로 hashCode()와 equals() 메소드를 만들어준다. 

```java
@EqualsAndHashCode(exclude={"id"}) 
public class Shape { 
    private String name;
    private double score;
    private String[] tags;
    private int id;
    ...
}   

@EqualsAndHashCode(callSuper=true)
public static class Square extends Shape { 
    ....
}

```


### 7. [@NoArgsConstructor, @RequiredArgsConstructor 그리고 @AllArgsConstructor](https://projectlombok.org/features/Constructor.html)

Constructor를 자동으로 만든다. 

```java
@RequiredArgsConstructor(staticName = "of")
@AllArgsConstructor(access = AccessLevel.PROTECTED)
public class Shape { 
    ...
}   
```

### 8. [@Data](https://projectlombok.org/features/Data.html)
@ToString, @EqualsAndHashCode, @Getter, @Setter, @RequiredArgsConstructor를 한번에!!!

```java
@Data
public class Shape { 
    ...
}   
```

### 9. [@Value](https://projectlombok.org/features/Value.html)

Immutable 클래스를 쉽게 만든다. 이건 자세한 내용을 참조하자. 

### 10. [@Builder](https://projectlombok.org/features/Builder.html)

builder 패턴을 쉽게 만든다. 이건 자세한 내용을 참조하자. 

### 11. [@SneakyThrows](https://projectlombok.org/features/SneakyThrows.html)

Throws를 제대로! 이건 자세한 내용을 참조하자. 

### 12. [@Synchronized](https://projectlombok.org/features/Synchronized.html)

synchronized를 제대로! 이건 자세한 내용을 참조하자. 

### 13. [@Getter(lazy=true)](https://projectlombok.org/features/GetterLazy.html)

성능을 잡아먹는 cache이슈를 피해서 lazy getter를 구현한다. 이건 자세한 내용을 참조하자. 

### 14. [@Log](https://projectlombok.org/features/Log.html)

주저리 주저리 긴 log선언을 깔끔하게 처리한다.  이건 자세한 내용을 참조하자. 
