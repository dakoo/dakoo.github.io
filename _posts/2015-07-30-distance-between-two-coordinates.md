---
layout: post
title: 두 좌표 사이의 거리 계산하는 방법
description: 두 좌표 사이의 거리 계산하는 방법
modified: 2015-07-30
tags: [webapp]
image:
  feature: abstract-18.png
link: http://chongmoa.com/3018
---

## 출처 

[C.m.a 사이트의 프로그래밍 게시판](http://chongmoa.com/3018)의 코드는 javascript로만 이루어진 간단한 거리 계산 방법을 소개하고 있다. 
이 코드를 활용하여 수원과 인천의 임의의 좌표간 거리를 계산을 해보았다. 그 결과는 네이버 맵이 제공하는 거리와 오차범위가 매우 적게 일치함을 확인하였다.  
[이 코드의 원 출처](http://www.movable-type.co.uk/scripts/latlong.html)는 복잡한 설명이 있었는데 C.m.a 사이트의 내용이 훨씬 직관적이므로 굳이 원 출처 사이트를 가서 확인할 필요는 없는 듯하다. 
상업적인 용도로 사용한다면 **Create Common License**가 어떤 것인지 확인후에 사용해야 할 것이다. 

### 코드 

### 거리 계산 함수 

코드는 아래와 같다. lat1 lon1, lat2, lon2는 두 position.coords객체의 latitude와 longitude를 의미한다.  

{% highlight javascript %}
  function calculateDistance(lat1, lon1, lat2, lon2) {
      var R = 6371; // km
      var dLat = (lat2-lat1).toRad();
      var dLon = (lon2-lon1).toRad(); 
      var a = Math.sin(dLat/2) * Math.sin(dLat/2) +
              Math.cos(lat1.toRad()) * Math.cos(lat2.toRad()) * 
              Math.sin(dLon/2) * Math.sin(dLon/2); 
      var c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1-a)); 
      var d = R * c;
      return d;
    }
    Number.prototype.toRad = function() {
      return this * Math.PI / 180;
    }
{% endjavascript %}


