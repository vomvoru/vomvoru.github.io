---
layout: post
title: 코드 스타일링
subtitle: 형식 맞추기
slug: code-styling
date: '2017-04-20 13:51:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
  - Clean Code
---
본 글은 [Clean Code 도서][1]의 내용을 참고하여 작성하였습니다.

//TODO 내용 정리와 함께, 내 생각을 적어야겠다.

# 형식을 맞추는 목적
* 코드 형식은 의사소통의 일환며, 의사소통은 전문 개발자의 1차적인 의무이다.
* 코드가 변경될 확률은 높고, 좋은 가독성은 변경의 어려움을 덜어준다.
* 코드는 변경되지만, 맨 처음 잡아놓은 형식은 대부분 변경되지 않는다.

# 파일의 적절한 행길이를 유지하라
![file size analysis](/images/2017/04/cleancode-file-size.PNG)[^2]
* 일반적으로 큰 파일보다, 작은 파일이 이해하기 쉽다.
* 500줄을 넘지 않고 대부분 200줄 정도인 파일로도 커다란 시스템을 구축할수 있다

# 고차원에서 저차원으로 (숲에서 나무로) (추상화의 순서)
* 소스 파일 첫부분은 고차원 개념과 알고리즘을 배치한다.
* 아래로 내려갈수록 세세한 개념을 배치한다.
* 호출되는 함수를 호출하는 함수보다 나중에 배치한다.

# 개념을 빈 행으로 구분하라
* 일련의 행 묶음은 완결된 생각 하나를 표현한다.
* 빈행은 새로운 개념을 시작한다는 시각적 단서다.
* 반대로 서로 밀집한 코드 행은 되도록 세로로 붙여서 작성하라.

# 변수 선언
* 변수는 사용하는 위치에 최대한 가까이 선언한다.
* 루프를 제어하는 변수는 흔히 루프 문 내부에서 선언한다.

```js
let arr = [0, 1, 2]
let len = arr.length
for(var i = 0 ; i < len ; i++){
  console.log(arr[i])
}
```

# 인스턴스 변수 선언
* 인스턴스 변수는 클래스의 맨 처음 혹은 마지막에 선언한다.
* 중요한점은 잘 알려진 위치(C++에서는 마지막, JAVA는 처음)에 선언하는것이다.

# 공백과 밀집도
```js
function getGreetings(name, gender) {
  let genderPerfix
  if(gender == MEN){
    genderPerfix = 'Mr'
  }else{
    genderPerfix = 'Ms'
  }
  return `hello ${genderPerfix}. ${name}`
}
```
* 공백은 밀집한 개념과 느슨한 개념을 표현한다.
* 할당문은 왼쪽 요소와 오른쪽 요소가 분명히 나뉜다.
* 함수 이름과 괄호 사이는 공백을 넣지 않는다. 함수와 인수는 서로 밀집하기 때문이다. 공백을 넣으면 한 개념이 아니라 별개로 보인다.
* 함수의 인수는 공백으로 분리한다. 쉼표를 강조해 인수가 별개라는 사실을 보여주기 위해서다.
* 연산자 우선순위를 강조하기 위해서도 공백을 사용한다.

```js
function determinant(a, b, c) {
  return b*b - 4*a*c
}
```

# 그 외 가독성을 해치는 것
* { } 가 없는 빈 루프문 (되도록 빈 루프문을 쓰지 말고, 어쩔수 없다면 { }를 꼭 작성하자)

```js
while(dis.read(buf, 0, readBufferSize) != -1); // warning
while(dis.read(buf, 0, readBufferSize) != -1){ } // good
```

* 들여쓰기 무시하기
* 가로 정렬 가로 정렬이 필요할 정도로 목록이 길다면, 쪼개야된다는 의미이다.

```java
public class FItNessExpediter implements ResponseSender
{
  private   Socket         socket;
  private   InputStream    input;
  private   OutputStream   output;
  private   Request        request;
  private   Response       response;
  private   FitNessContext context;
  protected long           requestParsingTimeLimit;
  private   long           requestProgress;
  private   long           requestParsingDeadline;

}
```


[1]: http://www.insightbook.co.kr/%EB%8F%84%EC%84%9C-%EB%AA%A9%EB%A1%9D/ppp-%EC%8B%9C%EB%A6%AC%EC%A6%88/%ED%81%B4%EB%A6%B0%EC%BD%94%EB%93%9C "Clean Code"

[^2]: 클린코드 그림5.1에서 가져온 그래프이다. 프로젝트별(가로축) 파일 길이(세로 축)를 나타낸다. 상자는 평균 위/아래로 (표준편차/2)를 나타낸다.
