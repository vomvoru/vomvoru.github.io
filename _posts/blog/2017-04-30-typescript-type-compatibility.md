---
layout: post
title: TypeScript's Type Compatibility
subtitle: structural subtyping
slug: typescript-type-compatibility
date: '2017-04-30 22:10:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

Typescript의 Type Compatibility는 structural subtyping을 기반으로 합니다. 이것은 nominal typing과는 대조적입니다.
```ts
interface Named {
    name: string;
}

class Person {
    name: string;
}

let p: Named;
// OK, because of structural typing
p = new Person();
```
위와 같은 코드에서는 `Person`클래스가 `Named`인터페이스 구현자라고 명시적으로 작성하지 않았으므로 nominal typing 기반인 C# 또는 Java에서 동일한 코드의 경우 에러가 발생합니다. 하지만, structural subtyping을 기반으로 하는 Typescript는 오류가 발생하지 않습니다.

Javascript가 작성되는 유연한 방식에 따라 Typescript의 structural subtyping이 설계되었습니다.

# A Note on Soundness

TypeScript’s type system allows certain operations that can’t be known at compile-time to be safe. When a type system has this property, it is said to not be “sound”. The places where TypeScript allows unsound behavior were carefully considered, and throughout this document we’ll explain where these happen and the motivating scenarios behind them.

# Starting out
Typescript의 structural type system의 기본규칙은 `y`가 `x`의 맴버를 포함하고 있다면 `x`가 `y`와 호환된다는 것입니다.
```ts
interface Named {
    name: string;
}

let x: Named;
// y's inferred type is { name: string; location: string; }
let y = { name: "Alice", location: "Seattle" };
x = y;
y = x; // error;
```
`y`가 `x`에 할당될 수 있는지 확인하기 위해 컴파일러는 `x`의 각 속성에 호환되는 맴버가 `y`에 있는지 확입니다. 위의 경우 `y`는 `string`타입인 name 속성을 가져야 합니다. `y`는 `name: "Alice"`을 가지고 있으므로 할당이 허용됩니다.

함수 호출 인수를 검사 할 때도 동일한 할당 규칙이 사용됩니다.
```ts
function greet(n: Named) {
    alert("Hello, " + n.name);
}
greet(y); // OK
```
`y`에는 `location` 이라는 별도의 속성이 있습니다. 그러나 호환성을 확인할때 대상 유형의 맴버(`Named`)만 고려됩니다.  
이 비교 프로세스는 재귀적으로 검사되어 각 구성원 및 하위 구성원의 type을 확인합니다.

# Comparing two functions

함수 type에서 매개변수의 경우 매개변수의 순서와 타입만을 고려합니다.

```ts
let x = (a: number) => 0;
let y = (b: number, s: string) => 0;

y = x; // OK
x = y; // Error
```

위 예제에서 `y = x` 와 같이 'discarding' 매개변수를 허용하는 이유는 Javascript에서 매개변수를 무시하는것이 매우 일발적이기 때문입니다.
예를 들어서 `Array.prototype.forEach`함수의 매개변수인 콜백함수는 배열요소, 인덱스, 배열 3가지의 매개변수를 제공합니다. 그럼에도 불구하고 첫 번째 매개변수만 사용하는 경우가 매우 일반적입니다.

```js
let items = [1, 2, 3];

// Don't force these extra parameters
items.forEach((item, index, array) => console.log(item));

// Should be OK!
items.forEach(item => console.log(item));
```

또한 `y = x`를 하고 `y(10, 'str')` 을 실행하면 `'str'`매개변수를 무시하고 실행이 가능하지만, `x = y`를 하고 `x(3)`을 실행하면 x의 타입상 오류는 걸리지 않지만 실제 실행되는 함수 `(b: number, s: string) => 0` 에서 문자열 매개변수 `s` 가 없는 상태로 실행되기 때문에 문제가 발생할 여지가 있습니다. 그러므로 위와 같은 규칙을 적용하는것은 매우 당연하다고 볼 수 있습니다.

## Function Parameter Bivariance

매개변수의 함수 타입(`handler`)을 비교할때, source 매개변수가 target 매개변수에 할당 가능하거나 그 반대인 경우 할당이 성공합니다. 이러한 규칙은 (unsound)견고하지 못하지만 실제로 이러한 종류의 오류는 거의 발생하지 않으며 이와 같은 규칙을 통해 많은 일반적인 Javascript 패턴을 사용할수 있습니다.
```ts
enum EventType { Mouse, Keyboard }

interface Event { timestamp: number; }
interface MouseEvent extends Event { x: number; y: number }
interface KeyEvent extends Event { keyCode: number }

function listenEvent(eventType: EventType, handler: (n: Event) => void) {
    /* ... */
}

// Unsound, but useful and common
listenEvent(EventType.Mouse, (e: MouseEvent) => console.log(e.x + "," + e.y));

// Undesirable alternatives in presence of soundness
listenEvent(EventType.Mouse, (e: Event) => console.log((<MouseEvent>e).x + "," + (<MouseEvent>e).y));
listenEvent(EventType.Mouse, <(e: Event) => void>((e: MouseEvent) => console.log(e.x + "," + e.y)));

// Still disallowed (clear error). Type safety enforced for wholly incompatible types
listenEvent(EventType.Mouse, (e: number) => console.log(e));
```

