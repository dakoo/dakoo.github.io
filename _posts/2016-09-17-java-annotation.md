---
layout: post
title: Java - Annotation
description: Java - Annotation
modified: 2016-09-17
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


Java Annotation은 클래스, 메소드, 필드의 **선언**시 정보 제공을 목적으로 사용된다. 이에 대해 알아보자. 

### 1. Java Annotation의 목적

Java annotation은 다음 3가지의 목적으로 사용된다. 

- 컴파일러를 위한 정보 제공: 컴파일 시 error나 warning 관련 정보
- 컴파일 시와 배포시 처리 시 정보 제공: code나 XML 등을 생성하기 위한 정보 제공
- 런타임 처리시 

### 2. 미리 정의된 Annotation 타입들

java.lang에 미리 정의된 Annotation 타입들은 다음과 같다. 

#### 2.1 일반

##### 2.1.1 @Deprecated

```java
    @Deprecated
    static void deprecatedMethod() { }
```

##### 2.1.2 @Override

interface의 method 구현시 또는 상속시 overriding한 경우 사용

```java
   @Override 
   int overriddenMethod() { }
```

##### 2.1.3 @SuppressWarnings

컴파일러에게 특정 Warning을 숨기도록 명령한다. 아래의 예는 Deprecated method라는 warning을 표시하지 않도록 하는 것이다. 

```java
   @SuppressWarnings("deprecation")
    void useDeprecatedMethod() {
        objectOne.deprecatedMethod();
    }
```

##### 2.1.4 @SafeVarargs

메소드의 varargs 아규먼트에 안전하지 않은 operation을 허용하지 않도록 한다. 


##### 2.1.5 @FunctionalInterface 

functional interface로 의도 되었다는 것을 명시한다. 

#### 2.2 다른 Annotation의 선언 적용되는 것들

##### 2.2.1 @Retention

- RetentionPolicy.SOURCE : Annotation이 소스 레벨에서만 유지되고 컴파일러에 의해서는 무시됨을 명시. 이를 이용해 annotation을 **comment** 대신 사용 
- RetentionPolicy.CLASS : 컴파일로 수준에서 유지되고 런타임에는 (JVM에 의해서는) 무시됨
- RetentionPolicy.RUNTIME : JVM 수준에서 유지됨 

##### 2.2.2 @Documented

javadoc 툴에 의해 문서화 되어야함

##### 2.2.3 @Target

Annotation이 특정 대상에만 적용될 수 있음을 명시. 그 예는 다음과 같다. 

- ElementType.ANNOTATION_TYPE: annotation type
- ElementType.CONSTRUCTOR: constructor
- ElementType.FIELD: field 또는 property
- ElementType.LOCAL_VARIABLE: local variable
- ElementType.METHOD: method-level annotation.
- ElementType.PACKAGE: package declaration.
- ElementType.PARAMETER: 메소드 parameter
- ElementType.TYPE: class의 element

##### 2.2.4 @Inherited

상속된 annotation type

##### 2.2.5 @Repeatable

Annotation이 같은 선언에서 반복될 수 있음을 명시


### 3. Annotation 만들기 

Annotation선언은 interface 선언하는 것과 동일하다. 단지 interface대신 @interface여야 한다.  메소드로 선언된 것들은 `xxx = yyy`형태로 입력하고, default 값은 `default` 키워드를 이용해 선언한다. 

```java
//ClassPreamble.java

package app;
import java.lang.annotation.*;

public @interface ClassPreamble {
   String author();
   String date();
   int currentRevision() default 1;
   String lastModified() default "N/A";
   String lastModifiedBy() default "N/A";
   // Note use of array
   String[] reviewers();
}
```

위와 같이 선언하면 다음과 같이 사용할 수 있다.


```java
//App.java
package app;

@ClassPreamble (
		   author = "John Doe",
		   date = "3/17/2002",
		   currentRevision = 6,
		   lastModified = "4/12/2004",
		   lastModifiedBy = "Jane Doe",
		   reviewers = {"Alice", "Bob", "Cindy"}
		)
public class App {
	public static void main(String argc[]) {
		System.out.println("Hello");
	}
}
```

다음과 같이 @Documented를 추가해서 javadoc에서 참조하고, 또한 comment 대신으로 사용하기 위해 @Retention(RetentionPolicy.SOURCE)을 추가할 수 있다. 
그리고 @Target(ElementType.TYPE)으로 클래스에만 적용할 수 있다. 

```java
//ClassPreamble.java
@Documented
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.SOURCE)
@interface ClassPreamble {
	...
}
```


### 4. 예제 

Reflection의 isAnnotationPresent()과 getAnnotation()과 결합하여 Annotation을 사용한 예를 보자. 
Annotation에 userRole과 BudgetLimit을 정하고 Reflection을 통해 이를 읽어 내는 것이다. 

```java
import java.lang.annotation.*;
import java.lang.reflect.*;
import java.util.*;

/* Annotation 선언 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@interface FamilyBudget {
   String userRole() default "GUEST";
   int budgetLimit() default 100;
}

class FamilyMember {
   /* Annotation 사용 */
   @FamilyBudget(userRole="SENIOR", budgetLimit=100)
   public void seniorMember(int budget, int moneySpend) {
      System.out.println("Senior Member");
      System.out.println("Spend: " + moneySpend);
      System.out.println("Budget Left: " + (budget - moneySpend));
   }
   /* Annotation 사용 */
   @FamilyBudget(userRole="JUNIOR", budgetLimit=50)
   public void juniorUser(int budget, int moneySpend) {
      System.out.println("Junior Member");
      System.out.println("Spend: " + moneySpend);
      System.out.println("Budget Left: " + (budget - moneySpend));
   }
}

public class Solution {
   public static void main(String[] args) {
      Scanner in = new Scanner(System.in);
      int testCases = Integer.parseInt(in.nextLine());
      while (testCases > 0) {
         String role = in.next();
         int spend = in.nextInt();
         try {
            Class annotatedClass = FamilyMember.class;
            Method[] methods = annotatedClass.getMethods();
            for (Method method : methods) {
               /* Annotation 정보 획 */
               if (method.isAnnotationPresent(FamilyBudget.class)) {
                  FamilyBudget family = method
                        .getAnnotation(FamilyBudget.class);
                  String userRole = family.userRole();
                  int budgetLimit = family.budgetLimit();
                  if (userRole.equals(role)) {
                     if(spend <= budgetLimit){
                        method.invoke(FamilyMember.class.newInstance(),
                              budgetLimit, spend);
                     }else{
                        System.out.println("Budget Limit Over");
                     }
                  }
               }
            }
         } catch (Exception e) {
            e.printStackTrace();
         }
         testCases--;
      }
   }
}
```

입력은 

```bash
3
SENIOR 75
JUNIOR 45
SENIOR 40
```

출력은 

```bash
Senior Member
Spend: 75
Budget Left: 25
Junior Member
Spend: 45
Budget Left: 5
Senior Member
Spend: 40
Budget Left: 60
```




### 5. 참조

- [Oracle Annotation 튜토리얼](https://docs.oracle.com/javase/tutorial/java/annotations/index.html)
- 

