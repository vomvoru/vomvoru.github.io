---
layout: post
title: 좋은 소프트웨어
subtitle: ISO/IEC 9126
slug: software-quality
date: '2017-05-23 16:05:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - 소프트웨어의 품질
    - programming
    - ISO/IEC 9126
    - ISO/IEC 25010
---

# ISO/IEC 25010(ISO/IEC 9126)
* 소프트웨어 품질의 특성을 정의하고 품질 평가의 Metrics를 정의한 국제 표준
* 사용자 관점에서 본 소프트웨어 품질 특성에 대한 표준
* 사용자, 개발자 모두에게 소프트웨어 제품의 품질을 평가하기 위한 지침
* 소프트웨어 품질을 객관적이고 계량적으로 평가할 수 있는 기본적인틀을 마련

소프트웨어 품질 Metrics는 주특성 8개로 구분되며 이러한 주특성은 다시 부특성으로 세분화된다. 부특성은 내부 Metrics나 외부 Metrics에 의해 측정이 가능하다. Quality in use(사용 품질)는 사용자 관점의 품질을 말한다.


```plantuml
@startuml

package "Quality Characteristics" {
    package "Internal and External Quality" {
        package "Functional Suitability" {
            class "completeness"{
                기능성숙도
            }
            class "correctness"
            class "functional\nappropriateness"
        }
        package "Reliability" {
            class "maturity"
            class "fault\ntolerance"
            class "recoverability"
            class "availability"
        }
        package "Usability" {
            class "appropriateness\nrecognizability"
            class "learnability"
            class "operability"
            class "user\ninterface\naesthetics"
            class "user\nerror\nprotection"
            class "accessibility"
        }
        package "Performance\nEfficiency" {
            class "time\nbehaviour"
            class "resource\nutilization"
            class "capacity"
        }
        package "Maintainability" {
            class "analyzability"
            class "modifiability"
            class "reusability"
            class "testability"
            class "modularity"
        }
        package "Portability" {
            class "adaptability"
            class "installability"
            class "replaceability"
        }
        package "Compatibility" {
            class "co-existence"
            class "interoperability"
        }
        package "Security" {
            class "confidentiality"
            class "integrity"
            class "non-repudiation"
            class "accountability"
            class "authenticity"
        }
    }
    package "Quality in use" {
        class "effectiveness"
        class "productivity"
        class "safety"
        class "satisfaction"
    }
}

"effectiveness" -[hidden]-> "productivity"
"productivity" -[hidden]-> "safety"
"safety" -[hidden]-> "satisfaction"

"completeness" -[hidden]-> "correctness"
"correctness" -[hidden]-> "functional\nappropriateness"

"maturity" -[hidden]-> "fault\ntolerance"
"fault\ntolerance" -[hidden]-> "recoverability"
"recoverability" -[hidden]-> "availability"

"appropriateness\nrecognizability" -[hidden]-> "learnability"
"learnability" -[hidden]-> "operability"
"operability" -[hidden]-> "user\ninterface\naesthetics"
"user\ninterface\naesthetics" -[hidden]-> "user\nerror\nprotection"
"user\nerror\nprotection" -[hidden]-> "accessibility"

"time\nbehaviour" -[hidden]-> "resource\nutilization"
"resource\nutilization" -[hidden]-> "capacity"

"analyzability" -[hidden]-> "modifiability"
"modifiability" -[hidden]-> "reusability"
"reusability" -[hidden]-> "testability"
"testability" -[hidden]-> "modularity"

"adaptability" -[hidden]-> "installability"
"installability" -[hidden]-> "replaceability"

"co-existence" -[hidden]-> "interoperability"

"confidentiality" -[hidden]-> "integrity"
"integrity" -[hidden]-> "non-repudiation"
"non-repudiation" -[hidden]-> "accountability"
"accountability" -[hidden]-> "authenticity"

"functional\nappropriateness" -[hidden]----> "analyzability"
"availability" -[hidden]---> "adaptability"
"accessibility" -[hidden]-> "co-existence"
"capacity" -[hidden]----> "confidentiality"

hide empty fields
hide empty methods
hide circle
@enduml
```



https://en.wikipedia.org/wiki/ISO/IEC_9126
http://www.splex.co.kr/isoiec-9126-25010
https://www.iso.org/standard/35733.html

http://ryudwig.tistory.com/entry/SW-%ED%92%88%EC%A7%88-%ED%8A%B9%EC%84%B1-ISO-9126
http://solarixer.blogspot.kr/2007/12/iso-9126.html


# Functionality
A set of attributes that bear on the existence of a set of functions and their specified properties. The functions are those that satisfy stated or implied needs.

* Suitability
* Accuracy
* Interoperability
* Security
* Functionality compliance

# Reliability
A set of attributes that bear on the capability of software to maintain its level of performance under stated conditions for a stated period of time.

* Maturity
* Fault tolerance
* Recoverability
* Reliability compliance

# Usability
A set of attributes that bear on the effort needed for use, and on the individual assessment of such use, by a stated or implied set of users.

* Understandability
* Learnability
* Operability
* Attractiveness
* Usability compliance

# Efficiency
A set of attributes that bear on the relationship between the level of performance of the software and the amount of resources used, under stated conditions.

* Time behaviour
* Resource utilization
* Efficiency compliance

# Maintainability
A set of attributes that bear on the effort needed to make specified modifications.

* Analyzability
* Changeability
* Stability
* Testability
* Maintainability compliance

# Portability
A set of attributes that bear on the ability of software to be transferred from one environment to another.

* Adaptability
* Installability
* Co-existence
* Replaceability
* Portability compliance
