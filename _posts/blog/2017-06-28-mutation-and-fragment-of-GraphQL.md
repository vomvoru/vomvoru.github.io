---
layout: post
title: GraphQL의 Mutation과 Fragment
excerpt: 수정작업을 하는 GraphQL문 Mutation
slug: mutation-and-fragment-of-GraphQL
date: '2017-06-28 01:30:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - GraphQL
---

본 내용은 [GraphQL 공식 홈페이지](http://graphql.org/)의 내용을 수정한 것이다.  
Relay에 대한 설명은 이곳(준비중)에 있다.  
본 글은 시리즈이다. [이곳](../about-graphql)을 참고하자.

# 개요

GraphQL에서 Mutation은 데이터 수정작업을 하는 GraphQL문을 의미한다.

Fragment는 재사용이 뛰어난 Query문의 파편(?)이라고 생각하면 됩니다. 또한 query type이 union 혹은 interface와 같이 여러 type이 있을수 있을때 Fragment의 타입 질의기능을 활용하여 특정 타입에만 적용되는 Query문을 작성할수 있습니다.

우선 구현방법(Schema)는 제쳐두고 사용방법(Query & Mutation)에 대해 살펴보자. 다만, 아래 예시에 사용된 schema가 궁금하다면 grpahql-js로 구현된 [여기](https://github.com/graphql/graphql.github.io/blob/e7b61aa37cbdf5972f895113a88c1459cf43aca4/site/_core/swapiSchema.js)를 참고하면 된다.(비교해 가면서 보면 더 빠른 이해가 가능하다.)

## Mutations

Mutation은 서버또는 데이터베이스의 데이터를 변화시키는 행위에 대한 구문을 의미한다. REST에서 모든 요청이 서버의 데이터를 변화시킬수 있지만 관습에 따라 데이터 변화에 GET 요청을 사용하지 않는 것이 좋습니다. GraphQL도 마찬가지로 `schema` 구현에 따라 어떤 Query문이든 서버 데이터를 변화시킬수 있지만 그러한 작업은 Mutation으로 정의하는것이 좋습니다.

query와 마찬가지로 Mutation이 객체 유형을 반환하면 중첩필드를 요청할수 있습니다. 이는 Mutation이후에 새로운 상태를 가져올때 유용할수 있습니다. 아래에 간단한 예시가 있습니다.


Request GraphQL
```graphql
mutation CreateReviewForEpisode($ep: Episode!, $review: ReviewInput!) {
  createReview(episode: $ep, review: $review) {
    stars
    commentary
  }
}
```

input variables
```json
{
  "ep": "JEDI",
  "review": {
    "stars": 5,
    "commentary": "This is a great movie!"
  }
}
```

Response JSON
```json
{
  "data": {
    "createReview": {
      "stars": 5,
      "commentary": "This is a great movie!"
    }
  }
}
```

### Mutation vs Query
Mutation는 수정 행위를 Query는 읽기 행위를 request하는 구문이라는 명시적인 차이점 이외에 동작적인 부분에서 확실한 차이점이 있습니다. 

**Query 필드가 병렬로 실행되는 동안 Mutation 필드는 순차적으로 실행됩니다.** 

즉, 하나의 요청에서 두 개의 Mutation를 보내면 순차적으로 실행되어 경쟁 상태가되지 않도록 합니다. (GraphQL의 구현 조건입니다.)


## Fragments

동일한 구조 반복하여 가지는 `field`를 정의할때 `fragment` 구문을 사용하면 편리합니다.

Request GraphQL
```graphql
{
  leftComparison: hero(episode: EMPIRE) {
    ...comparisonFields
  }
  rightComparison: hero(episode: JEDI) {
    ...comparisonFields
  }
}

fragment comparisonFields on Character {
  name
  friends {
    name
  }
}
```

Response JSON
```json
{
  "data": {
    "leftComparison": {
      "name": "Luke Skywalker",
      "friends": [
        {
          "name": "Han Solo"
        },
        {
          "name": "Leia Organa"
        },
        {
          "name": "C-3PO"
        },
        {
          "name": "R2-D2"
        }
      ]
    },
    "rightComparison": {
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

위의 예시는 두 `hero`의 데이터를 동일한 구조의 `field`를 가져올때 `fragment` 구문을 활용한 것 입니다.


## Inline Fragments
위 GraphQL은 다음과 같이 Fragment를 익명으로 작성할수 있습니다.
```graphql
{
  leftComparison: hero(episode: EMPIRE) {
    ...on Character {
      name
      friends {
        name
      }
    }
  }
  rightComparison: hero(episode: JEDI) {
    ...on Character {
      name
      friends {
        name
      }
    }
  }
}
```

이러면 Fragment의 장점인 재사용성이 없어지는데, 이렇게 사용하는 경우는 `on Character` 부분 즉 Fragment의 타입 질의기능을 사용할때 이렇게 사용하기도 합니다.

## Fragment의 타입 질의기능
많은 type system과 마찬가지로 GraphQL의 schema에는 interface와 union type이 있습니다. 여기서는 interface만을 예시로 들겠습니다. 만약 interface를 반환하는 field를 query하는 경우 Fragment의 타입 질의기능을 사용하여 interface를 구현한 각각의 `concrete type`을 구별하여 처리해야 합니다. 다음 예시를 참고하세요.

Request GraphQL
```graphql
query HeroForEpisode($ep: Episode!) {
  hero(episode: $ep) {
    name
    ... on Droid {
      primaryFunction
    }
    ... on Human {
      height
    }
  }
}
```

Input variables
```json
{
  "ep": "JEDI"
}
```

Response JSON
```json
{
  "data": {
    "hero": {
      "name": "R2-D2",
      "primaryFunction": "Astromech"
    }
  }
}
```

이 query에서 `hero` 필드는 `$ep` 인수에 따라 `Human` 또는 `Droid` 일 수있는 `Character interface`타입을 반환합니다. Fragment를 사용하지 않으면 `name`과 같이 `Character interface`에 있는 값만 요청할 수 있습니다. Fragment의 타입 질의기능을 사용하여 위와 같이 타입별로 처리할수 있습니다.

위와 관련된 `Character interface` 와 `Human` 타입, `Droid` 타입에 대한 schema를 참고하려면 아래를 참고하세요.

Schema
```graphql
interface Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
}

type Human implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  starships: [Starship]
  totalCredits: Int
}

