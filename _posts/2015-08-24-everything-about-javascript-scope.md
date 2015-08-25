---
layout: post
title: Javascript scope에 대해 알고자 했던 모든 것
description: Javascript scope에 대해 알고자 했던 모든 것
modified: 2015-08-25
tags: [javascript]
image:
  feature: abstract-8.png
---

이 글은 javascript 전문가인 Todd Motto의 [Everything you wanted to know about Javascript Scope](http://toddmotto.com/everything-you-wanted-to-know-about-javascript-scope/)라는 글을 Tood Motto의 동의를 받아 번역한 것이다. 사실 제대로 번역을 했다기 보다는 나의 관점에서 이해하기 용이한 수준으로 정리한 것이라 고백한다. 영문을 국문으로 번역하기 까다롭거나, 이해에 문제가 없는 부분은 건너 뛰거나 간략히 설명하였다. 그러므로, 내용에 대해 정확한 이해를 원한다면 Todd Motto의 원문을 참조하는 것을 권한다. 

---

Javascript는 **scope**이라는 낯선 개념을 가지고 있는데, 이것은 초보 Javascript 개발자 만이 아니라 경험이 많은 개발자도 이해하기 쉽지 않다. 이 글은 scope, closure, this, namespace, function scope, global scope, lexical scope, public/private scope 등과 같은 Javascript의 어려운 부분에 대한 이해를 높이는 것을 목적으로 한다. 

이 글을 통해 다음의 질문들에 대한 답을 얻기를 바란다. 

- scope이란 무엇인가?
- global/local scope이란?
- namespace란 무엇이며, scope과 어떻게 다른가?
- this 키워드는 무엇이며, scope에 어떻게 영향을 미치는가?
- function/lexical scope이란 무엇인가?
- closure란?
- public/private scope이란 무엇인가?
- 어떻게 위의 모든 개념을 이해하고, 만들고, 사용할 수 있는가?

## Scope이란 무엇인가?

Javascript에서 scope은 작성된 코드를 둘러싼 환경을 말한다. scope은 전역(global) 또는 지역적(local)으로 정의될 수 있다. 제대로된 코드를 작성하고, 더 나은 개발자가 되기 위해서는 Javacript scope에 대해 제대로 이해해야 한다. 이 글을 통해 변수나 function들을 사용할 수 있는 영역이 어디까지 인지를 이해하고, 코드를 둘러싼 scope을 변경할 수 있고, 더 빠르면서도 쉽게 유지 보수 할 수 있는 작성하게 될 것이다. 

scope의 개념은 쉽게 생각해 다음과 같은 것이다. scope A와 scope B 중 어디 안에 우리가 있는 것인지 파악하는 것이다. 

## Global scope이란?

어떤 Javascript code를 작성하기 전에는 우리는 global scope이라 불리는 곳에 있다. 만약 우리가 하나의 변수를 선언한다면 이것은 global로(전역으로) 정의된다. 

{% highlight javascript %}
//global scope
var name = 'Todd';
{% endhighlight %}

global scope은 가장 친한 친구이자 끔찍한 악몽과도 같다. 배우기 쉽고 문제도 거의 없다. 사람들은 global scope이 나쁘다고 하지만, 사실 그 이유를 설명하지는 못한다. global scope은 사실 나쁘지 않으며, 다양한 scope에서 접근하는 모듈이나 API를 만들기 위해서는 global scope을 사용해야 한다. 

{% highlight javascript %}
jQuery('.myClass');
{% endhighlight %}

global scope에서 jQuery에 접근하는 위의 코드는 *namespace*로써 이 접근을 설명할 수 있다. namespace는 때때로 scope과 혼동되어 사용 되지만, 일반적으로는 가장 높은 수준의 scope을 설명하는 것이다. 위의 경우에 *jQuery*는 global scope안에 있으며, 또한 namespace안에 있는 것이다. jQuery namespace는 global scope안에서 정의되며, 그 안의 모든 jQuery library를 위한 namespace로서 동작한다. 

## Local scope이란 무엇인가?

local scope은 global scope외의 scope을 의미한다. 일반적으로 하나의 global scope이 있고, 정의된 각각의 function은 그 자신의 local scope을 가지고 있다. 다른 function 내에 정의된 function은 바깥의 function에 연결된 local scope을 가지고 있다. 

만약 하나의 function과 그 안의 여러 변수들을 정의한다면 각각의 변수들의 scope은 그 함수로 제한된다. 다음의 예를 살펴보자.

{% highlight javascript %}
//scope A: 여기가 global scope
var myFunction = function(){
    //scope B: local scope은 여기
};
{% endhighlight %}

local scope으로 제한된 모든 item들은 global scope에 노출되지 않는다면, global scope에서 접근할 수 없다(not visible). 간단한 예는 다음과 같다. 

{% highlight javascript %}
var myFunction = function(){
    var name = 'Todd';
    console.log(name); //Todd
};
console.log(name); //Uncaught ReferenceError: name is not defined
{% endhighlight %}

변수 *name*의 scope은 local로 제한되고, 부모 scope(여기서는 global scope)에 노출되지 않기 때문에 undefined가 된다. 

## Function scope

Javascript내의 모든 scope들은 Function scope과 함께 만들어진다. (global scope은 만들어지는 것이 아니라 기본적으로 존재하는 것이다.) *for* 또는 *while*과 같은 loop이나 *if* 또는 *switch*와 같은 구문을 통해 만들어지지 않는다. **새로운 function = 새로운 scope** - 이것이 규칙이다. 이 scope 생성의 예는 다음과 같다. 

{% highlight javascript %}
//scope A
var myFunction = function(){
    //scope B
    var myOtherFunction = function(){
        //scope C
    };
};
{% endhighlight %}

새로운 scope과 새로운 변수, function, object를 생성하는 것은 쉽다. 

## Lexical scope

하나의 function내에 다른 function이 있다면, 내부의 function은 외부의 function의 scope에 접근할 수 있다. 이것을 우리는 **Lexical Scope** 또는 **Closure**라 부르며, 또는 **Static Scope**아라 하기도 한다. 간단한 예는 아래와 같다. 


{% highlight javascript %}
//scope A
var myFunction = function(){
    //scope B
    var name = 'Todd'; // scope B에서 정의
    var myOtherFunction = function(){
        //scope C: 'name' 변수에 접근할 수 있다.
    };
};
{% endhighlight %}

myOtherfunction이 myFunction내에서 **정의된** 것일 뿐 항상 myFunction내에서만 호출되지 않을 수 있다는 것을 기억하자. 

{% highlight javascript %}
var myFunction = function () {
  var name = 'Todd';
  var myOtherFunction = function () {
    console.log('My name is ' + name);
  };
  console.log(name);
  myOtherFunction(); // myOtherFunction 호출
};
myFunction(); // myFunction 호출
{% endhighlight %}

위의 코드를 실행하면 아래와 같은 결과를 얻을 수 있다. 

{% highlight bash %}
Todd
My name is Todd 
{% endhighlight %}

Lexical scope은 부모 scope안에 정의된 어떠한 변수, object, function과도 쉽게 연동된다. 그것들은 scope chain안에서 접근 가능하다. 예를 들어 다음과 같다. 

{% highlight javascript %}
var name = 'Todd';
var scope1 = function () {
  // name 은 여기서 접근 가능하다. 
  var scope2 = function () {
    //  name 은 여기서도 접근 가능하다. 
    var scope3 = function () {
      //  name 은 여기서도 역시 접근 가능하다. 
    };
  };
};
{% endhighlight %}

Lexical scope은 반대방향으로는 동작하지 않는다는 것은 기억해야 한다. 다음의 경우가 그렇다. 

{% highlight javascript %}
// name = undefined
var scope1 = function () {
  // name = undefined
  var scope2 = function () {
    // name = undefined
    var scope3 = function () {
      var name = 'Todd'; // local scope
    };
  };
};
{% endhighlight %}

우리는 변수 name의 참조를 return할 수는 있지만, 결코 그 변수 자체를 return할 수는 없다. 

## Scope Chain

알고있는 바와 같이, 정의된 function 각각은 자신만의 중첩된 scope을 가지고 있다. 다른 function 안에 정의된 local scope을 가진 내부 function은 바깥 function과 **연결**되어 있다. 그 연결을 우리는 scope chain이라고 부른다. 코드 안에 어디에 위치하느냐가 scope chain에서의 scope을 결정한다. function내의 변수를 'resolve'(번역을 어떻게 해야할 지 모르겠다)할 때, Javascript는 변수를 찾을 때까지 그 scope - scope chain의 가장 내부 scope - 에서 시작해서 scope chain을 따라 바깥쪽 scope 방향으로 검색을 한다. 

## Closure

Closure는 Lexical scope과 강하게 연결되어 있다. 어떻게 closure가 동작하는지 이해하기 위한 좋은 예는 *function reference*를 반환하는 경우이다. 
한 function의 scope 내에서 parent scope의 변수를 사용하는 것을 생각해보자. 그리고 그 function reference를 반환하면 parent scope의 변수는 어떻게 될까?

{% highlight javascript %}
var sayHello = function (name) {
  var text = 'Hello, ' + name;
  return function () {
    console.log(text);
  };
};
{% endhighlight %}

 *sayHello* 안에 closure 개념의 function을 정의해 외부에서 접근할 수 없는 scope을 만들었다. sayHello function을 호출하는 것만으로는 어떠한 일도 생기지 않는다. 어떤 출력도 없으며 단지 하나의 function이 반환될 뿐이다. 

{% highlight javascript %}
sayHello('Todd'); // 어떠한 일도 발생하지 않는다.
{% endhighlight %}

function을 반환하는 function을 사용하기 위해서는 할당 후 호출해야 한다는 의미이다. 

{% highlight javascript %}
var helloTodd = sayHello('Todd'); 
helloTodd(); //이것은 closure를 호출하여 'Hello, Todd'를 출력한다. 
{% endhighlight %}

사실, closure를 사용하기 위해 '할당 후 호출'이 필요하다는 것은 거짓말이다. 다음과 같이 사용할 수도 있다. 

{% highlight javascript %}
sayHello2('Bob')(); // 반환된 함수를 할당없이 바로 호출
{% endhighlight %}

### AngularJS에서의 Closure 사용예

AngularJS는 위와 같은 테크닉을 *$compile* 메소드를 위해 사용하고 있다. 이것은 현재 scope reference를 closure의 인수로 사용한다. 

{% highlight javascript %}
$compile(template)(scope);
{% endhighlight %}

우리는 위의 코드가 실제 다음과 같이 구현되어 있을 것이라 추측할 수 있다. 

{% highlight javascript %}
var $compile = function (template) {
   ...
  return function (scope) {
    //'template'과 'scope' 에 접근해서 무언가 한다. 
  };
};
{% endhighlight %}

function을 반환하는 function을 closure라고 부르지 않는다. 그보다는 간단히 말해, closure는 **바깥의 scope의 변수를 사용하는 immediate lexical scope**이라 할 수 있다.   

## Scope과 'this' 키워드

각 scope은 어떻게 그 function이 호출되느냐에 따라 달라지는 *this*의 값을 바인드 하고 있다. *this* 키워드를 다들 사용하고 있지만, 많은 사람들은 호출될 때에 따라 어떻게 그 것이 달라지는지 정확히 이해하지 못하고 있다. 

default로 *this*는 가장 바깥의 global object인 *window*를 가리킨다. function을 호출하는 방식을 달리하는 것으로 *this*의 값을 다르게 바인드 할 수 있다. 그 예는 다음과 같다. 

{% highlight javascript %}
var myFunction = function () {
  console.log(this); // this = global, [object Window]
};
myFunction();
{% endhighlight %}

{% highlight javascript %}
var myObject = {};
myObject.myMethod = function () {
  console.log(this); // this = Object { myObject }
};
{% endhighlight %}

{% highlight javascript %}
var nav = document.querySelector('.nav'); // <nav class="nav">
var toggleNav = function () {
  console.log(this); // this = <nav> element
};
nav.addEventListener('click', toggleNav, false);
{% endhighlight %}

### this 값의 변화

*this*와 관련된 문제도 있다. 예를 들어 같은 function내에서도 scope이 변화 될 수 있고, 이때 또한 *this*의 값도 변화될 수 있다. 

{% highlight javascript %}
var nav = document.querySelector('.nav'); // <nav class="nav">
var toggleNav = function () {
  console.log(this); // <nav> element
  setTimeout(function () {
    console.log(this); // [object Window]
  }, 1000);
};
nav.addEventListener('click', toggleNav, false);
{% endhighlight %}

무슨 일이 생긴 걸까? 이벤트 핸들러로부터 호출되지 않은 새로운 scope이 생성되었고, 그래서 default인 *window* object가 *this*의 값이 된 것이다. 새로운 scope에 영향받지 않는 *this* 값을 얻기 위해서는 어떻게 해야 할까? 그렇게 하기 위해서 *this*의 값을 저장할 새로운 변수 - 여기서는 *that* - 를 사용한 lexical 바인딩을 한다. 

{% highlight javascript %}
var nav = document.querySelector('.nav'); // <nav class="nav">
var toggleNav = function () {
  var that = this; //this의 값을 저장
  console.log(that); // <nav> element
  setTimeout(function () {
    console.log(that); // <nav> element
  }, 1000);
};
nav.addEventListener('click', toggleNav, false);
{% endhighlight %}

이것이 새롭게 생성된 scope에서도 원하는 *this* 값을 참조할 수 있는 방법이다. 

## .call(), .apply(), .bind()를 이용하여 scope 변경하기

때때로 Javascript의 scope을 조작할 필요가 있다. 반복문 내에서 scope이 어떻게 변경되는지에 대 설명하기 위한 간단한 샘플 코드는 아래와 같다. 

{% highlight javascript %}
var links = document.querySelectorAll('nav li');
for (var i = 0; i < links.length; i++) {
  console.log(this); // [object Window]
}
{% endhighlight %}

위의 코드에서 *this*의 값은 어떤 element도 가리키지 않으며, 우리는 어떤 것도 호출하거나 scope을 변경하기 위한 무엇도 하지 않았다. 
이제, 우리가 어떻게 scope을 바꿀 수 있는지 살펴보자. (사실, scope을 변경하는 것처럼 보이지만, 실제 우리가 하는 것은 function이 호출되는 환경을 변경하는 것이다.)

### .call()과 .apply()

*.call()*과 *apply()* 메소드는 정말 엄청나다! 그 메소드들을 통해 올바른 *this* 값을 바인드하고 있는 function의 scope안으로 들어갈 수 있다. 위의 샘플 코드를 변경하여 배열 내의 각 element을 *this* 값으로 하는 경우를 살펴 보자.  

{% highlight javascript %}
var links = document.querySelectorAll('nav li');
for (var i = 0; i < links.length; i++) {
    (function () {
      console.log(this);
    }).call(links[i]);
}
{% endhighlight %}

위의 *this*의 값이 반복되는 element를 가리키도록 function의 scope을 *links[i]*로 변경했다. 

### .call()과 .apply()의 차이

*.call()*이나 *.apply()*를 이용해 scope을 변화시킬 수 있는데 그 둘 사이에는 약간의 차이가 있다. 
*.call()(scope, arg1, arg2, arg3)*은 쉼표를 이용해 각 각의 인수를 구분하는 반면, *.apply(scope, [arg1, agr2])*는 하나의 배열을 이용해 인수를 넘겨준다. 

*.call()* 또는 *.apply()*를 하더라도 실제 function을 호출한다는 점에서는 동일하다. 

{% highlight javascript %}
myFunction(); //myFunction 호출
{% endhighlight %}

*.call()*을 사용해서 function을 호출 해보자.

{% highlight javascript %}
myFunction.call(scope); //.call()을 이용해 myFunction 호출
{% endhighlight %}

### .bind()

*.bind()*는 function을 호출하지 않는다. 대신 function을 호출하기 전에 그 값들과 단지 바인드 할 뿐이다. *.bind()*는 ECMASCript5에서 소개되었는데, 소개된 초기만큼 좋은 평가를 받지는 못하고 있다. 

function reference를 자신의 인수와 함께 다른 function의 인수로 사용할 수 있는지 살펴보자. 
물론, 우리는 function reference에 인수를 넣어 다른 function의 인수로 사용할 수 없다는 것을 알고 있다. 예를 들어 다음을 보자.

{% highlight javascript %}
// toggleNav가 addEventListener의 인수로 동작한다.  
nav.addEventListener('click', toggleNav, false);

// toggleNav(arg1, arg2)가 addEventListener의 인수로 동작하지 않고, 바로 실행된다. 
nav.addEventListener('click', toggleNav(arg1, arg2), false);
{% endhighlight %}

위의 경우를 다음과 같이 바꿀 수 있다. function을 새로 정의해 그 function을 다른 function의 인수로 사용하는 것이다. 

{% highlight javascript %}
nav.addEventListener('click', function () {
  toggleNav(arg1, arg2);
}, false);
{% endhighlight %}

하지만, toggleNav는 한 function의 내부 function으로 바뀌었기 때문에 scope이 바꾸어진 것이고 이것은 우리가 원하는 바가 아니다.     

*.bind()*는 이런 문제를 해결해 준다. function이 호출되지 않은 채 다른 function의 인수로 사용될 수 있게 한다. 

{% highlight javascript %}
nav.addEventListener('click', toggleNav.bind(scope, arg1, arg2), false);
{% endhighlight %}

toggleNav는 호출되지 않고, 또한 scope이 변화되지 않은채로(필요하다면 변화시킬 수 있다.) 그 인수들과 함께 다른 function의 인수로 사용되어 졌다. 

## Private 및 Public Scope

많은 프로그래밍 언어에는 public과 private scope이 있다. - Java와 C++에는 public/private 키워드가 존재한다. 하지만, Javascript에는 그런 것들이 없다. 대신 *Closure*를 통해 비슷한 개념을 만들어 낼 수 있다. 

Javascript 디자인 패턴을 이용하면, 예를 들어 **module 패턴**을 이용하면, 우리는 public/private scope을 만들수 있다. 

### private scope을 만드는 간단한 방법



private scope을 만드는 간단한 방법은 function들을 하나의 function으로 감싸는 것이다. 우리가 배운 바와 같이 function은 scope을 만들기 때문에 global scope이 아닌 다른 scope으로 function들을 이동시키는 것이다. 
 
{% highlight javascript %}
(function () {
  // 여기는 private scope
})();
{% endhighlight %}

그리고, function 내에서 사용하기 위해 몇 개의 function을 추가할 수 있다. 

{% highlight javascript %}
(function () {
  var myFunction = function () {
    // 여기서 필요한 작업을 한다. 
  };
})();
{% endhighlight %}

우리가 정의한 내부의 function을 외부에서 호출하면 에러가 발생한다. 즉, 우리는 private scope을 만드는 것을 성공한 것이다! 

{% highlight javascript %}
(function () {
  var myFunction = function () {
    // ...
  };
})();

myFunction(); // Uncaught ReferenceError: myFunction is not defined
{% endhighlight %}

### module 패턴

만약 그 내부 function을 public으로 만들기 원한다면? **module 패턴** (그리고 **revealing module 패턴**)이라 불리는 훌륭한 패턴을 이용하면 우리의 function의 scope을 올바르게 정할 수 있다. 

#### function 반환을 통한 module 패턴 구현

관련된 모든 코드를 포함하고 있는 'Module'이라 불리는 global namespace를 살펴보자. 

{% highlight javascript %}
// module 정의
var Module = (function () {
  return {
    myMethod: function () {
      console.log('myMethod has been called.');
    }
  };
})();

// module + 메소드의 호출
Module.myMethod();
{% endhighlight %}

위에서 *return* 구문은 *public* 메소드를 반환한다. 그 메소드는 global scope(namespace)에서 접근 가능하다. 위의 Module은 다음과 같이 확장 가능하다. 

{% highlight javascript %}
// module 정의
var Module = (function () {
  return {
    myMethod: function () {

    },
    someOtherMethod: function () {

    }
  };
})();

// module + 메소드의 호출
Module.myMethod();
Module.someOtherMethod();
{% endhighlight %}

private 메소드는 어떨까? 많은 개발자들이 global scope안에 모든 function들을 마구 정의하기 때문에 global namespace가 더럽혀진다. 굳이 외부에 공개될 필요가 없는 function들은 global scope에 있을 필요가 없고, 단지 API만이 global scope에 있으면 된다. 다음 코드는 return되지 않는 function들을 이용해 private scope을 만들어 낼수 있다는 것을 보여준다.  

{% highlight javascript %}
var Module = (function () {
  var privateMethod = function () {

  };
  return {
    publicMethod: function () {

    }
  };
})();
{% endhighlight %}

*publicMethod*는 외부에서 호출되어질 수 있는 반면 *privateMethod*는 그렇지 않다. 즉, *privateMethod*는 private scope에 있는 것이다. 이런 private scope안에 헬퍼, addClass, removeClass, Ajax/XHR 호출, 배열, 객체 등이 위치할 수 있다. 

다음의 예는 *public* 메소드가 반환된 이후에도 global scope에서는 접근할 수 없는 *private* 메소드에 접근할 수 있다는 것을 보여준다.  

{% highlight javascript %}
var Module = (function () {
  var privateMethod = function () {

  };
  return {
    publicMethod: function () {
      // prvateMethod()를 호출할 수 있다. 
    }
  };
})();
{% endhighlight %}

이것은 코드 보안성과 더불어 아주 강력한 수준의 상호 연동성을 제공한다. Javascript의 가장 중요한 부분 중 하나는 보안을 유지하는 것이다. 그것이 바로 우리가 모든 function들을 global scope에 정의해서 아무 곳에서나 접근하도록 하지 않는 이유이다. 

#### Object 반환을 통한 module 패턴 구현

public/private 메소드를 사용하기 위해 Object를 반환하는 예이다. 

{% highlight javascript %}
var Module = (function () {
  var myModule = {};
  var privateMethod = function () {

  };
  myModule.publicMethod = function () {

  };
  myModule.anotherPublicMethod = function () {

  };
  return myModule; // public 메소드들과 함께 Object 반환
})();

// 사용
Module.publicMethod();
{% endhighlight %}

네임 컨벤션 중 하나는 밑줄과 함께 *private* 메소드를 시작하는 것이다. 이것은 시각적으로 public과 private을 구분할 수 있게 도와준다. 

{% highlight javascript %}
var Module = (function () {
  var _privateMethod = function () {

  };
  var publicMethod = function () {

  };
})();
{% endhighlight %}

#### Object 스타일로 정의된 Object 반환을 통한 module 패턴 구현

익명의 *Object*를 반환할 때 간단히 function reference를 지정하는 *Object* 스타일로도 module 패턴을 만들수 있다. 

{% highlight javascript %}
var Module = (function () {
  var _privateMethod = function () {

  };
  var publicMethod = function () {

  };
  return {
    publicMethod: publicMethod,
    anotherPublicMethod: anotherPublicMethod
  }
})();
{% endhighlight %}

Happy scoping!





















































































