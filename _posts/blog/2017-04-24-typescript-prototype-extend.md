---
layout: post
title: TypeScript의 prototype 상속
subtitle: __extends 함수 분석
slug: typescript-prototype-extend
date: '2017-04-24 14:41:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - Prototype
  - extends
---

아래코드를 분석하며 Typescript가 Class를 어떻게 구현하는지 분석해보자
```ts
class Parent {
  constructor(public pub, protected pro, private prv){

  }
  static className:string = 'Parent';
  static getName():string{
    return Parent.className
  }
  protected printName():void{
    console.log(Parent.className)
  }
  public print():void{
    console.log('Parent print')
    console.log(this.pub)
    console.log(this.pro)
    console.log(this.prv)
  }
}

class Child extends Parent{
  constructor(public cpub, protected cpro, private cprv){
    super(cpub, cpro, cprv)
  }
  static className:string = 'Child' + Parent.className;
  static getName():string{
    return Parent.getName() + Child.className
  }
  protected printName():void{
    super.printName();
    console.log(Child.className + Parent.getName())
  }
  public print():void{
    console.log('Child print')
    console.log(this.pub)
    console.log(this.pro)
    console.log(this.cpub)
    console.log(this.cpro)
    console.log(this.cprv)
  }
}
```

위 코드의 변환

```js
var __extends = (this && this.__extends) || (function () {
    var extendStatics = Object.setPrototypeOf ||
        ({ __proto__: [] } instanceof Array && function (d, b) { d.__proto__ = b; }) ||
        function (d, b) { for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p]; };
    return function (d, b) {
        extendStatics(d, b);
        function __() { this.constructor = d; }
        d.prototype = b === null ? Object.create(b) : (__.prototype = b.prototype, new __());
    };
})();
var Parent = (function () {
    function Parent(pub, pro, prv) {
        this.pub = pub;
        this.pro = pro;
        this.prv = prv;
    }
    Parent.getName = function () {
        return Parent.className;
    };
    Parent.prototype.printName = function () {
        console.log(Parent.className);
    };
    Parent.prototype.print = function () {
        console.log('Parent print');
        console.log(this.pub);
        console.log(this.pro);
        console.log(this.prv);
    };
    return Parent;
}());
Parent.className = 'Parent';
var Child = (function (_super) {
    __extends(Child, _super);
    function Child(cpub, cpro, cprv) {
        var _this = _super.call(this, cpub, cpro, cprv) || this;
        _this.cpub = cpub;
        _this.cpro = cpro;
        _this.cprv = cprv;
        return _this;
    }
    Child.getName = function () {
        return Parent.getName() + Child.className;
    };
    Child.prototype.printName = function () {
        _super.prototype.printName.call(this);
        console.log(Child.className + Parent.getName());
    };
    Child.prototype.print = function () {
        console.log('Child print');
        console.log(this.pub);
        console.log(this.pro);
        console.log(this.cpub);
        console.log(this.cpro);
        console.log(this.cprv);
    };
    return Child;
}(Parent));
Child.className = 'Child' + Parent.className;
```

# Parent Class
```js
var Parent = (function () {
    function Parent(pub, pro, prv) {
        this.pub = pub;
        this.pro = pro;
        this.prv = prv;
    }
    Parent.getName = function () {
        return Parent.className;
    };
    Parent.prototype.printName = function () {
        console.log(Parent.className);
    };
    Parent.prototype.print = function () {
        console.log('Parent print');
        console.log(this.pub);
        console.log(this.pro);
        console.log(this.prv);
    };
    return Parent;
}());
Parent.className = 'Parent';
```
* javascript에서 클래스 구현에 가장 보편적인 방법인 함수를 이용하여 클래스를 구현
* `protoype` 속성을 이용해서 instance 메소드를 정의
* `this` 속성을 이용해서 instance 속성을 정의
* `Parent`함수에 속성을 추가해서 static 메소드를 정의
    * 익명함수 내에 있으므로 this를 이용해서 static 속성에 접근이 가능하다. 즉, `Parent.className`를 `this.className`로도 작성해도 작동상 문제가 없지만, 코드의 모호한점을 없애기 위해 클래스명을 사용해서 static 속성에 접근해야 한다고 생각한다.
