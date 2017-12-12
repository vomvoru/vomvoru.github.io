---
layout: post
title: TypeScript Type Guard
slug: typescript-type-guard
date: '2017-12-12 21:20:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

# Type Guard
타입스크립트 컴파일러가 타입체크를 할때, 개발자가 컴파일러에게 일정 범위 안에서 타입을 보장해줄수 있는 방법들

## type assertion
```ts
let pet = getSmallPet();

if ((<Fish>pet).swim) {
    (<Fish>pet).swim();
}
else {
    (<Bird>pet).fly();
}
```

## is
```ts
function isFish(pet: Fish | Bird): pet is Fish {
    return (<Fish>pet).swim !== undefined;
}

// Both calls to 'swim' and 'fly' are now okay.

if (isFish(pet)) {
    pet.swim();
}
else {
    pet.fly();
}
```

## typeof
```ts
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4);
```

## instanceof
```ts
// Type is 'SpaceRepeatingPadder | StringPadder'
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
    padder; // type narrowed to 'SpaceRepeatingPadder'
}
if (padder instanceof StringPadder) {
    padder; // type narrowed to 'StringPadder'
}
```

## Discriminated Union

Type guard를 적용 가능한 Discriminated Union을 만들때 규칙
- 타입들간 공통적인 문자열 리터럴 속성 - 구분자(the discriminant or tag)
- 타입들을 union 으로 묶어서 설정한 type alias
- 공통 속성에 Type guard를 적용.

```ts
interface Square {
    kind: "square";
    size: number;
}
interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}
interface Circle {
    kind: "circle";
    radius: number;
}

// kind속성이 Discriminated Union이 된다.
type Shape = Square | Rectangle | Circle;

function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.radius;
        // error: Property 'radius' does not exist on type 'Square'.
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
}
```