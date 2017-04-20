---
layout: post
title: TypeScript Basic Types
subtitle:
slug: typescript-basic-types
date: '2017-04-19 20:50:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
---

boolean, number, string,  
array, tuple, enum,  
any, void, undefined, null, never  

# boolean
```ts
let isDone: boolean = false;
```

# number
```ts
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
```

# string
```ts
let color: string = 'blue';
```

# array
```ts
let list: number[] = [1, 2, 3];
let genericList: Array<number> = [1, 2, 3];
```

# tuple
```ts
let x: [string, number]; // [string, number, string|number ... ]
x = ['hello', 10]; // OK
x = ['hello', 'world']; // Error
x = ['hello', 10, 'world']; // OK
x = ['hello', 10, 11]; // OK

console.log(x[0].substr(1)); // OK
console.log(x[1].substr(1)); // Error, 'number' does not have 'substr'
console.log(x[2].toString()) // OK, 'string' and 'number' both have 'toString'
console.log(x[2].substr(1)) // Error, 'string' and 'number' don't both have 'toString'
```

# enum
```ts
enum Color {Red = 1, Green, Blue}
let color: Color = Color.Green;
```

# any
```ts
let notSure: any = 4;
notSure = "maybe a string instead";
notSure = false;
notSure.substr(1); // OK
notSure.anythingMethod(); // OK
```

# void
```ts
function warnUser(): void {
  alert("This is my warning message");
}
let unusable: void = undefined;
unusable = null;
```

# undefined
```ts
let u: undefined = undefined;
```

# null
```ts
let n: null = null;
```

# never
```ts
function error(message: string): never {
    throw new Error(message);
}

function infiniteLoop(): never {
    while (true) {
    }
}
```

# type scope
![typescript type scope](/images/2017/04/typescriptTypeScope.png)
