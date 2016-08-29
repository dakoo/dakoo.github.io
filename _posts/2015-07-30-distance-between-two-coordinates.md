---
layout: post
title: Web - 두 좌표 사이의 거리 계산하는 방법
description: 두 좌표 사이의 거리 계산하는 방법
modified: 2015-07-30
tags: [web, javascript, geolocation]
comments: true
image:
  feature: abstract-18.png
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
{% endhighlight %}

### 사용 예 

html 파일 내에서의 사용 예는 다음과 같다. 

{% highlight html %}
<!DOCTYPE html> 
<html> 
<head> 
  <title>Geolocation Demo</title>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8"> 
</head>
<body> 
 
  <h1>Geolocation Demo</h1>
  <div id="tripmeter"> 
    <p> 
      시작 위치 (위도, 경도):<br/> 
      <span id="startLat"></span>°, <span id="startLon"></span>°
    </p> 
    <p> 
      현재 위치  (위도, 경도):<br/> 
      <span id="currentLat"></span>°, <span id="currentLon"></span>°
    </p> 
    <p> 
      시작 위치로 부터의 거리:<br/> 
      <span id="distance">0</span> km
    </p> 
  </div> 
 
  <script> 
    window.onload = function() {
      var startPos;
       
      if (navigator.geolocation) { 
        navigator.geolocation.getCurrentPosition(function(position) {
          startPos = position;
          document.getElementById("startLat").innerHTML = startPos.coords.latitude;
          document.getElementById("startLon").innerHTML = startPos.coords.longitude;
        }, function(error) {
          alert("Error occurred. Error code: "+error.code);
        });
    
        navigator.geolocation.watchPosition(function(position) {
          document.getElementById("currentLat").innerHTML = position.coords.latitude;
          document.getElementById("currentLon").innerHTML = position.coords.longitude;
          document.getElementById("distance").innerHTML =
            calculateDistance(startPos.coords.latitude, startPos.coords.longitude,
                              position.coords.latitude, position.coords.longitude);
        });
      }
    };
    // Reused code - copyright Moveable Type Scripts - retrieved May 4, 2010.
    // http://www.movable-type.co.uk/scripts/latlong.html
    // Under Creative Commons License http://creativecommons.org/licenses/by/3.0/
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
  </script> 
 
</body>
</html>
{% endhighlight %}


