---
layout: post
title: 안좋은 React Code 작성 방법
subtitle: How to write react bad code.
slug: how-to-write-react-bad-code
date: '2018-04-11 21:20:00 +0900'
categories: blog
author: vomvoru
comments: true
share: true
tags:
    - React
---

안좋은 React 코드 작성법을 경험을 토대로 올리겠습니다.


## render 함수 내부에서 새로운 Component Class 만들기

```js
class TestFail extends Component {
  state = {
    top: 0,
  };

  move = () => {
    this.setState({
      top: this.state.top + 50
    });
  };

  render() {
    const MoveButton = () => (
      <button
        style={{
          display: "block",
          position: "relative",
          transition: "top 1s",
          top: this.state.top
        }}
        onClick={this.move}
      >
        move
      </button>
    );

    return (
      <div>
        <span>Move Button</span>
        <MoveButton />
      </div>
    );
  }
}

export default TestFail;
```

- 위 코드는 CSS의 `transition` 속성이 작동하지 않음
- render 함수 내부에서 **항상 새로운 클래스가 생성**
- 새로운 클래스는 새로운 타입의 컴포넌트이므로 React Diff Algorithm에 따라 다시 리랜더링됨.


### 해결법
- render의 return 에 모두 작성하기
- Component Class 가 아니라 Component 자체를 담기
- Component Class 를 외부 (class 바깥 or 새로운 파일)에 정의하기

### 예시 코드
<iframe src="https://codesandbox.io/embed/p57m3kwl7" style="width:100%; height:500px; border:0; border-radius: 4px; overflow:hidden;" sandbox="allow-modals allow-forms allow-popups allow-scripts allow-same-origin"></iframe>
1. 문제 컴포넌트
2. 해결한 컴포넌트 1
3. 해결한 컴포넌트 2
4. 해결한 컴포넌트 3
