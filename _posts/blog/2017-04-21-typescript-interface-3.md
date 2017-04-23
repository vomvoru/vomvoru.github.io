---
layout: post
title: TypeScript Interface 3
subtitle: 클래스 타입
slug: typescript-interface-3
date: '2017-04-21 02:27:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - Interface
---

C# 및 Java와 같은 언어로 인터페이스를 사용하는 가장 일반적인 방법으로는, 클래스가 특정 인터페이스를 따르도록 하는 것 입니다. Typescript에서도 가능합니다.

```ts
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

# 클래스 확장 인터페이스 (interface Extending Classes)
인터페이스가 클래스를 확장(extends)시 클래스 유형을 상속하지만, 구현은 상속되지 않습니다.

```ts
class Control {
    private state: any = 1;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control implements SelectableControl { // pass
    select() { }
}

class TextBox  extends Control implements SelectableControl { // pass
    select() { }
}

class ImageURL implements SelectableControl{ // error!! Property 'state' is missing
    select() { }
}

class LocationStore implements SelectableControl{ // error!! Property 'state' is missing
    select() { }
}
```

# class의 static과 instance의 차이
아래 내용을 정확히 이해하기 위해서는 javascript의 prototype에 대한 사전 지식이 필요합니다.
클래스에는 static 유형과 instance 유형이라는 두 가지 유형이 있음을 명심하세요. 아래 코드는 오류가 발생합니다.
```ts
interface ClockConstructor {
    new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```
이는 인터페이스가 클래스를 검사시 instance만 검사하기 때문입니다. 생성자는 static 측면에 있으므로 검사에 포함되지 않습니다.

대신 클래스의 static 측면에서 직접 작업해야합니다.  
아래 예제에서는 instance 측면과 static 측면의 interface를 각각 따로 만들어서 (ClockConstructor, ClockInterface) 서로 분간하며 사용하고 있습니다.

```ts
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

위 코드를 javascript 코드로 변환시 다음과 같아집니다.

```js
function createClock(ctor, hour, minute) {
    return new ctor(hour, minute);
}

var DigitalClock = (function () {
    function DigitalClock(h, m) {
    }
    DigitalClock.prototype.tick = function () {
        console.log("beep beep");
    };
    return DigitalClock;
}());

var AnalogClock = (function () {
    function AnalogClock(h, m) {
    }
    AnalogClock.prototype.tick = function () {
        console.log("tick tock");
    };
    return AnalogClock;
}());

var digital = createClock(DigitalClock, 12, 17);
var analog = createClock(AnalogClock, 7, 32);
```

즉, 위 코드에서 ClockInterface는 'new 클래스명()' 으로 생긴 객체(instance)에 대한 검사를 진행하며 ClockConstructor는 클래스(static) 즉, 함수에 대한 검사를 진행하게 됩니다.

static 변수와 public 변수를 interface를 이용해서 어떻게 검사하는지에 대한 예시 코드와 변환 코드가 다음과 같습니다.

```ts
interface ClockConstructor {
    new (name: string, hour: number, minute: number): ClockInterface;
    getNum(): number;
}
interface ClockInterface {
    tick();
    name: string;
}

function createClock(ctor: ClockConstructor, name: string, hour: number, minute: number): ClockInterface {
    return new ctor(name, hour, minute);
}

class DigitalClock implements ClockInterface {
    public name;

    constructor(name: string, h: number, m: number) {
      this.name = name;
      DigitalClock.num++;
    }

    private static num = 0;
    static getNum(){
       return DigitalClock.num;
    }

    tick() {
        console.log(this.name + ": beep beep");
    }
}
class AnalogClock implements ClockInterface {
    public name;

    constructor(name: string, h: number, m: number) {
      this.name = name;
      AnalogClock.num++;
    }

    private static num = 0;
    static getNum(){
       return AnalogClock.num;
    }

    tick() {
        console.log(this.name + ": tick tock");
    }
}

let digital = createClock(DigitalClock, 'dig', 12, 17);
let analog = createClock(AnalogClock, 'ana', 7, 32);
```

```js
function createClock(ctor, name, hour, minute) {
    return new ctor(name, hour, minute);
}

var DigitalClock = (function () {
    function DigitalClock(name, h, m) {
        this.name = name;
        DigitalClock.num++;
    }
    DigitalClock.getNum = function () {
        return DigitalClock.num;
    };
    DigitalClock.prototype.tick = function () {
        console.log(this.name + ": beep beep");
    };
    return DigitalClock;
}());
DigitalClock.num = 0;

var AnalogClock = (function () {
    function AnalogClock(name, h, m) {
        this.name = name;
        AnalogClock.num++;
    }
    AnalogClock.getNum = function () {
        return AnalogClock.num;
    };
    AnalogClock.prototype.tick = function () {
        console.log(this.name + ": tick tock");
    };
    return AnalogClock;
}());
AnalogClock.num = 0;

var digital = createClock(DigitalClock, 'dig', 12, 17);
var analog = createClock(AnalogClock, 'ana', 7, 32);
```

# prototype 속성
prototype 속성을 이용하여 클래스를 받는 함수의 매개변수에서 `static` 타입뿐만이 아니라 `instance` 타입 체크도 가능합니다.
```ts
interface Keeper {
    new ();
    prototype: {
        keeper: any
    }
}

class BeeKeeper {
    hasMask: boolean;
}

class ZooKeeper {
    nametag: string;
}

class Animal {
    numLegs: number;
}

class Bee extends Animal {
    keeper: BeeKeeper;
}

class Lion extends Animal {
    keeper: ZooKeeper;
}

function findKeeper (a: Keeper) {
    return a.prototype.keeper;
}

findKeeper(Lion).nametag;  // typechecks!
```
