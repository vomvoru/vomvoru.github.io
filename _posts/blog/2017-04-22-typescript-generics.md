---
layout: post
title: TypeScript Generics
subtitle: 다양한 유형을 지원하기
slug: typescript-Generics
date: '2017-04-22 17:11:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - Functions
---

# 일반적인 선언법
```ts
function identity<T>(arg: T): T {
    return arg;
}
```

## 사용법
일반적으로 위 `identity` 함수는 아래와 같이 사용이 가능하다.
```ts
let output = identity<string>("myString");  // type of output will be 'string'
```

## type argument inference
다만, 매개변수를 통한 타입추론(type argument inference)으로 다음과 같이 사용이 가능하다.
```ts
let output = identity("myString");  // type of output will be 'string'
```

# 배열과 함께 사용하기
기본적인 사용법은 다음과 같다.
```ts
function loggingIdentity<T>(arg: T[]): T[] {
    console.log(arg.length);
    return arg;
}
```

또는 다른 배열 표현법인 Array인터페이스를 사용할수 있다.
```ts
function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length);
    return arg;
}
```

# Generics type
Generics type을 가진 함수변수를 설정하는 방법은 다음과 같다.
```ts
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
```

Generics type의 수와 사용법이 일치하는 한 이름은 신경쓰지 않는다.
```ts
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;
```

Generics type을 인터페이스와 함께 사용할수 있으며, 함수객체로도 설정 가능하다.
```ts
interface GenericIdentityFn {
    <T>(arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: GenericIdentityFn = identity;
```

위 인터페이스를 익명형태로(객체리터럴 형식) 사용할 수 있다.
```ts
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: {<T>(arg: T): T} = identity;
```

또한 미리 Genric을 지정하는 방법으로 인터페이스를 사용할 수 있습니다.
```ts
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

let numberIdentity: GenericIdentityFn<number> = identity;
let stringIdentity: GenericIdentityFn<string> = identity;

numberIdentity(123);
stringIdentity('test');

numberIdentity('test'); // error! 'test' is not number.
numberIdentity<string>('test'); // error! can not use genric -> '<string>'.
```

미리 Genric을 지정하는 방법으로 다음과 같이 사용하고 싶을수 있지만 함수에 사용된 Genric은 호출과 함께 사용되어야 되기 때문에 오류가 걸립니다. (이후 Typescript가 이러한 방법도 지원되면 좋겠습니다.)
```ts
function identity<T>(arg: T): T {
    return arg;
}

let numberIdentity = identity<number>; // error!
identity<number>(123); // pass
identity(123); // pass
```

# Generic Classes

일반적인 사용법은 다음과 같습니다.
```ts
class GenericNumber<T> {
  constructor(public zeroValue: T){ }
  add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>(0);
myGenericNumber.add = function(x, y) { return x + y; };

```
위 `GenericNumber`클래스는 `number` 타입뿐만 아니라 다양한 타입을 사용할수 있습니다.
`number`타입이 아닌 `string`타입을 지원하는 예제는 다음과 같습니다.
```ts
let stringNumeric = new GenericNumber<string>('');
stringNumeric.add = function(x, y) { return x + y; };

alert(stringNumeric.add(stringNumeric.zeroValue, "test"));
```

## static 속성

genric과 클래스를 같이 사용시 객체를 생성할때 genric을 설정하게 됩니다.
클래스에느 객체를 생성하지 않아도 사용할수 있는 `static` 속성이 있기때문에 `static` 속성은 genric값을 사용할수 없습니다.
```ts
class GenericNumber<T> {
  constructor(public zeroValue: T){ }
  static staticValue: T; // error!!
  add: (x: T, y: T) => T;
}
```

# Generic Constraints
generic은 여러가지 방법의 제약방
## extends
특정 인터페이스 혹은 타입을 제약조건으로 설정할수 있습니다.
```ts
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}

loggingIdentity(3);  // Error, number doesn't have a .length property
loggingIdentity({length: 10, value: 3}); // OK!
loggingIdentity('test'); // OK!
loggingIdentity([0, 1, 3]); // OK!
```

## extends keyof
다른 매개변수의 key에 의해 제한되는 매개변수를 genric으로 설정할수 있습니다.
```ts
function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a"); // okay
getProperty(x, "m"); // error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
```

## class 타입 체크
genric을 이용하여 클래스 타입을 체크할때 사용할수 있습니다.
```ts
function create<T>(c: {new(): T; }): T {
    return new c();
}
```
다음은 생성자 함수의 타입과 `prototype` 속성을 이용하여 `instance` 속성의 타입을 체크하고 제한하는 예제입니다.
```ts
interface Keeper<A, K> {
  new(): A;
  prototype: {keeper: K};
}

class BeeKeeper {
    hasMask: boolean;
}

class ZooKeeper {
    nametag: string;
    keeper: BeeKeeper;
}

class Animal {
    numLegs: number;
}

class Bee extends Animal {
    keeper: BeeKeeper;
}

class Lion extends Animal {
    keeper: ZooKeeper;
}

function findKeeper<A extends Animal, K> (a: Keeper<A, K>): K {
    return a.prototype.keeper;
}

findKeeper(Lion).nametag;  // OK!
findKeeper(Bee).hasMask;  // OK!
findKeeper(Bee).nametag;  // error!
findKeeper(ZooKeeper).hasMask;  // error!
findKeeper(ZooKeeper);  // error!
```