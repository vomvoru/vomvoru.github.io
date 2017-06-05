---
layout: post
title: TypeScript Generics
excerpt: 다양한 유형을 지원하기
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

또한 다른 배열 표현법인 Array인터페이스를 사용할수 있다.
```ts
function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length);
    return arg;
}
```

# Generic & function
Generics type을 가진 함수변수를 설정하는 방법은 다음과 같다.
```ts
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
```

Generics type의 구조가 일치하는 한 이름은 신경쓰지 않는다.
```ts
function identity<T>(arg: T): T {
    return arg;
}

let myIdentity: <U>(arg: U) => U = identity;
```

# Generic & interface
Generics type을 인터페이스와 함께 사용할수 있다.
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

## 미리 Genric을 지정하기
interface를 genric과 함께 사용시 genric을 미리 지정해야 합니다. 이를 활용하면 function type의 Genric을 미리 지정할수 있습니다.

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

위와 다르게 다음과 같이 사용하고 싶을수 있지만 함수에 사용된 Genric은 호출과 함께 사용되어야 되기 때문에 오류가 걸립니다.
```ts
function identity<T>(arg: T): T {
    return arg;
}

let numberIdentity = identity<number>; // error!
identity<number>(123); // pass
identity(123); // pass
```

# Generic & Class
일반적인 사용법은 다음과 같습니다.

```ts
class GenericOp<T> {
  constructor(public zeroValue: T){ }
  add: (x: T, y: T) => T;
}

let myGenericOp = new GenericOp<number>(0);
myGenericOp.add = function(x, y) { return x + y; };

```

위 `GenericOp`클래스는 `number` 타입뿐만 아니라 다양한 타입을 사용할수 있습니다.
`number`타입이 아닌 `string`타입을 지원하는 예제는 다음과 같습니다.

```ts
let stringOp = new GenericOp<string>('');
stringOp.add = function(x, y) { return x + y; };

alert(stringOp.add(stringOp.zeroValue, "test"));
```

## static 속성

genric과 클래스를 같이 사용시 객체를 생성할때 genric 값을 설정하게 됩니다. `new GenericOp<number>()`
클래스에느 객체를 생성하지 않아도 사용할수 있는 `static` 속성이 있기때문에 `static` 속성은 genric값을 사용할수 없습니다.

```ts
class GenericOp<T> {
  constructor(public zeroValue: T){ }
  static staticValue: T; // error!!
  add: (x: T, y: T) => T;
}
```

다만 함수에서 클래스를 매개변수로 받는 방식을 이용하면, 그 함수 내에서는 `static` 속성에 제한을 둘 수 있습니다.

```ts
class GenericOp<U> {
    constructor(public zeroValue: U){ }
    static staticValue;
    add: (x: U, y: U) => U;
}

interface GenericClass<T> {
  new<U>(zeroValue: U);
  staticValue: T;
}

function create<T>(c: GenericClass<number>): T {
    c.staticValue = ' '; // error
    c.staticValue = 0; // pass
    return new c(13);
}

create<GenericOp<number>>(GenericOp)

GenericOp.staticValue = ' '; // pass
GenericOp.staticValue = 0; // pass

```


# Generic Constraints
generic은 여러가지의 제약방식이 있습니다.

## extends T
특정 인터페이스(T)의 구현을 제약조건으로 설정할수 있습니다.
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

## keyof T
특정 인터페이스(T)의 맴버 명들을 union 타입으로 설정합니다.

```ts
interface Person {
    name: string;
    age: number;
    location: string;
}

type K1 = keyof Person; // "name" | "age" | "location"
type K2 = keyof Person[];  // "length" | "push" | "pop" | "concat" | ...
type K3 = keyof { [x: string]: Person };  // string
```

## K extends keyof T
keyof에 의해 생성된 T의 맴버명만을 허용하는 K를 설정할수 있습니다.
```ts
function getProperty<T, K extends keyof T>(obj: T, key: K) {
    return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a"); // okay
getProperty(x, "m"); // error: Argument of type 'm' isn't assignable to 'a' | 'b' | 'c' | 'd'.
```

## P in keyof T
keyof에 의해 생성된 T의 모든 맴버명을 P로 설정할수 있습니다.

```ts
interface Person {
    name: string;
    age: number;
    location: string;
}

type Partials<T> = {
    [P in keyof T]?: T[P];
};

type PartialPerson = Partials<Person>;
```

위 코드의 `PartialPerson`은 아래 코드의 `PartialPerson`과 같습니다.

```ts
interface PartialPerson {
    name?: string;
    age?: number;
    location?: string;
}
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
