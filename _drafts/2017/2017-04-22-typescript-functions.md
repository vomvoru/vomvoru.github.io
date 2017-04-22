---
layout: post
title: TypeScript functions
subtitle: 함수 타입
slug: typescript-functions
date: '2017-04-22 11:12:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - TypeScript
  - Functions
---

# 소개

Typescript에서 함수 타입을 설정하는 방법은 다음과 같습니다.
```ts
let myAdd: (x: number, y: number)=>number =
    function(x: number, y: number): number { return x+y; };
```

또한 매개변수명에 대해서는 검사하지 않고 순서에 따른 타입을 검사합니다.
```ts
let myAdd: (baseValue:number, increment:number) => number =
    function(x: number, y: number): number { return x + y; };
```

유형 추론(Inferring the types)을 지원하므로, 다음과 같이 생략하여 사용하여도 됩니다.
```ts
// myAdd has the full function type
let myAdd = function(x: number, y: number): number { return  x + y; };

// The parameters 'x' and 'y' have the type number
let myAdd: (baseValue:number, increment:number) => number =
    function(x, y) { return x + y; };
```

# Optional and Default Parameters
lastName을 선택적 매개변수로 사용하기 위해 다음과 같이 코드를 작성하면, TypeScript의 검사에서 통과되지 않습니다.
```ts
function buildName(firstName: string, lastName: string) {
    if(lastName === undefined){
        lastName = 'Smith'
    }
    return firstName + ' ' + lastName;
}

let result1 = buildName('Bob');                  // error, too few parameters
let result2 = buildName('Bob', 'Adams', 'Sr.');  // error, too many parameters
let result3 = buildName('Bob', 'Adams');         // ah, just right
```

TypeScript에서 선택적 매개변수를 사용하려면 매개변수명 끝에 ?를 붙이면 됩니다.
```ts
function buildName(firstName: string, lastName?: string) {
    if(lastName === undefined){
        lastName = 'Smith'
    }
    return firstName + ' ' + lastName;
}

let result1 = buildName("Bob");                  // works correctly now
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");         // ah, just right
```

또한 TypeScript는 기본값을 설정할수 있으며 자동적으로 선택적 매개변수화 됩니다.
```ts
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // works correctly now, returns "Bob Smith"
let result2 = buildName("Bob", undefined);       // still works, also returns "Bob Smith"
let result3 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result4 = buildName("Bob", "Adams");         // ah, just right
```

만약 첫번째 매개변수인 `firstName`을 기본값이 설정된 매개변수로 사용하려면 명시적으로 `undefined`를 전달하여 기본값을 사용할 수 있습니다.

```ts
function buildName(firstName = "Will", lastName: string) {
    return firstName + " " + lastName;
}

let result1 = buildName("Bob");                  // error, too few parameters
let result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
let result3 = buildName("Bob", "Adams");         // okay and returns "Bob Adams"
let result4 = buildName(undefined, "Adams");     // okay and returns "Will Adams"
```

# Rest Parameters
TypeScript에서는 Rest Parameters를 다음과 같이 지원합니다.
```ts
function buildName(firstName: string, ...restOfName: string[]) {
    return firstName + " " + restOfName.join(" ");
}

let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

# this parameter
TypeScript에서는 this에 대한 타입을 지정할 수 있습니다. 아래 코드에서 `createCardPicker` 함수안의 this는 타입이 지정되지 않은 any 타입입니다.
```ts
let deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```
이 문제를 해결하기 위해 fake parameter인 (명시적인) this 매개변수를 제공할수 있습니다.
인터페이스를 몇개 추가하고 명시적인 this 매개변수를 사용한 예시 코드는 다음과 같습니다.
```ts
interface Card {
    suit: string;
    card: number;
}
interface Deck {
    suits: string[];
    cards: number[];
    createCardPicker(this: Deck): () => Card;
}
let deck: Deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    // NOTE: The function now explicitly specifies that its callee must be of type Deck
    createCardPicker: function(this: Deck) {
        return () => {
            let pickedCard = Math.floor(Math.random() * 52);
            let pickedSuit = Math.floor(pickedCard / 13);

            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

# Overloads
매개변수 구성에 따라 다른 타입의 리턴값을 반환하는 함수의 경우에 Typescript는 다음과 같이 작성할 수 있습니다.
```ts
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        let pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        let pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

let myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

Overloads 목록이 아닌 다른 매개 변수 유형을 호출하면 오류가 발생합니다.
```ts
// Error: Argument of type '"test"' is not assignable to parameter
let pickedCard3 = pickCard('test');
```
