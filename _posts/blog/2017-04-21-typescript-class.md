---
layout: post
title: TypeScript class
subtitle:
slug: typescript-class
date: '2017-04-21 16:33:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - Class
---

# 소개

먼저 간단한 Class 예제를 살펴보겠습니다.
```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```
여기서 알수 있는 사실은 다음과 같습니다.
* 접근제어자(Access Modifier)를 적지 않으면 기본적으로 public 이다.
* 생성자는 `constructor` 메소드로 작성한다.
그외 `this`를 사용하는등 기본적인 class 작성법을 알 수 있습니다.


# Duck Typing

TypeScript의 Class는 Duck Typing(Structural subtyping)을 기반으로 검사합니다.
이는 다음과 같은 코드도 작성이 가능하며 에러가 걸리지 않는것을 알 수 있습니다.
```ts
class Duck {
    name: string;
    constructor(name: string) {
        this.name = name;
    }
    quack() {
        console.log("꽥꽥!!")
    }
}

class Chicken {
    name: string;
    size: number;
    constructor(name: string) {
        this.name = name;
    }
    quack() {
        console.log("꼬꼬댁!")
    }
}

let duck: Duck = new Chicken("cc"); // no error!!
```


# 상속

`extnds` 키워드와 `super` 키워드를 이용하여 상속을 구현할수 있습니다.
```ts
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        console.log("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        console.log("Galloping...");
        super.move(distanceInMeters);
    }
}

let sam = new Snake("Sammy the Python");
let tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);

/*
실행결과

Slithering...
Sammy the Python moved 5m.
Galloping...
Tommy the Palomino moved 34m.
*/
```
여기서는 다음과 같은 사항을 알 수 있습니다.
* `extends` 키워드를 통해 상속이 가능하다.
* 동일 메소드명을 사용하여 override가 가능하다.
    * override된 메소드의 인수의 타입과 return 타입등을 override한 메소드를 기준삼아 체크한다.
* super 키워드를 사용한다.
    * `constructor` 생성자 함수에서 `super()` 생성자 함수를 호출해야 한다.
    * `super` 키워드를 통해 상위 클래스의 (`public`, `protected`) instance 속성을 사용가능하다.

# 접근제어자(Access Modifier)
TypeScript의 접근제어자는 `public`, `private`, `protected` 가 있다. 그 외 `readonly`와 같이 사용이 가능하다.
Access 범위 및 접근제어자에 따라 다른 Duck Typing(Structural subtyping)식 타입 체크의 이해가 필요합니다.

## public
기본적으로 Access Modifier를 생략시 public으로 설정됩니다.
* 생성자, 메소드에서 접근 가능
* 객체에서 접근가능
* 자식클래스에서 접근가능

## private
* 생성자, 메소드에서 접근 가능
* 객체에서 접근불가능
* 자식클래스에서 접근불가능

## protected
* 생성자, 메소드에서 접근 가능
* 객체에서 접근불가능
* 자식클래스에서 접근가능

## 매개변수 속성(Parameter properties)
Parameter properties 를 이용하면 생성자의 매개변수에서 클래스의 속성의 선언과 초기화를 동시에 작성할수 있습니다.
```ts
class Octopus {
    constructor(readonly name: string, public size: number, private password: any) {
    }
}
```

## protected constructor
생성자를 protected로 설정할경우, 그 클래스에 대한 객체를 만들수는 없지만, 확장은 가능한 클래스가 만들어집니다.
예시코드는 다음과 같습니다.
```ts
class Person {
    protected name: string;
    protected constructor(theName: string) { this.name = theName; }
}

// Employee can extend Person
class Employee extends Person {
    private department: string;

    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }

    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

let howard = new Employee("Howard", "Sales");
let john = new Person("John"); // Error: The 'Person' constructor is protected
```

