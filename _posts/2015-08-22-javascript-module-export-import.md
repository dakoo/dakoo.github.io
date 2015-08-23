---
layout: post
title: Javascript에서의 모듈 export/import 
description: Javascript에서의 모듈 export/import 
modified: 2015-08-22
tags: [javascript]
image:
  feature: abstract-7.png
---

Javascript 모듈 export와 import는 쉬워 보이면서도 꽤나 까다롭다. 대부분의 예제는 function을 export/import하는 것에 촛점을 맞추고 있어 Object의 경우, 특히 Constructor를 export/import하는 것에 대한 예제가 부족하다.
이에 대한 설명이 [StackOverflow](http://stackoverflow.com/questions/20534702/node-js-use-of-module-exports-as-a-constructor)에 있어 핵심만 따서 설명한다.
 
### Module export/import

#### export

exports와 module.export는 사실 동일한 의미이라는 것을 우선 명심하자. 하나의 math 모듈에 두 개의 export하고자 하는 function plus, multiply가 있을때 다음과 같이 export할 수 있다. 

{% highlight javascript %}
//math.js
exports.plus = function(a, b) {
  return a + b;
};
   
exports.multiply = function(a, b) {
  return a * b;
};
{% endhighlight %}

위의 것은 다음과 같이 구현해도 동일하다. 
//math.js
{% highlight javascript %}
module.exports = {
	plus : function(a, b) {
	  return a + b;
	}, 
	multiply : function(a, b) {
	  return a * b;
	}	
};
{% endhighlight %}


다음과 같이 구현해도 된다. 

{% highlight javascript %}
//math.js
 var plus = function(a, b) {
  return a + b;
};
var multiply = function(a, b) {
  return a * b;
};
exports.plus = plus;
exports.multiply = multiply;
{% endhighlight %}

또는 

{% highlight javascript %}
//math.js
 var plus = function(a, b) {
  return a + b;
};
var multiply = function(a, b) {
  return a * b;
};
module.exports = {
	plus : plus, 
	multiply : multiply
};
{% endhighlight %}


#### import

다음과 같이 import해서 위의 function을 사용할 수 있다. 

{% highlight javascript %}
var math = require("./math.js");
console.log(math.plus(1, 2)); //3
console.log(math.multiply(1, 2)); //2
{% endhighlight %}

### Constructor의 export와 import

설명을 위해 위의 math 모듈에서 항상 덧셈과 곱셈시 같이 계산해야 하는 초기 값이 있다고 가정해보자. 
예를 들어 초기값이 100이면 plus(1, 2)이면 100+1+2를 반환해야하고, multiply(1, 2)이면 100*1*2을 반환해야 한다고 하자. 

#### export

constructor의 export는 다음과 같이 prototype을 이용해 함수를 정의해야 한다는 것을 기억해야 한다. 

{% highlight javascript %}
//math.js
function Math(val) {
  this.init_val = val;
}
Math.prototype.plus = function plus(a, b){
	return this.init_val + a + b;
}
Math.prototype.multiply = function multiply(a, b){
	return this.init_val * a * b;
}
module.exports = Math;
{% endhighlight %}

또는 다음 방법도 가능하지만, 위의 방법이 더 좋다. 

{% highlight javascript %}
//math.js
function Math(val) {
    this.init_val = val;
}
var plus = function plus(a, b){
    return this.init_val + a + b;
}
var multiply = function multiply(a, b){
    return this.init_val * a * b;
}
Math.prototype.plus = plus;
Math.prototype.multiply = multiply;
module.exports = Math;
{% endhighlight %}

#### import

{% highlight javascript %}
var Math = require("./math.js");
var math = new Math(100);
console.log(math.plus(1, 2)); //103
console.log(math.multiply(1, 2)); //200
{% endhighlight %}
