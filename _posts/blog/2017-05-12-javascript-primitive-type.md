---
layout: post
title: Javascript의 원시 타입
subtitle: typeof vs instanceof
slug: javascript-primitive-type
date: '2017-05-12 17:27:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - javascript
---

javascript의 `typeof` 와 `instanceof` 연산자를 비교해 보며 결과적으로 primitive type 을 이해보자.

# typeof
`typeof`연산자는 단항 연산자로 type을 문자열로 돌려준다.

```js
typeof valueName
```

위와 같이 실행하면 리턴하는 type의 문자열형태는 다음과 같다.

* 'undefined'
* 'boolean'
* 'number'
* 'string'
* 'object'
* 'function'

여러 값에 대한 typeof를 사용해본 결과는 다음과 같다.

```js
typeof 11 // 'number'
typeof NaN // 'number'
typeof 'str' // 'string'
typeof {} // 'object'
typeof [] // 'object'
typeof function () {} // 'function'
typeof null // 'object'
typeof undefined // 'undefined'
typeof new (function abc(){}) // 'object'
```

# instanceof
`instanceof`는 비교 연산자로 클래스 혹은 부모클래스의 객체인지 판별할때 사용된다.

```js
let Person = function(){ this.name = "jun"; }
let jun = new Person()

jun instanceof Person // true
jun instanceof Object // true
```

javascript의 코드로 설명하면 객체가 생성자 함수의 `prototype`속성에 의해 상속중인 객체(`__proto__`)의 생성자(`constructor`)인지 재귀적으로 비교하는 연산자이다.
위 설명을 코드로 표현하면 다음과 같다.

```js
let Person = function(){ this.name = "jun"; }
let jun = new Person()

jun instanceof Person // true
jun.__proto__.constructor === Person // true
jun instanceof Object // true
jun.__proto__.__proto__.constructor === Person // true
```

위 설명을 함수(`instanceofFn`)로 표현하면 다음과 같다. (이 함수는 조금 틀린부분이 있으며 글을 진행하며 수정 될 예정이다.)

```js
function instanceofFn(obj, cons){
    return _instanceofFn(obj, cons)

    function _instanceofFn(obj, cons){
        return (existProtoConstrutorMember(obj) && equalConstructor(obj, cons)) || _instanceofFn(obj.__proto__, cons)
    }
}

function existProtoConstrutorMember(obj){
    return !!(obj && obj.__proto__ && obj.__proto__.constructor)
}

function equalConstructor(obj, cons){
    return obj.__proto__.constructor === cons
}
```

# Primitive wrapper objects in JavaScript
javascript에서 문자열을 표현 하는 방법은 대표적으로 두가지가 있다.

```js
let str1 = new String('str1')
let str2 = 'str2'
```

위 표현방법의 차이는 `typeof` 연산자와 `instanceof` 연산자에서 드러난다.

```js
typeof str1 // 'object'
typeof str2 // 'string'

str1 instanceof String // true
str2 instanceof String // false

str1 instanceof Object // true
str2 instanceof Object // false
```

`str1` 변수는 String 함수에 의해 생성된 객체이므로 `instanceof`의 연산자의 작동에서는 문제될것이 없다.  
다만, `'str2'` 값은 리터럴로 생성된 primitive value 즉, 원시값으로 기본데이터이며 불변값이다. 다만 `str2` 변수와 `str1` 변수를 사용할때는 (String method를 사용하는 등) 차이가 없는데 그 이유는 primitive value를 사용할때는 Primitive wrapper objects로 감싸져서 사용되기 때문이다.  
다만, primitive type의 문자열형태를 출력하는 `typeof` 연산자 혹은 객체에 대한 클래스 혹은 부모클래스를 판별하는 `instanceof` 연산자의 경우에는 사용 결과에 차이가 나게 된다.

Primitive wrapper objects의 종류는 다음과 같다.

* `String` : string primitive wrapper object.
* `Number` : number primitive wrapper object.
* `Boolean` : Boolean primitive wrapper object.
* `Symbol` : Symbol primitive wrapper object.

wrapper object 에서 감싼 primitive value로 접근하는 방법은 `valueOf()` 메소드를 사용하면 된다.

## primitive type
참고로 primitive type의 종류는 다음과 같습니다.

* string
* number
* boolean
* null
* undefined
* symbol

## instanceofFn 의 수정
instanceofFn에서는 Primitive wrapper object를 고려하지 않았기 때문에 다음과 같은 오류가 있다.

```js
let str2 = 'str2'

str2 instanceof String // false
str2 instanceof Object // false

instanceofFn(str2, String) // true
instanceofFn(str2, Object) // true
```

이는 `valueOf()`를 이용하여 primitive value 인지 판단하는 코드(`isPrimitiveValue`)를 추가하여 해결할수 있다.

```js
function instanceofFn(obj, cons){
    if(isPrimitiveValue(obj)){
        return false;
    }
    return _instanceofFn(obj, cons)

    function _instanceofFn(obj, cons){
        return (existProtoConstrutorMember(obj) && equalConstructor(obj, cons)) || _instanceofFn(obj.__proto__, cons)
    }
}

function isPrimitiveValue(obj){
    if(obj.valueOf() === obj){
        let typeStr = typeof obj
        return typeStr === 'string' ||
            typeStr === 'number' ||
            typeStr === 'boolean'||
            typeStr === 'symbol'  
    }
    return false;
}

function existProtoConstrutorMember(obj){
    return !!(obj && obj.__proto__ && obj.__proto__.constructor)
}

function equalConstructor(obj, cons){
    return obj.__proto__.constructor === cons
}
```