## readonly와 함께 사용
* `readonly`, `public readonly`, `private readonly`, `protected readonly`
* 생성자나 선언시에만 (set)초기화 가능
```ts
class Octopus {
    readonly name: string;
    readonly numberOfLegs: number = 8;
    constructor (theName: string) {
        this.name = theName;
    }
}
let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit"; // error! name is readonly.
dad.numberOfLegs = 10; // error! numberOfLegs is readonly.
```

## 접근제어자별 Structural subtyping
TypeScript는 Structural subtyping 으로 호환가능성만을 따지는 느슨한 검사를 진행합니다.
그러나 private과 protected 속성을 비교할 때는 동일한 선언에서 유래한 속성을 가져야 검사를 통과할수 있습니다.
다음 예시 코드를 봐보자.
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
`Employee` 클래스는 `Animal` 클래스와 구조가 같지만, `name` 속성이 `private` 이므로 `Employee` 클래스의 `name` 속성과 `Animal` 클래스의 `name` 속성은 호환 불가능으로 판별되어 Error가 발생합니다.

`Rhino` 클래스의 경우 `Animal` 클래스를 상속받으므로 `Animal` 클래스의 `private`인 `name` 속성을 `Rhino` 클래스가 호환하므로 TypeScript는 호환가능으로 판별하여 통과하게 됩니다.

`public`의 경우 구조가 동일할시 TypeScript는 호환가능으로 판별하여 통과하게 됩니다.

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

# Accessors
TypeScript는 getters / setters를 지원합니다. 예시 코드는 다음과 같습니다.
```ts
class Person {
  private _fullName: string;
  constructor(
    public firstName: string,
    public lastName: string
  ){
    this._fullName = this.firstName + this.lastName;
  }

  get fullName(): string {
    return this.firstName + this.lastName;
  }
}

var person = new Person('p', 'ch');
console.log(person.fullName); //pch
person.lastName = 'ji'
console.log(person.fullName); //pji
person.fullName = 'pyk'; // error! fullName is readonly.
```
getter만 설정할경우 그 속성은 readonly가 됩니다.  
즉 코드상에서 `fullName` 속성은 readonly가 되어 마지막부분에서 에러가 발생합니다.

# static
static을 설정하는 방법과 사용하는 방법은 아래와 같습니다.
```ts
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        let xDist = (point.x - Grid.origin.x);
        let yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

let grid1 = new Grid(1.0);  // 1x scale
let grid2 = new Grid(5.0);  // 5x scale

console.log(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
console.log(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```
static 키워드로 선언하며 `클래스명.속성명` 으로 접근이 가능합니다.

# Abstract Classes & Abstract Method
추상 클래스는 스스로는 객체를 만들수 없고 다른 클래스에 상속 가능한 클래스입니다. 인터페이스와 다르게 속성에 대한 세부 구현을 포함할수 있습니다. 또한 `abstract` 키워드는 추상 클래스 뿐만 아니라 추상 메소드를 정의하는데 이용됩니다.  
추상 메소드는 인터페이스와 비슷하게 메소드의 구현을 강제하지만 인터페이스와 달리 접근제어자(Access Modifier)를 포함할수 있습니다.
```ts
abstract class Department {

    constructor(public name: string) {
    }

    printName(): void {
        console.log("Department name: " + this.name);
    }

    abstract printMeeting(): void; // must be implemented in derived classes
}

class AccountingDepartment extends Department {

    constructor() {
        super("Accounting and Auditing"); // constructors in derived classes must call super()
    }

    printMeeting(): void {
        console.log("The Accounting Department meets each Monday at 10am.");
    }

    generateReports(): void {
        console.log("Generating accounting reports...");
    }
}

let department: Department; // ok to create a reference to an abstract type
department = new Department(); // error: cannot create an instance of an abstract class
department = new AccountingDepartment(); // ok to create and assign a non-abstract subclass
department.printName();
department.printMeeting();
department.generateReports(); // error: method doesn't exist on declared abstract type
```