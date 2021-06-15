---
layout: post
author: ainochi-kor
title: React/15. Ref 전달하기(Forwarding Refs)
---

ref 전달은 컴포넌트를 통해 자식 중 하나에 ref를 자동으로 전달하는 기법입니다.

---

## DOM에 refs 전달하기

[기본 button DOM 요소를 렌더링하는 FancyButton 컴포넌트 가정]

``` js
function FancyButton(props) {
  return (
    <button className="FancyButton">
      {props.children}
    </button>
  );
}
```

FancyButton을 사용하는 다른 컴포넌트들은 일반적으로 내부 button DOM 요소에 대한 ref를 얻을 필요가 없다. 이는 컴포넌트들이 서로 DOM 구조에 지나치게 의존하지 않기 때문이다.  
  
**Ref전달하기는 일부 컴포넌트가 수신한 ref를 받아 조금 더 아래로 전달(즉, "전송")할 수 있는 옵트인 기능이다.**

[React.forwardRef를 사용하여 전달된 ref를 얻고, 그것을 렌더링 되는 DOM button으로 전달하기]

``` js
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 이제 DOM 버튼으로 ref를 직접 받을 수 있다.
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>
```

이런 방법으로 FancyButton을 사용하는 컴포넌트들은 button DOM 노드에 대한 참조를 가져올 수 있고, 필요한 경우 DOM button을 직접 사용하는 것처럼 접근할 수 있다.

[단계별 설명]

1. React.creatRef를 호출해서 React ref를 생성하고 ref 변수에 할당한다.
2. ref를 JSX 속성으로 지정해서 \<FancyButton ref={ref}>로 전달한다.
3. React는 이 ref를 forwardRef 내부의 (props, ref) => ... 함수의 두 번째 인자로 전달한다.
4. 이 ref를 JSX 속성으로 지정해서 \<button ref={ref}>으로 전달한다.
5. ref가 첨부되면 ref.current는 \<button> DOM 노드를 가리킨다.

> #### 알아두기
> 두 번째 ref 인자는 React.forwardRef와 같이 호출된 컴포넌트를 정희했을 때에만 생성된다. 일반 함수나 클래스 컴포넌트는 ref 인자를 받지도 않고 props에서 사용할 수도 없다.  
> Ref 전달은 DOM 컴포넌트에만 한정적이지 않다. 클래스 컴포넌트 인스턴스에도 전달할 수 있다.

---

## 컴포넌트 라이브러리 유지관리자를 위한 주의사항

**컴포넌트 라이브러리에서 forwardRef를 사용하기 시작할 때 이것을 변경사항으로 간주하고 라이브러리의 새로운 중요 버전을 릴리즈 해야 한다.** 다른 동작을 할 가능성이 높고 이전 동작에 의존하는 앱이나 다른 라이브러리들이 손상될 가능성이 크다.

---

## 고차원 컴포넌트에서의 ref 전달하기

HOC로 알려진 이 기술은 고차원 컴포넌트에서 부준적으로 유용할 수 있다.

[콘솔에 컴포넌트 props를 로깅하는 HOC 예제]

``` js
function logProps(WrappedComponent) {
  class LogProps extends React.Component {
    componentDidUpdate(prevProps) {
      console.log('old props:', prevProps);
      console.log('new props:', this.props)
    }

    render() {
      return <WrappedComponent {...this.props} />;
    }
  }

  return LogProps;
}
```

"logProps" HOC는 모든 props를 래핑하는 컴포넌트로 전달하므로 렌더링된 결과가 동일하게 된다. 

``` js
class FancyButton extends React.Component {
  focus() {
    // ...
  }

  // ...
}

// FancyButton을 내보내는 대신 LogProps를 내보낸다.
// FancyButton을 렌더링한다.
export default logProps(FancyButton);
```

여기서 ref는 전달되지 않는다. ref는 prop가 아니기 때문이다. HOC에 ref를 추가하면 ref는 래핑된 컴포넌트가 아닌 가장 바깥쪽 컨테이너 컴포넌트를 참조한다.  
  
FancyButton 컴포넌트를 위한 refs가 실제로는 LogProps 컴포넌트에 첨부된다는 것을 의미한다.

``` js
import FancyButton from './FancyButton';

const ref = React.createRef();


```