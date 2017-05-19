---
layout: post
title: strategy pattern
subtitle: 상황에 따라 전략을 변경하는 전략 패턴
slug: strategy-pattern
date: '2017-05-17 17:27:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - Typescript
    - Design pattern
    - Strategy pattern
---

# 정의
* **재사용** 이 되야하는 알고리즘의 **알고리즘군** 을 정의하고
* 각 알고리즘을 **캡슐화** 하여 (interface로 묶는다)
* 객체가 알고리즘군내에서 알고리즘을 **교환해서 사용** 할 수 있도록 만든다.

# 효과
* 메소드의 알고리즘의 재사용이 가능하다.
* (그 알고리즘을 사용하는 메소드를 가진)클라이언트와는 **독립적으로 알고리즘을 변경** 할 수 있다.
* 동적으로 알고리즘을 변경할 수 있다.

# UML

```plantuml
class Context{
    - StrategyA strategyA
}

interface StrategyA{
    + excute()
}

class ConcreteStrategyA1{
    + excute()
}

class ConcreteStrategyA2{
    + excute()
}

Context *-- StrategyA
StrategyA <|.. ConcreteStrategyA1
StrategyA <|.. ConcreteStrategyA2
```

위와 같은 구조로, `Context` 는 `strategy` 속성으로 `strategy.excute()` 메소드를 사용하여 `ConcreteStrategy1` 또는 `ConcreteStrategy2` 등의 여러 알고리즘(전략)을 선택하여 사용할수 잇다.

```plantuml
class Context{
    - StrategyA strategyA
    + setStrategyA()
}

interface StrategyA{
    + excute()
}

class ConcreteStrategyA1{
    + excute()
}

class ConcreteStrategyA2{
    + excute()
}

Context *-- StrategyA
StrategyA <|.. ConcreteStrategyA1
StrategyA <|.. ConcreteStrategyA2
```

`setStrategy()` 메소드를 구현시 동적으로 `strategy`(알고리즘(전략))를 바꿀 수 있다.

```plantuml
class Context{
    - StrategyA strategyA
    - StrategyB strategyB
}

interface StrategyA{
    + excute()
}

class ConcreteStrategyA1{
    + excute()
}

class ConcreteStrategyA2{
    + excute()
}

interface StrategyB{
    + excute()
}

class ConcreteStrategyB1{
    + excute()
}

class ConcreteStrategyB2{
    + excute()
}

class ConcreteStrategyB3{
    + excute()
}

Context *-- StrategyA
StrategyA <|.. ConcreteStrategyA1
StrategyA <|.. ConcreteStrategyA2

Context *-- StrategyB
StrategyB <|.. ConcreteStrategyB1
StrategyB <|.. ConcreteStrategyB2
StrategyB <|.. ConcreteStrategyB3
```

다음과 같이 2개 이상의 알고리즘군을 사용할수도 있으며

```plantuml
abstract class Parent{
    method1()
    {abstract} method2()
    - StrategyA strategy
}

class ContextA{
    method2()
}

class ContextB{
    method2()
}

interface Strategy{
    + excute()
}

class ConcreteStrategyA{
    + excute()
}

class ConcreteStrategyB{
    + excute()
}

Parent *-- Strategy
Strategy <|.. ConcreteStrategyA
Strategy <|.. ConcreteStrategyB

Parent <|-- ContextA
Parent <|-- ContextB

ContextA *-- ConcreteStrategyA : strategy = new ConcreteStrategyA()
ContextB *-- ConcreteStrategyB : strategy = new ConcreteStrategyB()
```

공통된 method(`method1`)는 상속으로 알고리즘(전략)이 다른 메소드(`method2`)는 `abstract method`로 지정하여 `ContextA.method2()` 에서는 `ConcreteStrategyA.excute()`의 알고리즘을 `ContextB.method2()` 에서는 `ConcreteStrategyB.excute()`의 알고리즘을 사용하도록 구현할수도 있다.

# example
```ts
interface FlyBehavior {
  fly(): void;
}

interface QuackBehavior {
  quack(): void;
}

class FlyWithWings implements FlyBehavior {
  public fly(): void {
    console.log('fly!!')
  }
}

class FlyNoWay implements FlyBehavior {
  public fly(): void {
    console.log('no fly!!')
  }
}

class Quack implements QuackBehavior {
  public quack(): void {
    console.log('quack!!')
  }
}

class MuteQuack implements QuackBehavior {
  public quack(): void {
    console.log('...')
  }
}

class Squeak implements QuackBehavior {
  public quack(): void {
    console.log('Squeak!!')
  }
}

abstract class Duck {
  protected flyBehavior: FlyBehavior;
  protected quackBehavior: QuackBehavior;

  public abstract display(): void;

  public performFly(): void {
    this.flyBehavior.fly()
  }

  public performQuack(): void {
    this.quackBehavior.quack()
  }

  public swim(): void {
    console.log('swim~')
  }
}

class MallardDuck extends Duck {
  constructor() {
    super();
    this.quackBehavior = new Quack();
    this.flyBehavior = new FlyWithWings();
  }

  public display(): void {
    console.log('i am MallardDuck')
  }
}

class RobotDuck extends Duck {
  constructor() {
    super();
    this.quackBehavior = new Squeak();
    this.flyBehavior = new FlyWithWings();
  }

  public display(): void {
    console.log('i am RobotDuck')
  }
}

class ModelDuck extends Duck {
  constructor() {
    super();
    this.quackBehavior = new MuteQuack();
    this.flyBehavior = new FlyNoWay();
  }

  public display(): void {
    console.log('i am ModelDuck')
  }
}

class RubberDuck extends Duck {
  constructor() {
    super();
    this.quackBehavior = new Squeak();
    this.flyBehavior = new FlyNoWay();
  }

  public display(): void {
    console.log('i am RubberDuck')
  }
}

(function main() {
  const mallard: Duck = new MallardDuck();
  const robot: Duck = new RobotDuck();
  const model: Duck = new ModelDuck();
  const rubber: Duck = new RubberDuck();

  excute(mallard)
  excute(robot)
  excute(model)
  excute(rubber)

  function excute(duck: Duck): void {
    duck.display();
    duck.swim();
    duck.performFly();
    duck.performQuack();
  }
})()
```
