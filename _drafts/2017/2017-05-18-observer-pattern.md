---
layout: post
title: observer pattern
subtitle:
slug: observer-pattern
date: '2017-05-18 18:22:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - Design pattern
    - Observer pattern
---

# 용어
observer object:
subject object: 감지할 상태를 가진 객체, 상태의 변화 감지후 observer object로 변화사실을 알려주는 객체

# 정의
* 상태 변화를 감지할 객체를 정의하고
* 각 알고리즘을 **캡슐화** 하여
* 알고리즘군내에서 알고리즘을 **교환해서 사용** 할 수 있도록 만든다.

# 효과
* 메소드의 알고리즘의 재사용이 가능하다.
* (그 알고리즘을 사용하는 메소드를 가진)클라이언트와는 **독립적으로 알고리즘을 변경** 할 수 있다.
* 동적으로 알고리즘을 변경할 수 있다.

# UML
준비중

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
