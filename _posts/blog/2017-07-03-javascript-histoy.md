---
layout: post
title: javascript의 역사
subtitle: 브라우저의 역사와 함께보는 javascript의 역사
slug: javascript-histoy
date: '2017-07-03 10:36:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - javascript
---

Javascript는 웹 브라우저에서 동적인 화면을 위해 만들어진 언어이므로 웹 브라우저의 역사를 기준으로 살펴보겠습니다.

우선 웹 브라우저의 역사를 살펴보면 세계최초의 웹 브라우저인 WorldWideWeb은 유럽 입자 물리 연구소 CERN(쎄른)에서 만들어졌습니다.

CERN에서는 실험에서 얻은 데이터들이 매우 다양한 운영체제와 파일 형식으로 저장되어 있어서 특정한 정보를 찾는 것이 매우 어려웠습니다.

이와 같은 이유로 'Tim Berners-Lee'는 다른 종류의 데이터들을 검색할수 있어서 현대 wiki시스템과 비슷한 역할을 하는 인콰이어(ENQUIRE)라는 소프트웨어를 만들었습니다. 

![Tbl](/images/2017/07/Tbl.jpg)

> 팀 버너스 리(Tim Berners-Lee)는 수학에 대한 끊임없는 열정을 가지고 전자제품에 대한 관심도가 높았으며 대학에서 물리학을 전공하고 소프트웨어 고문기사 자격으로 CERN에 입사하였습니다

> 인콰이어(ENQUIRE)는 특정 정보 단위와 다른 정보 단위와의 연관관계를 데이터베이스에 카드형식으로 저장하고, 링크를 통해 서로 다른 카드를 연결하여 다양한 관계를 설명하는데 도움을 주었습니다. [https://en.wikipedia.org/wiki/ENQUIRE](https://en.wikipedia.org/wiki/ENQUIRE)

인콰이어(ENQUIRE) 소프트웨어에서 더 나아가 전세계 사람들이 자료를 자유롭게 공유할수있는 소프트웨어를 구상하였고 WorldWideWeb 이라는 최초의 웹 브라우저를 개발하였습니다.

그리고 WorldWideWeb을 개발하면서 이때 웹을 구성하는 세 가지 핵심요소인 HTML, URL, HTTP가 개발되었습니다.

> WorldWideWeb을 개발한 초기에는 웹사이트가 130개정도에 불과했지만 프로젝트를 오픈소스화 하면서 전세계적으로 급격하게 증가했습니다.

이후에 violaWWW, Erwise(이알와이즈) 등 수많은 초기 브라우저들이 등장했는데 그 중 Mosaic(모자이크) 웹 브라우저는 직관적인 인터페이스와 간단한 설치 등의 장점으로 초기 웹의 성장에 중요한 역할을 하였습니다.

그 후에 기존 Mosaic 웹 브라우저의 개발자들이 모여서 Mosaic Communication 이라는 회사를 설립하고 Netscape(넷스케이프)라는 웹 브라우저를 만들었습니다.

![ns](/images/2017/07/ns.png)

그 후 정적인 웹이 아닌 동적인 웹을 위해 JAVA애플릿이 등장하였습니다. 여기서 Netscape는 더 나아가 JAVA애플릿을 대체하는 웹에 특화된 스크립트 언어를 생각했고, 기존 스크립트 언어인 Perl, Python, Scheme(스킴)등 과는 조금 다르면서 쉽게 개발이 가능한 Mocha라는 스크립트 언어를 10일만에 개발하였습니다.

이후 LiveScript로 이름이 변경되고 최종적으로 Javascript라는 이름으로 개발되었습니다.

> Netscape의 마케팅적 이유로 JAVA의 인기를 화용하기 이와 같은 이름이 지어졌습니다. 

이후 IE등 다른 여러 브라우저에서 기준없이 Javascript에 대한 문법이 만들어져서 Netscape사는 표준화 기구인 ECMA International에 Javascript에 대한 표준화 작업을 요청 하였습니다.

그 결과로 Javascript의 표준 명세인 ECMAScript가 나올수 있었습니다.

![es](/images/2017/07/es.jpg)

~~표준화가 적용되기까지 우리 개발자들은 생고생을(...)~~

현재는 V8엔진등과 같은 기술을 기반으로 서버(nodeJS, ...)와 클라이언트(electron, ...), 어플리케이션(React native, ...)등 여러 분야에서 사용되는 언어가 되었습니다.

<iframe src="//www.slideshare.net/slideshow/embed_code/key/jYJjMRBtQBhl7L" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/ssuserb4d89d/javascript-77469645" title="Javascript&#x27;s History" target="_blank">Javascript&#x27;s History</a> </strong> from <strong><a target="_blank" href="https://www.slideshare.net/ssuserb4d89d">철현 박</a></strong> </div>

참고 자료: 
* [ENQUIRE wikipedia](https://en.wikipedia.org/wiki/ENQUIRE)
* [Javascript wikipedia](https://en.wikipedia.org/wiki/JavaScript)
* [Tim_Berners-Lee wikipedia](https://en.wikipedia.org/wiki/Tim_Berners-Lee)
* [LG CNS-IT Life](http://blog.lgcns.com/1165)