---
layout: post
author: ainochi-kor
title: React Hook/07. Hooks API Reference
---


> Hook은 React 16.8에 새로 추가된 기능이다. Hook은 class를 작성하지 않고도 state와 다른 React의 기능들을 사용할 수 있게 해준다.

이 페이지는 React에 내장된 Hook API를 설명합니다.  
  
#### 기본 Hook
- useState
- useEffect
- useContext

#### 추가 Hooks
- useReducer
- useCallback
- useMemo
- useRef
- useImperativeHandle
- useLayoutEffect
- useDebugValue

---

## 기본 Hook

### useState

> ``` js 
> const [state, setState] = useState(initialState);
> ```

상태 유지 값과 그 값을 갱신하는 함수를 반환한다.  
  
최초로 렌더링을 하는 동안, 반환된 state(state)는 첫 번째 전달된 인자(initialState)의 값과 같습니다.  
  
setState 함수는 state를 갱신할 때 사용합니다. 새 state 값을 받아 컴포넌트 리렌더링을 큐에 등록합니다.

> ``` js 
> setState(newState);
> ```

다음 리렌더링 시에 useState를 통해 반환받은 첫 번째 값은 항상 갱신된 최신 state가 됩니다.

> #### 주의
> React는 setState 함수 동일성이 안정적이고 리렌더링 시에도 변경되지 않을 것이라는 것을 보장합니다. 이것이 useEffect 나 useCallback 의존성 목록에 이 함수를 포함하지 않아도 무방한 이유입니다.  
  
### 함수적 갱신

이전 state를 사용해서 새로운 state를 계산하는 경우 함수를 setState로 전달할 수 있습니다. 그 함수는 이전 값을 받아 갱신된 값을 반환할 것입니다. 여기에 setState의 양쪽 형태를 사용한 카운터 컴포넌트의 예가 있습니다.  
  

``` js
function Counter({initialCount}) {
  const [count, setCount] = useState(initialCount);
  return (
    <>
      Count: {count}
      <button onClick={() => setCount(initialCount)}>Reset</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>-</button>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
    </>
  )
}
```

"+"와 "-" 버튼은 함수 형식을 사용하고 있다. 이것은 갱신된 값이 갱신되기 이전의 값을 바탕으로 계산되기 이전의 값을 바탕으로 계산되기 때문이다. 반면, "Reset" 버튼은 카운트를 항상 0으로 설정하기 때문에 일반적인 형식을 사용한다.  
  
업데이트 함수가 현재 상태와 정확히 동일한 값을 반환한다면 바로 뒤에 일어날 리렌더링은 완전히 건너뛰게 된다.

> #### 주의
> 클래스 컴포넌트의 setState 메서드와는 다르게, useState는 갱신 객체(update objects)를 자동으로 합치지는 않습니다. 함수 업데이터 폼을 객체 전개 연사자와 결합함으로써 이 동작을 복제할 수 있습니다.
> ``` js
> const [state, setState] = useState({});
> setState(prevState => {
>  // Object.assign would also word
>  return {...prevState, ...updatedValue};
> })
> ```
> 다른 방법으로는 useReducer가 있는데 이는 여러 개의 하윗 값들을 포함한 state 객체를 관리하는 데에 더 적합하다.

### 지연 초기 state

initialState 인자는 초기 렌더링 시에 사용하는 state이다. 그 이후의 렌더링 시에는 이 값은 무시된다. 만약 초기 state가 고비용 계산의 결과라면, 초기 렌더링 시에만 실행될 함수를 대신 제공할 수 있다.  
  
``` js
const [state, setState] = useState(() => {
  const initialState = someExpensiveComputation(props);
  return initialState;
});
```

### State 갱신의 취소

State Hook을 현재의 state와 동일한 값으로 갱신하는 React는 자식을 렌더링 한다거나 무엇을 실행하는 것을 회피하고 그 처리를 종료한다.(React는 Object.is 비교 알고리즘을 사용한다.)  
  
실행을 회피하기 전에 React에서 특정 컴포넌트를 다시 렌더링하는 것이 여전히 필요할 수도 있다는 것에 주의하자. React가 불필요하게 트리에 그 이상으로 [더 깊게]는 관여하지 않을 것이므로 크게 신경 쓰지 않아도 된다만, 만약 렌더링 시에 고비용의 계산을 하고 있다면 useMemo를 사용하여 그것들을 최적화할 수 있다.  
  
