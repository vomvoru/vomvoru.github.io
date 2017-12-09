---
layout: post
title: typescript의 모듈 해석 방식
excerpt: typescript module resolution
slug: typescript-module-resolution
date: '2017-12-09 20:43:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

```ts
import { a } from "moduleA";
```

다음과 같은 구문이 있을때 typescript 컴파일러는 모듈을 옵션에 따라 `Classic` 또는 `Node` 방식으로 찾습니다.
(.ts, .tsx, .d.ts 혹은 allowJs옵션이 켜져있다면 .js)

# Relative vs. Non-relative

## Relative
```ts
import Entry from "./components/Entry";
import { DefaultHeaders } from "../constants/http";
import "/mod";
```

## Non-relativ
```ts
import * as $ from "jquery";
import { Component } from "@angular/core";
```

# Classic vs Node

## Classic
Typescript의 기본 해석 방식이였으며, 현재는 Node 방식을 주로 사용한다. 이 옵션은 이전버전과의 호환성때문에 남아있다.

### Relative
```ts
// /root/src/folder/A.ts 파일
import { b } from "./moduleB"
```

1. `/root/src/folder/moduleB.ts`
1. `/root/src/folder/moduleB.d.ts`

### Non-relativ
```ts
// /root/src/folder/A.ts 파일
import { b } from "moduleB"
```

1. `/root/src/folder/moduleB.ts`
1. `/root/src/folder/moduleB.d.ts`
1. `/root/src/moduleB.ts`
1. `/root/src/moduleB.d.ts`
1. `/root/moduleB.ts`
1. `/root/moduleB.d.ts`
1. `/moduleB.ts`
1. `/moduleB.d.ts`


## Node
node.js의 모듈 해석 방식을 모방한 방식으로 다른점은 더 많은 확장자(.ts, .tsx)를 확인한다는 점이다.

### Relative
```ts
// /root/src/folder/A.ts 파일
import { b } from "./moduleB"
```

1. `/root/src/moduleB.(ts, tsx, d.ts)`
1. `/root/src/moduleB/package.json (if it specifies a "types" property)`
1. `/root/src/moduleB/index.(ts, tsx, d.ts)`

### Non-relativ
```ts
// /root/src/folder/A.ts 파일
import { b } from "moduleB"
```

1. `/root/src/node_modules/moduleB.(ts, tsx, d.ts)`
1. `/root/src/node_modules/moduleB/package.json (if it specifies a "types" property)`
1. `/root/src/node_modules/moduleB/index.(ts, tsx, d.ts)`

1. `/root/node_modules/moduleB.(ts, tsx, d.ts)`
1. `/root/node_modules/moduleB/package.json (if it specifies a "types" property)`
1. `/root/node_modules/moduleB/index.(ts, tsx, d.ts)`

1. `/node_modules/moduleB.(ts, tsx, d.ts)`
1. `/node_modules/moduleB/package.json (if it specifies a "types" property)`
1. `/node_modules/moduleB/index.(ts, tsx, d.ts)`

복잡해보이지만, 자세히보면 부모디덱토리를 차례차례 검사하는 단순한 과정이다.

# 관련 옵션
## moduleResolution
` Classic | Node ` 방식을 설정할수 있다.

## module
module 옵션에 따라 moduleResolution 옵션이 설정된다.

` module == AMD | System | ES2015 ` 이면 `moduleResolution = Classic`
나머지는 `moduleResolution = Node` 로 설정된다.

## Base URL & paths
`baseUrl`은 모듈을 Non-relativ으로 가져올 때 `baseUrl`에 상대적인 경로로 모듈을 가져오게 됩니다.  
즉, 로드되는 부모 모듈에 관계없이 모듈을 로드 할 위치를 설정할수 있는 메커니즘을 제공합니다.

`paths`는 가져올 모듈의 위치를 미리 설정할수 있습니다. 또한 `baseUrl`를 기준으로 설정됩니다.  
예를들어 jquery모듈을 항상 slim 버전을 가져오고 싶을때에는 다음과 같이 설정하면 됩니다.

