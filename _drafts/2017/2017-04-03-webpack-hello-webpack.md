---
layout: post
title: webpack 강의 1편
subtitle: install webpack
slug: webpack-guide-1-Hello-wepback
date: 2017-04-02 10:05:01 +0900
categories: blog
author: vomvoru
comments: true
share: true
tags:
- webpack
---

# webpack?
* 하나의 파일로 js 파일을 번들화 가능
* ES2015, ES2016등으로 코드를 작성가능
* 코드의 preprocess 지원 (less, sass, typescript, jsx ...)
* HMR의 공식 지원
* javascript로 image, css 등의 모든 유형의 파일을 관리 가능
* 코드 분리 기능
* 그 외 이 글에서 다루지 못한 고급기능들

![webpack-concepts](/images/2017/04/webpack-concepts.png)

# Webpack install
## Local install
``` bash
npm i --save-dev webpack
```

## Global install
``` bash
npm i -g webpack
```
> 프로젝트별로 webpack의 버전이 다를 수 있기때문에 권장되지 않음

## Bleeding Edge
webpack의 실험적인 기능을 사용하기 위해 설치하는 방법.
실험적인 기능으로, 불안정하다.
``` bash
npm i webpack/webpack#<tagname/branchname>
```
