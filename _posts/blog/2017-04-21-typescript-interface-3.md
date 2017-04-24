---
layout: post
title: TypeScript interface 3
subtitle: interface & class
slug: typescript-interface-3
date: '2017-04-21 02:27:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - interface
---

C# 및 Java와 같은 언어로 인터페이스를 사용하는 가장 일반적인 방법으로는, 클래스가 특정 인터페이스를 따르도록 하는 것 입니다. Typescript에서도 `implements` 키워드로 지원합니다.

```ts
interface Clockinterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements Clockinterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

# 클래스의 인터페이스 확장 (interface Extending Classes)
인터페이스가 클래스를 인터페이스처럼 확장(`extends`)시 클래스 유형을 상속하지만, 구현은 상속되지 않습니다.
또한 `private` 또는 `protected` 맴버가 있는 클래스를 인터페이스에 확장(`extends`)시 그 인터페이스를 구현(`implements`)하는 클래스는 그 인터페이스에 확장(`extends`)되었던 클래스를 반드시 확장(`extends`)받아야 합니다.

## public 유형만을 가지고 있는 클래스의 인터페이스 확장
```ts
class Control {
    public state: number = 1;
}

interface SelectableControl extends Control {
    select(): void;
}

class Class1 implements SelectableControl { // error!! Property 'state' is missing
    select() { }
}

class Class2 implements SelectableControl { // pass
    public state: number = 2;
    select() { }
}

class Class3 extends Control implements SelectableControl { // pass
    select() { }
}

```

## private 유형을 가지고 있는 클래스의 인터페이스 확장
```ts
class Control {
    private state: number = 1;
}

interface SelectableControl extends Control {
    select(): void;
}

class Class1 implements SelectableControl { // error!! Property 'state' is missing
    select() { }
}

class Class2 implements SelectableControl { // error!! Private property 'state' is missing
    private state: number = 2;
    select() { }
}

class Class3 extends Control implements SelectableControl { // pass
    select() { }
}

```
