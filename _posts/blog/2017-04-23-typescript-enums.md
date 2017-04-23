---
layout: post
title: TypeScript Enum
subtitle:
slug: typescript-Enum
date: '2017-04-23 23:05:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - Enum
---

# 일반적인 선언법
```ts
enum Direction {
    Up,
    Down,
    Left,
    Right
}
```

# constant members & computed member
Typescript의 Enum에는 constant member(상수맴버)와 computed member(계산된 맴버)가 있습니다.

## constant member 인 경우
* initializer(=)가 없고 앞의 맴버가 상수맴버인 경우 앞의 맴버에 1을 더한값이 됩니다. (규칙1)
* 첫번째 요소에 initializer(=)가 없으면 0으로 설정됩니다. (규칙2)
* 아래 형태의 경우 상수맴버 입니다. (규칙3)
    * 아래 정의된 값들에 의한 단항연산(\+, \-, \~) 혹은 이항연산(\+, \-, \*, /, %, \<\<, \>\>, \>\>\>, &, \|, ^) (규칙3-1)
        * numeric literal (규칙3-1-1)
        * 이전에 정의 된 상수맴버 (규칙3-1-2)
    * NaN 또는 Infinity (규칙3-2)

## 예제코드를 통해 이해하기

```ts
enum Direction {
    Up = 1,
    Down, // 규칙1
    Left, // 규칙1
    Right // 규칙1
}

enum FileAccess {
    None, // 규칙2
    Read    = 3, // 규칙3-1
    Write   = 1 << 2, // 규칙3-1-1
    ReadWrite  = Read | Write, // 규칙3-1-2
    TEST1 = Read | Write | None + 13 // 규칙3-1-1, 규칙3-1-2
    TEST2 = (Read == Write) ? 5: 7, // computed member
    G = "123".length // computed member
}
```
위 코드를 변환시 다음과 같다.

```ts
var Direction;
(function (Direction) {
    Direction[Direction["Up"] = 1] = "Up";
    Direction[Direction["Down"] = 2] = "Down";
    Direction[Direction["Left"] = 3] = "Left";
    Direction[Direction["Right"] = 4] = "Right";
})(Direction || (Direction = {}));
var FileAccess;
(function (FileAccess) {
    FileAccess[FileAccess["None"] = 0] = "None";
    FileAccess[FileAccess["Read"] = 3] = "Read";
    FileAccess[FileAccess["Write"] = 4] = "Write";
    FileAccess[FileAccess["ReadWrite"] = 7] = "ReadWrite";
    FileAccess[FileAccess["TEST1"] = 15] = "TEST1";
    FileAccess[FileAccess["TEST2"] = (FileAccess.Read == FileAccess.Write) ? 5 : 7] = "TEST2";
    FileAccess[FileAccess["G"] = "123".length] = "G";
})(FileAccess || (FileAccess = {}));

```

위와 같이 computed member인 `FileAccess.TEST2`, `FileAccess.G`는 계산식 그대로 변환되어 출력되며
나머지 constant member들은 모두 계산되어 출력된다.

# reverse mapping
역 맵핑이 가능하다
```ts
enum Enum {
    A
}
let a = Enum.A;
let nameOfA = Enum[Enum.A]; // "A" reverse mapping
```

# const enum
enum 값에 엑세스시 코드생성 및 추가 간접비용을 줄이려면 const enum을 사용할수 있습니다.
```ts
const enum Enum {
    A = 1,
    B = A * 2
}
let a = Enum.A;
```

위 코드는 아래와 같이 변환됩니다.
```js
var a = 1 /* A */;
```

다만 const enum의 경우 computed member를 포함할 수 없으며 reverse mapping을 지원하지 않습니다.

# Ambient enums

Ambient enums은 이미 존재하는 열거형의 모양을 Typescript에 (혹은 IDE에) 설명할때 사용됩니다.
```ts
declare enum Enum {
    A = 1,
    B,
    C = 2
}
```
Ambient enums와 non-ambient enums의 가장 중요한 차이점은 initializer(=)가 없는 맴버는 computed member로 간주한다는 것 입니다.
```ts
declare enum Foo1 {
    X, // Computed Not 0! Careful!
    Y = 2, // constant
    Z, // Computed! Not 3! Careful!
}

enum Foo2 {
    X, // constant
    Y = 2, // constant
    Z, // constant
}
```
