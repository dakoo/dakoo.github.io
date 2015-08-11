---
layout: post
title: Geolocation API와 Google map API 
description: Geolocation API와 Google map API 예제들
modified: 2015-08-01
tags: [webapp, javascript]
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

### Place Search API

주소를 검색하여 위치 정보를 알 수 있는 Place Search API는 [Google 사이트](https://developers.google.com/maps/documentation/javascript/examples/places-autocomplete)가 가장 잘 설명하고 있다. 

html에서 *map-canvas*는 map을 그리기 위한 것이고 *pac-input*은 Search box를 그리기 위한 것이다. 
{% highlight html %}
    ...
    <script src="https://maps.googleapis.com/maps/api/js?v=3.exp&signed_in=false&libraries=places"></script>
    ...
    ...
    <input id="pac-input" class="controls" type="text" placeholder="Enter a location">
    <div id="map-canvas"></div>
    ...
{% endhighlight %}

input form을 map과 연결하고 autocomplete event listener에서 search 결과를 받으면 map을 갱신하는 예는 아래와 같다. 

{% highlight javascript %}
/* Default values */
var current_position = {
latitude: 51.508742,
longitude:-0.120850
};
var current_boundary = {
x1:-0.12445,
y1: 51.506,
x2:-0.11725,
y2: 51.5114
};
var current_zoom_level = 16;

function initialize() {
var geocoder = new google.maps.Geocoder();
var input = (document.getElementById('pac-input'));

if(!navigator.geolocation){
drawMap();
};
function success(new_position){
current_position.latitude = new_position.coords.latitude;
current_position.longitude = new_position.coords.longitude;
updateBoundary(
new_position.coords.longitude - 0.0036,
new_position.coords.latitude - 0.003,
new_position.coords.longitude + 0.0036,
new_position.coords.latitude + 0.003);
drawMap();
};
function error(){
drawMap();
};
navigator.geolocation.getCurrentPosition(success, error);
function drawMap() {
/*center_position*/
var center = new google.maps.LatLng(current_position.latitude, current_position.longitude);

/* map */
var mapOptions = {
center: center,
zoom: current_zoom_level,
disableDefaultUI: true,
mapTypeId: google.maps.MapTypeId.ROADMAP
};
var map = new google.maps.Map(document.getElementById('map-canvas'), mapOptions);

var user_marker = new google.maps.Marker({
position: center,
map:map,
draggable: true,
icon: 'me.png',
animation: google.maps.Animation.DROP
});

/*rectangle boundary*/
var rectangle = new google.maps.Rectangle({
map: map,
strokeColor: "#FF565A",
strokeOpacity: 0.3,
strokeWeight: 1,
fillColor: "#FFB89B",
fillOpacity: 0.2,
editable: true,
//draggable: true,
bounds: new google.maps.LatLngBounds(
new google.maps.LatLng(current_boundary.y1, current_boundary.x1),
new google.maps.LatLng(current_boundary.y2, current_boundary.x2))
});

/*infowindow for geocode*/
geocoder.geocode({'location': center}, function (results, status) {
if (status == google.maps.GeocoderStatus.OK) {
if (results[1]) {
var infowindow = new google.maps.InfoWindow({
content: results[1].formatted_address
});
infowindow.open(map, user_marker);
setTimeout(function () { infowindow.close();}, '1000');
}
}
});

/* place searchbox */
var autocomplete = new google.maps.places.Autocomplete(input);
map.controls[google.maps.ControlPosition.TOP_LEFT].push(input);
autocomplete.bindTo('bounds', map);
autocomplete.setTypes([]);

/*marker event listener */
google.maps.event.addListener(user_marker, 'dragend', function () {
updateUserPosition(user_marker.getPosition().lat(), user_marker.getPosition().lng());
drawMap();
});

/*boundary event listener*/
google.maps.event.addListener(rectangle, 'bounds_changed', function () {
updateBoundary(rectangle.getBounds().getSouthWest().lng(),rectangle.getBounds().getSouthWest().lat(),
rectangle.getBounds().getNorthEast().lng(), rectangle.getBounds().getNorthEast().lat());
});

/* searchbox event listener*/
google.maps.event.addListener(autocomplete, 'place_changed', function () {
var place = autocomplete.getPlace();
if (!place.geometry) {
window.alert("We cann't find the place");
return;
}
if (place.geometry.location){
updateUserPosition(place.geometry.location.lat(), place.geometry.location.lng());
drawMap();
} else if(place.geometry.viewport){
updateUserPosition(place.geometry.viewport.getBounds().getCenter().lat(), place.geometry.viewport.getBounds().getCenter().lng());
drawMap();
} else {
window.alert("We cann't find the place");
return;
}
});
/* zoom event listener */
google.maps.event.addListener(map, 'zoom_changed', function() {
updateZoomLevel(map.getZoom());
});
}; //end of drawmap

function updateUserPosition(latitude, longitude){
var dx1 = current_position.longitude - current_boundary.x1;
var dy1 = current_position.latitude - current_boundary.y1;
var dx2 = current_position.longitude - current_boundary.x2;
var dy2 = current_position.latitude - current_boundary.y2;
/* update position */
current_position.latitude = latitude;
current_position.longitude = longitude;
/* update boundary */
updateBoundary(current_position.longitude - dx1,current_position.latitude - dy1,
current_position.longitude - dx2, current_position.latitude - dy2);
//TODO update user information of server
}
function updateBoundary(x1, y1,x2, y2){
current_boundary.x1 = x1;
current_boundary.y1 = y1;
current_boundary.x2 = x2;
current_boundary.y2 = y2;
//TODO update user information of server
}
function updateZoomLevel(zoom_level){
current_zoom_level = zoom_level;
}
}; //end of initialize

google.maps.event.addDomListener(window, 'load', initialize);

{% endhighlight %}


## 기타 

Google map 위에 정보를 표시하거나 선을 그리기 위해서는 polygon, circle, shape, marker API 등을 알아야 한다. 역시 w3schools와 google developer 사이트가 제일 좋다. 