> 위 규칙에 관련되어서는 글을 작성한 현재에도 많은 [논의][issue 1394]가 진행되고 있습니다.
> 위 규칙으로 문제되는 코드를 [이슈][issue 1394]에서 가져와서 아래에 작성하였으므로 참고바랍니다.
> 위 코드또한 `listenEvent`함수 내에서 `handler({timestamp:1234})` 가 실행된다면 컴파일 오류는 걸리지 않지만 런타임 오류에 걸립니다.


```ts
class Base { public a; }
class Derived extends Base { public b; }

function useDerived(derived: Derived) { derived.b; }

const useBase: (base: Base) => void = useDerived; // this must not be allowed
useBase(new Base());    // no compile error, runtime error
```

## Optional Parameters and Rest Parameters

선택적 매개변수와 필수 매개변수는 서로 바꿔서 사용할 수 있습니다. 또한 rest 매개변수는 무한대의 선택적 매개변수인 것처럼 타입검사가 처리됩니다.

```ts
function invokeLater(args: any[], callback: (...args: any[]) => void) {
    /* ... Invoke callback with 'args' ... */
}

// Unsound - invokeLater "might" provide any number of arguments
invokeLater([1, 2], (x, y) => console.log(x + ", " + y));

// Confusing (x and y are actually required) and undiscoverable
invokeLater([1, 2], (x?, y?) => console.log(x + ", " + y));
```

위 `invokeLater`함수를 다음과 같이 변경하여도 에러가 발생하지 않습니다.

```ts
function invokeLater(args: any[], callback: (a, b) => void) {
    /* ... Invoke callback with 'a', 'b' ... */
}
```

# Enums

`enum`타입은 `number`타입과 호환되며 `number`타입은 `enum`타입과 호환됩니다. 다른 `enum`타입과는 서로 호환되지 않습니다.

```ts
enum Status { Ready, Waiting };
enum Color { Red, Blue, Green };

let status = Status.Ready;
status = Color.Green;  //error
```

# Class

클래스는 객체리터럴 유형 및 인터페이스와 유사하게 작동하지만, 다른점은 정적 및 인스턴스 유형을 모두 가지고 있다는 점 입니다. 클래스 유형의 두 객체를 비교할 때 인스턴스의 맴버만 비교되며 정적맴버 및 생성자는 호환성에 영향을 주지 않습니다.

## private member & protected member
TypeScript는 Structural subtyping 으로 호환가능성만을 따지는 느슨한 검사를 진행합니다.
그러나 **다른 클래스끼리 private과 protected 속성을 비교할 때는 상속된 속성을 가져야 검사를 통과할수 있습니다.**
설명을 위한 예시 코드입니다.

```ts
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = employee; // Error: 'Animal' and 'Employee' are not compatible
animal = rhino; // PASS!
```
`Employee` 클래스는 `Animal` 클래스와 구조가 같지만, `name` 속성이 `private` 이므로 `Employee` 클래스의 `name` 속성과 `Animal` 클래스의 `name` 속성은 구조만 같고 상속되지 않았으므로 TypeScript는 호환 불가능 구조로 판별되어 Error가 발생합니다.

`Rhino` 클래스의 경우 `Animal` 클래스를 상속받으므로 `Animal` 클래스의 `private`인 `name` 속성을 `Rhino` 클래스가 상속하여 가져왔으므로 TypeScript는 호환가능으로 판별하여 통과하게 됩니다.


`public`의 경우 상속된 값인지 관계없이 구조가 동일할시 TypeScript는 호환가능으로 판별하여 통과하게 됩니다.  
아래 변형된 코드를 참고하세요.
```ts
class Animal {
    public name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    public name: string;
    constructor(theName: string) { this.name = theName; }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = employee; // PASS!
animal = rhino; // PASS!
```

# Generics
TypeScript는 Structural subtyping이므로 generic은 실제로 사용될 때만 영향을 줍니다.

```ts
interface Empty<T> {
}

interface NotEmpty<T> {
    data: T;
}

interface NumberEmpty {
    data: number;
}



let a: Empty<number>;
let b: Empty<string>;
let c: NotEmpty<number>;
let d: NotEmpty<string>;
let e: NumberEmpty;

a = b;  // pass
c = d;  // error
c = e;  // pass
d = e;  // error
```

generic이 지정되지 않은 경우 `any`로 판단하여 호환성을 검사합니다.

```ts
let identity = function<T>(x: T): T {
    // ...
}

let reverse = function<U>(y: U): U {
    // ...
}

identity = reverse;  // Okay because (x: any)=>any matches (y: any)=>any
```

# Subtype vs Assignment
So far, we’ve used ‘compatible’, which is not a term defined in the language spec. In TypeScript, there are two kinds of compatibility: subtype and assignment. These differ only in that assignment extends subtype compatibility with rules to allow assignment to and from `any` and to and from enum with corresponding numeric values.

Different places in the language use one of the two compatibility mechanisms, depending on the situation. For practical purposes, type compatibility is dictated by assignment compatibility even in the cases of the `implements` and `extends` clauses. For more information, see the [TypeScript spec](https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md).


[issue 1394]: https://github.com/Microsoft/TypeScript/issues/1394
