---
layout: post
title: decorator pattern
subtitle: wrapper
slug: decorator-pattern
date: '2017-05-18 18:22:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - Typescript
    - Design pattern
    - Decorator pattern
---

# 정의
* 한 객체(Subject)의 상태가 바뀌면
* 그 객체에 의존하는 다른 객체들(Obsrver)에게 연락이 가고
* 자동으로 지정 메서드가 실행, 갱신되는 방식으로
* **일대다(one-to-many) 의존성을 정의** 합니다.

# 도입 조건
* 다양한 상태(필드 값)에 의존해서 메서드가 다른 행동을 해야 한다.
* '단일 책임 원칙'을 준수하지 못한다. 클래스가 다수의 책임을 갖는다.

# 효과
* 다양한 조합의 객체를 만들수 있다.
* 서브클래싱 대신 쓸 수 있는 유연한 대안이 될 수 있다.
* 여러 데코레이터로 감싸다 보면 코드가 복잡해질수 있다.
    * creational pattern과의 조합으로 해결할수 있다.

# UML

```plantuml
interface Subject {
    -decoratorCollection: Array<Decorator>
    +regisierDecorator(decorator: Decorator)
    +unregisterDecorator(decorator: Decorator)
    +notifiyDecorators()
}

class ConcreateSubject{
    -decoratorCollection: Array<Decorator>
    +regisierDecorator(decorator: Decorator)
    +unregisterDecorator(decorator: Decorator)
    +notifiyDecorators()
    +getState()
}

interface Decorator{
    +update(subject: Subject)
}

class ConcreateDecoratorA{
    +update(subject: Subject)
}

class ConcreateDecoratorB{
    +update(subject: Subject)
}

class ConcreateDecoratorC{
    +update(subject: Subject)
}

ConcreateSubject ..|> Subject

ConcreateDecoratorA .up.|> Decorator
ConcreateDecoratorB .up.|> Decorator
ConcreateDecoratorC .up.|> Decorator

Subject o--> Decorator : decoratorCollection
```

`notifiyDecorators()` 메소드가 실행시 `regisierDecorator(decorator: Decorator)` 메소드에 의해 등록된 `decoratorCollection`변수안의 decorator들의 `update(subject: Subject)` 메소드가 실행되며 각 decorator에 `subject: Subject` 가 전달된다. 그리고 변경된 상태값을 `ConcreateSubject.getState()`등의 메소드를 통해 가져올수 있다.

위 방식은 pull 방식이며 push 방식은 다음과 같다.

```plantuml
interface Subject {
    -decoratorCollection: Array<Decorator>
    +regisierDecorator(decorator: Decorator)
    +unregisterDecorator(decorator: Decorator)
    +notifiyDecorators()
}

class ConcreateSubject{
    -decoratorCollection: Array<Decorator>
    +regisierDecorator(decorator: Decorator)
    +unregisterDecorator(decorator: Decorator)
    +notifiyDecorators()
}

interface Decorator{
    +update(states)
}

class ConcreateDecoratorA{
    +update(states)
}

class ConcreateDecoratorB{
    +update(states)
}

class ConcreateDecoratorC{
    +update(states)
}

ConcreateSubject ..|> Subject

ConcreateDecoratorA .up.|> Decorator
ConcreateDecoratorB .up.|> Decorator
ConcreateDecoratorC .up.|> Decorator

Subject o--> Decorator : decoratorCollection
```

pull 방식과의 차이점은 `update()`메소드로 `Subject` 객체가 아닌 상태(state)값 자체가 전달되는것이다.

일반적으로 pull 방식이 더 좋은 방식으로 평가됩니다. `states` 매개변수 부분이 변경되면 모든 Decorator 객체를 변경해야 되기 때문입니다.

# example
pull 방식과 push 방식을 typescript로 모두 작성해보았다.

## pull 방식

