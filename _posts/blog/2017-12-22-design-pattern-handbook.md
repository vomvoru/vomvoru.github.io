---
layout: post
title: design pattern
subtitle: handbook
slug: design-pattern-handbook
date: '2017-12-12 21:20:00 +0900'
categories: draft
author: vomvoru
comments: true
share: true
tags:
    - Design pattern
---

# 전략 패턴(strategy pattern)
사용할 전략(메소드)를 다른 클래스들에서 구현하여 적절히 빌려쓰기

추후에 UML 작성

## 구현
1. 제공할 메소드를 가진 전략 인터페이스를 구현
1. 전략 인터페이스를 구현한 클래스들 구현

## 사용
1. 전략 인터페이스를 가진 클래스를 구현하여 전략객체들을 적절히 선택하여 사용(코딩)
1. 위 전략객체들을 동적으로 선택할수 있는 메소드를 구현 하는것도 가능

## 예시
추후에 예시 작성(실 사용처)


# 옵저버 패턴(observer pattern)
구독 객체들을 가지고 있다가 자신의 상태 변경시 구독 객체들에게 상태 전달

추후에 UML 작성

## 구현
1. 상태 변경시 실행할 메소드(update)를 가진 구독 인터페이스(IObserver) 및 클래스들 구현
1. 상태를 감시할 인터페이스(ISubject) 및 클래스들 구현
    - IObserver 배열
    - IObserver 배열을 설정할 registerObserver, removeObserver 메서드

## 사용
1. 상태를 감시할 Subject객체안의 registerObserver 메소드를 이용하여 상태가 변경시 구독받을 Observer객체들을 배열로 저장
1. Subject객체의 상태가 변경시 배열을 돌며 update 메소드를 실행
1. removeObserver 메소드로 배열에서 제외시켜 구독을 취소할수 있다.

## 예시
추후에 예시 작성(실 사용처)

# 데코레이터 패턴(decorator pattern)
기존 객체를 받아서 메소드 혹은 속성들을 데코레이터 객체들로 오버라이딩 한 객체를 반환하는 패턴

추후에 UML 작성

## 구현
1. 데코레이팅 받는 객체의 부모클래스 생성(Component)
    - 데코레이팅 받지 않을 경우 메소드와 속성의 기본값을 설정
1. Component를 상속받는 데코레이팅 받는 클래스들 구현
1. Component를 상송받는 데코레이터용 부모 추상클래스 구현(Decorator)
    - 데코레이팅 하는 클래스별로 분류하는 역할
    - 데코레이팅 하는 클래스에서 꼭 구현해야 되는 메소드를 설정할수 있는 역할
1. Decorator를 상속받는 데코레이터 클래스 구현
    - 데코레이팅 할 객체를 저장하는 속성 및 설정하는 메소드 구현(주로 생성자 함수로 구현)
    - 기존 객체의 메소드를 이용하여 메소드를 데코레이팅(Override)

## 사용
1. 데코레이팅할 객체를 데코레이터 객체의 메소드(주로 생성자)에 넘겨주어서 데코레이팅된 객체를 받는다

비슷한 패턴 (데코레이터, 전략)끼리 비교하는 글 작성

## 예시
추후에 예시 작성(실 사용처)

# 팩토리 패턴