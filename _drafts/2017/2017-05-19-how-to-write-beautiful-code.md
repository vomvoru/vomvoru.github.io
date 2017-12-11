---
layout: post
title: 좋은 코드 작성하기 1
subtitle: 소프트웨어 품질
slug: how-to-write-beautiful-code-1
date: '2017-05-22 12:53:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - SOLID
    - 소프트웨어의 품질
    - programming
    - ISO/IEC 9126
    - ISO/IEC 25010
---

## 캡슐화

## 모듈화

## 추상성

## 다형성

## 가독성

##

# 코드 냄새(Code smell)

wikipida와 cleancode 도서를 참고하여 정리하였다.

* 일반적 부분
  * **중복 된 코드**: 코드의 중복, 리터럴값의 중복, 알고리즘의 중복등을 고려
  * **인위적인 복잡성**: 단순한 설계로 충분한데 디자인패턴을 사용하여 개발기간, 가독성이 떨어지지 않았는가
  * **if문, switch문**: if문과 switch문은 그 아래로 코드가 추가될 여지가 다분하다. 다형성을 생각하자
      * Factory Pattern 등을 통해 if문과 switch문을 한곳에 둔다면 괜찬다
  * **주석**: 주석이 없어도 이해되는 코드가 좋은 코드이다.
      * 식별자(이름)을 잘 이해되게 명명하였는가?
      * 기능별로 함수를 잘 분리 하였는가? (단일책임원칙)
  * **리터럴**: 리터럴은 명명된 상수로 사용하는 것이 좋다.
* 클래스 부분
  * **큰 클래스**: 여러개의 책임을 지고 있지 않은가
  * **기능 의존**(Feature envy): 다른 클래스의 메소드를 지나치게 사용하는 클래스
  * **부적절한 친밀도**: 다른 클래스의 구현 세부정보에 종속된 클래스
      * 인터페이스 활용을 고려
  * **거부된 유산**: 서브클래스가 부모클래스로 상속받은것중 필요하지 않는게 있다.
      * Strategy Pattern, Decorator Pattern 을 고려하라
      * Replace Inheritance를 하여 위임을 활용하라
  * **데이터 덩어리**: 함께 자주 사용되는 데이터들은 묶어야 가독성이 좋아진다.
      * 객체(클래스)로 묶는것을 고려한다.
      * 데이터들을 사용하는 함수들의 메서드화도 고려한다.
* 메서드 부분
  * **3개 이상의 매개변수**: 매개변수가 많을수록 이해가 힘들고, 매개변수 순서 및 갯수의 변화는 다른 코드에 영향을 준다.
      * 메서드가 여러개의 책임을 지고 있지 않은가.
      * [Replace Parameter with Method](https://refactoring.com/catalog/replaceParameterWithMethod.html) 고려하기
      * [Preserve Whole Object](https://refactoring.com/catalog/preserveWholeObject.html) 고려하기
      * [Introduce Parameter Object](https://refactoring.com/catalog/introduceParameterObject.html) 를 고려하기
  * **너무 긴 메서드**: 가독성을 위해 메서드의 분리가 필요하다.
* 식별자(이름) 부분
  * **잛은 식별자**: 식별자의 명확성이 저하될수 있다. 의도를 분명히 밝혀라
    * a, b -> source, destination
    * 루프에서 반복횟수를 세는 변수 i, j, k 는 괜찬다.
  * **유형이 들어간 식별자**: 요즘에는 IDE가 발전하여 변수유형을 변수명에 적지 않아도 된다
    * 클래스, 인터페이스 앞에 C 나 I를 붙이는것 등을 의미한다
    * List, Array 등도 마찬가지다.
    * 하지만 당연히 팀 규칙이 가장 우선시 된다.
  * **클래스 이름이 명사가 아니다**
  * **메서드 이름이 동사가 아니다**
  * **불용어가 존재**: 불용어는 이름을 길게 만들뿐 아무런 정보를 제공하지 못한다
    * 불용어: info, Data, a, an, the ...

# 디자인 냄새(Design smell)

# 좋은 코드를 위한 원칙

## SOLID

http://www.nextree.co.kr/p6960/
https://zetawiki.com/wiki/%EA%B0%9D%EC%B2%B4%EC%A7%80%ED%96%A5_%EA%B0%9C%EB%B0%9C_5%EB%8C%80_%EC%9B%90%EB%A6%AC_SOLID
https://ko.wikipedia.org/wiki/SOLID

### SRP

### OCP

### LSP

### ISP

### DIP

## 알고리즘

## 디자인 패턴

https://en.wikipedia.org/wiki/GRASP_(object-oriented_design)

## Package principles

### 응집도

#### 재사용 동등 원리 (REP)

#### 공통 재사용 원칙 (CRP)

#### 공통 폐쇄 원칙 (CCP)

### 결합도

#### 비주기 의존성 원칙 (ADP)

#### 안정 의존성 원칙 (SDP)

#### 안정적인 추상 원칙 (SAP)

SAP는 안정적인 패키지가 추상

# 코드 분석 도구
https://en.wikipedia.org/wiki/List_of_tools_for_static_code_analysis

위 내용을 다 정리하고 디자인 패턴책을 한권 정리, clean code책 정리 등 번갈아가며 수행