### useEffect

``` js
useEffect(didUpdate);
```

명령형 또는 어떤 effect를 발생하는 함수를 인자로 받는다.  
  
변형, 구독, 타이머, 로깅 또는 다른 부작용(side effects)은 (React의 렌더링 단계에 따르면) 함수 컴포넌트의 본문 안에서는 허용되지 않는다. 만약 이를 수행한다면 그것은 매우 혼란스러운 버그 및 UI의 불일치를 야기하게 될 것이다.  
  
대신에 useEffect를 사용하세요. useEffect에 전달된 함수는 화면에 렌더링이 완료된 후에 수행되게 될 것 입니다. React의 순수한 함수적인 세계에서 명령적인 세계로의 탈출구로 생각하세요.  
  
기본적으로 동작은 모든 렌더링이 완료된 후에 수행된다. 어떤 값이 변경되었을 때만 실행되게 할 수도 있다.   
  
### effect 정리

effect는 종종 컴포넌트가 화면에서 제거될 때 정리해야 하는 리소스를 만든다. 가령 구독이나 타이머 ID와 같은 것입니다. 이것을 수행하기 위해서 useEffect로 전달된 함수는 정리(clean-up) 함수를 반환할 수 있습니다. 예를 들어 구독을 생성하는 경우는 아래와 같습니다.
  
``` js
useEffect(() => {
  const subscription = props.source.subscribe();
  return () => {
    // Clean up the subscription
    subscription.unsubscribe();
  };
});
```

정리 함수는 메모리 누수 방지를 위해 UI에서 컴포넌트를 제거하기 전에 수행됩니다. 더불어, 만약 컴포넌트가 (그냥 일반적으로 수행하는 것처럼) 여러 번 렌더링 된다면 **다음 effect가 수행되기 전에 이전 effect는 정리된다.** 위의 예에서, 매 갱신마다 새로운 구독이 생성된다고 볼 수 있다. 갱신마다 불필요한 수행이 발생하는 것을 회피하기 위해서는 다음 절을 참고하세요.  

### effect 타이밍

componentDidMount와 componentDidUpdate와는 다르게, useEffect로 전달된 함수는 지연 이벤트 동안에 레이아웃 배치와 그리기를 완료한 후 발생한다. 이것은 구독이나 이벤트 핸들러를 설정하는 것과 같은 다수의 공통적인 부작용에 적합한다. 왜냐면 대부분의 작업이 브러우저에서 화면을 업데이트하는 것을 차단해서는 안 되기 때문이다.  
  
그렇지만, 모든 effect가 지연될 수는 없습니다. 예를 들어 사용자에게 노출되는 DOM 변경은 사용자가 노출된 내용의 불일치를 경험하지 않도록 다음 화면을 다 그리기 이전에 동기화 되어야 한다. (그 구분이란 개념적으로는 수동적 이벤트 리스터에 능동적 이벤트 리스터의 차이와 유사하다) 이런 종류의 effect를 위해 React는 useLayoutEffect 라는 추가적인 Hook을 제공한다. 그것은 useEffect와 동일한 시그니처를 가지고 있고 그것이 수행될 때에만 차이가 난다.

useEffect는 브라우저 화면이 다 그려질 때까지 지연되지만, 다음 어떤 새로운 렌더링이 발생하기 이전에 발생하는 것도 보장한다. React는 새로운 갱신을 시작하기 전에 이전 렌더링을 항상 완료하게 된다.  
  
### 조건부 effect 발생

effect의 기본 동작은 모든 렌더링을 완료한 후 effect를 발생하는 것입니다. 이와 같은 방법으로 만약 의존성 중 하나가 변경된다면 effect는 항상 재생성된다.  
  
그러나 이것은 이전 섹션의 구독 예제와 같이 일부 경우에는 과도한 작업일 수 있다. source props가 변경될 때에만 필요한 것이라면 매번 갱신할 때마다 새로운 구독을 생성할 필요가 없다.  
  
