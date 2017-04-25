---
layout: post
title: TypeScript의 타입추론
subtitle:
slug: typescript-type-inference
date: '2017-04-25 13:29:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

# 기본
Typescript는 지정된 타입이 없을때 자동으로 타입을 유추해주는 'type inference'기능이 있습니다.

```ts
let x = 3;
```

위 코드에서 변수 x는 number로 타입이 유추되어 자동설정 됩니다. 이러한 추론은 변수나 속성을 초기화하고, 매개 변수 기본값을 설정하고, 함수 반환 유형을 결정할 때 (return) 발생합니다.

# Best common type
배열과 같이 여러가지 값의 타입에서 타입유추는 값마다의 타입중
