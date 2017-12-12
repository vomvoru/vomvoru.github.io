---
layout: post
title: TypeScript Advanced Types
slug: typescript-advanced-types
date: '2017-05-03 16:02:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

# Intersection Type `&`
Intersection Type은 여러 타입을 하나로 결합할때 사용합니다.

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

class Person {
    constructor(public name: string) { }
}
interface Loggable {
    log(): void;
}
class ConsoleLogger implements Loggable {
    log() {
        // ...
    }
}
var jim = extend(new Person("Jim"), new ConsoleLogger());
var n = jim.name;
jim.log();
```

# Union Type `|`
Union Type은 여러 타입을 지원할때 사용됩니다.

```ts
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
```

Union Type은 모든 타입에 공통적인 맴버들만 접근 할 수 있습니다.

```ts
interface Bird {
    fly();
    layEggs();
}

interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors
```

# Type Guards and Differentiating Types

보통 Javascript에서 사용 가능한 맴버를 구별하여 사용하는 코드는 아래와 같습니다. 하지만 Typescript에서는 아래코드에서 오류를 발생합니다.

```ts
let pet = getSmallPet();

// Each of these property accesses will cause an error
if (pet.swim) {
    pet.swim();
}
else if (pet.fly) {
    pet.fly();
}
```

동일한 코드가 작동되도록 하려면 type assertion 을 사용해야 합니다.

```ts
let pet = getSmallPet();

if ((<Fish>pet).swim) {
    (<Fish>pet).swim();
}
else {
    (<Bird>pet).fly();
}
```

# User-Defined Type Guards
위의 경우에 type assertion을 여러번 사용해야되는 불편함이 있습니다. 더 좋은 방법으로 Typescript에는 type guard라는 것이 있습니다.
type guard는 특정 범위에서 type을 보장하는 몇가지 표현식입니다. 아래와 같이 type guard를 설정하는 방법은 여러개가 있습니다.

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

`typeof` 키워드 또한 Typescript는 type guard 표현식중 하나로 사용이 가능합니다.

```ts
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
```

## instanceof

`instanceof` 키워드 또한 Typescript는 type guard 표현식중 하나로 사용이 가능합니다.

```ts
interface Padder {
    getPaddingString(): string
}

class SpaceRepeatingPadder implements Padder {
    constructor(private numSpaces: number) { }
    getPaddingString() {
        return Array(this.numSpaces + 1).join(" ");
    }
}

class StringPadder implements Padder {
    constructor(private value: string) { }
    getPaddingString() {
        return this.value;
    }
}

function getRandomPadder() {
    return Math.random() < 0.5 ?
        new SpaceRepeatingPadder(4) :
        new StringPadder("  ");
}

// Type is 'SpaceRepeatingPadder | StringPadder'
let padder: Padder = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
    padder; // type narrowed to 'SpaceRepeatingPadder'
}
if (padder instanceof StringPadder) {
    padder; // type narrowed to 'StringPadder'
}
```

### typeof vs instanceof

typeof는 primitive type을 구분할때 사용하고 instanceof는 클래스를 구분할때 사용하면 됩니다.

## ||

> 아래 코드는 `--strictNullChecks` 플래그 추가를 전재로 진행됩니다.

다음과 같은 방법으로 `null` 값을 허용하지 않는 범위를 만들수 있습니다.

```ts
function f(sn: string | null): string {
    if (sn == null) {
        return "default";
    }
    else {
        return sn;
    }
}
```

또는 `||` 를 사용할 수 있습니다.

```ts
function f(sn: string | null): string {
    return sn || "default";
}
```

컴파일러가 `null` 또는 `undefined`를 제거할수 없는 경우 type assertion 연산자를 이용하여 수동으로 제거할 수 있습니다.

```ts
function broken(name: string | null): string {
  function postfix(epithet: string) {
    return name.charAt(0) + '.  the ' + epithet; // error, 'name' is possibly null
  }
  name = name || "Bob";
  return postfix("great");
}

function fixed(name: string | null): string {
  function postfix(epithet: string) {
    return (<string>name).charAt(0) + '.  the ' + epithet; // ok
  }
  name = name || "Bob";
  return postfix("great");
}
```

`null` 또는 `undefined`를 제거하는 간단한 type assertion 이 있는데 그 문법은 postfix`!` 으로 다음과 같이 사용됩니다.

```ts
function fixed(name: string | null): string {
  function postfix(epithet: string) {
    return name!.charAt(0) + '.  the ' + epithet; // ok
  }
  name = name || "Bob";
  return postfix("great");
}
```

# Type Aliases

Type Aliases은 타입의 새이름을 작성합니다. 다음과 같이 지정할 수 있습니다.

```ts
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
```

Type Aliases는 interface와 마찬가지로 generic을 지정할 수 있습니다.

```ts
type Container<T> = { value: T };
```

또한 자기 자신의 Type Aliases를 속성에서 참조 할 수 있습니다.

```ts
type Tree<T> = {
    value: T;
    left: Tree<T>;
    right: Tree<T>;
};
```

교차 타입(`&`)과 함께 다음과 같이 사용이 가능합니다.

```ts
type LinkedList<T> = T & { next: LinkedList<T> };

