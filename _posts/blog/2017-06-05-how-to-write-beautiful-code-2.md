---
layout: post
title: 좋은 코드 작성하기 2
excerpt: 의존도와 결합도
slug: how-to-write-beautiful-code-2
date: '2017-06-05 12:53:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - 소프트웨어의 품질
    - 응집도
    - 결합도
---

# 높은 응집도 낮은 결합도(High Cohesion, Loose Coupling)

이는 모듈 혹은 클래스, 더 나아가서 함수 작성시에 적용되는 사항이다.
일관성을 위해 모듈로 통합해서 글을 작성하였다.

> 응집도: 모듈내 상호 작용의 정도  
> 결합도: 두 모듈간의 상호 작용의 정도 (의존도)

**응집도가 낮다면**
* 여러 책임(기능)을 한 모듈이 지고 있다

**결합도가 높다면**
* 한 모듈의 수정에 영향받는 의존된 모듈이 다수이다.

**결국 응집도가 낮고 결합도가 높다면**
* 이해가 힘들고
* 변화에 예민하다 (1개의 기능변화에 다수의 모듈이 변경되야한다)

다음은 결합도를 낮은 순으로, 응집도를 높은 순으로 설명한 것이다.

```plantuml
class "내용 결합도" {
  Content coupling
  --
  하나의 모듈이 다른 모듈의 내부 동작을 수정하거나 내부 동작에 의존
  ==
  캡슐화에 의한 은닉화가 필요하다.
}

class "공통 결합도" {
  Common coupling
  --
  두 개의 모듈이 같은 글로벌 데이터를 공유(전역변수 공유)
  ==
  캡슐화한 모듈을 하나 더 만들어서 데이터를 제공한다.
}

class "외부 결합도" {
  External coupling
  --
  두 개의 모듈이 외부에서 도입된 데이터 포맷, 통신 프로토콜, 또는 디바이스 인터페이스를
  공유할 때 발생한다. 이는 기본적으로 외부 툴이나 디바이스와의 통신과 관련이 있다.
  ==
  외부에서 도입된 api에 맞는 모듈을 하나 더 만들어서 해결(어댑터 패턴 형태)
}

class "제어 결합도" {
  Control coupling
  --
  하나의 모듈이 다른 모듈으로 무엇을 해야하는지에 대한 정보를 넘겨줌으로써
  다른 모듈의 흐름을 제어
  ..
  여러 모듈이 한개(혹은 그 이상)의 기능에 대한 역할(책임)을 짊어지고 있기 때문에
  기능 변경시 caller, callee 모두 수정이 필요하다.
  ==
  오버로딩 혹은 1개 함수를 책임별로 분할하여 해결
}

class "스탬프 결합도" {
  Stamp coupling
  --
  복합 자료구조(구조체)의 값을 매개변수로 받는 경우
  ..
  자료구조의 형태가 바뀌면 수정이 필요하다.
  ==
  외부로 공개된 함수의 매개변수를 기본 자료형으로 변경하여 해결
}

class "자료 결합도" {
  Data coupling
  --
  기본 자료형의 값을 매개변수로 받는경우
  ..
  매개변수의 형태가 바뀌면 수정이 필요하다.
}

class "메시지 결합도" {
  Message coupling
  --
  가장 낮은 결합도이다. 이는 state decentralization을 통해 이룰 수 있고,
  컴포넌트 간의 통신은 파라메터나 메시지 패싱을 통해 이루어져 한다.
  (위키백과 참조)
}

"내용 결합도" <|-- "공통 결합도"
"공통 결합도" <|-- "외부 결합도"
"외부 결합도" <|-- "제어 결합도"
"제어 결합도" <|-- "스탬프 결합도"
"스탬프 결합도" <|-- "자료 결합도"
"자료 결합도" <|-- "메시지 결합도"

note right of "내용 결합도"
  module A{
    main(){
      B.setData(123)
    }
  }
  module B{
    data = 1;
    setData(data)
  }
end note

note right of "공통 결합도"
  dbTableNmae = 'ABC'
  //모듈 A, B가 전역변수 dbTableNmae 을 공유
  module A{
    deletePost()
  }
  module B{
    deleteUser()
  }

  // 해결방법: 캡슐화된 모듈 DB를 만들어서 사용
end note

note right of "외부 결합도"
  // 두 A, B 모듈은 외부에 공개된 API를 통한 통신으로
  // getData함수와 getName함수를 구현한다.  
  module A{
    getData()
  }
  module B{
    getName()
  }

  // 해결방법: 캡슐화된 외부 통신용 모듈을 만들어서 사용
end note

note right of "제어 결합도"
  module A{
    main(){
      B.local(true)
    }
  }
  module B{
    local(isExec){ //isExec 변수에 의해 흐름이 제어 됨
      if(isExec){

      }else{

      }
    }
  }
end note

note right of "스탬프 결합도"
  module canvas{
    struct point {x, y}
    drawPoint(point xy) // point라는 구조체가 의존됨
  }
  module B{
    add(a, b, c){
      canvas.point xy = {1, 3}
      canvas.drawPoint(xy)
    }
  }

  // 해결방법: 의존되는 데이터 구조체등을 기본데이터로 변경
  // drawPoint(x, y){
  //  이 안에서는 point 구조체를 사용해도 무관
  // }
  // 혹은 인터페이스를 이용
end note

note right of "자료 결합도"
  module A{
    add(a, b)
    // call by value
  }
  module B{
    c = add(a, b)
  }
end note

note right of "메시지 결합도"
  redux에서 사용되는 기법인듯 하나, 정확한 정의를 찾지 못하였다.
  외부 결합도와 헷갈리고, 정보가 없다
end note

hide empty fields
hide empty methods
hide circle
```

