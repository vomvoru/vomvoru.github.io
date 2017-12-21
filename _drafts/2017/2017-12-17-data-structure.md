---
layout: post
title: TypeScript Type Guard
slug: typescript-type-guard
date: '2017-12-12 21:20:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - TypeScript
---

# 종류와 정의
- 선형구조 : 데이터를 순서있게 한 방향으로 나열한 자료구조
    - 배열 : 한가지 타입 데이터를 나열하여 저장한 자료구조
    - 튜플 : 두가지 이상 타입을 미리 지정한 순서(A타입, B타입, A타입, B타입 ...)로 나열하여 저장한 자료구조
    - 연결리스트(linked list)
        - 단순 연결리스트 : 각 노드가 데이터, 다음 노드 주소를 가진 자료구조
        - 이중 연결리스트 : 각 노드가 데이터, 다음 노드 주소, 이전 노드 주소를 가진 자료구조
        - 원형 연결리스트 : 마지막 노드가 첫번째 노드의 주소를 가진 자료구조 (이중 or 단순)
    - 스택 : (LIFO, push, pop) 마지막 노드를 빼고 넣을수 있는 자료구조
    - 큐 : (FIFO, queue, dequeue) 마지막 노드에 넣고 처음 노드를 뺄수 있는 자료구조
        - 링크드 큐 : 일반적인 큐
        - 원형 큐 : 큐의 앞 빈공간 낭비를 줄이는 자료구조
    - 덱 : 처음과 마지막 노드를 빼고 넣을수 있는 자료구조
- 비선형구조
    - 그래프 : 연결되어 있는 데이터간의 관계를 표현하는 자료구조
        - 방향 그래프 : 두 정점을 연결하는 간선에 방향이 있는 그래프
        - 무방향 그래프 : 두 정점을 연결하는 간선에 방향이 없는 그래프
        - 완전 그래프 : 각 정점에서 모든 정점을 연결한 그래프(방향, 무방향)
        - 가중 그래프 : 두 정점을 연결하는 간선에 가중치를 할당한 그래프
        - 트리 : 방향 그래프의 일종으로 자신을 가리키는 노드가 1개 이하인 그래프
            - 일반트리
            - 이진트리 : 노드를 2개 이하만 가리킬수 있는 그래프
    - 해시테이블 : 데이터를 해시함수에 입력하여 나온 주소에 데이터를 저장하는 자료구조


# 비교

## 배열
- 저장할 데이터의 최대 개수가 정해져 있음
- 중간노드의 삭제/삽입 작업이 적음
- 인덱스를 이용한 빠른 검색이 필요
- 구현이 간단하다

## 리스트
- 저장할 데이터의 개수가 정해져 있지 않음
- 중간노드의 삭제/삽입 작업이 많음
- 검색작업이 적음
- 구현이 복잡하다

## 원형 큐 VS 링크드 큐
- 원형 큐는 크기가 제한되어 있고 , 링크드 큐는 제한되어 있지 않음(메모리 범위)
- 원형 큐는 속도가 빠름
- 링크드 큐는 구현이 더 쉽고 직관적임(링크드 리스트가 구현되어 있을시)


# 활용

## 스택
- LIFO 특징을 활용할수 있는 상황
- 되돌리기 기능
- 후위표기법 (234++)

## 큐
- 대기열
- 터널링
- pipe 구조
- 스트리밍 서비스에서 버퍼링시

## 덱
- 스케줄링
- 우선순위 조절
- 맨 앞에 있는것을 뒤로 넣는 등

## 트리(이진트리)
- 수식 계산
- 검색


# 해시테이블

http://luyin.tistory.com/191