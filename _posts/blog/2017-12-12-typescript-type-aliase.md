---
layout: post
title: TypeScript Type Aliase
slug: typescript-type-aliase
date: '2017-12-12 21:21:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

# & , |
## Intersection Type(&)

```ts
function extend<T, U>(first: T, second: U): T & U {
    let result = <T & U>{};
    for (let id in first) {
        (<any>result)[id] = (<any>first)[id];
    }
    for (let id in second) {
        if (!result.hasOwnProperty(id)) {
            (<any>result)[id] = (<any>second)[id];
        }
    }
    return result;
}
```

## Union Type(|)
```ts
var union: string|number
union = '1'
union = 1
```

# Type Aliase

```ts
// 일반적인 사용
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
    if (typeof n === "string") {
        return n;
    }
    else {
        return n();
    }
}

// generic
type Container<T> = { value: T };

// self reference
type Tree<T> = {
    value: T;
    left: Tree<T>;
    right: Tree<T>;
};

// 속성에서 자기 자신을 참조하는 경우가 아니면 error
type Yikes = Array<Yikes>; // error
```

## Interface vs Type Aliase

|                           | Interface | Type Aliase |
|---------------------------|-----------|-------------|
| extends, implements       | O         | X           |
| &, \|, Discriminated Union | X         | O           |

# String Literal Types

```ts
type Easing = "ease-in" | "ease-out" | "ease-in-out";
class UIElement {
    animate(dx: number, dy: number, easing: Easing) {
        if (easing === "ease-in") {
            // ...
        }
        else if (easing === "ease-out") {
        }
        else if (easing === "ease-in-out") {
        }
        else {
            // error! should not pass null or undefined.
        }
    }
}

let button = new UIElement();
button.animate(0, 0, "ease-in");
button.animate(0, 0, "uneasy"); // error: "uneasy" is not allowed here
```

# this type & method chaining
```ts
class BasicCalculator {
    public constructor(protected value: number = 0) { }
    public currentValue(): number {
        return this.value;
    }
    public add(operand: number): this {
        this.value += operand;
        return this;
    }
    public multiply(operand: number): this {
        this.value *= operand;
        return this;
    }
    // ... other operations go here ...
}

let v = new BasicCalculator(2)
            .multiply(5)
            .add(1)
            .currentValue();
```

# index types
## index type query operator
`keyof T`
```ts
interface Person {
    name: string;
    age: number;
}

// index type query operator
let personProps: keyof Person; // 'name' | 'age'
```

## indexed access operator
`T[K]`
```ts
function getProperty<T, K extends keyof T>(o: T, name: K): T[K] {
    return o[name]; // o[name] is of type T[K]
}

let name: string = getProperty(person, 'name');
let age: number = getProperty(person, 'age');
let unknown = getProperty(person, 'unknown'); // error, 'unknown' is not in 'name' | 'age'
```

## Mapped types
`Keys`가 Union타입 이라고 한다면 `K in Keys`에 대해 및 그 활용법

### 개념
```ts
type Keys = 'option1' | 'option2';
type Flags = { [K in Keys]: boolean };
```
위의 `Flags`타입은 아래의 `Flags`타입과 동일하다.
```ts
type Flags = {
    option1: boolean;
    option2: boolean;
}
```

### 활용
```ts
interface Person {
    name: string;
    age: number;
}

type Readonly<T> = {
    readonly [P in keyof T]: T[P];
}
type Partial<T> = {
    [P in keyof T]?: T[P];
}

type PersonPartial = Partial<Person>;
type ReadonlyPerson = Readonly<Person>;
```

위의 `PersonPartial`, `ReadonlyPerson`은 아래의 `PersonPartial`, `ReadonlyPerson`와 같다.
```ts
interface PersonPartial {
    name?: string;
    age?: number;
}

interface PersonReadonly {
    readonly name: string;
    readonly age: number;
}
```

### 표준라이브러리에 이미 있는것
`Readonly<T>`, `Partial<T>`, `Pick<T, K>`, `Record<K, T>`

```ts
// T객체의 모든 키(P)가 읽기전용인 type
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
}

// T객체의 모든 키(P)를 옵션인 type
type Partial<T> = {
    [P in keyof T]?: T[P];
}

// T객체의 K 키들을 가진 type
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
}

// T타입인 K 키들을 가진 type
type Record<K extends string, T> = {
    [P in K]: T;
}
```