```plantuml
class "기능적 응집도" {
  Functional Cohesion
  --
  모듈 내부의 모든 기능이 단일한 목적을 위해 수행되는 경우
  강한 단일 지향헉인 목표(목적)을 가지는 모듈이여야 한다.
  단 한가지의 책임을 가져야 한다.
}

class "순차적 응집도" {
  Sequential Cohesion
  --
  모듈 내에서 한 활동으로 부터 나온 출력값을 다른 활동이 사용할 경우
}

class "통신적 응집도" {
  Communication Cohesion
  --
  동일한 입력과 출력을 사용하여 다른 기능을 수행하는 활동들이 모여있을 경우
}

class "절차적 응집도" {
  Procedural Cohesion
  --
  항상 특정 실행 순서를 따름에 따라 그륩화 하는 것
}

class "시간적 응집도" {
  Temporal Cohesion
  --
  연관된 기능이라기 보단 특정 시간에 처리되어야 하는 활동들을 한 모듈에서 처리할 경우
}

class "논리적 응집도" {
  Logical Cohesion
  --
  유사한 성격을 갖거나 특정 형태로 분류되는 처리 요소들이 한 모듈에서 처리되는 경우
}

class "우연적 응집도" {
  Coincidental Cohesion
  --
  모듈 내부의 각 구성요소들이 연관이 없을 경우
}

"기능적 응집도" <|-- "순차적 응집도"
"순차적 응집도" <|-- "통신적 응집도"
"통신적 응집도" <|-- "절차적 응집도"
"절차적 응집도" <|-- "시간적 응집도"
"시간적 응집도" <|-- "논리적 응집도"
"논리적 응집도" <|-- "우연적 응집도"

note right of "기능적 응집도"
  XML 어휘 분석 모듈
  거래 기록 읽기 모듈
end note

note right of "순차적 응집도"
  module DataControl{
    getData()
    deleteData(data)
    // getData 함수에서 출력된 data값을
    // deleteData함수에서 입력으로 사용.
  }
end note

note right of "통신적 응집도"
  module nameControl{
    // DB에 저장된 name
    print(name)
    select(name)
  }
end note

note right of "절차적 응집도"
  module fs{
    checkAuth()
    openFile()
    // openFile은 대체적으로 checkAuth함수로 체크하고 실행해야됨.
  }
end note

note right of "시간적 응집도"
  module init{
    init()
    memset()
    // 특정시간대(초기실행)에 실행되어야 되는 함수들 모임
  }
end note

note right of "논리적 응집도"
  module input{
    keyboardInput()
    mouseInput()
  }
end note

note right of "우연적 응집도"
  module A{
    addNumber(num1, num2)
    displayTerminal(text)
  }
end note

hide empty fields
hide empty methods
hide circle
```
