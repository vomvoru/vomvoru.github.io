---
layout: post
title: MVC MVP MVVM
subtitle: MVX
slug: mvc-mvp-mvvm
date: '2017-12-24 23:17:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
---

# MVC, MVP, MVVM
기본적으로 단일책임 원칙하에 기능을 분리하면서 생긴 개념

## MVC
![mvc](/images/2017/12/mvc.png)

* Model: 데이터를 주거나 변경하는 역할으로 View나 Controller에 의존되지 않습니다. (Controller에서 명령을 받습니다)
* View: 사용자에게 보여주는 부분에 대한 책임을 맡습니다. MVC에서 View는 단순히 보이는 부분만을 담당하고 사용자에게 이벤트를 받는 것 조차 Controller에 맡겨집니다.
* Controller: View에서 사용자의 행동에 대한 이벤트를 받고 Model에 적용한다. Model의 변화를 받고 View에 적용한다

### 장점
* Model과 View가 어디에도 종속적이지 않고 분리될 수 있다.

### 단점
* Controller가 Model과 View에 단단히 의존되면서 Model과 View에 변화가 일어나면 Controller도 변경해야 한다
* 시간이 지날수록 Controller에 더 많은 코드가 모이게 된다.

## MVP
MVC + ViewInterface
![mvp](/images/2017/12/mvp.png)
* Model: MVC의 Model과 같습니다 (Presenter 명령을 받습니다)
* View: MVC의 View와는 다르게 이벤트를 받는것에 대한 책임을 추가로 맡습니다. 이벤트에 대한 행동은 Presenter에서 설정되며 View는 그 인터페이스를 담당합니다.
* Presenter: MVC의 Controller과 같지만, View의 Interface에 의존한다.

### 장점
* MVC보다 유지보수성이 좋고 View의 Interface에 의존하면서 Presenter가 View에 덜 의존적입니다.

### 단점
* MVC보다 깔끔한 형태지만 단순한 프로그램에서는 MVC보다 더 복잡해질수 있습니다.
    * View의 Interface를 구현해야 되기 때문
* MVC와 마찬가지로 시간이 지날수록 Presenter에 더 많은 코드가 모이게 된다.

## MVVM
MVC + ViewModelInterface + (view가 실행할 ViewModel의 메소드)hook
![mvvm](/images/2017/12/mvvm.png)
* Model: MVC의 Model과 같습니다 (View Model에서 명령을 받습니다)
* View: View Model의 인터페이스에 의존하여 데이터를 보여주거나 함수를 실행하게 됩니다. View Model를 감지하는 옵저버 패턴이 이용됩니다.
* View Model: view에 변화를 알려줄 옵저버블 데이터를 준비합니다. 또한 뷰가 모델에 이벤트를 전달할수 있도록 hook을 준비합니다. 그러면서도 View Model이 View에 종속되지 않습니다.

### 장점
* 유지보수성이 좋다

### 단점
* 구헌이 복잡하고 코드양이 많아질수 있다


# 결론
- MVC는 MVP, MVVM보다 유지보수성은 적지만, 규모가 작은 앱에서는 좀 더 간결한 코드를 작성할수 있다 
- MVP, MVVM은 유지보수성이 괜찬으나, 코드가 Presenter, View Model에 모이게 된다.
- 코드가 모이게 되는 위 문제를 해결하기 위해 Action, Dispatcher로 분리한 flux패턴이 나오게 된다
    - 좀더 단방향성을 높여서 데이터 흐름의 파악이 원할하게 함
    - 코드의 양은 좀 더 늘어나게 된다
