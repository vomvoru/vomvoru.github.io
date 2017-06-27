---
layout: post
title: GraphQL의 query
excerpt: 읽기작업을 하는 GraphQL문 query
slug: query-of-GraphQL
date: '2017-06-28 01:00:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - GraphQL
---

본 내용은 [GraphQL 공식 홈페이지](http://graphql.org/)의 내용을 수정한 것입니다. 
본 글은 시리즈입니다. [이곳](../about-GraphQL)을 참고하세요.

**grpahql-js로 구현된 [schema(클릭)](https://github.com/graphql/graphql.github.io/blob/e7b61aa37cbdf5972f895113a88c1459cf43aca4/site/_core/swapiSchema.js)를 참고하며 읽으시면 이해가 좀더 빨리됩니다.**

# 개요

GraphQL에서 Query는 읽기작업을 하는 GraphQL문을 의미합니다. 

## Fields

GraphQL에서 Query의 기본적인 사용법은 다음과 같습니다.

Request GraphQL
```graphql
{
  hero {
    name
  }
}
```

다음과 같은 request를 보낸다면 response는 다음과 같습니다

Response JSON
```json
{
  "data": {
    "hero": {
      "name": "R2-D2"
    }
  }
}
```

앞의 예에서 hero의 이름을 요청하였지만, friends 즉, object를 요청할수도 있습니다. 단, `object` 자체만을 요청할수는 없고(`object`로 요청문이 끝날수는 없다) `scalar` 혹은 `enum` 타입으로 요청문이 끝나야 합니다. 

Request GraphQL
```graphql
{
  hero {
    name
    # GraphQL은 이렇게 주석을 추가할수 있습니다.
    friends {
      name # 이 값은 scalar 타입이므로 가능하다.
    }
  }
}
```

Response JSON
```json
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

즉 다음과 같은 형태의 GraphQL은 오류정보를 response 합니다.

Request GraphQL
```graphql
{
  hero {
    name
    friends # friends는 scalar 타입이 아닙니다.
  }
}
```

schema문에서 Query 타입과 Character 인터페이스를 보시면 더 잘 알수 있습니다. 아래는 schema문의 일부분을 가져온 것 입니다.


```graphql

//...

type Query {
  hero(episode: Episode): Character
  reviews(episode: Episode!): [Review]
  search(text: String): [SearchResult]
  character(id: ID!): Character
  droid(id: ID!): Droid
  human(id: ID!): Human
  starship(id: ID!): Starship
}

//...

interface Character {
  # The ID of the character
  id: ID!
  # The name of the character
  name: String!
  # The friends of the character, or an empty list if they have none
  friends: [Character]
  # The friends of the character exposed as a connection with edges
  friendsConnection(first: Int, after: ID): FriendsConnection!
  # The movies this character appears in
  appearsIn: [Episode]!
}

// ...

```
`Query` 타입 필드의 hero를 보면 `Character` 인터페이스 형식이라는것을 알 수 있고 `Character` 인터페이스의 `friends`를 보시면 `[Character]` 즉, `scalar` 타입이 아닌 `Character`의 배열인 것을 알수 있습니다. 그리고 `Character` 인터페이스의 `name`은 `String`으로 `scalar` 타입인것을 알 수 있습니다.

아래 내용도 위와 같이 [schema코드](https://github.com/graphql/graphql.github.io/blob/e7b61aa37cbdf5972f895113a88c1459cf43aca4/site/_core/swapiSchema.js)를 참고하여 보시면 됩니다.

추가로 `scalar` 타입은 `int`, `float`, `String`, `Boolean`, `ID` 이 있습니다. [여기](http://facebook.github.io/graphql/#sec-Scalars)에서 좀 더 자세히 알아볼수 있습니다. 참고로 `ID` 타입은 Cache작업에 주로 이용됩니다.


## Arguments

schema 에서 작성이 되었다면 GraphQL에서도 인수를 전달할수 있습니다.

Request GraphQL
```graphql
{
  human(id: "1000") {
    name
    height
  }
}
```

Response JSON
```json
{
  "data": {
    "human": {
      "name": "Luke Skywalker",
      "height": 1.72
    }
  }
}
```

scalar필드에도 인수를 전달하여 서버에서 데이터 변환을 구현할수도 있습니다. 물론 schema에 작성이 되어있어야 합니다.

Request GraphQL
```graphql
{
  # 전달하는 id값도 바꿔보았습니다.
  human(id: "1001") {
    name
    height(unit: FOOT)
  }
}
```

Response JSON
```json
{
  "data": {
    "human": {
      "name": "Darth Vader",
      "height": 6.6272968
    }
  }
}
```

여기서는 언급하지 않지만 schema 작성시 인수를 사용할수 있도록 정의할때 기본값, 필수값, 선택값등을 설정할수도 있습니다.

## Aliases

별칭을 사용하여 결과값의 이름(key)을 바꿀수 있습니다. 

이 기능을 이용하여 동일한 `field`에 다른 인수를 사용하여 이름에 대한 충돌이 없이 response를 받을수 있습니다.

Request GraphQL
```graphql
{
  empireHero: hero(episode: EMPIRE) {
    name
  }
  jediHero: hero(episode: JEDI) {
    name
  }
}
```

Response JSON
```json
{
  "data": {
    "empireHero": {
      "name": "Luke Skywalker"
    },
    "jediHero": {
      "name": "R2-D2"
    }
  }
}
```

## Operation name & Variables

이제까지 query의 이름을 생략하여 사용하였지만 생략하지 않고 사용한다면 다음과 같은 이점이 있습니다. 

* Operation name을 정의하여 모호하지 않은 코드를 작성할수 있다.
* 디버깅 할때 Operation name이 들어간 로그등을 참고하면 코드의 추적이 더 쉬워진다.
* Variable 기능을 활용할수 있다.

GraphQL은 클라이언트에서 GraphQL문에대한 인수를 정의하여 query문에서 사용할수 있는 Variable 기능을 제공합니다.


Request GraphQL
```graphql
query HeroNameAndFriends($episode: Episode) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

위의 graphql문과 함께 아래의 json 데이터가 입력된다.

Input variables
```json
{
  "episode": "JEDI"
}
```

이에 대한 결과는 다음과 같다.

Response JSON
```json
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

위와 같이 Variable기능을 활용한 구문은 재사용성이 높아서 다른곳에서도 유연하게 사용이 가능하게 된다.  
참고로 위 `Episode` type은 `enum` 이다.

### Default variables

다음과 같이 기본값을 설정할수도 있습니다.

Request GraphQL
```graphql
query HeroNameAndFriends($episode: Episode = "JEDI") {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

### Required variables

`$name: type!`를 사용하여 필수값을 설정할수도 있습니다. 

가장 처음 Variables 예시는 `!`를 쓰지 않았기 때문에 필수값이 아니라서 NULL이 허용됩니다.

Request GraphQL
```graphql
query HeroNameAndFriends($episode: Episode!) {
  hero(episode: $episode) {
    name
    friends {
      name
    }
  }
}
```

### Available types for variables

variables에 사용 가능한 Type은 `scalar`, `enum`, `input object type` 이 있습니다. 

여기서 `input object type`은 schema에서 정의하는 Type으로 예시는 다음과 같다.

schema에 다음과 같이 `input object type`인 `ReviewInput`가 정의되어 있고

```graphql
input ReviewInput {
  stars: Int!
  commentary: String
}
```

`ReviewInput`을 사용하는 `createReview` 이라는 `mutation`이 다음과 같이 schema에 정의되어 있다면

```graphql
type Mutation {
  createReview(episode: Episode, review: ReviewInput!): Review
}
```

`input object type`을 variables기능에서 `mutation`과 함께 다음과 같이 사용이 가능하다.

Request GraphQL
```graphql
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
}
```

### Directives
GraphQL에는 질의문도 있습니다.

variables를 사용하여 동적 query문을 생성하는 방법에 대해서 위에서 설명하였지만 query문의 구조를 동적으로 변경하는 방법이 필요할수도 있습니다. core GraphQL사양에 포함된 다음과 같은 질의문을 활용하면 구조를 동적으로 변경할수 있습니다.

* `@include(if: Boolean)` if인수가 true일때 둘러싼 GraphQL문을 포함한다.
* `@skip(if: Boolean)` if인수가 true일때 둘러싼 GraphQL문을 제외한다.

GraphQL을 구현하는 코드에 따라 완전히 새로운 Directive를 정의할 수도 있습니다. 위 Directive를 사용한 예시는 다음과 같습니다.

Request GraphQL
```graphql
query Hero($episode: Episode, $withFriends: Boolean!) {
  hero(episode: $episode) {
    name
    friends @include(if: $withFriends) {
      name
    }
  }
}
```

`withFriends`입력 데이터가 `false` 일때

Input variables
```json
{
  "episode": "JEDI",
  "withFriends": false
}
```

Response JSON
```json
{
  "data": {
    "hero": {
      "name": "R2-D2"
    }
  }
}
```

`withFriends`입력 데이터가 `true` 일때

Input variables
```json
{
  "episode": "JEDI",
  "withFriends": true
}
```

Response JSON
```json
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "friends": [
        {
          "name": "Luke Skywalker"
        },
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        }
      ]
    }
  }
}
```

본 글은 시리즈입니다. [이곳](../about-GraphQL)을 참고하세요.