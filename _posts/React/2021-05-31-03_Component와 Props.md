---
layout: post
author: ainochi-kor
title: React/03. Component와 Props
---

컴포넌트를 통해 UI를 재사용 가능한 개별적인 여러 조각으로 나누고, 각 조각을 개별적으로 살펴볼 수 있다.

개념적으로 컴포넌트는 JavaScript 함수와 유사하다. "props"라고 하는 임의의 입력을 받은 후, 화면에 어떻게 표시되는지를 기술하는 React 엘리먼트를 반환한다.

---

## **함수 컴포넌트와 클래스 컴포넌트**

컴포넌트를 정의하는 가장 간단한 방법은 JavaScript 함수를 작성하는 것이다.

``` js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

ES6 class를 사용하여 컴포넌트 정의

``` js
class Welcom extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

- React 관점에서는 두 가지 유형의 컴포넌트는 동일하다.
- class는 몇가지 추가 기능이 있다.

## **컴포넌트 렌더링**

DOM

``` js
const element = <div />;
```

React 엘리먼트는 사용자 정의 컴포넌트로도 나타낼 수 있다.

``` js
const element = <Welcome name="Sara" />;
```

React가 사용자 정의 컴포넌트로 작성한 엘리먼트를 발견하면 JSX 어트리뷰트와 자식을 해당 컴포넌트에 단일 객체로 전달한다.  
이 객체를 "props"라고 한다. 

렌더링 예시

``` js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;

ReactDOM.render(
  element,
  document.getElementId('root');
);
```
이 예시 절차

1. \< Welcom name name="Sara" /\> 엘리먼트로 ReactDOM.render()를 호출한다.
2. React는 {name: 'Sara'}를 props로 하여 Welcome 컴포넌트를 호출한다.
3. Welcom 컴포넌트는 결과적으로 \<\h1 h1\> Hello, Sara/<\/h1\> 엘리먼트를 반환합니다.
4. ReactDOM은 \<\h1\>Hello, Sara\<\/h1> 엘리먼트와 일치하도록 DOM을 효율적으로 업데이트합니다. 

>주의: 컴포넌트 이름은 항상 대문자로 시작합니다.
> React는 소문자로 시작하는 컴포넌트를 DOM 태그로 처리한다.
> 컴포넌트의 경우 범위안에 같은 이름의 클래스가 존재해야 한다.


---  

## **컴포넌트 합성**

- 컴포넌트는 자신의 출력에 다른 컴포넌트를 참조할 수 있다. 
- 모든 세부 단계에서 동일한 추상 컴포넌트를 사용할 수 있음을 의미한다.
- React 앱에서는 버튼, 폼, 다이얼로그, 화면 등의 모든 것들이 흔히 컴포넌트로 표현됩니다.

``` js
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

function App() {
  return (
    <div>
      <Welcom name="Sara" />
      <Welcom name="Cahel" />
      <Welcom name="Edite" />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementId('root');
)
```

- 일반적으로 새 React 앱은 최상위에 단일 App 컴포넌트를 가지고 있다.
- 기존 앱에 React를 통합하는 경우에는 Button과 같은 작은 컴포넌트부터 시작해서 뷰 계층의 상단으로 올라가면서 점진적으로 작업해야 할 수 있다.