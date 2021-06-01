---
layout: post
author: ainochi-kor
title: React/04. State and Lifecycle
---

이 페이지는 React 컴포넌트 안의 state와 생명주기에 대한 개념을 소개.

[시계 예시]

``` js
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocalTimeString()}.</h2>
    </div>
  );

  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

스스토 업데이트 하는 타이머

``` js
function Clock(props) {
  return (
    <div>
      <h1>Hello, world</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

## **함수에서 클래스로 변환하기**

1. React.Component를 확장하는 동일한 이름의 ES6 class를 생성한다.
2. render()라고 불리는 빈 메서드를 추가한다.
3. 함수의 내용을 render() 메서드 안으로 옮긴다.
4. render() 내용 안에 있는 props를 this.props로 변경한다.
5. 남아있는 빈 함수 선언을 삭제한다.

``` js
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

Clock은 함수가 아닌 클래스로 정의된다.

render 메서드는 업데이트가 발생할 때마다 호출되지만 DOM 노드로 \<Clock /\>을 렌더링하는 경우 Clock 클래스의 단일 인스턴스만 사용된다. 이것은 state와 생명주기 메서드와 같은 부가적인 기능을 사용할 수 있게 해준다.

---

## **클래스에 로컬 State 추가하기**
세 단계에 걸쳐서 date를 props에서 state로 이동

1. render() 메서드 안에 있는 this.props.date를 this.state.date로 변경

``` js
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

2. 초기 this.state를 지정하는 class constructor를 추가.

``` js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

3. \<Clock /\> 요소에서 date props을 삭제

``` js
ReactDOM.render(
  \<Clock /\>,
  document.getElementById('root');
)
```

[결과]

``` js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

---

## **생명주기 메서드를 클래스에 추가하기**

많은 컴포넌트가 있는 애플리케이션에서 컴포넌트가 삭제될 때 해당 컴포넌트가 사용중이던 리소르를 확보하는 것이 중요하다.

Clock이 처음 DOM에 렌더링 될 때마다 타이머를 설정하려고 한다.
이를 React에서 "마운팅"이라 한다.  
  
Clock에 의해 생성된 DOM이 삭제될 때마다 타이머를 해제하려고 한다.  
  
컴포넌트 클래스에서 특별한 메서드를 선언하여 컴포넌트가 마운트되거나 언마운트 될 때 일부 코드를 작동할 수 있다.

``` js
class Clock extends React.Component {
  constructor(props) {
    super.(props);
    this.state = {date: new Date()};
  }

  conponentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    ;)
  }
}
```