`paths`는 `*`를 사용할수 있는데 그러면 좀 더 정교한 맵핑이 가능합니다. 다음과 같은 파일 구조가 있습니다. 
```
projectRoot
├── folder1
│   ├── file1.ts (imports 'folder1/file2' and 'folder2/file3')
│   └── file2.ts
├── generated
│   ├── folder1
│   └── folder2
│       └── file3.ts
└── tsconfig.json
```
그리고 `file1.ts` 파일에서 `folder1/file2` 과 `folder2/file3` 모듈 2개를 `import` 한다고 한다면 다음과 같은 설정을 하여 해결할수 있습니다.
```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "*": [
        "*",
        "generated/*"
      ]
    }
  }
}
```
그리고 다음과 같은 순서로 모듈을 검색합니다.

### import ‘folder1/file2’
1. 패턴 `*` 가 일치하므로 와일드 카드에 전체모듈명을 캡쳐한다.
1. 첫번째 치환 규칙을 적용하여 치환: `*` -> `folder1/file2`
1. `baseUrl`과 결합 -> `projectRoot/folder1/file2.ts`
1. `projectRoot/folder1/file2.ts` 파일이 존재하므로 종료

### import ‘folder2/file3’
1. 패턴 `*` 가 일치하므로 와일드 카드에 전체모듈명을 캡쳐한다.
1. 첫번째 치환 규칙을 적용하여 치환: `*` -> `folder2/file3`
1. `baseUrl`과 결합 -> `projectRoot/folder2/file3.ts`
1. `projectRoot/folder2/file3.ts` 파일이 존재하지 않으므로 다음단계로
1. 두번째 치환 규칙을 적용하여 치환: `generated/*` -> `generated/folder2/file3`
1. `baseUrl`과 결합 -> `projectRoot/generated/folder2/file3.ts`
1. `projectRoot/generated/folder2/file3.ts` 파일이 존재하므로 종료

## rootDirs
`rootDirs` 를 이용하면 가상 디덱토리를 설정할수 있습니다.  
가상 디덱토리는 여러 디렉토리를 모두 결합하여 하나의 단일 디렉토리로 보는 것을 의미합니다.

다음과 같은 파일 구조이며 import가 다음과 같이 이루어진다고 봅시다.

```
src
└── views
    └── view1.ts (imports './template1')
    └── view2.ts

generated
└── templates
        └── views
            └── template1.ts (imports './view2')
```

이 경우  `src/views` 디덱토리와 `generated/templates/views` 디덱토리가 합쳐진다고 가정하면 더 편하게 모듈을 불러올수 있습니다. 이를 가상디덱토리 개념으로 설정한다면 다음과 같이 설정하여 사용이 가능합니다.

```json
{
  "compilerOptions": {
    "rootDirs": [
      "src/views",
      "generated/templates/views"
    ]
  }
}
```

## Tracing module resolution

모듈을 해석하는 부분에서 디버깅이 필요하다면 `traceResolution` 옵션을 이용하여 모듈 해석에 대한 추적로그를 볼수 있습니다.

대략적으로 다음과 같은 로그가 나오게 됩니다.
```
======== Resolving module 'typescript' from 'src/app.ts'. ========
Module resolution kind is not specified, using 'NodeJs'.
Loading module 'typescript' from 'node_modules' folder.
File 'src/node_modules/typescript.ts' does not exist.
File 'src/node_modules/typescript.tsx' does not exist.
File 'src/node_modules/typescript.d.ts' does not exist.
File 'src/node_modules/typescript/package.json' does not exist.
File 'node_modules/typescript.ts' does not exist.
File 'node_modules/typescript.tsx' does not exist.
File 'node_modules/typescript.d.ts' does not exist.
Found 'package.json' at 'node_modules/typescript/package.json'.
'package.json' has 'types' field './lib/typescript.d.ts' that references 'node_modules/typescript/lib/typescript.d.ts'.
File 'node_modules/typescript/lib/typescript.d.ts' exist - use it as a module resolution result.
======== Module name 'typescript' was successfully resolved to 'node_modules/typescript/lib/typescript.d.ts'. ========
```

## noResolve

`noResolve` 컴파일러 옵션은 컴파일러에 명령 줄에서 전달되지 않은 파일을 컴파일에 추가하지 않도록 지시합니다.

```ts
// app.ts
import * as A from "moduleA" // OK, 'moduleA' passed on the command-line
import * as B from "moduleB" // Error TS2307: Cannot find module 'moduleB'.
```
다음과 같은 `app.ts` 파일을 아래 방식으로 컴파일시
```
tsc app.ts moduleA.ts --noResolve
```
`moduleA`, `moduleB`가 모두 존재해도 아래와 같은 에러가 발생합니다.
```
Error TS2307: Cannot find module 'moduleB'.
```