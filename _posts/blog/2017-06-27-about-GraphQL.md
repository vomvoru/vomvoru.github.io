---
layout: post
title: GraphQL 이란?
subtitle: A query language for your API
slug: about-GraphQL
date: '2017-06-27 23:57:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - GraphQL
---

본 내용은 [GraphQL 공식 홈페이지](http://graphql.org/)의 내용을 수정한 것입니다.  

# 개요

GraphQL은 facebook에서 만든 **A query language for your API** 로 공식 홈페이지에서 설명하고 있습니다. 

여기서 API란 서버상에서 제공하는 API를 의미하며 이는 **클라이언트와 서버의 통신언어** 로도 생각할수 있습니다.  
또한 GraphQL은 하나의 통신 규약이라고 볼 수 있으며 이를 여러 언어에서 구현한 코드는 [여기](http://graphql.org/code/)에서 볼 수 있습니다.

(아래의 장점과 단점은 공식 홈페이지에 설명된 내용과 개인적인 생각을 추가하였다.)

GraphQL의 장점은 다음과 같습니다. 

* 요청메세지가 값이 없는 JSON과 비슷하며 받는 데이터는 JSON형태이다.
    * **요청에 따른 응답데이터 구조를 예측할수 있다.**
    * **직관적이다.**
* 단일요청으로 원하는 데이터를 한번에 가져올수 있다.
    * **REST API의 N+1 Problem을 해결** 할 수 있다.
* GraphQL은 type system을 지원한다.
    * 개발 혹은 사용시 좀 더 명확한 오류메세지를 제공할 수 있다.
* GraphiQL 등의 강력한 도구(통신 테스트 도구 등)를 사용할 수 있다.
* 확장이 용이하다.


GraphQL의 단점은 다음과 같습니다.

* **러닝커브** 가 조금 있다.
* GraphQL의 type을 정의한 코드, mutation을 정의한 코드가 추가되면서 **단순한 App에서는 코드가 더 복잡해진다.**
* 캐싱 기능의 구현이 복잡하다(하지만 대부분의 라이브러리가 지원한다.)
* 출시한지 얼마 안되어 안정성 문제가 생실수 있다.(하지만 facebook과 github등에서 사용한다고 한다.)
* JSON이 선호되며 JSON을 사용하지 않으면 불편할수 있다. [Spec](http://facebook.github.io/graphql/#sec-JSON-Serialization)

## 용어 정의

* **Query** : 읽기작업을 하는 GraphQL문을 의미한다.
    * **field** : query에 있는 값(속성)
* **Mutation** : 데이터 수정작업을 하는 GraphQL문을 의미한다.
* **schema** : Query와 Mutation의 retrun type과 arguments type 및 custom type, custom interface, enum type 등과 input value의 default값 등을 정의한 코드를 의미한다.
* **resolver** : schema에서 정의된 Query와 Mutation의 구조에 맞추어 retrun type과 arguments type에 맞추어 설정한 코드를 의미한다.

참고로 위 구성요소중 resolver만 GraphQL를 구현한 Server Library에 의존되고 나머지는 GraphQL에 의존됩니다.

즉, **Query, Mutation은 schema에서 구조가 정의되고. resolver에서 행동이 정의되며 schema에서 정의된 구조에 맞춰서, 클라이언트에서 Query, Mutation문으로 request하고 서버에서 JSON으로 response 합니다.**

## 목차

0. about : 현재 글
1. [query](../query-of-GraphQL)
2. [mutation & fragment](../mutation-and-fragment-of-GraphQL)