```ts
import * as _ from 'lodash';

interface Subject {
  registerObserve(o: Decorator): void;
  removeObserve(o: Decorator): void;
  notifyObserve(): void;
}

interface Decorator {
  update<T extends Subject>(subject: T): void;
}

interface DisplayElement {
  display(): void;
}

class WeatherData implements Subject {
  private decorators: Decorator[] = [];
  private _temperature: number;
  private _humidity: number;
  private _pressure: number;

  get temperature(): number {
    return this._temperature;
  }

  get humidity(): number {
    return this._humidity;
  }

  get pressure(): number {
    return this._pressure;
  }

  public registerObserve(o: Decorator): void {
    if (!_.includes<Decorator>(this.decorators, o)) {
      this.decorators.push(o)
    }
  }

  public removeObserve(o: Decorator): void {
    _.pull<Decorator>(this.decorators, o);
  }

  public notifyObserve(): void {
    console.log('--notifyObserve--')
    this.decorators.forEach((decorator) => {
      decorator.update(this)
    })
  }

  public measurementsChanged(): void {
    this.notifyObserve();
  }

  public setMeasurements(temperature: number, humidity: number, pressure: number): void {
    this._temperature = temperature;
    this._humidity = humidity;
    this._pressure = pressure;

    this.measurementsChanged();
  }

  public run(): void {
    setTimeout(() => {
      const temperature = _.random(75, 85)
      const humidity = _.random(60, 95)
      const pressure = _.random(28, 31, true)

      this.setMeasurements(temperature, humidity, pressure)
      this.run();
    }, _.random(500, 1500))
  }
}

class CurrentConditionsDisplay implements Decorator, DisplayElement {
  private temperature: number;
  private humidity: number;

  public constructor(private weatherData: Subject) {
    this.weatherData.registerObserve(this);
  }

  public update(subject: WeatherData): void {
    this.temperature = subject.temperature;
    this.humidity = subject.humidity;
    this.display();
  }

  public display(): void {
    console.log(`Current coditions: ${this.temperature}F degrees and ${this.humidity}% humidity.`);
  }
}

class StatisticsDisplay implements Decorator, DisplayElement {
  private maxTemp: number = 0.0;
  private minTemp: number = 200;
  private tempSum: number = 0.0;
  private numReadings: number = 0;

  public constructor(private weatherData: Subject) {
    this.weatherData.registerObserve(this);
  }

  public update(subject: WeatherData): void {
    this.tempSum += subject.temperature;
    this.numReadings++;

    if (subject.temperature > this.maxTemp) {
      this.maxTemp = subject.temperature;
    }

    if (subject.temperature < this.minTemp) {
      this.minTemp = subject.temperature;
    }

    this.display();
  }

  public display(): void {
    console.log(`Avg/Max/Min temperature = ${(this.tempSum / this.numReadings)}/${this.maxTemp}/${this.minTemp}`)
  }
}

class ForecastDisplay implements Decorator, DisplayElement {
  private currentPressure: number = 29.92;
  private lastPressure: number;

  public constructor(private weatherData: Subject) {
    this.weatherData.registerObserve(this);
  }

  public update(subject: WeatherData): void {
    this.lastPressure = this.currentPressure;
    this.currentPressure = subject.pressure;

    this.display();
  }

  public display(): void {
    let forecastMsg: string = '';
    if (this.currentPressure > this.lastPressure) {
      forecastMsg = 'Improving weather on the way!';
    } else if (this.currentPressure === this.lastPressure) {
      forecastMsg = 'More of the same';
    } else if (this.currentPressure < this.lastPressure) {
      forecastMsg = 'Watch out for cooler, rainy weather';
    }

    console.log(`Forecast: ${forecastMsg}`);
  }
}

class HeatIndexDisplay implements Decorator, DisplayElement {
  private heatIndex: number = 0.0;

  public constructor(private weatherData: Subject) {
    this.weatherData.registerObserve(this);
  }

  public update(subject: WeatherData): void {
    this.heatIndex = this.computeHeatIndex(subject.temperature, subject.humidity);
    this.display();
  }

  public display(): void {
    console.log(`Heat index is ${this.heatIndex}`);
  }

  private computeHeatIndex(t: number, rh: number): number {
    const index: number = ((16.923 + (0.185212 * t) + (5.37941 * rh) - (0.100254 * t * rh)
      + (0.00941695 * (t * t)) + (0.00728898 * (rh * rh))
      + (0.000345372 * (t * t * rh)) - (0.000814971 * (t * rh * rh)) +
      (0.0000102102 * (t * t * rh * rh)) - (0.000038646 * (t * t * t)) + (0.0000291583 *
        (rh * rh * rh)) + (0.00000142721 * (t * t * t * rh)) +
      (0.000000197483 * (t * rh * rh * rh)) - (0.0000000218429 * (t * t * t * rh * rh)) +
      0.000000000843296 * (t * t * rh * rh * rh)) -
      (0.0000000000481975 * (t * t * t * rh * rh * rh)));
    return index;
  }
}

export default function main() {
  const weatherData: WeatherData = new WeatherData();
  const currentDisplay: CurrentConditionsDisplay = new CurrentConditionsDisplay(weatherData);
  const statisticsDisplay: StatisticsDisplay = new StatisticsDisplay(weatherData);
  const forecastDisplay: ForecastDisplay = new ForecastDisplay(weatherData);
  const heatIndexDisplay: HeatIndexDisplay = new HeatIndexDisplay(weatherData);

  weatherData.run();
}
```

## push 방식

