---
layout: post
title: Geolocation API와 Google map API 
description: Geolocation API와 Google map API 예제들
modified: 2015-07-30
tags: [webapp]
image:
  feature: abstract-10.png
link: https://developers.google.com/maps/documentation/javascript/examples/
---

## 위치와 지도 API의 사용  

위치 기반의 webapp을 개발하기 위해서는 현재 위치를 알 수 있는 geolocation API, 지도를 그리고 정보를 표시하기 위한 google map API, 위도 및 경도와 주소간 변환을 담당하는 geocode API 등이 필요하다. 
사용법이 어렵지 않지만, 내용이 많으므로 필요한 내용을 이해하기 위해서는 간단한 샘플들을 접해보는 것이 좋다.  

### Geolocation API

Geolocation API는 별도의 library없이 즉시 사용할 수 있는 HTML5 API이다. *navigator.geolocation.getCurrentPosition* 함수를 이용해 location의 획득을 시도하는데 성공하면 success_callback이 position 객체와 함께 호출되고 실패하면 error_callback이 호출된다. 

{% highlight javascript %}
navigator.geolocation.getCurrentPosition(success_callback, error_callback);
{% endhighlight %}

- [자세한 한글 설명이 있는 사이트](https://developer.mozilla.org/ko/docs/WebAPI/Using_geolocation) 
- [쉬운 샘플 코드](http://www.w3schools.com/html/tryit.asp?filename=tryhtml5_geolocation_error) 

### Google Map Javascript API

Google Map API를 사용하기 위해서는 API_KEY를 획득해야 한다. 대부분 w3schools를 참조하면 된다. 

- [API_KEY 획득 및 사용방법](https://developers.google.com/maps/documentation/javascript/tutorial)
- [가장 기본 샘플- geolocation으로 얻은 위치 지도 표시](https://developers.google.com/maps/documentation/javascript/examples/map-geolocation)
- [자세한 설명과 샘플](http://www.w3schools.com/googleapi/) 

### Google Geocode API

주소를 좌표를 변환하는 것을 geocodig이라 한다. 그 반대 변환을 reverse geocoding이라 부른다. 

- [Geocoding- 주소에서 좌표알아내는 샘플](https://developers.google.com/maps/documentation/javascript/examples/geocoding-simple)
- [Reverse Geocoding- 좌표에서 주소 알아내는 샘플](https://developers.google.com/maps/documentation/javascript/examples/geocoding-reverse)

## 기타 

Google map 위에 정보를 표시하거나 선을 그리기 위해서는 polygon, circle, shape, marker API 등을 알아야 한다. 역시 w3schools와 google developer 사이트가 제일 좋다. 
