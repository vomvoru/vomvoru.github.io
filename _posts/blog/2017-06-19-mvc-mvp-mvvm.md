---
layout: post
title: mvc mvp mvvm
subtitle: mv...
slug: mvc-mvp-mvvm
date: '2017-06-22 15:29:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - mvc
    - mvp
    - mvvm
---


# MVC, MVP, MVVM
기본적으로 단일책임 원칙하에 기능을 분리하면서 생긴 개념  
각 구조별 구현 차이는 의존도 및 복잡도 차이  
  
아래에서 데이터 컨트롤은 데이터의 추가/수정/삭제/변화 감지를 할수있는 인터페이스 사용을 의미한다  

---

## MVC

detail

```plantuml
class Model {
    데이터 인터페이스 제공
}

class View {
    보이는 부분 담당
}

class Controller{
    View 이벤트 등록
    View 이벤트 행동 설정
    Model에서 데이터 컨트롤
}

Controller *-left-> Model : 데이터 컨트롤
Controller *-right-> View : 이벤트 등록, 행동 설정

hide empty fields
hide empty methods
hide circle
```

* Model: 데이터를 주거나 변경하는 역할으로 View나 Controller에 의존되지 않습니다. (Controller에서 명령을 받습니다)
* View: 사용자에게 보여주는 부분에 대한 책임을 맡습니다. MVC에서 View는 단순히 보이는 부분만을 담당하고 사용자에게 이벤트를 받는 것 조차 Controller에 맡겨집니다.
* Controller: View에서 사용자의 행동에 대한 이벤트를 받고 Model에 적용한다. Model의 변화를 받고 View에 적용한다

### 장점
* Model과 View가 어디에도 종속적이지 않고 분리될 수 있다.

### 단점
* Controller가 Model과 View에 단단히 의존되면서 Model과 View에 변화가 일어나면 Controller도 변경해야 한다
* 시간이 지날수록 Controller에 더 많은 코드가 모이게 된다.

---

## MVP

detail

```plantuml
class Model {
    데이터 인터페이스 제공
}

class View {
    보이는 부분 담당
}

Interface ViewInterface {
    View 이벤트 등록
}

class Presenter{
    ViewInterface 이벤트 행동 설정
    Model에서 데이터 컨트롤
}

View .left.|> ViewInterface
Presenter *-left-> Model : 데이터 컨트롤
Presenter *-right-> ViewInterface : 이벤트 행동 설정

hide empty fields
hide empty methods
hide circle
```

simple

```plantuml
class Model {
    데이터 인터페이스 제공
}

class View {
    보이는 부분 담당
    이벤트 등록
}

class Presenter{
    View 이벤트 행동 설정
    Model에서 데이터 컨트롤
}

Presenter *-left-> Model : 데이터 컨트롤
Presenter *-right-> View : 이벤트 행동 설정

hide empty fields
hide empty methods
hide circle
```

* Model: MVC의 Model과 같습니다 (Presenter 명령을 받습니다)
* View: MVC의 View와는 다르게 이벤트를 받는것에 대한 책임을 추가로 맡습니다. 이벤트에 대한 행동은 Presenter에서 설정되며 View는 그 인터페이스를 담당합니다.
* Presenter: MVC의 Model과 같지만, View의 Interface에 의존한다.

### 장점
* MVC보다 깔끔한 형태로 View의 Interface에 의존하면서 Presenter가 View에 덜 의존적입니다.

### 단점
* MVC보다 깔끔한 형태지만 단순한 프로그램에서는 MVC보다 더 복잡해질수 있습니다.
* 시간이 지날수록 Presenter에 더 많은 코드가 모이게 된다.

---

## MVVM

detail

```plantuml
class Model {
    데이터 인터페이스 제공
}

class View {
    보이는 부분 담당
    ModelViewInterface로 템플릿 적용
    이벤트 등록 및 이벤트 행동에 ModelViewInterface 사용
}

Interface ModelViewInterface {
    데이터 인터페이스 제공
}

class ModelView{
    Model에서 데이터 컨트롤
}

ModelView .right.|> ModelViewInterface
ModelView *-left-> Model : 데이터 컨트롤
View *-left-> ModelViewInterface : 데이터 컨트롤

hide empty fields
hide empty methods
hide circle
```

simple

```plantuml
class Model {
    데이터 인터페이스 제공
}

class View {
    보이는 부분 담당
    ModelViewInterface로 템플릿 적용
    이벤트 등록 및 이벤트 행동에 ModelViewInterface 사용
}

class ModelView{
    Model에서 데이터 컨트롤
    데이터 인터페이스 제공
}

ModelView *-left-> Model : 데이터 컨트롤
View *-left-> ModelView : 데이터 컨트롤

hide empty fields
hide empty methods
hide circle
```

* Model: MVC의 Model과 같습니다 (View Model에서 명령을 받습니다)
* View: View Model의 인터페이스에 의존하여 데이터를 보여주거나 함수를 실행하게 됩니다. View Model를 감지하는 옵저버 패턴이 이용됩니다.
* View Model: view에 변화를 알려줄 옵저버블 데이터를 준비합니다. 또한 뷰가 모델에 이벤트를 전달할수 있도록 hook을 준비합니다. 그러면서도 View Model이 View에 종속되지 않습니다.