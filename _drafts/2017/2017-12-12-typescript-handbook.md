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
- `Boolean`, `Number`, `String`
- `Array`, `Tuple`, `Enum`
- `Never` : **도달할수 없는**

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

정리필요
https://vomvoru.github.io/blog/typescript-interface-3/

