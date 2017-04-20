---
layout: post
title: TypeScript Interface 2
subtitle: 함수 타입
slug: typescript-interface-2
date: '2017-04-21 02:27:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - Interface
---

# 함수 타입
Typescript의 Interface는 Object의 모양을 정의한다고 하였습니다. Javascript에서 함수또한 Object이므로 함수도 Interface로 모양을 정의할 수 있습니다.

함수의 인수구조, return 구조를 설정하는 예시입니다.
```ts
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string): boolean {
    let result = source.search(subString);
    return result > -1;
}
```

그리고 인수의 순서에 따른 타입만을 체크하기 때문에 함수 인수의 이름은 일치할 필요는 없습니다.

```ts
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string): boolean {
    let result = src.search(sub);
    return result > -1;
}
```

인수의 타입은 당연히 일치해야 합니다.

```ts
let otherFunction: SearchFunc;
otherFunction = function(src: string, sub: number) { //error!
    return true;
}
```

인수의 개수가 더 많아도 안됩니다.

```ts
let otherFunction: SearchFunc;
otherFunction = function(src: string, sub: string, i:number) { //error!
    return true;
}
```

하지만 인수의 개수가 더 적은것은 괜찬습니다.

```ts
let otherFunction: SearchFunc;
otherFunction = function(src: string) { //error!
    return true;
}
```

## 하이브리드 타입
Javascript에서 함수는 객체이며 속성또한 가질수 있습니다. TypeScript는 이러한 유형을 interface로 지원합니다.

아래는 하이브리드 타입을 이용하는 예시입니다.
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
