---
layout: post
title: strategy pattern
subtitle: strategy-pattern
slug: strategy-pattern
date: '2017-05-12 17:27:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - Design pattern
    - Strategy pattern
---

# 적용 조건
* 메소드의 행동이 (잘) 바뀌지 않는 것은 부모클래스에서 메소드를 상속한다.
* 다음과 같은 메소드가 있다.
    * 메소드의 행동이 잘 바뀐다.
    * 메소드의 행동이 객체별로 다르면서도 똑같은 알고리즘도 있다.
    * 메소드의 행동을 동적으로 변경할 필요가 있다.

# 정의
strategy pattern은 **알고리즘군** 을 정의하고 각각을 **캡슐화** 하여 **교환해서 사용** 할수 있도록 만든다. strategy pattern을 활용하면 클라이언트와는 **독립적으로 알고리즘을 변경** 할 수 있다.

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
