---
layout: post
title: Web - 빠르고 편리한 코딩을 위한 WebStorm live template 기능
modified: 2015-09-05
tags: [web]
comments: true
image:
  feature: abstract-12.png
---

WebStorm의 기능 중 코딩 속도를 빠르게 할 수 있는 방법으로 live template이 있다. template을 이용하면 빈번히 사용되는 코드를 한두번의 타이핑만으로 쉽게 완성할 수 있다. 자신만의 live template을 등록하고 사용하는 방법을 살펴보자. 

<section id="table-of-contents" class="toc">
  <header>Overview</header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section>
<!-- /#table-of-contents -->


# live template이란?

코딩창에서 *itar*를 타이핑한 뒤 **탭 키**를 누르면 자동으로 다음 코드가 완성된다. 

{% highlight javascript %}
for (var i = 0; i < array.length; i++) {
    var obj = array[i];
    
}
{% endhighlight %}

이때 커서가 변수 *i*로 이동해 있는데 타이핑을 하면 변수 이름이 바뀌는 것을 알 수 있으며, 탭 키를 다시 누르면 커서가 *array*로 이동해서 즉시 수정을 할 수 있음을 알 수 있다. 이런 template을 이용하면 매우 빠르게 코딩을 할 수 있다. 

# live template 등록

샘플을 이용하여 live template을 등록하는 방법을 알아보자. 

## 샘플 요구사항 

위에서 설명한 *itar*과 동일하게 동작하는 *ffor* template을 등록해 본다. 
*ffor*를 타이핑한 후 탭 키를 누르면 다음과 같은 코드가 완성되고, 탭 키를 눌러 이동하면서 편집하는 포인트는 변수 *i*인데, 첫 *i*의 이름을 변경하면 나머지 3개의 *i*위치에도 변수명이 동시에 변경되어야 한다. 그리고 다시 탭 키를 누르면 0이 변경될 수 있다. 탭을 다시 누르면, *array*도 변화 시킬 수 있는데 이때 *array[i]*의 *array*도 함께 변경되어야 한다. 마지막으로 탭 키를 누르면 *obj*위치의 변수명을 변경 할 수 있다. 

{% highlight javascript %}
for (var i = 0; i < array.length; i++) {
    var obj = array[i];
}
{% endhighlight %}

## 등록하기 

1. 메뉴에서 *Preference*를 선택하여 실행한다. 

2. Preference창에서 editor > Live templates를 선택한다. 

3. Live templates group 중 원하는 group을 선택한 후 **+**를 누르고 *Live Template*을 선택한다. 

4. Live template 입력하기 

- **Abbreviation**에는 'ffor'
- **description**에는 'for 문 자동완성'
- **Template Text**에는 다음과 같이 추가한다. 

{% highlight javascript %}
for (var $i$ = $0$; $i$ < $array$.length; $i$++) {
    var $obj$ = $array$[$i$];
}
{% endhighlight %}

- **Edit varables**를 선택하면 *Edit Template Variables*창이 뜨는데 Name 열은 채워져 있고, 나머지는 비어져 있다. Default Value는 맨처음에 드러날 값인데, 주의할 점은 반드시 " "로 둘러싸서 문자열로 입력해야 한다는 것이다. 예를 들어 Name i의 Default Value는 "i"로 한다. 
 - Option의 **Reformat according to style**을 선택한다. 
 - 마지막으로 No application contexts. **Define**에서 *Define*을 눌러 원하는 javascript를 선택한다. 

## 테스트 

WebStorm의 editor 창에서 javascript 파일을 열고, *ffor*을 타이핑한 뒤 탭 키를 누른다. 그 뒤 위의 요구사항대로 동작하는 지 확인해 본다. 

# 정리

위의 예를 통해 live template을 등록하는 방법을 간단히 살펴보았다. javascript의 예제를 살펴보았지만, 동일한 방법으로 언어별, framework별로 적용이 가능하다. 


