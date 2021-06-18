---
layout: post
author: ainochi-kor
title: React/26. Render Props
---

"render prop"란, React 컴포넌트 간에 코드를 공유하기 위해 함수 props를 이용하는 간단한 테크닉이다.  
  
render props 패턴으로 구현된 컴포넌트는 자체적으로 렌더링 로직을 구현하는 대신, react 엘리먼트 요소를 반환하고 이를 호출하는 함수를 사용한다.

``` js
<DataProvider render = {data => (
  <h1>Hello {data.target}</h1>
)} />
```

render props를 사용하는 라이브러리는 React Router, Downshift, Formik이 있다.  
  
---

## 횡단 관심사(Cross-Cutting Concerns)를 위한 render props 사용법

컴포넌트는 React에서 코드의 재사용성을 위해 사용하는 주요 단위이다. 하지만 컴포넌트에서 캡슐화된 상태나 동작을 같은 상태를 가진 다른 컴포넌트와 공유하는 방법이 항상 명확하진 않다.  
  
[웹 어플리케이션에서 마우스 위치를 추적하는 로직 컴포넌트]

``` js
class MouseTracker extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0};
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY,
    });
  }

  render() {
    return (
      <div 
        style={{ height: '100wh' }} 
        onMouseMove={this.handleMousMove}>
        <h1>Move the mouse around!</h1>
        <p>The current mouse position is ({this.state.x}, {this.state.y})</p>
      </div>
    );
  }
}
```

스크린 주위로 마우스 커서를 움직이면 컴포넌트가 마우스의 (x, y) 좌표를 <p>에 나타낸다.  
  
React에서 컴포넌트는 코드 재사용의 기본 단위이므로, 우리가 필요로 하는 마우스 커서 트래킹 행위를 /<Mouse> 컴포넌트로 캡슐화하여 어디서든 사용할 수 있게 리팩토링

``` js
// <Mouse> 컴포넌트가 우리가 원하는 행위를 캡슐화 한다
class Mouse extends React.Component{
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div 
        style={{ height: '100wh' }}
        onMouseMove={this.handleMouseMove}
      >
        <p>The current mouse position is ({this.state.x}, {this.state.y})</p>
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <>
        <h1> 
          Move the mouse around!
        </h1>
        <Mouse />
      </>
    );
  }
}
```

이제 \<Mouse> 컴포넌트는 마우스 움직임 이벤트를 감지하고, 마우스 커서의 (x, y) 위치를 저장하는 행위를 캡슐화했다. 그러나 아직 완벽하게 재사용할 수 있지 않다.  
  
예를 들어, 마우스 주위에 고양이 그림을 보여주는 \<Cat> 컴포넌트가 있다면, 우리는 \<Cat mouse={{x, y}}> props을 통해 Cat 컴포넌트에 마우스 좌표를 전달하고 화면에 어떤 위치에 이미지를 보여줄지 알려 주고자 한다.  
  
#### [1. \<Mouse> 컴포넌트의 render 메서드 안에 \<Cat> 컴포넌트를 넣어 렌더링]

``` js
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{position: 'absolute', left: mouse.x, top: mouse.y}} />
    );
  }
}

class MouseWithCat extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0};
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}
      >
        <Cat mouse={this.state} />
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    <div>
      <h1>Move the mouse around!</h1>
      <MouseWithCat />
    </div>
  };
}
```

이러한 접근 방법은 특정 사례에서는 적용할 수 있지만, 행위의 캡슐화(마우스 트래킹)라는 목표는 당성하지 못했다.  
  
마우스 위치를 추적할 수 있는 새로운 component(\<MouseWithCat>과 근본적으로 다른) 제작.
  
여기에 render prop를 사용하여 \<Mouse> 컴포넌트 안에 \<Cat> 컴포넌트를 하드 코딩해서 결과물을 바꾸는 대신에, \<Mouse>에게 동적으로 렌더링할 수 있도록 해주는 함수 prop을 제공하는 것.(render prop의 개념)

``` js
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = {x: 0 , y: 0};
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div 
        style={{ height: '100vh' }} 
        onMouseMove={this.handleMouseMove}
      >
      {this.props.render(this.state)}
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    <div>
      <h1>Move the mouse around!</h1>
      <Mouse render={mouse => (
        <Cat mouse={mouse} />
      )}>
    </div>
  };
}

```

이제 컴포넌트의 행위를 복제하기 위해 하드 코딩할 필요 없이 render 함수에 prop으로 전달해줌으로써, \<Mouse> 컴포넌트는 동적으로 트래킹 기능을 가진 컴포넌트들을 렌더링할 수 있다.  
  
정리하자면, **render prop은 무엇을 렌더링할지 컴포넌트에 알려주는 함수이다.**

---

## render 이외의 Props 사용법

"render props pattern"으로 불린다는 이유로 prop name으로 render를 사용할 필요는 없다. 어떤 함수형 prop이든 render prop가 될 수 있다.

``` js
<Mouse children={mouse => (
  <p>The mouse position is {mouse.x} , {mouse.y}</p>
)} />
```

실제로 JSX element의 "어트리뷰트" 목록에 하위 어트리뷰트 이름을 지정할 필요는 없다. 대신에, element 안에 직접 넣을 수 있어야 한다.

``` js
<Mouse>
  {mouse => (
    <p>The mouse position is {mouse.x}, {mouse.y}</p>
  )}
</Mouse>
```

이 테크닉은 react-motion API에서 실제로 사용한다. 이 테크닉은 자주 사용되지 않기 때문에, API를 디자인할 때 children은 함수 타입을 가지도록 propTypes를 지정하는 것이 좋다.

``` js
Mouse.propTypes = {
  children: PropTypes.func.isRequired
};

```

---

## 주의사항

### React.PureComponent에서 render props pattern을 사용할 땐 주의헤야 한다

render props 패턴을 사용하면 React.PureComponent를 사용할 때 발생하는 이점이 사라질 수 있다. 얕은 prop 비교는 새로운 prop에 대해 항상 false를 반환한다. 이 경우 render마다 render prop으로 넘어온 값을 항상 새로 생성한다.  
  
``` js
class Mouse extends React.PureComponent {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = {x: 0 , y: 0};
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div 
        style={{ height: '100vh' }} 
        onMouseMove={this.handleMouseMove}
      >
      {this.props.render(this.state)}
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>Move the mouse around!</h1>

        {/*
          이것은 좋지 않다 'render' prop이 가지고 있는 값은 각각 다른 컴포넌트를 렌더링 할 것이다.
        */}
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```
이 예제에서 \<MouseTracker>가 render 될때마다, \<Mouse render>의 prop으로 넘어가는 함수가 계속 생성된다. 따라서 React.PureComponent를 상속받은 \<Mouse> 컴포넌트 효과가 사라지게 된다.  
  
이 문제를 해결하기 위해서, 다음과 같이 인스턴스 메서드를 사용해서 prop를 정의해야 한다.

``` js
class MouseTracker extends React.Component {
  // 'this.renderTheCat'을 항상 생성하는 메서드를 정의
  // 이것은 render를 사용할 때마다 같은 함수를 참조한다.

  renderTheCat(mouse) {
    return <Cat mouse={mouse} />;
  }

  render() {
    return (
      <div>
        <h1>Mouse the mouse around</h1>
        <Mouse render={this.renderTheCat /}>
      </div>
    )
  }
}

만약 prop을 정적으로 정의할 수 없는 경우에는 \<Mouse> 컴포넌트는 React.Component를 상속받아야 한다.