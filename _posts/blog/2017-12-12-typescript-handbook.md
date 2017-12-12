---
layout: post
title: typescript Handbook
subtitle: typescript 요약 정리
slug: typescript-handbook
date: '2017-12-12 17:44:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

# Basic Types
- `boolean`, `number`, `string`
- `Array`, `Tuple`, `enum`
- `never` : **도달할수 없는**
- `symbol`

함수내부에 문한루프 혹은 에러를 던질때 사용

```ts
function error(message: string): never {
    throw new Error(message);
}

function infiniteLoop(): never {
    while (true) {
    }
}
```

- `Any`, `Void`
- `null`, `Undefined`

`null`, `Undefined`은 strictNullChecks플래그 비활성화시 never타입을 제외한 모든 타입에 설정 가능

```ts
let u: undefined = undefined;
let n: null = null;
let str: string;
let num: number;

str = undefined; // pass
str = null; // pass
num = undefined; // pass
num = null; // pass
```

> Type assertions 정리 필요


# Interface

## Optional
```ts
interface SquareConfig {
    width: number;
    color?: string;
}
```

## readonly
```ts
interface Point {
    readonly x: number;
    readonly y: number;
}

```

## extends
```ts
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}
```

# Class
```ts
class User {
    static num: number;

    nickname: string; //== public nickname:string;
    protected name: string;
    private password: string;

    constructor(name: string, nickname: string, password: string){
        num++;
        this.name = name
        this.nickname = nickname
        this.serializedPassword = password
    }

    print(tag: string){
        console.log(tag, `i am ${this.nickname}.`)
    }

    // getter, setter
    // getter 만 설정시 readonly가 된다.
    set serializedPassword(password: string){
        this.password = Encode.md5(password)
    }

    get serializedPassword():string {
        return this.password
    }
}

// 상속 (extends, super)
class Admin extends User {
    constructor(
        public adminLevel:number, // Parameter properties
        name: string,
        nickname:string,
        password: string){
        super(name, nickname, password)
    }

    // override
    // override된 메소드의 매개변수, 리턴값 타입을 그대로 상속받음.
    print(tag){
        console.log(tag, `i am admin.`)
    }
}
```

## Access Modifier

O: 접근 가능, X: 접근 불가능

|                  | public | protected | private |
|------------------|--------|-----------|---------|
| 내부(메소드)     | O      | O         | O       |
| 상속(자식클래스) | O      | O         | X       |
| 외부(객체)       | O      | X         | X       |

## Interface vs Abstract vs Protected constructor

O: 가능, X: 불가능

|                        | interface | Abstract Class, method | protected constructor |
|------------------------|-----------|------------------------|-----------------------|
| 메소드 타입 강제       | O         | O                      | O                     |
| 메소드 접근제어자 강제 | X         | O                      | X                     |
| 내부 구현 코드 구현    | X         | O                      | 필수                  |

# function

## 타입체크
```ts
let myAdd: (x: number, y: number)=>number;

// 매개변수명에 대해서는 검사하지 않고 순서에 따른 타입을 검사
myAdd = function(a: number, b: number): number { return a + b; };

// 유형 추론(Inferring the types)을 지원
myAdd = function(a, b) { return a + b; };

// 매개변수의 개수가 적어도 됨
let myAdd = function(x: number): number { return x + x; };

// 매개변수의 개수가 많으면 안됨
let myAdd = function(x: number, y: number, z:number): number { return x + x; }; //error
```
## 파라미터 종류

```ts
function createUser(
    id: string, // 필수
    nickname?: string, // 옵션
    level = 1, // 기본값
    ...friend: number[] // rest
){
    console.log('createUser')
}

// undefined 활용
createUser('pch', undefined, undefined, [1])
```

## function & interface
interface는 객체 타입을 설정. 함수도 객체인 것을 생각하면 다음과 같은 코드도 된다.
```ts
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

## this 타입 지정
- this에 대한 타입을 명시적으로 지정
- noImplicitThis 플래그를 활성화시 필수

```ts
interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card; // 중요부분!
}
let deck: Deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    // NOTE: 이 함수는 이제 피 호출자가 Deck 유형이어야한다고 명시 적으로 지정합니다.
    createCardPicker: function(this) { // 중요부분!
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

## Overloads(다양한 함수 타입 정의)
```ts
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}
```

# Enum

```ts
enum Direction {
    Up,
    Down = 1,
    Left,
    Right
}
```

## reverse mapping
```ts
enum Enum {
    A
}
let a = Enum.A;
let nameOfA = Enum[Enum.A]; // "A" reverse mapping
```

## const enum
코드생성 및 추가 간접비용을 줄이기
```ts
const enum Enum {
    A = 1,
    B = A * 2
}

let a = Enum.A;
let nameOfA = Enum[Enum.A]; // error
// const enum 은 reverse mapping 지원 안함
```

변환후
```js
var a = 1 /* A */;
```

`preserveConstEnums` 프래그 활성화 시
```js
var Enum;
(function (Enum) {
    Enum[Enum["A"] = 0] = "A";
})(Enum || (Enum = {}));

var a = Enum.A;
```

# 추천 글

- [type문](https://vomvoru.github.io/blog/typescript-type-aliase/)
- [type guard](https://vomvoru.github.io/blog/typescript-type-guard/)
- [generic](https://vomvoru.github.io/blog/typescript-Generics/)
- [컴파일 옵션](https://vomvoru.github.io/blog/tsconfig-compiler-options-kr/)
- [모듈 해석 방식](https://vomvoru.github.io/blog/typescript-module-resolution/)

# 그 외

- [TypeScript의 타입추론](https://vomvoru.github.io/blog/typescript-type-inference/)
- [class의 static과 instance의 차이](https://vomvoru.github.io/blog/typescript-interface-3/)
- [readonly vs const](https://vomvoru.github.io/blog/typescript-readonly-vs-const/)