이것을 수행하기 위해서는 useEffect에 두 번째 인자를 전달하라. 이 인자는 effet가 종속되어 있는 값을 배열이다. 이를 적용한 예는 아래와 같다.

``` js
useEffect(
  () => {
    const subscription = props.source.subscribe();
    return () => {
      subscription.unsubscribe();
    };
  },
  [props.source],
);
```

자 이제, props.source가 변경될 때에만 구독이 재생성될 것이다. 

> #### 주의
> 이 최적화를 사용하는 경우, 값의 배열이 시간이 지남에 따라 변경되고 effect에 사용되는 컴포넌트 범위의 모든 값들(예를 들어, props와 state와 같은 값들)을 포함하고 있는지 확인하세요. 그렇지 않다면 여러분의 코드는 이전 렌더링에서 설정된 오래된 값을 참조하게 될 것이다. how to deal with functions 와 array values change too often 할 때 무엇을 할 것인지에 대해서 조금 더 알아보자.  
>   
> 만약 effect를 수행하고 (mount를 하거나 unmount 할 때) 그것을 한 번만 실행하고 싶다면 두 번째 인자로 빈 배열([])을 전달할 수 있다. 이를 통해 effect는 React에게 props나 state에서 가져온 어떤 값에도 의존하지 않으므로, 다시 실행할 필요가 전혀 없다는 것을 알려주게 된다. 이것을 특별한 경우로 간주하지는 않고 의존성 값의 배열이 항상 어떻게 동작하는지 직접적으로 보여주는 것뿐이다.  
>  
> 만약 빈 배열([])을 전달한다면 effect 안에 있는 props와 state는 항상 초깃값을 가지게 될 것이다. 두 번째 인자로써 []을 전달하는 것이 친숙한 componentDidMount 와 componentWillUnmount에 의한 개념과 비슷하지만 느껴지겠지만, effect가 너무 자주 리렌더링 되는 것을 피하기 위한 보통 더 나은 해결책이 있다. 또한 브라우저가 모두 그려질 때까지 React는 useEffect의 수행을 지연하기 때문에 다른 작업의 수행이 문제가 되지는 않는다는 것을 잊지 말라.
>    
> eslint-plugin-react-hooks 패키지의 exhaustive-deps 규칙을 사용하기를 권장합니다. 그것은 의존성이 바리지 않게 정의되었다면 그에 대해 경고하고 수정하도록 알려준다.  
  
의존성 값의 배열은 effect 함수의 인자로 전달되지는 않는다. 그렇지만 개념적으로는, 이 기법은 effect 함수가 무엇일지를 표현하는 방법이다. effect 함수 안에서 참조되는 모든 값은 의존성 값의 배열에 드러나야 한다. 나중에 충분히 발전된 컴파일러가 이 배열을 자동적으로 생성할 수 있을 것이다.

### useContext

``` js
const value = useContext(MyContext);
```

context 객체(React.createContext에서 반환된 값)을 받아 그 context의 현재 값을 반환한다. context의 현재 값은 트리 안에서 이 Hook을 호출하는 컴포넌트에 가장 가까이에 있는 \<MyContext.Provider> 의 value prop에 의해 결정된다.  
  
컴포넌트에서 가장 가까운 <MyContext.Provider>가 갱신되면 이 Hook은 그 MyContext provider에게 전달된 가장 최신의 context value를 사용하여 렌더러를 트리거 한다. 상위 컴포넌트에서 React.memo 또는 shouldComponentUpdate를 사용하더라도 useContext를 사용하고 있는 컴포넌트 자체에서부터 다시 렌더링된다.  
  
useContext로 전달한 인자는 context 객체 그 자체이어야 함을 잊지 말라.

- 맞는 사용 : useContext(MyContext)
- 틀린 사용 : useContext(MyContext.Consumner)
- 틀린 사용 : useContext(MyContext.Provider)

useContext를 호출한 컴포넌트는 context 값이 변경되면 항상 리렌더링 될 것이다. 만약 컴포넌트를 리렌더링 하는 것에 비용이 많이 든다면, 메모이제이션을 사용하여 최적화할 수 있다.