type Droid implements Character {
  id: ID!
  name: String!
  friends: [Character]
  appearsIn: [Episode]!
  primaryFunction: String
}
```

## Meta fields

위와같은 방법을 이용하여 데이터를 받으면 받은 데이터가 `Character interface`인 것은 알수 있지만 `Human` 타입인지 `Droid`타입인지 구별을 할 수 있는 방법이 필요합니다. query의 모든 지점에서 meta field인 `__typename`을 활용하면 해당 지점에서 객체 타입의 이름을 가져올 수 있습니다.  

Request GraphQL
```graphql
{
  search(text: "an") {
    __typename
    ... on Human {
      name
    }
    ... on Droid {
      name
    }
    ... on Starship {
      name
    }
  }
}
```

Response JSON
```json
{
  "data": {
    "search": [
      {
        "__typename": "Human",
        "name": "Han Solo"
      },
      {
        "__typename": "Human",
        "name": "Leia Organa"
      },
      {
        "__typename": "Starship",
        "name": "TIE Advanced x1"
      }
    ]
  }
}
```

GraphQL은 그외 다양한 Meta Field를 제공하며 [여기 Spec](http://facebook.github.io/graphql/#sec-Schema-Introspection)에서 확인할수 있습니다. 제공되는 Meta Field를 나열하면 다음과 같이 Schema에 대한 정보를 제공해 주는 Meta field가 대부분 입니다. `__Schema`, `__Type`, `__TypeKind`, `__Field`, `__InputValue`, `__EnumValue`, `__Directive `