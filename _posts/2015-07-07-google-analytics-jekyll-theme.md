---
layout: post
title: Jekyll theme을 위한 Google Analytics 사용법
description: Jekyll theme을 위한 Google Analytics 사용법
modified: 2015-07-07
tags: [blog]
comments: true
image:
  feature: abstract-14.png
---

<section id="table-of-contents" class="toc">
  <header>
    <h3>Overview</h3>
  </header>
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

[google analytics](http://www.google.com/intl/ko_ALL/analytics/features/index.html)는 mobile app이나 web 사이트의 방문자의 사이트 활동, 사이트 유입경로, 고객의 재방문을 유도하는 방법에 대한 분석 정보를 제공하는 강력한 도구이다. 이를 사용하여 서비스를 더욱 사용자가 원하는 방향으로 이끌수 있다. 

<figure>
	<img src="/images/googleanalytics1.PNG" alt="">
</figure>

Jekyll theme에 적용하는 방법의 설명을 통해 어떻게 google analytics를 사용하는지에 대해 알아보자.  

## 사전 준비

[google analytics](http://www.google.com/intl/ko_ALL/analytics/index.html) 가입을 한다. google 계정이 있다면 사용가능하다. 

## google analytics 설정

1. google analytics에서 Admin(관리)를 선택한다. 

<figure>
	<img src="/images/googleanalytics2.PNG" alt="">
</figure>

2. 가운데 Property 열의 create new property를 선택한다. 

<figure>
	<img src="/images/googleanalytics3.PNG" alt="">
</figure>

3. Website Name은 적당하게 작성하고, **Website URL**은 블로그의 URL을 작성한다. Industry category와 Reporting Time Zone은 적당히 작성 후 **Get Tracking ID**을 누른다. 

<figure>
	<img src="/images/googleanalytics4.PNG" alt="">
</figure>

4. Admin(관리)의 Property 열의 **Tracking Info > Tracking Code**를 선택한다.

<figure>
	<img src="/images/googleanalytics5.PNG" alt="">
</figure>

5. **Tracking code**를 복사해 둔다. 

<figure>
	<img src="/images/googleanalytics6.PNG" alt="">
</figure>

## 블로그 설정

Web사이트의 모든 html 파일에 Tracking code를 붙여 넣으면 google analytics를 사용할 수 있다. 그렇게 모두 붙여 넣는 것은 매우 어려운 일다. 다행히 내가 사용하는 Jekyll theme인 [hpstr theme](https://mmistakes.github.io/hpstr-jekyll-theme/)은 google analytics 적용을 위해  **_includes/scripts.html** 파일을 만들어 두었다. 이 파일에 Tracking Code를 붙여 넣자. 

## 사용

이제, [google analytics](http://www.google.com/intl/ko_ALL/analytics/index.html)에서 블로그의 방문자 활동에 대해 자세히 살펴 볼 수 있다. 이를 통해 블로그를 더욱 발전시켜보자. 