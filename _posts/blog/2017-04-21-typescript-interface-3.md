---
layout: post
title: TypeScript interface 3
subtitle: interface & prototype
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

# class의 static과 instance의 차이
아래 내용을 정확히 이해하기 위해서는 javascript의 `prototype`에 대한 사전 지식이 필요합니다.
클래스에는 **static 유형** 과 **instance 유형** 이라는 두 가지 유형이 있음을 명심하세요. 아래 코드는 오류가 발생합니다.
```ts
interface ClockConstructor {
    new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```
이는 **인터페이스가 implement로 클래스를 검사시 instance만 검사** 하기 때문입니다. 생성자는 static에 있으므로 검사에 포함되지 않습니다.

다음과 같이 instance 유형과 static 유형의 interface를 각각 따로 만들어서 사용할수 있습니다.

```ts
interface Clockinterface {
    currentTime: Date
}

interface ClockConstructor {
    new (hour: number, minute: number): Clockinterface;
}

class _Clock implements Clockinterface{
    currentTime: Date;
    constructor(h: number, m: number) { }
}

const Clock:ClockConstructor= _Clock;

var c1 = new Clock(1,3);
```

아래 코드는 클래스를 매개변수로 받는 경우 instance 유형과 static 유형의 interface를 각각 따로 만들어서 사용하는 또 다른 예시입니다.

```ts
interface ClockConstructor {
    new (hour: number, minute: number): Clockinterface;
}
interface Clockinterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): Clockinterface {
    return new ctor(hour, minute);
}

class DigitalClock implements Clockinterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements Clockinterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

위 코드를 javascript 코드로 변환시 다음과 같습니다.

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

Clockinterface는 instance 유형을 ClockConstructor는 static 유형을 검사한다는것을 알 수 있습니다.
instance 측면과 static 측면을 javascript의 `prototype`, `__proto__` 속성으로 이해하면 다음과 같습니다.
* instance 측면
    * `this.속성명` -> 상속받은 instance 속성 & 정의된 instance 속성, 메소드
    * `this.__proto__.속성명` === `클래스명.prototype.속성명` -> 상속받은 instance 메소드
* static 측면
    * `클래스명.속성명`-> 정의된 static 속성, 메소드
    * `클래스명.__proto__.인스턴스명` -> 상속받은 static 속성, 메소드

즉, 위 코드에서 Clockinterface는 'new 클래스명()' 으로 생긴 객체(instance)에 대한 검사를 진행하며 ClockConstructor는 클래스(static 맴버(생성자, static 속성))에 대한 검사를 진행하게 됩니다.

interface를 이용해서 static 속성과 public 속성을 어떻게 검사하는지에 대한 예시 코드와 변환 코드가 다음과 같습니다.

```ts
interface ClockConstructor {
    new (name: string, hour: number, minute: number): Clockinterface;
    getNum(): number;
}
interface Clockinterface {
    tick();
    name: string;
}

function createClock(ctor: ClockConstructor, name: string, hour: number, minute: number): Clockinterface {
    return new ctor(name, hour, minute);
}

class DigitalClock implements Clockinterface {
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
class AnalogClock implements Clockinterface {
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
**prototype 속성을 사용** 하여 클래스를 받는 함수의 매개변수에서 `static` 타입뿐만이 아니라 `instance` 타입 체크도 가능합니다.

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
