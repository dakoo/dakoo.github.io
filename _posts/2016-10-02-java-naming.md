---
layout: post
title: Java - 네이밍
description: Java - 네이밍
modified: 2016-10-02
tags: [java, design]
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

코딩에서 네이밍은 항상 힘들다. Java 네이밍 관련 글 중 좋은 글이 있어 간단히 정리해 보자. 여기서 설명하는 내용은 사실 Java만이 아니라 많은 언어에 사용 가능하다. 

자세한 내용은 항상 그렇듯 [원문](http://javarevisited.blogspot.sg/2014/10/10-java-best-practices-to-name-variables-methods-classes-packages.html)을 참고하자.

## Java 네이밍 베스트 프랙티스  

#### 1. 무의미한 이름을 피해라

abc, temp, data와 같은 개발자의 의도를 담고 있지 않은 이름은 프로페셔널 프로젝트에서는 사용되어서는 안된다. 단지 자신만이 사용하는 테스트용 프로그램에서만 사용해라.

#### 2. 의미있는 이름을 부여하라 

public과 private 모두 개발자의 의도를 전달하는 의미있는 이름을 사용하라. getPD()보다는 getPayDate()가 훨씬 개발자의 의도를 잘 전달한다. 

#### 3. 의도가 드러난다면 되도록 짧은 이름을 선호하라

의도가 드러나는 경우엔 되도록 짧은 이름이 긴 이름보다 좋다. 예를 들어 getPayDate()가 retreivePaymentDate()보다 좋다. 

#### 4. 비슷한 이름들을 피해라

유사한 이름만큼 나쁜 건 없다. 예를 들어 변수 이름이 employee와 employees는 모두 같으며 마지막만 다르다. 이런 유사성은 미묘한 버그를 이끌고 코드 리뷰를 어렵게 한다. 만약 컬렉션을 표현하고자 한다면 listOfEmployees가 employees보다 더 명확하다. 이 때 주의할 점은 list는 모두 알다 시피 ordered 컬렉션이므로 자연스럽게 저 이름을 사용하면 ordered라고 인식하게 된다는 것이다. 

#### 5. 축약형 보다 서술형이 더 좋다

getLiquidityIndicator()가 getLInd()보다 좋다. 축약형을 선택하는 경우는 축약형으로도 개발자의 의도가 명백히 전달되는 경우 만이다. 
긴 이름을 사용하게 되는 이유 중 하나는 한 메소드에서 여러 일을 하려 하기 때문이다. 이런 경우 메소드를 분할 하는 것을 고려해라. 예를 들어, loadAndUpdateAllInstrumentRecords()는 loadInstruments()와 updateInstruments()로 분할 될 수 있다. 

#### 6. Java 코딩 컨벤션을 따르라

다음의 Java 코딩 컨벤션들은 아주 일반적인 것들이다. 

- 클래스 이름은 대문자로 시작: Employee, Student, Thread
- 메소드 이름은 소문자로 시작하며 camelCase: getEmployee(), getPayDate()
- 변수 이름은 역시 소문자로 시작하며 camelCase:  price, quantity, totalAmount
- constant는 모두 대문자: MAX_QUANTITY, MAX_PRICE

#### 7. 일관적인 네이밍을 사용하고 동의어는 피하라

같은 동작을 하는 메소드들은 일관적인 이름을 사용하라. 예를 들어 각기 다른 모듈에서 destroy(), kill(), finish()를 사용하는 것은 좋지 않다. 그 보다는 그 중 하나를 택해서 사용하는 것이 더 좋다. 

#### 8. 전통적인 프로그래밍 컨벤션을 따르라

무의미한 이름을 피하라고 했지만, 예외가 있다. loop에서 i와 j는 매우 전통적인 변수명으로 모든 프로그래머들에게 익숙하다. 

```java
for(int i=0; i<10; i++){
  // your code
}
```

#### 9. 클래스 이름은 명사여야 한다. 

잘 알다시피 다음은 아주 일반적인 Java에서의 네이밍 규칙이다. 

- 클래스 이름은 명사: Employee, Thread, String
- 메소드 이름은 동사로 시작: get, set, do, invoke
- 인터페이스 이름은 어떤 능력이나 CAN DO(Runnable)을 나타냄: Runnable, Callable
- 패키지 이름은 회사와 프로젝트 구조(com.company.project.module)를 반영: org.apache.commons.beanutils.BasicDynaBean

#### 10. _, m_, o_와 같은 prefix를 붙이지 말라

개발자들 중 일부는 _, mㅡ, o_ 등을 이용해 로컬 멤버임을 나타내는 것 등을 하고자 한다. 하지만, 이런 것 보다는 의미있는 이름을 사용하는 것이 더 좋다. 프로젝트에 이미 m_ 네이밍 컨벤션을 사용 중이라면 재 작성하거나 모든 프로젝트를 바꾸기 보다 유지하는 것이 더 좋다. 
또한 일부 개발자들은 bExit으로 boolean을 표시하거나 iMax로 integer임을 표시하는 헝가리안 표기법을 사용한다. 이건 legacy 코드로 작업할 때 심각하게 나쁜 것은 아니지만 처음부터 개발할 때는 되도록 사용하지 말자. 

#### 11. ASCII 문자가 아니거나 로컬 언어로부터의 단어는 피하자. 

쉽게 말하면 영어가 아닌 것을 피하자. 프로그래밍 세계에서는 영어가 공용어이다. 

#### 12. is, has, can, do와 같은 일반적인 동사를 적절히 사용하자 

is, has, can를 적절히 이용하면 코드 가독성을 높일 수 있다. 예를 들어 isAlive(), hasNext(), canExecute()와 같은 메소드는 이해가 쉽고 가독성이 좋다. 
조건문에 사용되는 변수명에도 동일하게 사용하여 가독성을 높일 수 있다. 

```java
if(isRaining){
   bringUmbrella();
}
```
