---
layout: post
title: 좋은 코드 작성하기 1
excerpt: 소프트웨어 품질
slug: how-to-write-beautiful-code-1
date: '2017-05-22 12:53:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - 소프트웨어의 품질
    - ISO/IEC 9126
    - ISO/IEC 25010
---

# 개요
이 글을 쓰는 목적은 다음과 같다.
* 개발자로서 좋은 코드를 작성하겠다는 목적
    * 여러 종류의 디자인 패턴의 장단점에 대한 분석을 체계적으로 정리
    * 작성된 (혹은 작성할) 코드의 설계와 분석능력을 키워서 코드의 질적 향상
    * 머리속에 여기저기 흩어진 좋은 코드 또는 설계 작성법에 대한 지식을 정리


**좋은 코드** 와 **좋은 설계** 의 목적은 소프트웨어 품질을 높이기 위함이다. 그러므로 소프트웨어 품질 기준에 대한 이야기를 시작으로 좋은 코드와 설계의 기준 및 방법(규칙)에 대해 정리해 보도록 하자.

# 소프트웨어 품질
소프트웨어 품질 국제 표준인 ISO/IEC 25010 일부를 참고하여 정리하면 다음과 같다.

```plantuml
@startuml

class "내부/외부 품질\nInternal and External Quality"

"내부/외부 품질\nInternal and External Quality" --> "기능적합성\nFunctional Suitability"
"내부/외부 품질\nInternal and External Quality" --> "신뢰도\nReliability"
"내부/외부 품질\nInternal and External Quality" --> "유용성\nUsability"
"내부/외부 품질\nInternal and External Quality" --> "수행 효율성\nPerformance\nEfficiency"
"내부/외부 품질\nInternal and External Quality" --> "유지 보수성\nMaintainability"
"내부/외부 품질\nInternal and External Quality" --> "이식성\nPortability"
"내부/외부 품질\nInternal and External Quality" --> "호환성\nCompatibility"
"내부/외부 품질\nInternal and External Quality" --> "보안성\nSecurity"

hide empty fields
hide empty methods
hide circle
@enduml
```

이러한 요소들중 개발자가 특히 관심가져야 할 요소들을 정리하였다.

```plantuml
@startuml

Title "내부/외부 품질\nInternal and External Quality"

package "신뢰도\nReliability" {
    class "maturity"{
        성숙도
        --
        시스템이 표준 작동하에서 신뢰도에
        대한 요구를 충족시키는 정도
    }
    class "fault\ntolerance"{
        결점 완화
        --
        하드웨어 혹은 소프트웨어의 결점이 존재하도
        의도한대로 작동하는가
        ..예시..
        네트워크가 불완전할때의 처리
        구 브라우저를 사용할때의 처리
    }
    class "recoverability"{
        복구 가능성
        --
        중단 및 실패가 발생시 시스템이 원하는 상태로
        재설정된 데이터를 복구할 수 있는 정도
    }
    class "availability"{
        가용성
        --
        서버와 네트워크, 프로그램등이 정상적으로 사용가능한 정도
        기동률과 비슷한 의미로 수식으로 설명할경우
        사용시간을 전체 사용시간으로 나눈 값을 말한다
    }
}
hide empty fields
hide empty methods
hide circle
@enduml
```
```plantuml
@startuml
package "수행 효율성\nPerformance\nEfficiency" {
    class "time\nbehaviour"{
        시간 반응성
        --
        응답 및 처리시간과 처리율
        (시간 복잡도와 관련이 깊다)
    }
    class "resource\nutilization"{
        자원 활용
        --
        자원(메모리 등)의 사용율
        (공간 복잡도와 관련이 깊다)
    }
    class "capacity"{
        기억 용량
        --
        사용자수, 통신 대역폭, 데이터베이스가
        저장할수있는 아이템의 양에 대한 최대한계율
    }
}
hide empty fields
hide empty methods
hide circle
@enduml
```
```plantuml
@startuml
package "유지보수성\nMaintainability" {
    class "analysability"{
        분석성
        --
        소프트웨어의 결함이나 고장의 원인 혹은
        변경될 부분들에 대한 진단을 가능하게 하는 능력
        ..예시..
        테스트코드를 작성해두고 그 테스트결과를
        continuous integration 서비스로
        주기적인 점검을 하는 방법이 있다.
    }
    class "modifiability"{
        수정가능성
        --
        시스템이 장애의 발생 없이 혹은 기존 품질을
        떨어뜨리지 않고 효율적으로 수정될 수 있는 정도
    }
    class "reusability"{
        재사용성
        ..예시..
        module, function, component
        등의 재사용 방법이 있다.
    }
    class "testability"{
        테스트가능성
        ..예시..
        테스트코드의 유무와 관련이 있다
    }
    class "modularity"{
        모듈성
    }
}
hide empty fields
hide empty methods
hide circle
@enduml
```
```plantuml
@startuml
package "보안성\nSecurity" {
    class "confidentiality"{
        기밀성
        --
        시스템이 조건(사용자등)에 따라 접근이 허용된
        데이터에만 접근이 가능하도록 하는것
    }
    class "integrity"{
        무결성
        --
        원래의 정보 또는 신호가 전송/저장/변환 이후에도
        동일함을 유지하는것
        --
        https의 적용 및 Checksum, CRC등의 기법
        MD5, 해쉬함수등의 적용등을 할수 있고
        데이터베이스의 무결성 유지도 비슷한 의미이다.
    }
    class "non-repudiation"{
        부인방지
        --
        송수신 거래사실을 이후에 증명 가능하게 함으로서
        거래 사실을 부인 못하게 하는 공증과 같은 보안기능
    }
    class "accountability"{
        책임추적성
        --
        사용자 식별 및 활동 감사 추적
    }
    class "authenticity"{
        인증성
        --
        사용자의 진위 확인
    }
}

hide empty fields
hide empty methods
hide circle
@enduml
```

위의 내용에서 알 수 있듯이 소프트웨어 품질은 여러 요소들을 고려해야 한다.
특히 개발자가 집중해야되는 사항은 신뢰도, 성능(수행효율성), 유지보수성, 보안성등이 있다.