interface Person {
    name: string;
}

var people: LinkedList<Person>;
var s = people.name;
var s = people.next.name;
var s = people.next.next.name;
var s = people.next.next.next.name;
```

그러나 속성에 지정하는 위와 같은 예가 아닌 아래와 같은 경우는 불가능 합니다.

```ts
type Yikes = Array<Yikes>; // error
```

# Interfaces vs Type Aliases

interface는 Type Aliases 와 다르게 `extend` 또는 `mplement` 기능의 사용이 가능합니다

```ts
interface A {
  x: number;
}
interface B extends A {
  y: string;
}
```

Type Aliases는 interface와 다르게 `&` 또는 `|` 또는 별칭을 지정할 수 있습니다.

```ts
type NumOrStr = number | string;
type NeatAndCool = Neat & Cool;
type JustSomeOtherName = SomeType;
```

# String Literal Types
String Literal Type은 문자열에 있어야 하는 정확한 값을 지정할 수 있습니다.

```ts
let done: 'done';
done = 'done';
done = 'pass'; // error
```

String Literal Type은 공용체 타입(`|`) type guard, type aliases 와 같이 잘 사용됩니다. 이러한 기능을 사용하여 문자열에서 열거형(Union) 동작을 만들수 있습니다.

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

# Discriminated Unions

Type guard를 적용 가능한 Discriminated Union은 다음과 같은 규칙으로 만들수 있습니다.

1. 타입들간 공통적인 문자열 리터럴 속성 - 구분자(the discriminant or tag)
2. 타입들을 union 으로 묶어서 설정한 type alias
3. 공통 속성에 Type guard를 적용.

코드로 예를 들면 다음과 같습니다.

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

type Shape = Square | Rectangle | Circle;

function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
}
```

첫번째로 `union`할 `interface`를 지정합니다. 각각의 `interface`는 `kind`라는 공통 속성을 가지고 있고, 그 값은 문자열값으로 모두 다른 값을 가지고 있습니다. `kind` 속성은 discriminant 이나 tag로 정의합니다.

위 인터페이스를 union으로 묶은 `Shape`타입은 discriminated union 입니다.

discriminated union인 `Shape`타입은 `area`함수를 보면 알 수 있듯이 Type guard의 적용이 가능합니다

# Exhaustiveness checking (철저한 검사)
우리는 discriminated union의 모든 조건을 다루지 못할 때 오류를 발생시키고 싶습니다. 예를들어 `Shape`에 `Triangle`이 추가되면 `area`함수에서 다음과 같은 에러를 기대합니다.

```ts
interface Triangle {
    kind: "triangle";
    length: number;
}

type Shape = Square | Rectangle | Circle | Triangle;
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
    // should error here - we didn't handle case "triangle"
}
```

그러나 실제로는 위와 같은 에러를 발생하지 않으며 위와 같은 기능을 기대하려면 두가지 방법이 있습니다.

첫번째로 `--strictNullChecks`플래그를 키고 반환유형을 지정하는 것 입니다.

```ts
function area(s: Shape): number { // error: returns number | undefined
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
    }
}
```

두번째로 `never` 타입을 이용한 방법이 있습니다.

```ts
function assertNever(x: never): never {
    throw new Error("Unexpected object: " + x);
}
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
        default: return assertNever(s); // error here if there are missing cases
    }
}
```

# this 타입 및 메소드 체이닝

`this`타입은 class 혹은 interface에서 사용되는 바인딩된 `this`를 의미합니다.
이를 이용하여 메소드 체이닝을 구현할 수 있습니다.

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

새 클래스는 변경하지 않고 이전 메소드를 사용할 수 있습니다.

```ts
class ScientificCalculator extends BasicCalculator {
    public constructor(value = 0) {
        super(value);
    }
    public sin() {
        this.value = Math.sin(this.value);
        return this;
    }
    // ... other operations go here ...
}

let v = new ScientificCalculator(2)
        .multiply(5)
        .sin()
        .add(1)
        .currentValue();
```

Without `this` types, `ScientificCalculator` would not have been able to extend `BasicCalculator` and keep the fluent interface. `multiply` would have returned `BasicCalculator`, which doesn’t have the `sin` method. However, with `this` types, `multiply` returns `this`, which is `ScientificCalculator` here.

# Index types
