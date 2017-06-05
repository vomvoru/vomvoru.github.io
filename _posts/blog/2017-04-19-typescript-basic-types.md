---
layout: post
title: TypeScript Basic Types
excerpt:
slug: typescript-basic-types
date: '2017-04-19 20:50:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

# 종류

* `boolean`
* `number`
* `string`
* `array`
* `tuple`
* `enum`
* `any`
* `void`
* `undefined`
* `null`
* `never`

# boolean
`boolean`타입을 지정
```ts
let isDone: boolean = false;
```

# number
`number`타입을 지정
```ts
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```

# string
`string`타입을 지정
```ts
let color: string = 'blue';
```

# array
`array`타입을 지정
```ts
let list: number[] = [1, 2, 3];
let genericList: Array<number> = [1, 2, 3];
```

# tuple
`tuple`타입은 특정구조의 배열을 표현할 수 있습니다. 표현된 배열구조 이후의 값은 union 유형이 사용됩니다.
```ts
let x: [string, number]; // [string, number, string|number, string|number ... ]
x = ['hello', 10]; // OK
x = ['hello', 'world']; // Error
x = ['hello', 10, 'world']; // OK
x = ['hello', 10, 11]; // OK
x = ['hello', 10, true]; // Error

console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
console.log(x[2].toString()) // OK, 'string' and 'number' both have 'toString'
console.log(x[2].substr(1)) // Error, 'string' and 'number' don't both have 'toString'
```

# enum
`enum`타입은 숫자 값 집합에 친숙한 이름을 지정할 수 있습니다.
```ts
enum Color {Red = 1, Green, Blue}
let color: Color = Color.Green;
```

# any
`any`타입은 알수 없는 변수타입에 지정하여 Typescript 타입검사를 하지 않도록 합니다.
```ts
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false;
notSure.substr(1); // OK
notSure.anythingMethod(); // OK
```

# void
`void`타입은 일반적으로 반환값이 없는 함수의 리턴타입에 지정합니다.
```ts
function warnUser(): void {
  alert("This is my warning message");
}
let unusable: void = undefined;
unusable = null;
```


# null & undefined
`null`과 `undefined`타입은 `--strictNullChecks`플래그가 설정되지 않은 한 `never`타입을 제외한 모든 타입에 설정될 수 있습니다.

> 일반적으로 `--strictNullChecks` 플래그의 사용이 권장됩니다.

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

# never
`never`타입은 발생하지 않을 값의 타입을 나타냅니다. 항상 에러만을 throw 하거나 무한루프로 절대 반환되지 않는 함수의 리턴값에 일반적으로 사용됩니다.
```ts
function error(message: string): never {
    throw new Error(message);
}

function infiniteLoop(): never {
    while (true) {
    }
}
```

# Type assertions
Type assertions은 Typescript에게 개발자가 이 변수의 타입은 무엇이다라고 강제하는 것 입니다. 다음 두가지 방법이 있습니다.

## angle-bracket
```ts
let someValue: any = "this is a string";

let strLength: number = (<string>someValue).length;
```

## as syntax

> jsx 문법과 함께 사용시 as 문법만 허용됨

```ts
let someValue: any = "this is a string";

let strLength: number = (someValue as string).length;
```

다른 언어의 형변환과 비슷하지만, 특별한 검사나 데이터 재구성을 수행하지 않습니다. TypeScript는 프로그래머가 타입 검사를 수행했다고 가정합니다.

# type scope

확실한 이해를 위해 각 범위에 따른 집합관계를 표현하였습니다.

![typescript type scope](/images/2017/04/typescriptTypeScope.png)

## point  
* naver와 void. any 3개의 비교
* void와 null, undefined의 관계
* null, undefined가 etc...에 들어가있는 형태