> #### 팁
> 만약 여러분이 Hook 보다 context API에 친숙하다면 useContext(MyContext)는 클래스에서의 static contextType = MyContext 또는 \<MyContext.Consumer>와 같다고 보면 된다.  
>  
> useContext(MyContext)는 context를 읽고 context의 변경을 구독하는 것만 가능하다. context의 값을 설정하기 위해서는 여전히 트리의 윗 계층에서 \<MyContext.Provider>가 필요하다.

### useContext를 Context.Provider와 같이 사용하자.

``` js
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  }, 
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

const ThemeContext = React.creactContext(themes.light);

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme.background, color: theme.foreground}}>
      I am styled by theme context!
    </button>
  );
}
```
해당 예시는 Context 고급 안내서에서 사용했던 예시가 hook으로 수정되었으며 안내서에서 Context를 언제, 어떻게 사용하는지 자세히 알 수 있다.

---

## 추가 Hook

다음의 Hook는 이전 섹션에서의 기본 Hook의 변경이거나 특정한 경우에만 필요한 것.

### useReducer

``` js
const [state, dispatch] = useReducer(reducer, initalArg, init);
```

useState의 대체 함수이다. (state, action) => newState의 형태로 reducer를 받고 dispatch 메서드와 짝의 형태로 현재 state를 반환한다.  
  
다수이 하윗값을 포함하는 복잡한 정적 로직을 만드는 경우나 다음 state가 이전 state에 의존적인 경우에 보통 useState 보다 useReducer를 선호한다. 또한 useReducer는 자세한 업데이트를 트리거 하는 컴포넌틔 성능을 최적화할 수 있게 하는데, 이것은 콜백 대신 dispatch를 전달할 수 있기 때문이다.  
  
아래의 useState 내용에 있던 카운터 예제인데 reducer를 사용해서 다시 작성한 것이다.

``` js
const initialState = {count : 0};

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    default: 
      throw new Error();
  }
}

const Counter = () => {
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <>
      Count: {state.count}
      <button onmClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onmClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

> #### 주의
> React는 dispatch 함수의 동일성이 안정적이고 리렌더링 시에도 변경되지 않으리라는 것을 보장한다. 이것이 useEffect나 useCallback 의존성 목록에 이 함수를 포함하지 않아도 괜찮은 이유이다.

### 초기 state의 구체화

useReducer state의 초기화에는 두 가지 방법이 있다. 유스케이스에 따라서 한 가지를 선택하라. 가장 간단한 방법은 초기 state를 두 번째 인자로 전달하는 것이다.

``` js
const [state, dispatch] = useReducer(reducer, {count: initialCount});
```

> #### 주의
> React에서는 Reducer의 인자로써 state = initialState와 같은 초기값을 나타내는, Redux에서는 보편화된 관습을 사용하지 않는다. 때때로 초기값은 props에 의존할 필요가 있어 Hook 호출에서 지정되기도 한다. 만약 초기값을 나타내는 것이 정말 필요하다면 useReduccer(reducer, undefined, reducer) 를 호출하는 방법으로 Redux를 모방할 수 있지만 권장하지 않는다.

### 초기화 지연

초기 state를 조금 지연해서 생성할 수도 있다. 이를 위해서 init 함수를 세 번째 인자로 전달한다. 초기 state는 init(initalArg)에 설정될 것이다.

``` js
function init(initialCount) {
  return {count: initialCount}
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    case 'reset':
      return init(action.payload);
    default: 
      throw new Error();
  }
}

function Counter({initialCount}) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  
  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({type: 'reset', payload:initialCount})}>Reset</button>
      <button onmClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onmClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

### dispatch의 회피

Reducer Hook에서 현재 state와 같은 값을 반환하는 경우 React는 자식을 리렌더링하거나 effect를 발생하지 않고 이것들을 회피할 것이다. (React는 Object.is 비교 알고리즘을 사용한다)  
  
실행을 회피하기 전에 React에서 특정 컴포넌트를 다시 렌더링하는 것이 여전히 필요할 수도 있다는 것에 주의하자. React가 불필요하게 트리에 그 이상으로 [더 깊게] 까지는 가지 않을 것이므로 크지게 신경 쓰지 않아도 된다. 만약 렌더링 시에 고비용의 계산을 하고 있다면 useMemo를 사용하여 그것을 최적화할 수 있다.