```ts
import * as _ from 'lodash';

interface Subject {
  registerObserve(o: Decorator): void;
  removeObserve(o: Decorator): void;
  notifyObserve(): void;
}

interface Decorator {
  update(temperature: number, humidity: number, pressure: number): void;
}

interface DisplayElement {
  display(): void;
}

class WeatherData implements Subject {
  private decorators: Array<Decorator> = [];
  private temperature: number;
  private humidity: number;
  private pressure: number;

  public registerObserve(o: Decorator): void {
    if (!_.includes<Decorator>(this.decorators, o)) {
      this.decorators.push(o)
    }
  }

  public removeObserve(o: Decorator): void {
    _.pull<Decorator>(this.decorators, o);
  }

  public notifyObserve(): void {
    console.log('--notifyObserve--')
    this.decorators.forEach((decorator) => {
      decorator.update(
        this.temperature,
        this.humidity,
        this.pressure
      )
    })
  }

  public measurementsChanged(): void {
    this.notifyObserve();
  }

  public setMeasurements(temperature: number, humidity: number, pressure: number): void {
    this.temperature = temperature;
    this.humidity = humidity;
    this.pressure = pressure;

    this.measurementsChanged();
  }

  public run(): void {
    setTimeout(() => {
      const temperature = _.random(75, 85)
      const humidity = _.random(60, 95)
      const pressure = _.random(28, 31, true)

      this.setMeasurements(temperature, humidity, pressure)
      this.run();
    }, _.random(500, 1500))
  }
}

class CurrentConditionsDisplay implements Decorator, DisplayElement {
  private temperature: number;
  private humidity: number;

  public constructor(private weatherData: Subject) {
    this.weatherData.registerObserve(this);
  }

  public update(temperature: number, humidity: number, pressure: number): void {
    this.temperature = temperature;
    this.humidity = humidity;
    this.display();
  }

  public display(): void {
    console.log(`Current coditions: ${this.temperature}F degrees and ${this.humidity}% humidity.`);
  }
}

class StatisticsDisplay implements Decorator, DisplayElement {
  private maxTemp: number = 0.0;
  private minTemp: number = 200;
  private tempSum: number = 0.0;
  private numReadings: number = 0;

  public constructor(private weatherData: Subject) {
    this.weatherData.registerObserve(this);
  }

  public update(temperature: number, humidity: number, pressure: number): void {
    this.tempSum += temperature;
    this.numReadings++;

    if (temperature > this.maxTemp) {
      this.maxTemp = temperature;
    }

    if (temperature < this.minTemp) {
      this.minTemp = temperature;
    }

    this.display();
  }

  public display(): void {
    console.log(`Avg/Max/Min temperature = ${(this.tempSum / this.numReadings)}/${this.maxTemp}/${this.minTemp}`)
  }
}

class ForecastDisplay implements Decorator, DisplayElement {
  private currentPressure: number = 29.92;
  private lastPressure: number;

  public constructor(private weatherData: Subject) {
    this.weatherData.registerObserve(this);
  }

  public update(temperature: number, humidity: number, pressure: number): void {
    this.lastPressure = this.currentPressure;
    this.currentPressure = pressure;

    this.display();
  }

  public display(): void {
    let forecastMsg: string = '';
    if (this.currentPressure > this.lastPressure) {
      forecastMsg = 'Improving weather on the way!';
    } else if (this.currentPressure === this.lastPressure) {
      forecastMsg = 'More of the same';
    } else if (this.currentPressure < this.lastPressure) {
      forecastMsg = 'Watch out for cooler, rainy weather';
    }

    console.log(`Forecast: ${forecastMsg}`);
  }
}

class HeatIndexDisplay implements Decorator, DisplayElement {
  private heatIndex: number = 0.0;

  public constructor(private weatherData: Subject) {
    this.weatherData.registerObserve(this);
  }

  public update(temperature: number, humidity: number, pressure: number): void {
    this.heatIndex = this.computeHeatIndex(temperature, humidity);
    this.display();
  }

  public display(): void {
    console.log(`Heat index is ${this.heatIndex}`);
  }

  private computeHeatIndex(t: number, rh: number): number {
    const index: number = ((16.923 + (0.185212 * t) + (5.37941 * rh) - (0.100254 * t * rh)
      + (0.00941695 * (t * t)) + (0.00728898 * (rh * rh))
      + (0.000345372 * (t * t * rh)) - (0.000814971 * (t * rh * rh)) +
      (0.0000102102 * (t * t * rh * rh)) - (0.000038646 * (t * t * t)) + (0.0000291583 *
        (rh * rh * rh)) + (0.00000142721 * (t * t * t * rh)) +
      (0.000000197483 * (t * rh * rh * rh)) - (0.0000000218429 * (t * t * t * rh * rh)) +
      0.000000000843296 * (t * t * rh * rh * rh)) -
      (0.0000000000481975 * (t * t * t * rh * rh * rh)));
    return index;
  }
}

export default function main() {
  const weatherData: WeatherData = new WeatherData();
  const currentDisplay: CurrentConditionsDisplay = new CurrentConditionsDisplay(weatherData);
  const statisticsDisplay: StatisticsDisplay = new StatisticsDisplay(weatherData);
  const forecastDisplay: ForecastDisplay = new ForecastDisplay(weatherData);
  const heatIndexDisplay: HeatIndexDisplay = new HeatIndexDisplay(weatherData);

  weatherData.run();
}
```
