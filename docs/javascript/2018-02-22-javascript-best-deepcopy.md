---
layout: post
title: Javascript - deep copy가 필요할 때 무얼 사용해야 할까?
modified: 2018-02-22
tags: [javascript]
parent: Javascript
nav_order: 2
---

Javascript가 지원하는 다양한 Object copy 방식 중 deepcopy를 지원하는 것은 무엇인지 확인해보자. 

# Deepcopy를 지원할 것 같은 javascript Object copy 방법들

## Object.assign()

[MDN]((https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/assign))에서는 다음과 같이 설명한다. 마치 deep copy가 가능한 듯한 설명이다. 

- The Object.assign() method is used to copy the values of all enumerable own properties from one or more source objects to a target object. It will return the target object.

간단히 아래와 같이 copy하면서 특정 프로퍼티 값을 업데이트 할 수 있다.  
 
 ```javascript
 const object1 = {
  a: 1,
  b: 2,
};

const object2 = Object.assign({}, object1, {a: 100});

console.log(object2.a); //100
console.log(object2.b); //2
 ```

## Spread syntax(...)

object나 array를 [...](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)를 이용해 프로퍼티들을 뽑아내고 object literal({})이나 array literal([])로 다시 object나 array를 구성하는 방식이다. 복사를 하면서 특정 프로퍼티의 값만 바꿀때 유용하다. 

 ```javascript
const object1 = {
    a: 1,
    b: 2,
};
  
const object2 = {...object1, a: 100}
console.log(object2.a); //100
console.log(object2.b); //2

const array1 = [1, 2, 3, 4];
const array2 = [...array1];
console.log(array2);//[1, 2, 3, 4]
 ```

## lodash의 deepClone()

위의 두가지 방식과는 달리 lodash 라이브러리를 이용하는 방식으로 lodash는 shallow copy를 지원하는 [clone()](https://lodash.com/docs/4.17.5#clone)과 deep copy를 지원하는 [cloneDeep()](https://lodash.com/docs/4.17.5#cloneDeep)이 있는데 여기서는 cloneDeep()만 살펴본다. 

 ```javascript
import * as _ from 'lodash';
 
const object1 = {
  a: 1,
  b: 2
};

const object2 = _.cloneDeep(object1);
object2.a = 100;

console.log(object2.a); //100
console.log(object2.b); //2
 ```

# 진짜 deep copy를 지원하는지 테스트

## 테스트 방식

위의 3가지 방식이 deep copy를 지원하는지 테스트를 해보자. 방식은 다음과 같다. 

다음과 같이 2단계 깊이의 object를 copy를 하고, 복사본의 프로퍼티의 값을 변경했을 때 원본의 값도 변경되었는지 보는 것이다. reference가 복사되어 원본의 값도 변한 경우 deep copy가 되지 않은 것이다.

 ```javascript
const original = { a : {b : 2}};
 ```
 
## Object.assign()

아래와 같이 deep copy가 되지 못하여 original의 값도 변경되었다. 

 ```javascript 
const original = { a : {b : 2}};
let copy = Object.assign({}, original);
copy.a.b = 100; 
console.log(original.a.b);  //expected: 2 but actual: 100
 ```

## spread (...)

역시 아래와 같이 deep copy가 되지 못하여 original의 값도 변경되었다. 

 ```javascript 
const original = { a : {b : 2}};
let copy = {...original};
copy.a.b = 100; 
console.log(original.a.b);  //expected: 2 but actual: 100
 ```

## _.cloneDeep

deep copy가 되어 original의 값은 변경되지 않았다. 

 ```javascript 
const original = { a : {b : 2}};
let copy = _.cloneDeep(original);
copy.a.b = 100; 
console.log(original.a.b);  //2
 ```

# 결론

deep copy가 필요한 경우 Object.assign()이나 spread(...) 를 사용하지 말고, lodash의 cloneDeep()을 사용하자.
