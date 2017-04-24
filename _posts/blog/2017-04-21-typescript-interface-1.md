---
layout: post
title: TypeScript interface 1
subtitle: Duck Typing
slug: typescript-interface-1
date: '2017-04-21 02:27:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - interface
---

# 인터페이스

Typescript 의 interface는 javasript Object의 '모양'이 맞는지 체크합니다.
이를 'Duck Typing' 또는 'Structural subtyping'이라고 합니다.

다음은 익명 인터페이스의 한 예입니다.

```ts
interface LabelledValue {
    label: string;
}

function printLabel(labelledObj: LabelledValue) {
    console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

myObj가 string타입 값을 가진label 속성을 가지고 있는지 검사합니다. size 속성은 신경쓰지 않는 관대한 검사를 합니다.

익명 인터페이스로도 구현이 가능합니다.

```ts
function printLabel(labelledObj: { label: string }) {
    console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

# 선택적 속성

인터페이스의 속성명 끝에 ?를 붙이면 Optional 속성으로도 설정이 가능합니다.

```ts
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
    let newSquare = {color: "white", area: 100};
    if (config.color) {
        newSquare.color = config.color;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

let mySquare = createSquare({color: "black"});
```

선택적 손성의 장점은 인터페이스의 속성이 아닌 속성을 사용하는 것을 방지하며, typescript의 전반적인 장점인 ide에서 속성에 대한 정보를 미리 확인이 가능하다는 것입니다.

다음은 color 속성을 clor 로 잘못 적었을 경우 TypeScript에서 Error로 알려주는 코드의 예시입니다.
```ts
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    let newSquare = {color: "white", area: 100};
    if (config.color) {
        // Error: Property 'clor' does not exist on type 'SquareConfig'
        newSquare.color = config.clor;
    }
    if (config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}

let mySquare = createSquare({color: "black"});
```

# 읽기 전용 속성

읽기 전용 속성은 속성명 앞에 'readonly '를 적으면 됩니다.

```ts
interface Point {
    readonly x: number;
    readonly y: number;
}
```

다음은 읽기 전용 속성인 x 값을 수정하려고 할시 TypeScript에서 Error로 알려주는 코드의 예시입니다. (위 코드와 이어집니다)

```ts
let p1: Point = { x: 10, y: 20 };
p1.x = 5; // error!!
```

## ReadonlyArray
읽기 전용 배열을 설정하기 위한 `ReadonlyArray` 인터페이스가 미리 정의되어 있습니다.

```ts
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // error!!
ro.push(5); // error!!
ro.length = 100; // error!!
a = ro; // error!!
```

마지막 줄이 error가 난 이유는 사실 `readonly` 속성때문이 아닙니다. `number[]` 와 `ReadonlyArray<number>` 의 타입은 서로 다르기 때문에 나오는 오류입니다.
이는 type assertion 로 에러를 없앨수 있습니다.

```ts
a = ro as number[];
```

사실 genric과 interface를 이용하여 `ReadonlyArra`y를 직접 만들수도 있지만, 편의를 위해 TypeScript에서 지원하는것 같습니다. 직접 만든 예시는 아래와 같습니다.

```ts
interface CustomReadonlyArray<T>{
  readonly [index: number]: T;
}
```

# Excess Property Checks (초과 속성 검사)
인터페이스를 사용시 미리 정의된 속성만을 사용할수 있습니다.

```ts
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig) {
    // ...
}

// error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 });
```
다음 코드의 경우 color 속성이 없고 colour 속성이 있으므로 typescript의 검사에서 에러가 걸리게 됩니다.
하지만 코드상에서 colour속성또한 color의 별칭으로 사용할수도 있습니다.  
객체가 몇 가지 추가속성을 가질 수 있다고 확신하는 경우 다음과 같이 초과 속성에 대한 정의를 할 수 있습니다.

```ts
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any;
}
```

다만, 위 방식은 지양하는것이 좋으며 되도록 코드의 질적 향상을 위해 다른 방법을 찾는것이 좋습니다.  
아래 방법은 type guard, Type Aliases, interface 를 이용한 다른 방법입니다.

```ts
interface SquareBaseConfig {
    width?: number;
}

interface SquareColorConfig extends SquareBaseConfig{
    color: string;
}

interface SquareColourConfig extends SquareBaseConfig{
    colour: string;
}

type SquareConfig = SquareColorConfig | SquareColourConfig

function createSquare(config: SquareConfig) {
  console.log(config.width);
  if(isColorConfig(config)){
    console.log(config.color);
  }else if(isColourConfig(config)){
    console.log(config.colour);
  }
}

function isColorConfig(config): config is SquareColorConfig {
  return config.color !== undefined;
}

function isColourConfig(config): config is SquareColourConfig {
  return config.colour !== undefined;
}

createSquare({ colour: "red", width: 100 });
createSquare({ color: "red", width: 100 });

```

# 인터페이스 확장
인터페이스는 여러 인터페이스를 확장할수 있습니다.
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

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```
