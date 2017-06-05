---
layout: post
title: observer pattern
excerpt:
slug: observer-pattern
date: '2017-05-18 18:22:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - Typescript
    - Design pattern
    - Observer pattern
---

# 정의
특정 객체의 상태가 변경되면 다른 객체가 감지하여 갱신(update)하는 디자인 패턴.

# 도입 조건
* 특정 객체의 상태변화를 감지 할 필요가 있다.

# 도입 방법
1. 감지할 상태를 가진 객체(`Subject`)를 정의한다.
1. 상태에 따라 실행할 메서드를 가진 객체들(`Obsrver`)을 정의한다.
1. `Obsrver`는 감지할 상태를 가진 객체(`Subject`)에 등록(`regist`)한다.
1. `Subject`는 자신의 상태를 등록된 (`Obsrver`)객체들을 가지고 있는다.
1. `Subject`가 자신의 상태가 변경될시 등록된 `Obsrver` 객체들에게 알려준다(`Subject.notifyObserve()` --> `Observer.update()`)

# 효과
* 서로 상호작용(의존)하는 객체 사이가 약결합(Loose Coupling) 한다.
* 특정 인터페이스만을 구현하므로 Subject와 Observer는 독립적으로 재사용이 가능하다.
* Observer는 언제든지 새롭게 추가 할 수 있다.
* **일대다(one-to-many) 관계** 가 된다.

# UML

```plantuml
interface Subject {
    -observerCollection: Array<Observer>
    +regisierObserver(observer: Observer)
    +unregisterObserver(observer: Observer)
    +notifiyObservers()
}

class ConcreateSubject{
    -observerCollection: Array<Observer>
    +regisierObserver(observer: Observer)
    +unregisterObserver(observer: Observer)
    +notifiyObservers()
    +getState()
}

interface Observer{
    +update(subject: Subject)
}

class ConcreateObserverA{
    +update(subject: Subject)
}

class ConcreateObserverB{
    +update(subject: Subject)
}

class ConcreateObserverC{
    +update(subject: Subject)
}

ConcreateSubject ..|> Subject

ConcreateObserverA .up.|> Observer
ConcreateObserverB .up.|> Observer
ConcreateObserverC .up.|> Observer

Subject o--> Observer : observerCollection
```

`notifiyObservers()` 메소드가 실행시 `regisierObserver(observer: Observer)` 메소드에 의해 등록된 `observerCollection`변수안의 observer들의 `update(subject: Subject)` 메소드가 실행되며 각 observer에 `subject: Subject` 가 전달된다. 그리고 변경된 상태값을 `ConcreateSubject.getState()`등의 메소드를 통해 가져올수 있다.

위 방식은 pull 방식이며 push 방식은 다음과 같다.

```plantuml
interface Subject {
    -observerCollection: Array<Observer>
    +regisierObserver(observer: Observer)
    +unregisterObserver(observer: Observer)
    +notifiyObservers()
}

class ConcreateSubject{
    -observerCollection: Array<Observer>
    +regisierObserver(observer: Observer)
    +unregisterObserver(observer: Observer)
    +notifiyObservers()
}

interface Observer{
    +update(states)
}

class ConcreateObserverA{
    +update(states)
}

class ConcreateObserverB{
    +update(states)
}

class ConcreateObserverC{
    +update(states)
}

ConcreateSubject ..|> Subject

ConcreateObserverA .up.|> Observer
ConcreateObserverB .up.|> Observer
ConcreateObserverC .up.|> Observer

Subject o--> Observer : observerCollection
```

pull 방식과의 차이점은 `update()`메소드로 `Subject` 객체가 아닌 상태(state)값 자체가 전달되는것이다.

일반적으로 pull 방식이 더 좋은 방식으로 평가됩니다. `states` 매개변수 부분이 변경되면 모든 Observer 객체를 변경해야 되기 때문입니다.

# 실제 사용형태


# example
pull 방식과 push 방식을 typescript로 모두 작성해보았다.

## pull 방식

```ts
import * as _ from 'lodash';

interface Subject {
  registerObserve(o: Observer): void;
  removeObserve(o: Observer): void;
  notifyObserve(): void;
}

interface Observer {
  update<T extends Subject>(subject: T): void;
}

interface DisplayElement {
  display(): void;
}

class WeatherData implements Subject {
  private observers: Observer[] = [];
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

  public registerObserve(o: Observer): void {
    if (!_.includes<Observer>(this.observers, o)) {
      this.observers.push(o)
    }
  }

  public removeObserve(o: Observer): void {
    _.pull<Observer>(this.observers, o);
  }

  public notifyObserve(): void {
    console.log('--notifyObserve--')
    this.observers.forEach((observer) => {
      observer.update(this)
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

class CurrentConditionsDisplay implements Observer, DisplayElement {
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

class StatisticsDisplay implements Observer, DisplayElement {
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

class ForecastDisplay implements Observer, DisplayElement {
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

class HeatIndexDisplay implements Observer, DisplayElement {
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
  registerObserve(o: Observer): void;
  removeObserve(o: Observer): void;
  notifyObserve(): void;
}

interface Observer {
  update(temperature: number, humidity: number, pressure: number): void;
}

interface DisplayElement {
  display(): void;
}

class WeatherData implements Subject {
  private observers: Array<Observer> = [];
  private temperature: number;
  private humidity: number;
  private pressure: number;

  public registerObserve(o: Observer): void {
    if (!_.includes<Observer>(this.observers, o)) {
      this.observers.push(o)
    }
  }

  public removeObserve(o: Observer): void {
    _.pull<Observer>(this.observers, o);
  }

  public notifyObserve(): void {
    console.log('--notifyObserve--')
    this.observers.forEach((observer) => {
      observer.update(
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

class CurrentConditionsDisplay implements Observer, DisplayElement {
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

class StatisticsDisplay implements Observer, DisplayElement {
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

class ForecastDisplay implements Observer, DisplayElement {
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

class HeatIndexDisplay implements Observer, DisplayElement {
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