* `Parent`함수에 속성을 추가해서 static 속성을 정의

# Child Class
```js
var Child = (function (_super) {
    __extends(Child, _super);
    function Child(cpub, cpro, cprv) {
        var _this = _super.call(this, cpub, cpro, cprv) || this;
        _this.cpub = cpub;
        _this.cpro = cpro;
        _this.cprv = cprv;
        return _this;
    }
    Child.getName = function () {
        return Parent.getName() + Child.className;
    };
    Child.prototype.printName = function () {
        _super.prototype.printName.call(this);
        console.log(Child.className + Parent.getName());
    };
    Child.prototype.print = function () {
        console.log('Child print');
        console.log(this.pub);
        console.log(this.pro);
        console.log(this.cpub);
        console.log(this.cpro);
        console.log(this.cprv);
    };
    return Child;
}(Parent));
Child.className = 'Child' + Parent.className;
```
* 익명함수에 extend될 클래스(생성자 함수)를 `_super` 매개변수로 전달
* `__extends(Child, _super);` 호출
    * static 속성과 instance 속성을 extends
    * constructor값 또한 올바른 값(Child)로 설정
* 생성자 함수를 호출시에는 `_super.call(this, ...)`형태
* 부모 instance 메소드를 호출시에는 `_super.prototype.메소드명.call(this, ...)`형태
* 부모 instance 속성을 호출시에는 `this.속성명`형태
* 부모 static 메소드를 호출시에는 `Parent.메소드명(...)`형태
* 부모 static 속성을 호출시에는 `Parent.속성명`형태

# `__extends` 함수

```ts
var __extends = (this && this.__extends) || (function () {
    var extendStatics = Object.setPrototypeOf ||
        ({ __proto__: [] } instanceof Array && function (d, b) { d.__proto__ = b; }) ||
        function (d, b) { for (var p in b) if (b.hasOwnProperty(p)) d[p] = b[p]; };
    return function (d, b) {
        extendStatics(d, b);
        function __() { this.constructor = d; }
        d.prototype = b === null ? Object.create(b) : (__.prototype = b.prototype, new __());
    };
})();
```
* `extendStatics` 함수로 static 속성, 메소드 extend
    * `Object.setPrototypeOf` 함수가 존재시 그대로 사용
    * `__proto__` 속성을 지원한다면 `Child.__proto__ = Parent`로 `Parent`의 static속성들을 `Child`에서 사용가능하도록 한다.
        * `({ __proto__: [] }` instanceof Array [Detect '__proto__' support][76176558]
    * 모든 방법이 통하지 않으면, 루프를 돌면서 `Parent`의 static속성들을 `Child`에 직접 설정한다.
* `prototype`속성을 이용한 instance 메소드 extend
    * `Parent`가 `null`이면 Object.create로 `__proto__`가 비어있는(없는) 객체를 `Child.prototype`에 설정
    * `Parent`가 `null`이 아니면 `__.prototype` 를 `Parent.prototype`로 설정후 `new __()`를 `Child.prototype`에 설정한다.
        * `this.constructor = Child`를 통해 `constructor`속성을 올바르게(`Child`) 설정
* 자식 생성자 함수 내에서 `_super.call(this, ...)` 부모 생성자 함수 호출로 instance 속성을 extend

## static 속성, 메소드의 extends를 한줄로 표현

* `Child.__proto__ === Parent`

## instance 메소드의 extends를 두줄로 표현

* `Child.prototype.__proto__ === Parent.prototype`  
* `(new Child()).__proto__.__proto__ === Parent.prototype`

## instance 속성의 extends를 한줄로 표현

* `_super.call(this, ...) // 부모생성자 호출!`

## 관계도
UML이 아니라서 죄송합니다 ㅎ

![typescript-prototype-extend-relationship-diagram](/images/2017/04/IMG_0132.jpg)



[76176558]: https://coderwall.com/p/jial4q/detect-__proto__-support-__proto__-instanceof-array "Detect '__proto__' support"
