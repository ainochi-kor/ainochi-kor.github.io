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
이러한 메서드들은 "생명주기 메서드"라고 불린다.

componentDidMount() 메서드는 컴포넌트 출력물이 DOM에 렌더링 된 후에 실행된다.

componentDidMount() 메서드에 타이머 설정
``` js
componentDidMount() {
  this.timerID = setInterval(
    () => this.tick(),
    1000
  );
}
```

this (this.timerID)에서 어떻게 타이머 ID를 제대로 저장하는지 주의.

this.props가 React에 의해 스스로 설정되고 this.state가 특수한 의미가 있지만, 타이머 ID와 같이 데이터 흐름 안에 포함되지 않는 어떤 항목을 보관할 필요가 있다면 자유럽게 클래스에 수동으로 부가적인 필드를 추가해도 된다.  
  
componentWillUnmount() 생명주기 메서드 안에 있는 타이머 분해

``` js
componentWillUnmount() {
  clearInterval(this.timerID);
}
```

Clock 컴포넌트가 매초 작동하도록 하는 tick() 메서드 구현
- 컴포넌트 로컬 state를 업데이트하기 위해 this.setState()를 사용

``` js
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world</h1>
        <h2>It is {this.state.date.toLocaleTimerString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

[호출 순서]

1. \<Clock \/>가 ReactDOM.render()로 전달되었을 때 React는 Clock 컴포넌트의 constructor를 호출한다. Clock 현재 시간이 포함된 객체로 this.state를 초기화한다. 나중에 이 state를 업데이트 할 것.

2. React는 Clock 컴포넌트의 render() 메서드를 호출한다. 이를 통해 React는 화면에 표시되어야 할 내용을 알게 된다. 그 다음 React는 Clock의 렌더링 출력값을 일치시키기 위해 DOM을 업데이트한다.

3. Clock 출력값이 DOM에 삽입되면, React는 componentDidMount() 생명주기 메서드를 호출한다. 그 안에서 Clock 컴포넌트는 매초 컴포넌트의 tick() 메서드를 호출하기 위한 타이머를 설정하도록 브라우저에 요청한다.

4. 매초 브라우저가 tick() 메서드를 호출합니다. 그 안에서 Clock 컴포넌트는 setState() 에 현재 시각을 포함하는 객체를 호출하면서 UI 업데이트를 진행한다. setState() 호출 덕분에 React는 state가 변경된 것을 인지하고 화면에 표시될 내용을 알아내기 위해 render() 메서드를 다시 호출한다. 이 때 render() 메서드 안의 this.state.date가 달라지고 렌더링 출력값을 업데이트된 시각을 포함한다.

5. Clock 컴포넌트가 DOM으로부터 한 번이라도 삭제된 적이 있다면 React는 타이머를 멈추기 위해 componentWillUnmount() 생명주기 메서드를 호출합니다.

---

## **State를 올바르게 사용하기**

setState()에 대해서 알아야 할 세가지

###  직접 State를 수정하지 않기.

``` js
// Wrong
this.state.comment = 'Hello';
```
 

대신에 setState()를 사용

``` js
// Correct
this.setState({comment: 'Hello'});
```

this.state를 지정할 수 있는 유일한 공간은 바로 constructor이다.

### State 업데이트는 비동기적일 수 있다.

React는 성능을 위해 setState() 호출을 단일 업데이트로 한꺼번에 처리할 수 있다.  
  
this.props와 this.state가 비동기적으로 업데이트될 수 있기 때문에 다음 state를 계산할 때 해당 값에 의존해서는 안된다.

카운터 업데이트 실패 예제

``` js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

이를 수정하기 위해 객체보다는 함수를 인자로 사용하는 다른 형태의 setState()를 사용한다. 그 함수 이전 state를 첫 번째 인자로 받아들일 것이고, 업데이트가 적용된 시점의 props를 두 번째 인자로 받아들일 것이다.  
  
``` js
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

위에서 화살표 함수를 사용했지만, 일반 함수에서도 정상으로 동작한다.

``` js
// Correct
this.setState(function (state, props) {
  return {
    counter: state.counter + props.increment
  };
});
```

### State 업데이트는 병합된다.

setState()를 호출할 때 React는 제공한 객체를 현재 state로 병합한다.

- state는 다양한 독립적인 변수를 포함할 수 있다.

``` js
constructor(props) {
  super(props);
  this.state = {
    posts: [],
    comments: []
  };
}
```

별도의 setState() 호출로 이러한 변수를 독립적으로 업데이트할 수 있다.

``` js
componentDidMount() {
  fetchPosts().then(response => {
    this.setState({
      posts: response.post
    });
  });

  fetchComments().then(response => {
    this.setState({
      comments: response.comments
    });
  });
}
```

병합은 얕게 이루어지기 때문에 this.setState({comments})는 this.state.post에 영향을 주진 않지만 this.state.comments는 완전히 대체된다.

---

## **데이터는 아래로 흐릅니다**

부모 컴포넌트나 자식 컴포넌트 모두 특정 컴포넌트가 유상태인지 또는 무상태인지 알 수 없고, 그들이 함수나 클래스로 정의되었는지에 대해서 관심을 가질 필요가 없다.  
  
이 때문에 state는 종종 로컬 또는 캡슐화라고 불린다. state가 소유하고 설정한 컴포넌트 이외에는 어떠한 컴포넌트도 접근할 수 없다.  
  
컴포넌트는 자신의 state를 자식 컴포넌트에 props로 전달할 수 있다.

``` js
<FormattedDate date={this.state.date} />
```

FormattedDate 컴포넌트는 date를 자신의 props로 받을 것이고 이것이 Clock의 state로부터 왔는지, Clock의 props에서 왔는지, 수동으로 입력한 것인지 알지 못한다.

``` js
function FormattedDate(props) {
  return <h2>It is {props.date.toLocaleTimeString()}.</h2>;
}
```

state로부터 파생된 UI 또는 데이터는 오직 트리구조에서 자신의 "아래"에 있는 컴포넌트에만 영향을 미친다.

모든 컴포넌트가 완전히 독립적이라는 것을 보여주기 위해 App 렌더링하는 세 개의 \<Clock\>

``` js
function App() {
  return (
    <div>
      <Clock />
      <Clock />
      <Clock />
    </div>
  );
}

ReactDOM.render(
  <App />,
  document.getElementById('root')
);

```

각 Clock은 자신만의 타이머를 설정하고 독립적으로 업데이트를 한다.
