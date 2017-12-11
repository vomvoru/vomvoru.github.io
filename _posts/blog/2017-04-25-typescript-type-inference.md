---
layout: post
title: TypeScript의 타입추론
slug: typescript-type-inference
date: '2017-04-25 13:29:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

# 기본
Typescript는 지정된 타입이 없을때 자동으로 타입을 유추해주는 'type inference'기능이 있습니다.

```ts
let x = 3;
```

위 코드에서 `x`는 `number`로 타입이 유추되어 `let x:number`로 자동설정 됩니다. 이러한 추론은 변수나 속성을 초기화하고, 매개 변수 기본값을 설정하고, 함수 반환 유형을 결정할 때 (return) 발생합니다.

# Best common type
배열과 같이 여러가지 값의 타입에서 타입유추는 값마다의 타입중 모든 값에 맞는 (super type)타입을 결정합니다. 아래 한 배열의 예시코드의 Best common type 을 찾도록 하겠습니다.

```ts
let x = [0, 1, null];
```

1. 맴버별 type 판단
    * 0: `number`, 1: `number`, null: `null`
2. 맴버별 type중 모든 값에 맞는 타입 판단
    * `number` 타입과 `null` 타입중 0, 1, null 모든 값에 맞는 타입은 `number`입니다.
3. 그러므로 Best common type 은 `number`

다음은 Best common type 을 찾지 못하는 경우의 예시입니다.

```ts
class Animal{
    getCategory() {
        return 'Animal'
    }
}

class Rhino extends Animal{
    getName() {
        return 'Animal'
    }
    run() {

    }
}

class Elephant extends Animal{
    getName() {
        return 'Animal'
    }
    jump() {

    }
}

class Snake extends Animal{
    getName() {
        return 'Animal'
    }
    slide() {

    }
}

let zoo = [new Rhino(), new Elephant(), new Snake()];
```

이상적으로 우리는 zoo변수가 Animal[]로 추정되기를 원할수도 있지만 strictly Animal타입인 값이 없으므로 Animal[]로 추정되지 않습니다. 이 문제를 해결하려면 Animal[] 타입을 명시적으로 제공하세요.

```ts
let zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()];
```

Best common type을 찾지 못하면 타입은 `union` 타입(`(Rhino | Elephant | Snake)[]`)으로 설정됩니다.  
[change log(Best Common Type부분 참고)](https://github.com/Microsoft/TypeScript/issues/805)

```ts
let zoo = [new Rhino(), new Elephant(), new Snake(), new Animal()];
```

위와 같이 되어있을 경우에는 `zoo`변수는 `Animal[]`으로 타입이 지정됩니다.

# Contextual Type
타입 유추는 "the other direction"에서도 작동합니다. 즉, 좌항에 의해 우항의 타입이 유추될수 있습니다.

```ts
window.onmousedown = function(mouseEvent) {
    console.log(mouseEvent.buton);  //<- Error
};
```

위의 코드에서 Typescript type검사기는 `Window.onmousedown`함수 타입을 사용하여 우항의 함수식 타입을 추론하였습니다.
그래서 `mouseEvent`매개변수 타입을 추론할수 있었고 오류를 발생시킬수 있었습니다.
