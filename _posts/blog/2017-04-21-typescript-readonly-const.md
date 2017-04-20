---
layout: post
title: typescript readonly vs const
subtitle: 
slug: typescript-readonly-vs-const
date: '2017-04-21 02:27:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - javascript
  - ES5
  - const
  - readonly
---

readonly는 객체 속성을 상수화 시킬 수 있으며 const는 변수를 상수화 시킬 수 있습니다. 다음은 const가 속성을 상수화 시킬 수 없다는 것을 보여주는 에시입니다.  
const는 지정된 변수 만을 상수화 시킵니다. 속성이 어떻게 되던가 관심밖이지요.  
eadonly는 interface의 속성을 설정하는 것이므로 객체 속성의 상수화만 가능합니다.

```ts
const constString: string = 'const value'

constString = 'change value'; // error
```

```ts
interface fooBar {
  foo: any,
  bar: any
}

interface ReadonlyfooBar {
  readonly foo: any,
  readonly bar: any
}

const constObject: fooBar = {
  foo: 1,
  bar: 'str'
}

let interfaceObject: ReadonlyfooBar = {
  foo: 1,
  bar: 'str'
}


constObject.foo = 2; // pass
constObject.bar = 'rts'; // pass
constObject = { // error!!
  foo: 2,
  bar: 'ttt'
}

interfaceObject.foo = 2; // error!!
interfaceObject.bar = 'rts'; // error!!
interfaceObject = { // pass
  foo: 2,
  bar: 'ttt'
}

```
