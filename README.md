# study-react

(2023/07/05~)

- https://react.dev/
- https://react-ko.dev/

## One-way data flow

React는 컴포넌트 계층 구조를 따라 부모 컴포넌트에서 자식 컴포넌트로 내려 가는 단방향 데이터 흐름을 따른다. 기본적으로 자식 컴포넌트는 부모 컴포넌트의 데이터를 수정할 수 없다. 부모 컴포넌트가 props로 데이터를 수정할 수 있는 메서드를 전달하는 방식으로 역방향 데이터 흐름을 추가할 순 있다. 단방향 데이터 흐름은 양방향 데이터 흐름에 비해 작성해야할 것이 많지만 데이터의 흐름이 명확하여 상태를 추적하기 쉽다.

> **출처**
>
> - https://react-ko.dev/learn/thinking-in-react#step-4-identify-where-your-state-should-live
> - https://react-ko.dev/learn/thinking-in-react#step-5-add-inverse-data-flow

## Component

React 컴포넌트는 재사용 가능한 UI 요소이다. 컴포넌트는 본질적으로 브라우저에 마크업을 렌더링하는 JavaScript 함수이다. 

> **출처**
>
> - https://react-ko.dev/learn/your-first-component#defining-a-component

React는 컴포넌트 계층 구조를 기반으로 한다. React 애플리케이션은 "root" 컴포넌트에서 시작한다. Client API인 `createRoot`를 사용하여 생성하고, 생성된 루트의 `render` 메서드를 사용하여 React 컴포넌트를 루트 아래에 표시할 수 있다.

> **출처**
>
> - https://react-ko.dev/learn/your-first-component#components-all-the-way-down
> - https://react-ko.dev/reference/react-dom/client/createRoot

React 컴포넌트 함수는 유일한 인자로 `props` 객체를 받는다.

> **출처**
>
> - https://react-ko.dev/learn/passing-props-to-a-component#step-2-read-props-inside-the-child-component

### 순수 함수로서의 컴포넌트

React는 컴포넌트를 순수 함수라고 가정한다. 순수 함수는 각자 독립적이며, 동일한 입력에 대해 동일한 출력을 반환한다. 컴포넌트는 순수 함수로서 렌더링 전에 존재하는 객체나 변수를 변경하지 않으며, 동일한 입력에는 항상 동일한 JSX를 반환해야 한다.

아래와 같은 경우는 안된다.

```jsx
let guest = 0;

function Cup() {
  // Bad: changing a preexisting variable!
  // 나쁨: 기존 변수를 변경합니다!
  guest = guest + 1;
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaSet() {
  return (
    <>
      <Cup />
      <Cup />
      <Cup />
    </>
  );
}

```

> **출처**
>
> - https://react-ko.dev/learn/keeping-components-pure

React 렌더링에서 입력은 props, state, context가 있는데 이들은 모두 읽기 전용으로 취급해야한다. 컴포넌트가 렌더링 되는 동안에는 변수나 객체를 변경해서는 안된다. 컴포넌트는 순수 함수로서 계산만 해야하고 동일한 입력에 대해 동일한 출력을 반환해야하기 때문이다. `<Strict Mode>`를 적용하면 컴포넌트를 두 번 호출하여 순수하지 않은 컴포넌트를 알아낼 수 있다.

> **출처**
>
> - https://react-ko.dev/learn/keeping-components-pure#detecting-impure-calculations-with-strict-mode

### Local mutation

렌더링하는 동안 방금 생성한 변수와 객체를 변경하는 것은 괜찮다.

```jsx
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  let cups = [];
  for (let i = 1; i <= 12; i++) {
    cups.push(<Cup key={i} guest={i} />);
  }
  return cups;
}
```

즉, `TeaGathering`의 동일한 렌더링 중에 `cups`가 변경되었으므로 괜찮다. 이를 **지역 변이(local mutation)**이라고 한다.

### side effect

애플리케이션은 반드시 side effect를 동반한다. side effect는 렌더링이 완료된 후(커밋까지 완료된 후)에 일어나는 변경이다. React에서 side effect는 두 가지 경우에서 발생할 수 있다.

1. 이벤트 핸들러의 실행으로 side effect가 발생하는 경우. 이벤트 핸들러는 렌더링이 완료된 후 이벤트의 발생으로 실행된다. 따라서 이벤트 핸들러는 순수 함수가 아닐 수도 있다.
2. 렌더링으로 side effect가 발생하는 경우. 이 경우 side effect를 *(React)Effect*라고 한다. [Effect](#Effect)를 참고한다.

> **출처**
>
> - https://react-ko.dev/learn/keeping-components-pure#where-you-_can_-cause-side-effects
> - https://react-ko.dev/learn/synchronizing-with-effects#what-are-effects-and-how-are-they-different-from-events

### 왜 순수성인가?

컴포넌트를 순수하게 작성하면 다음과 같은 이점을 얻을 수 있다.

1. 컴포넌트를 브라우저가 아닌 다른 환경(예: 서버)에서 실행할 수 있다.
2. 캐싱해도 안전하므로, 입력이 변경되지 않는 경우 렌더링을 건너뛰어 성능을 향상시킬 수 있다. 
3. 언제든지 계산을 중단해도 안전하므로, 깊은 컴포넌트 트리를 렌더링하는 도중 데이터가 변경되는 경우 outdated된 트리를 렌더링하지 않고 렌더링을 아예 다시 시작할 수 있다.

> **출처**
>
> - https://react-ko.dev/learn/keeping-components-pure#where-you-_can_-cause-side-effects

## JSX

JSX는 JavaScript XML의 약자로 JavaScript를 확장한 문법이다. JSX는 본질적으로 JavaScript 객체로 `React.createElement` 함수 호출로 컴파일된다.

```jsx
function Greeting({ name }) {
  return (
    <h1 className="greeting">
      Hello <i>{name}</i>. Welcome!
    </h1>
  );
}
```

```jsx
import { createElement } from 'react';

function Greeting({ name }) {
  return createElement(
    'h1',
    { className: 'greeting' },
    'Hello ',
    createElement('i', null, name),
    '. Welcome!'
  );
}
```

> **출처**
>
> - https://react-ko.dev/reference/react/createElement#creating-an-element-without-jsx

그리고 JSX와 `React.createElement` 함수 호출은 React Element를 생성한다.

```javascript
// 약간 단순화됨
{
  type: Greeting,
  props: {
    name: 'Taylor'
  },
  key: null,
  ref: null,
}
```

React Element 객체가 생성된다고 렌더링되진 않는다.

> **출처**
>
> - https://react-ko.dev/reference/react/createElement#what-is-a-react-element-exactly

## Props

**props**는 부모 컴포넌트에서 자식 컴포넌트에게 전달하는 데이터이다. props는 변경 불가능한 값이다. props를 통한 데이터의 흐름은 단방향으로, 자식 컴포넌트에서는 부모 컴포넌트가 전달한 props를 바꿀 수 없다.

### prop drilling

state가 여러 컴포넌트 간 공유되어야 할 경우, 가장 가까운 공통 조상 컴포넌트로 state를 끌어올려 props로 전달해줄 수 있다. 그런데 props를 제공하는 컴포넌트와 사용하는 컴포넌트가 너무 멀리 떨어져있으면 전달이 어렵고 컴포넌트를 변경하는 게 어려운 상황이 생길 수 있다. 이러한 문제를 "prop drilling" 문제라고 한다.

prop drilling 문제는 다음과 같은 방법으로 해결할 수 있다.

1. state를 끌어올리지 않는다. 즉, 컴포넌트를 하위 컴포넌트로 쪼개지 않는다.
2. state를 전역으로 올린다. [React의 Context API](#Context-API)나 외부 전역 상태 관리 라이브러리를 사용한다.

> **출처**
>
> - https://react-ko.dev/learn/passing-data-deeply-with-context#the-problem-with-passing-props

## State

**state**는 컴포넌트 내부의 데이터로 인스턴스 간에 공유되지 않는다. state는 컴포넌트 내부에서 변경 가능한 값으로, 컴포넌트는 자신의 상태를 변경할 수 있다. state의 변경은 렌더링을 촉발시키며 이 변경은 렌더링 이후에도 유지된다. 이와 달리 지역 변수의 변경은 렌더링을 촉발시키지 않으며 렌더링 이후에도 유지되지 않는다. (변경이 렌더링을 촉발시키지 않으나, 변경을 렌더링 이후에도 유지하고 싶은 경우에는 ref 객체를 사용하도록 한다. [useRef](#useRef)를 참고한다.)

> **출처**
>
> - https://react-ko.dev/learn/state-a-components-memory#state-is-isolated-and-private
> - https://react-ko.dev/learn/state-a-components-memory#when-a-regular-variable-isnt-enough

### 스냅샷으로서의 state

React는 컴포넌트를 호출(=렌더링)하여 특정 시점의 UI 스냅샷(=JSX 스냅샷)을 반환한다. 컴포넌트의 props, 이벤트 핸들러, 지역 변수는 모두 컴포넌트 호출 당시의 state를 사용하여 계산된다. 달리 말하여, **한 렌더링 내에서 state의 값은 불변한다**.

#### 이벤트 핸들러 계산 예시 1

예를 들어보자. 아래 버튼을 한 번씩 누른다고 `number`의 값이 3씩 증가하지 않는다. 

```jsx
<button onClick={() => {
  setNumber(number + 1);
  setNumber(number + 1);
  setNumber(number + 1);
}}>+3</button>
```

직전 렌더링에서 `number` state의 값이 `0`이었다고 하자. 그렇다면 이벤트 핸들러는 해당 값을 사용하여 다음과 같이 계산된다.

```jsx
<button onClick={() => {
  setNumber(0 + 1);
  setNumber(0 + 1);
  setNumber(0 + 1);
}}>+3</button>
```

이 버튼을 누르면 React는 다음 렌더링에서 `number` state를 `1`로 설정하기 위해 준비를 한다. state의 업데이트로 렌더링이 촉발되면 React는 `number` state를 `1`로 컴포넌트를 렌더링한다. 계산 결과는 다음과 같다.

```jsx
<button onClick={() => {
  setNumber(1 + 1);
  setNumber(1 + 1);
  setNumber(1 + 1);
}}>+3</button>
```

#### 이벤트 핸들러 계산 예시 2

다른 예시를 보자, 비동기 함수를 사용하여 렌더링 이후에 콜백이 호출하면 어떻게 되는가?

```jsx
<button onClick={() => {
        setNumber(number + 5);
        setTimeout(() => {
          alert(number);
        }, 3000);
}}>+5</button>
```

`number` state의 초기값이 `0`이라고 할 때, 예시 1과 마찬가지로 출력되는 `number` state의 값은 `5`가 아니다. 계산 결과가 다음과 같기 때문이다.

```jsx
<button onClick={() => {
        setNumber(0 + 5);
        setTimeout(() => {
          alert(0);
        }, 3000);
}}>+5</button>
```

> **출처**
>
> - https://react-ko.dev/learn/state-as-a-snapshot

## UI 트리에서 컴포넌트의 위치와 state

### 동일한 위치에 있는 동일한 컴포넌트는 state를 유지한다

React에서는 UI 트리에서 동일한 위치에 있는 동일한 컴포넌트는 state를 유지한다.

```jsx
export default function App() {
    const [isFancy, setIsFancy] = useState(false);
    
    if (isFancy) return <Counter color="blue"/>;
    else return <Counter color="purple" />
}
```

```javascript
export default function App() {
    const [isFancy, setIsFancy] = useState(false);
    
    return <Counter color={isFancy ? "blue" : "purple"} />
}
```

클릭 이벤트로 카운터의 카운트가 올라가게한다고 하자. 첫번째 예시의 경우 `isFancy` 값에 따라 서로 다른 인스턴스를 생성하여 반환하는 것처럼 보인다. 그러나 두 예제는 완전히 동일하다. JSX는 내부 state를 보유하지 않고 실제 DOM 노드가 아니기 때문에 인스턴스도 아니기 때문이다. `<Counter>`는 같은 위치에 렌더링되는 한 루트의 첫번째 자식이라는 동일한 "주소"를 가진다. React는 이를 통해 이전 렌더링과 다음 렌더링 사이에 이들을 일치시킨다.

> **출처**
>
> - https://react-ko.dev/learn/conditional-rendering#are-these-two-examples-fully-equivalent
> - https://react-ko.dev/learn/preserving-and-resetting-state#same-component-at-the-same-position-preserves-state

### 동일한 위치에 있는 다른 컴포넌트는 state를 재설정한다

UI 트리에서 동일한 위치의 다른 컴포넌트는 state를 초기화한다.

```jsx
<div>
    {isClicked ? <p>See you later!</p> : <Counter />}
</div>
```

이 경우 `<div>`의 첫 자식은 원래 `<Counter>`였으나 클릭 이벤트로 `<p>`로 변경되어, UI 트리에서 `<Counter>`가 제거되고 state도 소멸된다.

#### 왜 컴포넌트 함수 정의를 중첩하면 안되는가?

내부의 컴포넌트 함수 정의는 외부의 컴포넌트 함수를 렌더링할 때마다 생성되기 때문에, 동일한 컴포넌트처럼 보여도 다른 객체이다. 그래서 UI 트리에서 같은 위치에 렌더링되어도 다른 컴포넌트를 렌더링하는 것과 같다.

```jsx
import { useState } from 'react';

export default function MyComponent() {
  const [counter, setCounter] = useState(0);

  function MyTextField() {
    const [text, setText] = useState('');

    return (
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
    );
  }

  return (
    <>
      <MyTextField />
      <button onClick={() => {
        setCounter(counter + 1)
      }}>Clicked {counter} times</button>
    </>
  );
}

```

> **출처**
>
> - https://react-ko.dev/learn/preserving-and-resetting-state#different-components-at-the-same-position-reset-state

### 동일한 위치에 있는 동일한 컴포넌트의 state 재설정하기

```jsx
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter person="Taylor" />
      ) : (
        <Counter person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

버튼이 클릭될 때마다 플레이어를 바꾸려고 한다. 그러려면 `isPlayerA`를 바꿀 때마다 `<Counter>`가 재설정되어야하는데 이 경우 그러지 못한다. 동일한 위치에 있는 동일한 컴포넌트의 state를 재설정하는 방법은 두 가지이다.

1. 다른 위치에 렌더링하기
2. 컴포넌트에 `key`로 고유성 부여하기

#### 다른 위치에 렌더링하기

```jsx
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA &&
        <Counter person="Taylor" />
      }
      {!isPlayerA &&
        <Counter person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

`person="Taylor"`인 `<Counter>`는 `<div>`의 첫번째 자식이고 `person="Sarah"`인 `<Counter>`는 `<div>`의 두번째 자식이므로 둘은 다른 위치의 다른 컴포넌트이다. 하지만 이 방법은 분기가 여러 개라면 적절하지 않다.

#### 컴포넌트에 `key`로 고유성 부여하기

컴포넌트에 `key`를 부여하면 React가 위치가 아니라 `key`로 컴포넌트를 식별할 수 있다. 이때 `key`는 부모 내에서만 고유하다. React의 관점에서 동일한 위치에 렌더링해도 다른 컴포넌트로 인식하여 state를 계속 재설정한다.

```jsx
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

> **출처**
>
> - https://react-ko.dev/learn/preserving-and-resetting-state#resetting-state-at-the-same-position

#### state 유지하기

UI에서는 초기화를 하면서, state를 유지하고 싶을 수 있다. 카운터 예제로 보면 버튼을 클릭할 때마다 이전 플레이어의 카운트 데이터가 계속 출력되는 거다. 세 가지 방법이 있다.

1. 모든 컴포넌트를 렌더링하되 현재 플레이어를 제외하고 모두 CSS로 숨기기: UI가 적으면 적합하지만 DOM 노드가 많으면 속도가 매우 느려질 수 있다.
2. 부모 컴포넌트로 state 끌어올려서 보관하기: 가장 일반적인 해결책이다.
3. 별도의 저장소 사용하기: HTML5 모던 스토리지나, 전역 스토어를 사용할 수 있다.

어떤 방식이든 `key`를 부여하는 것은 플레이어의 카운터는 구별되어야하므로 적절하다.

> 주석: props에 의해 컴포넌트 내부의 값들이 계산되는 점을 사용하여, props에 의존하여 별도의 저장소로부터 데이터를 불러오게 하면 굳이 부모로 끌어올리지 않아도 된다.
>
> ```jsx
> function Counter({ player }) {
>      const { count } = usePlayer(player);
>      return <div>{count}</div>
> }
> ```

> **출처**
>
> - https://react-ko.dev/learn/preserving-and-resetting-state#resetting-state-at-the-same-position

## Props vs State

State는 컴포넌트 내부의 메모리로 해당 컴포넌트만이 자신의 State를 변경할 수 있다. Props는 함수가 전달받는 인자와 같아 부모 컴포넌트가 자식 컴포넌트에게 전달하는 값으로 변경 불가능한 값이다.

> **출처**
>
> - https://react-ko.dev/learn/thinking-in-react#props-vs-state

## key

항목에 `key`를 지정하면 React가 변경 사항을 추적하여 DOM 트리에 반영하는데 큰 도움이 된다.

> **출처**
>
> - https://react-ko.dev/learn/rendering-lists#keeping-list-items-in-order-with-key

### key는 왜 필요한가?

React가 항목들을 고유하게 식별하기 위해서이다. 특히 리스트 안의 요소들을 생각한다면, 요소의 순서보다는 키가 항목을 식별하는데 더 적절하다.

랜덤으로 key를 생성하는 것은 적절하지 않다. 렌더링할 때마다 key가 생성되어 key가 일치하지 않아 매 렌더링마다 모든 컴포넌트와 DOM이 다시 생성되기 때문이다.

> **출처**
>
> - https://react-ko.dev/learn/rendering-lists#why-does-react-need-keys

## Rendering and Committing

> **출처**
>
> - https://react-ko.dev/learn/render-and-commit

React는 화면에 무언가를 표시하기 위해 세 단계를 거친다.

1. Trigger: 렌더링이 촉발된다.
2. Render: 컴포넌트가 렌더링된다.
3. Commit: DOM에 커밋된다.

### 단계 1: 렌더링이 촉발된다

React에서 렌더링이 촉발되는 경우는 두 가지이다.

1. **첫 렌더링(initial render)**: 컴포넌트의 첫 렌더링인 경우
2. **리렌더링(re-render)**: 컴포넌트의 state나 조상 컴포넌트의 state 중 하나가 업데이트된 경우

#### 첫 렌더링의 촉발

```jsx
import Button from './Button.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Button />);
```

`createRoot`로 생성한 루트(root)의 `render()`에 컴포넌트를 전달하면 해당 컴포넌트의 첫 렌더링을 촉발한다. `render()`에 전달된 컴포넌트를 루트 컴포넌트(root component)라고 한다.

#### 리렌더링의 촉발

```jsx
import React from 'react';

export default function Button() {
    const [count, setCount] = React.useState(0);
    const handleClick = () => {
        setCount(c => c + 1);
    };
    return <button onClick={handleClick}>{count}</button>;
}
```

첫 렌더링 이후, `set` 함수에 새로운 state 값을 전달하면 추가적으로 렌더링을 촉발한다. `set` 함수를 호출하면 새로운 렌더링이 큐(queue)에 추가된다.

### 단계 2: React가 컴포넌트를 렌더링한다

<mark>React에서 **렌더링(Rednering)**이란 컴포넌트를 호출하는 것이다.</mark> 렌더링은 재귀적이다. React는 중첩된 컴포넌트가 없고 무엇은 화면에 표시해야하는지 정확히 알기 전까지 컴포넌트를 렌더링하고, 그리고 해당 컴포넌트가 반환하는 컴포넌트를 렌더링하는 과정을 반복한다. (즉, 컴포넌트의 state가 변경되면 반드시 하위 컴포넌트도 모두 리렌더링된다. 하위 컴포넌트의 리렌더링을 막고 싶다면 `React.memo`를 사용한다.)

1. 첫 렌더링에서 React는 루트 컴포넌트를 호출한다. 이 동안 React는 컴포넌트에 대한 DOM 노드를 생성한다.
2. 리렌더링에서 React는 state 업데이트로 렌더링이 촉발된 컴포넌트를 호출한다. 이 동안 React는 이전 렌더링과 비교하여 변경된 속성을 계산한다. (계산만 하고 어떤 작업도 수행하지 않는다.)

### 단계 3: React가 DOM에 변경 사항을 커밋한다

React는 컴포넌트를 호출한 결과 값을 바탕으로 DOM을 수정한다.

1. 첫 렌더링 이후, React는 첫 렌더링 동안 생성한 DOM 노드를 `appendChild()` DOM API를 사용하여 화면에 표시한다.
2. 리렌더링 이후, React는 리렌더링 동안 계산된 값과 DOM을 일치하도록 한다. 이때 이전 렌더링과 최신 렌더링 간에 차이가 있는 경우에만 DOM 노드를 변경한다. 

### 최종적으로 화면에 표시: 브라우저 페인트

브라우저 렌더링(browser rendering)이란 렌더링이 완료되고 React가 DOM 업데이트한 이후 브라우저가 화면을 리페인트(repaint)하는 과정을 말한다. React 렌더링과 구별하기 위해 문서에서는 **"페인팅(painting)"**으로 명시할 것이다.

## Batching

```jsx
function ColorPicker() {
    const [color, setColor] = React.useState('yellow');
    const
    
    const handleClick = () => {
        setColor('blue');
        setColor('purple');
    }
    
    return <button onClick={handleClick}>{color}</button>
}
```

React는 단일 이벤트에 대해서는 이벤트 핸들러 내부의 `set` 함수를 묶어 실행한다. `set` 함수마다 리렌더링을 촉발시키지 않는다는 뜻이다. 기본적인 동작은 다음과 같다.

1. 이벤트 핸들러의 코드를 실행하며 `set` 함수를 만날 때마다 큐에 넣는다.
2. 렌더링 도중 `useState`를 만나면 해당하는 큐를 순회하며 `set` 함수를 꺼내 실행한다.
3. 최종 값(렌더링의 state 값)을 계산한다.

이것을 **batching**이라고 하며, React는 이 작업을 통해 리렌더링의 횟수를 줄이고 한 번의 리렌더링마다 많은 state 업데이트를 처리한다.

그러나 React는 여러 번 발생하는 이벤트에 대해서는 batch하지 않는다. 예를 들어, 각각의 클릭은 개별적으로 처리된다. 첫번째 클릭으로 form을 비활성화하면 두번째 클릭으로 form이 다시 제출되지 않도록 보장한다.

### when using updater function

`set` 함수에 큐의 이전 state를 기반으로 다음 state를 계산하는 함수**(업데이터 함수; updater function)**를 전달할 수 있다. updater 함수는 렌더링 중에 실행되므로 순수해야하고 결과만 반환해야한다.

#### updater function만 사용하는 예시

```jsx
setNumber(n => n + 1);
setNumber(n => n + 1);
setNumber(n => n + 1);
```

1. `setNumber(n => n + 1)` 함수는 `n => n + 1`을 큐에 추가한다.
2. `setNumber(n => n + 1)` 함수는 `n => n + 1`을 큐에 추가한다.
3. `setNumber(n => n + 1)` 함수는 `n => n + 1`을 큐에 추가한다.
4. 다음 렌더링 도중에 `useState`를 호출하면 큐를 순회하며 이전 `number` state의 값을 업데이터 함수의 `n` 인수로 전달하고 그 후부터는 이전 업데이터 함수의 반환값을 가져와 다음 업데이터 함수에 `n`으로 전달하는 것을 반복한다.
5. 최종적으로 `3`을 `number` state로 저장하고 `useState`에서 반환한다.

#### updater function와 교체할 값을 함께 사용하는 예시

첫번째 예시를 살펴보자.

```jsx
setNumber(number + 5);
setNumber(n => n + 1);
```

1. `setNumber(number + 5)` 함수는 `number`가 `0`이므로 `setNumber(0 + 5)`이다. "`5`로 바꾸기"를 큐에 추가한다.
2. `setNumber(n => n + 1)` 함수는 `n => n + 1`을 큐에 추가한다.
3. 다음 렌더링 도중에 큐를 순회한다.
4. 최종적으로 `6`을 `number` state로 저장하고 `useState`에서 반환한다.

두번째 예시를 살펴보자.

```jsx
setNumber(number + 5);
setNumber(n => n + 1);
setNumber(42);
```

1. `setNumber(number + 5)` 함수는 `number`가 `0`이므로 `setNumber(0 + 5)`이다. "`5`로 바꾸기"를 큐에 추가한다.
2. `setNumber(n => n + 1)` 함수는 `n => n + 1`을 큐에 추가한다.
3. `setNumber(42)`의 경우 "`42`로 바꾸기"를 큐에 추가한다.
4. 최종적으로 `42`를 `number` state로 저장하고 `useState`에서 반환한다.

> **출처**
>
> - https://react-ko.dev/learn/queueing-a-series-of-state-updates
> - https://react-ko.dev/reference/react/useState#setstate-caveats

일찍 리렌더링을 촉발하려면 [`flushSync`](#flushSync)를 사용한다.

## Context

> **출처**
>
> - https://react-ko.dev/learn/passing-data-deeply-with-context

React의 Context API를 사용하면 prop drilling 문제를 해결할 수 있다. 기본 사용 방식은 아래와 같다.

1. **Create**: context를 생성한다.
2. **Use**: 데이터가 필요한 컴포넌트에서 해당 context를 사용한다.
3. **Provide**: 데이터를 명시하는 컴포넌트에서 해당 context를 제공한다.

### 단계 1: context 생성하기

`createContext`로 context를 생성할 수 있다. context를 제공하지 않는다면 `defaultValue`가 사용된다. `defaultValue`를 절대 변경할 수 없다.

```jsx
export const MyContext = React.createContext(defaultValue);
```

### 단계 2: context 사용하기

데이터를 사용할 컴포넌트에서 `useContext`에 context를 전달하면 컴포넌트는 해당 context에서 값을 읽을 수 있다. 컴포넌트는 UI 트리에서 가장 가까운 context provider가 지정한 값을 사용한다.

```jsx
function MyComponent() {
    const myValue = React.useContext(MyContext);
    
    return <div>{myValue}</div>;
}
```

### 단계 3: context 제공하기

context provider 컴포넌트 하위의 컴포넌트는 모두 `useContext`를 사용하여 context가 제공하는 값을 읽을 수 있다.

```jsx
function Layout({ value, children }) {
    return <MyContext.ContextProvider value={value}>{children}</MyContext.ContextProvider>
}

function App() {
    return (
        <Layout value={1}>
            {/*이 <Layout> 하위의 <MyComponent>는 1을 표시한다 */}
            <MyComponent />
            <MyComponent />
        </Layout>
        <Layout value={2}>
            {/*이 <Layout> 하위의 <MyComponent>는 2를 표시한다 */}
            <MyComponent />
            <MyComponent />
        </Layout>
    );
}
```



## Effect

Effect는 렌더링으로 발생하는 사이드 이펙트를 의미한다. 예를 들어, 화면에 채팅 컴포넌트를 표시한 후 채팅 서버와 연결하고 싶을 수 있으나 마땅한 이벤트가 없을 수 있다. 이때 Effect를 사용할 수 있다.

> 출처
>
> - https://react-ko.dev/learn/synchronizing-with-effects#what-are-effects-and-how-are-they-different-from-events

### 단계 1: Effect 선언하기

`useEffect`로 렌더링이 완료될 때마다 실행될 Effect를 명시할 수 있다. 즉, `useEffect`에 전달된 함수의 실행은 렌더링이 완료될 때까지 지연된다.

```jsx
import React, { useEffect } from 'react';

function MyComponent() {
    useEffect(() => {
        // 컴포넌트의 렌더링이 완료될 때마다 실행된다.
    });
    
    return <div />;
}
```



게임을 예시로 들어보자.

```javascript
import { useEffect } from 'react';

function App() {
    return <Game />;
}

function Game() {
    useEffect(() => {
        const server = createConnection();
        server.connect();
    });
    
    return <div />;
}
```

위 예제에서 `<Game>` 컴포넌트의 렌더링이 완료될 때마다 서버에 연결한다.

### 단계 2: Effect 의존성 지정하기

> **마운트(mount)**란, 컴포넌트가 처음으로 렌더링되어 화면에 나타나는 것을 말한다.

- 컴포넌트가 마운트될 때 그 한 번만 side effect를 발생시키고 싶다면, 의존성에 빈 배열 `[]`를 전달하면 된다.

```jsx
import React, { useEffect } from 'react';

function MyComponent() {
    useEffect(() => {
        // 컴포넌트가 마운트될 때 한 번만 실행된다.
    }, []);
    
    return <div />;
}
```

- 컴포넌트가 마운트될 때 그 한 번, 그리고 state나 props의 변경으로 인한 리렌더링이 완료될 때마다 side effect를 발생시키고 싶다면 의존성 배열에 해당 값들을 전달하면 된다.

```jsx
import React, { useEffect } from 'react';

function MyComponent({ a }) {
    useEffect(() => {
        // 컴포넌트가 마운트될 때 한 번, 그리고
        // a의 변경으로 리렌더링이 완료될 때마다 실행된다.
    }, [a]);
    
    return <div />;
}
```

React는 의존성 배열을 `Object.is`로 비교하고 이전 렌더링과 동일하면 Effect의 재실행을 건너뛴다. 한편, 



예를 들어보자. `<App />` 컴포넌트에 인풋을 추가하고, 인풋의 값을 state로 관리해보자.

```jsx
import { useState } from 'react';

function App() {
    const [input, setInput] = useState('');
    
    function handleInputChange(e) {
        setInput(e.target.value);
    }
    
    return (
        <>
        	<input value={input} onChange={handleInputChange}/>
        	<Game />
        </>
    );
}

function Game() {
    useEffect(() => {
        const server = createConnection();
        server.connect();
    });
    
    return <div />;
}
```

이때 `<Game />`은 렌더링이 완료될 때마다 서버에 연결을 반복한다. 그러니 사용자가 인풋 값을 변경할 때마다 `<App />`이 리렌더링될 때마다 `<Game />`의 리렌더링도 이루어진다. 하지만 나는 `<Game />` 컴포넌트가 마운트될 때 그 한 번만 서버에 연결하기를 원할 수 있다. 그렇다면 의존성 배열에 `[]`를 전달하면 된다.

```jsx
import { useEffect } from 'react';


function Game() {
    useEffect(() => {
        const server = createConnection();
        server.connect();
    }, []);
    
    return <div />;
}
```

### 단계 3: 클린업 추가하기

`useEffect`에 전달한 함수에서 반환하는 함수는 **클린업 함수(cleanup function)**이다. 클린업 함수를 전달하지 않는다면 빈 클린업 함수를 반환한 것처럼 동작한다. 클린업 함수는 Effect가 다시 실행되기 전마다, 그리고 컴포넌트가 **언마운트(unmount)될** 때 한 번 실행된다.

```jsx
import React, { useEffect } from 'react';

function MyComponent() {
    useEffect(() => {
        return () => {
            // Effect가 다시 실행되기 전마다, 그리고
            // 컴포넌트가 언마운트될 때 한 번 실행된다.
        }
    });
    
    useEffect(() => {
        return () => {
            // 컴포넌트가 언마운트 될 때 한 번 실행된다.
        }
    }, []);
    
    return <div />;
}
```



예를 들어보자. `<Game />`은 마운트될 때마다 서버와의 연결을 만든다. 이 연결은 끊지 않는 한 계속 쌓이게 된다. 이때 연결을 끊는 함수를 반환하면 된다.

```jsx
import { useEffect } from 'react';


function Game() {
    useEffect(() => {
        const server = createConnection();
        server.connect();
        
        return () => server.disconnect();
    }, []);
    
    return <div />;
}
```

> 출처
>
> - https://react-ko.dev/learn/synchronizing-with-effects#how-to-write-an-effect

### 언제 사용하는가?

#### 이벤트 구독하기

```jsx
useEffect(() => {
   function handleClick(e) {
       console.log('클릭 이벤트 발생');
   }
    
    window.addEventListener('click', handleClick);
    
    return () => window.removeEventListener('click', handleClick);
});
```

cleanup 함수를 추가하여 클릭 이벤트에 한 번에 한 개의 `handleClick` 이벤트 핸들러만 등록되도록 한다.

#### 데이터 페칭하기

[React에서 데이터 페칭하기](#React에서-데이터-페칭하기)를 참고한다.

> **출처**
>
> - https://react-ko.dev/learn/synchronizing-with-effects#how-to-handle-the-effect-firing-twice-in-development

### Effect가 아닌 것들

1. 애플리케이션을 초기화할 때: 컴포넌트가 마운트될 때 한 번이 아니라, 애플리케이션이 시작될 때 한 번만 실행되어야하는 로직은 컴포넌트 외부에 넣어도 된다.

   ```diff
   +if (typeof window !== 'undefined') {
   +	checkAuthToken();
   +	loadDataFromLocalStorage();
   }
   
   function App() {
   -	useEffect(() => {
   -		checkAuthToken();
   -		loadDataFromLocalStorage();
   -	}, []);
   }
   ```

2. 멱등하지 않은 메서드를 실행하는 것: 가령 HTTP POST 요청을 `useEffect`에서 실행한다면, 컴포넌트가 마운트될 때 해당 요청이 이루어질 것이다. `<Strict Mode>`에서는 렌더링이 두 번 실행되므로 이것이 잘못되었음을 알 수 있다.

> 출처
>
> - https://react-ko.dev/learn/synchronizing-with-effects#not-an-effect-initializing-the-application

### 기본 동작 방식

컴포넌트의 props, 지역 변수, 이벤트 핸들러, Effect는 컴포넌트 호출 당시의 state로 계산이 된다.

```jsx
import React from 'react';

function Game({ mode }) {
    React.useEffect(() => {
        const server = createConnection(mode);
        server.connect();
        
        return () => server.disconnect();
    }, [mode]);
    
    return <div>게임 모드: {mode}</div>;
}
```

1. 첫 렌더링 때 `mode`가 `'소환사의 협곡'`이었다면 `useEffect`는 다음과 같이 계산된다.
   ```jsx
   React.useEffect(() => {
       const server = createConnection('소환사의 협곡');
       server.connect();
   
       return () => server.disconnect();
   }, ['소환사의 협곡']);
   ```
2. 첫번째 렌더링이 완료되고(컴포넌트가 마운트되고) 첫번째 Effect가 실행된다. `mode`가 `'소환사의 협곡'`인 서버와 연결된다.
3. 상위 컴포넌트가 리렌더링을 촉발하여 `<Game>` 컴포넌트도 리렌더링된다.
4. 두번째 렌더링 때 `mode`가 `'소환사의 협곡'`로 바뀌지 않았으므로 `useEffect`는 다음과 같이 계산된다.
   
   ```jsx
   React.useEffect(() => {
       const server = createConnection('소환사의 협곡');
       server.connect();
   
       return () => server.disconnect();
   }, ['소환사의 협곡']);
   ```
5. 두번째 렌더링이 완료되고 두번째 Effect가 실행되어야 하나, 의존성 배열이 이전 Effect와 동일하므로 건너뛴다.
6. 상위 컴포넌트가 `setMode('칼바람 나락')`으로 리렌더링을 촉발하여 `<Game>` 컴포넌트도 리렌더링된다.
7. 세번째 렌더링 때 `mode`가 `'칼바람 나락'`이므로 `useEffect`는 다음과 같이 계산된다.
   ```jsx
   React.useEffect(() => {
       const server = createConnection('칼바람 나락');
       server.connect();
   
       return () => server.disconnect();
   }, ['칼바람 나락']);
   ```
8. 세번째 렌더링이 완료되고 세번째 Effect가 실행되기 전, 의존성 배열의 내용이 변경되었으므로 가장 최근에 실행된 Effect의 cleanup 함수가 실행된다. 즉, 첫번째 Effect의 cleanup 함수가 실행되어 `mode`가 `'소환사의 협곡'`인 서버와 연결이 끊어진다.
   첫번째 Effect의 cleanup 함수가 실행된 후, 세번째 Effect가 실행된다. `mode`가 `'칼바람 나락'`인 서버와 연결된다.
9. `<Game>` 컴포넌트가 언마운트될 때, 가장 마지막으로 실행된 Effect의 cleanup 함수가 실행된다. 즉, 세번째 Effect의 cleanup 함수가 실행된다. `mode`가 `'칼바람 나락'`인 서버와 연결이 끊어진다.

> 출처
>
> - https://react-ko.dev/learn/synchronizing-with-effects#each-render-has-its-own-effects

## React에서 데이터 페칭하기

컴포넌트를 렌더링하기 위해 필요한 데이터를 서버에서 페칭해와야한다고 하자. `useState`와 `useEffect`를 사용해볼 수 있다. 기본적은 로직은 아래와 같다.

1. 컴포넌트의 렌더링이 완료된다. 현재는 `useState`에 전달된 초기값을 화면에 보여준다.
2. `useEffect`가 실행된다. 데이터를 페칭해오고 해당 데이터로 `setState`를 호출한다.
3. 컴포넌트의 리렌더링이 완료된다. 페칭해 온 데이터를 화면에 보여준다.

기본적인 구현체는 아래와 같다.

```jsx
import React from 'react';

function FetchComponent (url){
    const [data, setData] = React.useState(null);
    
    React.useEffect(() => {
        async function fetchData() {
            const res = await fetch(url);
            const newData = await res.json();
            
            setData(newData);
        }
        
        fetchData();
    }, [url]);
    return <div>JSON.stringify(data)</div>
}
```

### 왜 `setup` 함수는 `async`일 수 없는가?

```javascript
useEffect(async () => {
    const val = await Promise.resolve(1);
    console.log(val);
}, []);
```

왜 이처럼 `setup` 함수는 `async` 함수로 만들 수 없을까? 다음 제한들을 살펴보자.

1. `async` 함수는 암묵적으로 프로미스 객체를 반환한다.
2. `setup` 함수는 cleanup 함수를 반환하거나 아무것도 반환하지 않는다.

이에 따르면 `setup` 함수는 `async` 함수일 수 없다. 하지만 `setup` 함수 내에서 비동기 함수를 실행할 수 있으므로, 다음과 같이 비동기 로직은 `async` 함수로 감싼 후 호출해주면 된다.

```jsx
useEffect(async () => {
    async function resolve() {
        const val = await Promise.resolve(1);
        console.log(val);
    }
    
    resolve();
}, []);
```

> 출처
>
> - https://www.robinwieruch.de/react-hooks-fetch-data/

### useEffect를 사용했을 때의 문제점: 경쟁 상태 발생

> **경쟁 상태(race condition)**는 공유 자원에 두 개 이상의 프로세스가 접근할 때 그 접근 순서에 따라 결과가 달라지는 상황을 뜻한다. [운영체제 - 프로세스 동기화](https://github.com/leegwae/operating-system/blob/main/Process%20Synchronization.md)

데이터를 페칭하는 컴포넌트가 연달아 리렌더링된다고 하자. 앞 선 요청이 뒤따른 요청보다 응답이 늦어질 수도 있다. 즉, 매번 서로 다른 결과를 표시하는 경쟁 상태가 발생할 수 있다.

[링크의 예시](https://codesandbox.io/s/race-condition-with-useeffect-8dv53q?file=/src/FetchComponent.js)를 살펴보자. 우선 `<App >` 컴포넌트는 사용자가 버튼을 누를 때마다 새로이 요청할 자원의 id를 생성한다. 여기서는 요청 id가 임의의 난수이다.

```jsx
import React from "react";

export default function App() {
  const [request, setRequest] = React.useState(0);

  const handleClick = () => {
    const nextRequest = Math.round(Math.random() * 100);
    setRequest(nextRequest);
  };

  return (
    <div>
      <div>current request: {request}</div>
      <button onClick={handleClick}>request next!</button>
      <FetchComponent request={request} />
    </div>
  );
}

```

`<FetchComponent />`는 `<App />`으로부터 요청 id를 받아 해당 id로 네트워크 요청하고 응답을 받아 표시한다. 여기서는 네트워크 요청이 `setTimeout`으로 resolve를 지연한 임의의 Promise 객체이다. 이 프로미스는 요청 id를 그대로 resolve한다.

```jsx
import React from 'react';

function FetchComponent({ request }) {
  const [response, setResponse] = React.useState(0);

  React.useEffect(() => {
    async function fetch() {
      const res = await new Promise((resolve) => {
        const delay = Math.round(Math.random() * 1000 * 10);
        setTimeout(() => resolve(request), delay);
      });
      setResponse(res);
    }

    fetch();
  }, [request]);

  return <div id="response">current response: {response}</div>;
}
```

### 경쟁 상태 방지하기

cleanup 함수를 활용하여 경쟁 상태를 방지해보자. 여기서는 두 가지 방법을 알아볼 것이다.

1. 데이터가 stale한지 나타내는 플래그 사용하기
2. `AbortController` API 사용하기

#### 데이터가 stale한지 나타내는 플래그 사용하기

데이터가 stale한지 나타내는 플래그 변수를 선언한 후, cleanup 함수가 실행되면 stale하다고 표시한다. 사용자가 버튼을 연달아 눌러 `<FetchComponent>`가 리렌더링되면 이전 요청이 뒤늦게 응답 와도 `stale === true`이므로 `set` 함수가 실행되지 않을 것이다.

```diff
import React from 'react';

function FetchComponent({ request }) {
  const [response, setResponse] = React.useState(0);

  React.useEffect(() => {
+   let stale = false;
    async function fetch() {
      const res = await new Promise((resolve) => {
        const delay = Math.round(Math.random() * 1000 * 10);
        setTimeout(() => resolve(request), delay);
      });
-      setResponse(res);
+      if (!stale) setResponse(res);
    }

    fetch();
    
+   return () => { stale = true; }
  }, [request]);

  return <div id="response">current response: {response}</div>;
}
```

#### `AbortController` API 사용하기

`AbortController`를 사용하여 하나 이상의 웹 요청(Web request)을 중단할 수 있다. (그러니 이 방법은 위에서처럼 프로미스로 네트워크 요청을 모킹한 경우에는 쓸 수 없다.) 네트워크 요청하는 함수에 컨트롤러의 `signal`을 전달한 후, cleanup 함수가 실행되면 `abort` 메서드를 호출하여 요청을 중단한다. 사용자가 버튼을 연달아 눌러 `<FetchComponent>`가 리렌더링되면 이전 렌더링의 요청은 중단되어 `set` 함수가 실행되지 않을 것이다.

```diff
import React from 'react';

function FetchComponent (url){
	const [data, setData] = React.useState(null);
    
	React.useEffect(() => {
+		const controller = new AbortController();
		async function fetchData() {
+			try {
-				const res = await fetch(url);
+				const res = await fetch(url, { signal: controller.signal });
				const newData = await res.json();

				setData(newData);
+			} catch (err) {
+				if (err.name !== 'AbortError') throw err;
+				// 여기서 네트워크 요청을 중단하여 발생한 에러를 처리한다.
+			} catch (err) {
+				// 여기서 그 밖의 에러를 처리한다.
+			}
		}
        
		fetchData();
        
+		return () => controller.abort();
	}, [url]);

	return <div>JSON.stringify(data)</div>
}
```

> 출처
>
> - https://react-ko.dev/learn/synchronizing-with-effects#fetching-data
> - https://react-ko.dev/reference/react/useEffect#fetching-data-with-effects
> - https://www.robinwieruch.de/react-hooks-fetch-data/
> - https://maxrozen.com/race-conditions-fetching-data-react-with-useeffect
> - https://developer.mozilla.org/en-US/docs/Web/API/AbortController

### useEffect를 사용한 데이터 페칭의 단점

`useEffect`를 사용한 데이터 페칭의 단점은 아래와 같다.

1. Effect를 사용하는 것은 효율적이지 않다. Effect는 클라이언트에서 실행되므로, 클라이언트는 서버로부터 모든 HTML과 JavaScript를 받아와 React 앱을 렌더링한 후에야 데이터도 페칭해야한다는 것을 알 수 있다.
2. Effect를 사용하면 네트워크 워터폴이 만들어질 수 있다. 상위 컴포넌트를 렌더링하고 데이터를 페칭한 후 하위 컴포넌트에 대해서도 그러는 작업이 재귀적으로 발생한다.
3. Effect를 사용하면 응답 캐싱, 요청 중복 제거, 네트워크 워터폴 방지(데이터를 미리 로딩하거나 라우트에 데이터 요청을 호이스팅한다)을 직접 구현해야 한다.

### 어떤 대안을 사용할 것인가?

1. 프레임워크(Next.js, Remix 등)를 사용하고 해당 프레임워크에서 제공하는 빌트인 데이터 페칭 메커니즘을 사용한다.
2. 클라이언트 사이드 캐시를 만들거나 오픈 소스(SWR, React Query, React Router 등)를 사용한다.

> 출처
>
> - https://react-ko.dev/learn/synchronizing-with-effects#what-are-good-alternatives-to-data-fetching-in-effects

## Effect는 필요하지 않을 수도 있다

Effect가 필요하지 않은 경우에는 대개 아래와 같다.

1. Effect에서 렌더링을 위해 데이터 변경하기
2. Effect로 사용자 이벤트 처리하기

> - https://react-ko.dev/learn/you-might-not-need-an-effect#how-to-remove-unnecessary-effects

### 1. state의 변경으로 또다른 state 변경하기

```diff
import React, { useState, useEffect } from 'react';

function Form() {
  const [firstName, setFirstName] = useState('Luxanna');
  const [lastName, setLastName] = useState('Crownguard');
  
  // 💥: 불필요한 state와 Effect!
-  const [fullName, setFullName] = useState('');
-	useEffect(() => {
-		setFullName(`${firstName}${lastName}`);
-	}, [firstName, lastName]);
  
  // ✨: 렌더링 중에 계산하기
+  const fullName = `${firstName}${lastName}`;
  
  // ...
}
```

`fullName`을 state로 두고 `firstName`이나 `lastName`이 변경되면 다음과 같은 일이 발생한다.

1. `firstName`이나 `lastName`이 변경되어 `<Form>`의 리렌더링이 촉발된다.
2. `<Form>`이 변경된 state로 렌더링된다.
3. 렌더링이 완료된 후, 렌더링 동안 계산된 Effect가 실행된다. 이때 `fullName`이 변경되어 또다시 `<Form>`의 리렌더링이 촉발된다.
4. `<Form>`이 변경된 state로 렌더링된다.
5. 렌더링이 완료된 후, 의존성 배열이 변경되지 않았으므로 Effect의 실행은 건너뛴다.

`fullName`을 렌더링 중에 계산하면 다음과 같은 일이 발생한다.

1. `firstName`이나 `lastName`이 변경되어 `<Form>`의 리렌더링이 촉발된다.
2. `<Form>`이 변경된 state로 렌더링된다.

불필요한 리렌더링이 발생하지 않는 것이다.

> 출처
>
> - https://react-ko.dev/learn/you-might-not-need-an-effect#updating-state-based-on-props-or-state

이때 계산이 비싸다면, `useMemo`를 사용하여 메모이제이션할 수 있다.

```diff
import { useMemo, useState } from 'react';

function TodoList({ todos, filter }) {
  // 💥: `getFilteredTodos`가 비싸다면
-	const filteredTodos = getFilteredTodos(todos, filter);
  
  // ✨: `useMemo`로 메모이제이션한다
+	const filteredTodos = useMemo(() => getFilteredTodos(todos, filter), [todos, filter]);
  
  // ...
}
```

>출처
>
>- https://react-ko.dev/learn/you-might-not-need-an-effect#caching-expensive-calculations

### 2. prop의 변경으로 모든 state 변경하기

다음 [예시](https://codesandbox.io/s/react-resetting-state-on-prop-change-in-an-effect-yc9jg4?file=/src/App.js)를 살펴보자. `<App>` 컴포넌트는 사용자가 버튼을 클릭할 때마다 사용자가 입력한 사용자 이름의 `<Profile>` 컴포넌트를 보여준다.

```jsx
import React from "react";

function App() {
  const [userId, setUserId] = React.useState("");
  const inputRef = React.useRef(null);

  function handleClick() {
    setUserId(inputRef.current.value);
  }

  return (
    <div>
      <input ref={inputRef} />
      <button onClick={handleClick}>프로필 이동하기</button>
      <hr />
      <Profile userId={userId} />
    </div>
  );
}
```

`<Profile>`에서 사용자는 인풋에 값을 입력하여 댓글을 달 수 있다. `<Profile>`은 `comment` state를 사용하여 인풋의 값을 관리하고 있다.

```jsx
function Profile({ userId }) {
  const [comment, setComment] = React.useState("");

  return (
    <div>
      <div>{userId}님의 프로필</div>
      댓글 달기:
      <input value={comment} onChange={(e) => setComment(e.target.value)} />
    </div>
  );
}
```

React의 UI 트리에서 동일한 위치에 있는 동일한 컴포넌트는 리렌더링되어도 state를 유지한다. [참고](#동일한-위치에-있는-동일한-컴포넌트는-state를 유지한다) 이에 따르면 `userId`가 변경되어 `<App>`과 그 하위 컴포넌트인 `<Profile>`의 리렌더링이 발생해도 `<Profile>`의 state인 `comment`는 초기화되지 않는다. 그래서 `useEffect`를 사용하여 `userId`가 변경되면 `setComment`를 호출하고자 할 수 있다.

```diff
function Profile({ userId }) {
  const [comment, setComment] = React.useState("");
	
+	React.useEffect(() => {
+		setComment("");
+ }, [userId]);
  
  // ...
}
```

그러나 이 경우 `userId`의 변경으로 `<Profile>`에 총 두 번의 리렌더링을 발생시킨다. 이 경우 컴포넌트에 고유한 `key`를 부여하여 컴포넌트를 식별할 수 있도록 하면 된다.

```diff
import React from "react";

function App() {
  //...
  return (
  	<div>
  		{/* ... */}
-			<Profile userId={userId} />
+			<Profile userId={userId} key={userId} />
		</div>
  );
}

function Profile({ userId }) {
  const [comment, setComment] = React.useState("");
	
-	React.useEffect(() => {
-		setComment("");
- }, [userId]);
  
  // ...
}
```

### 3. props의 변경으로 일부 state 변경하기

props가 바뀌었을 때 모든 state를 재설정하려면, `key`를 사용하여 컴포넌트에 고유성을 부여하면 된다. 그러나 props가 바뀌었을 때 일부 state만 변경하고 싶다면 다음과 같이 Effect를 사용할 수 있다. `item`이 바뀔 때 `bar` 상태만 초기화하고 싶다고 하자.

```jsx
function MyComponent({ item  }) {
  const [foo, setFoo] = useState(null);
  const [bar, setBar] = useState(null);
  
  useEffect(() => {
    setBar(null);
  }, [item])

  // 생략...
}
```

그러나 Effect에서 `set` 함수를 호출하면 불필요한 렌더링이 발생하게 된다. 차라리 다음과 같이 렌더링 도중에 `set` 함수를 호출하는 것이 좋다.

```diff
function MyComponent({ item  }) {
  const [foo, setFoo] = useState(null);
  const [bar, setBar] = useState(null);
  
-  useEffect(() => {
-   setBar(null);
- }, [item]);

+	const [prevItem, setPrevItem] = useState(item);
+ if (prevItem !== item) {
+		setPrevItem(item);
+		setBar(null);
+	}

  // 생략...
}
```

[이전 렌더링의 데이터를 참조하기](#이전-렌더링의-데이터를-참조하기)를 참고한다.

> **출처**
>
> - https://react-ko.dev/learn/you-might-not-need-an-effect#adjusting-some-state-when-a-prop-changes
> - https://react-ko.dev/reference/react/useState#storing-information-from-previous-renders

### 4. 외부 스토어 구독하기

[useSyncExternalStore](#useSyncExternalStore)를 참고한다.



TODO: https://react-ko.dev/learn/sharing-state-between-components#controlled-and-uncontrolled-components

## Hooks

Hooks는 React가 렌더링 중일 때만 사용할 수 있는 함수이다. Hooks를 통해 React의 기능들을 사용할 수 있는데, state가 이 중 하나이다.

> **출처**
>
> - https://react-ko.dev/learn/state-a-components-memory#meet-your-first-hook
> - https://react-ko.dev/reference/react

## useState

`useState`는 컴포넌트에서 state 변수를 추가할 수 있게 해주는 React Hook이다. 컴포넌트가 렌더링될 때마다 `useState`는 배열을 제공한다.

```javascript
const [state, setState] = React.useState(initialState);
```

### 매개변수 `initialState`

첫 렌더링에서 state의 값으로 사용할 초기값이다. 첫 렌더링 이후의 렌더링에서는 무시된다.

- 함수인 경우 *초기화 함수(initializer function)*로 취급한다. 순수해야하며, 인자를 가지지 않고 반드시 값을 반환해야한다. 이 함수를 호출한 값을 초기값으로 사용한다.

- 그 외의 값은 초기값으로 사용한다. 만일 함수 호출을 전달한다면, 이 함수는 매 렌더링마다 실행되나 반환값은 첫 렌더링에서만 사용된다. 따라서 함수가 비용이 비싸다면 초기화 함수로 전달하도록 한다.

  ```jsx
  // 😣: 함수 호출은 매 렌더링마다 expensiveFn이 실행된다.
  const [state, setState] = React.useState(expensiveFn());
  
  // 🥰: 초기화 함수는 첫 렌더링때만 expensiveFn이 실행된다.
  const [state, setState] = React.useState(expensiveFn);
  ```

> **출처**
>
> - https://react-ko.dev/reference/react/useState#usestate
> - https://react-ko.dev/reference/react/useState#avoiding-recreating-the-initial-state

### 반환값

```jsx
const [state, setState] = React.useState(초기값);
```

두 개의 값을 담은 배열을 반환한다.

1. state 변수: 현재 렌더링 중인 state의 값을 저장하고 있다. 첫번째 렌더링 중의 값은 `initialState`와 일치한다.
2. `set` 함수: 다음 렌더링의 state 값을 전달하여 리렌더링을 촉발할 수 있다.

### `set` 함수

`set` 함수에 전달된 값을 이용하여 **다음 렌더링의 state 값**을 지정할 수 있다. `set` 함수는 현재 렌더링의 state 값을 바꾸지 않으며 다만 리렌더링을 촉발시킨다. 항상 한 렌더링 내의 state는 불변하며, state의 업데이트는 반드시 리렌더링을 촉발시킨다.

- 함수인 경우, 이 함수는 업데이터 함수(updater function)로 취급한다. 업데이터 함수는 순수하며 결과만 반환해야한다. 업데이터 함수는 인자로 큐에 있는 이전 state의 값을 받는다.

  ```jsx
  setState(n => n + 1);	// 큐에 있는 이전 state에 1를 더한 값으로 교체한다.
  ```

- 그 외의 값은 경우, 다음 렌더링의 state는 해당 값이 된다.

  ```jsx
  setState(2);	// 2로 교체한다
  setState(state + 1); // 현재 렌더링 중인 state에 1을 더한 값으로 교체한다.
  ```

[Batching - 업데이터 함수 사용하기](#when-using-updater-function)를 참고한다.

### 기본 작동 방식

```javascript
const [index, setIndex] = useState(0);
```

작동 방식은 다음과 같다.

1. 컴포넌트가 처음으로 렌더링된다. `useState(0)`는 `[0, setIndex]`를 반환하며, React는 `index`의 최신 값을 `0`로 기억한다.
2. `setIndex(index + 1)`을 실행한다. React는 `index`가 `1`임을 기억하고 다음 렌더링을 촉발한다.
3. 컴포넌트가 두번째로 렌더링된다. `useState(0)`이지만 React는 `index`가 `1`임을 기억하고 있으므로, `[1, setIndex]`를 반환한다.

> **출처**
>
> - https://react-ko.dev/learn/state-a-components-memory#anatomy-of-usestate

이에 따르면, props를 초기값으로 전달받은 state는 해당 props가 변한다고 그 값으로 다시 초기화되는게 아니다.

```jsx
function MyComponent({ state }) {
  const [prevState, _] = useState(state);
  
  return <div>prev state is {prevState}</div>;
}

function App() {
  const [state, setState] = useState(0);
  
  return (
    <>
    	<button onClick={() => setState(state => state + 1)}>
      	current count is {state}
    	</button>
    </>
  );
}
```

버튼을 눌러 `state`는 업데이트되지만 `prevState`는 업데이트되지 않는다. 상태의 업데이트는 오로지 해당 상태의 `set` 함수를 호출하여 가능하다. 초기값은 첫 렌더링에서만 사용되고, 리렌더링부터는 버려진다.



### React는 어떤 state를 반환해야하는지 어떻게 알 수 있는가?

`useState`는 어떤 state인지 식별할 수 있는 키 값을 전달받지 않는다. 그러면 어떻게 무슨 state 변수를 반환해야하는지 알 수 있을까? 그것은 동일한 컴포넌트에서 매 렌더링마다 훅이 항상 같은 순서로 호출되기 때문이다.

React는 내부적으로 컴포넌트마다 state와 `set` 함수 쌍의 배열을 유지한다. `useState`를 호출할 때마다 state 쌍을 제공하고 인덱스를 증가시킨다.

```javascript
let componentHooks = [];
let currentHookIndex = 0;

// 단순화된 useState 로직
function useState(initialState) {
  let pair = componentHooks[currentHookIndex];
  if (pair) {
    // 첫번째 렌더링이 아니기 때문에 이미 state 쌍이 존재한다.
    // 해당 쌍을 반환하고 다음 훅 호출을 준비한다.
    currentHookIndex++;
    return pair;
  }

  // 첫번째 렌더링이라면 state 쌍을 생성하고 저장한다.
  // so create a state pair and store it.
  pair = [initialState, setState];

  function setState(nextState) {
    // 사용자가 state 변화를 요청하면 새로운 값을 쌍에 넣는다.
    // put the new value into the pair.
    pair[0] = nextState;
    updateDOM();
  }

  // 다음 렌더링에 사용할 state 쌍을 저장하고 다음 훅 호출을 준비한다.
  componentHooks[currentHookIndex] = pair;
  currentHookIndex++;
  return pair;
}
```

> **출처**
>
> - https://react-ko.dev/learn/state-a-components-memory#how-does-react-know-which-state-to-return
> - TODO: https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e

### React는 무엇을 기준으로 state가 변경되었다고 판단하는가?

React는 `Object.is`로 이전 렌더링의 state와 `set` 함수에 전달된 값을 비교하여 같다고 판단하면 state가 업데이트되었다고 판단하고 리렌더링을 촉발시킨다.

```javascript
if (Object.is(prevState, curState)) {
    triggerRender();
}
```

이에 따르면 state가 객체일 때, 다음 렌더링에 적용할 state 값으로 기존의 state 객체를 넘기면 같은 객체로 판별되어 리렌더링이 촉발되지 않는다.

### 왜 `set` 함수에 넘길 객체는 새로 생성해야하는가?

`set` 함수에는 다음 렌더링에 적용할 값을 전달한다. React는 이 값과 전 렌더링의 값을 `Object.is`로 비교하여 같으면 업데이트를 무시한다. 이에 따르면, 객체의 프로퍼티 값을 수정해도 동일한 객체를 가리키고 있으므로 업데이트가 무시된다. 그러니 새로운 객체를 생성하여 넘기도록 한다.

```javascript
// 😣: 기존 객체를 변이하여 넘기면 업데이트가 무시된다.
obj.foo = 'changed';
setObj(obj);
// 🥰: 새로운 객체를 생성하여 넘기면 업데이트한다.
setObj({ ...obj, foo: 'changed' });
```

> **출처**
>
> - https://react-ko.dev/reference/react/useState#ive-updated-the-state-but-the-screen-doesnt-update

### `set` 함수는 비동기적으로 동작한다

```jsx
const handleClick = () => {
    setValue('changed');
    console.log(value);	// unchanged
}
```

`set` 함수를 호출했는데도 로그에는 바뀐 값으로 출력되지 않는다. 우선, 컴포넌트의 props, 지역 변수, 이벤트 핸들러는 컴포넌트 호출 당시의 state로 계산이 된다. 이에 따르면 `handleClick`의 모습은 사실상 아래와 같다. 

```jsx
const handleClick = () => {
    setValue('changed');
    console.log('unchanged');
}
```

또한 React는 하나의 이벤트마다 이벤트 핸들러 내부의 `set` 함수들을 동기적으로 실행하지 않고 큐에 모아넣었다가 다음 렌더링 때 state를 계산하며 실행한다. ([Batching](#Batching)을 참고한다.)

이러한 경우 `set` 함수에 전달할 값을 따로 변수에 저장해야한다.

```jsx
const handleClick = () => {
    const newValue = 'changed';
    setValue(newValue);
    console.log(newValue);	// unchanged
}
```

> **출처**
>
> - https://react-ko.dev/reference/react/useState#adding-state-to-a-component
> - https://react-ko.dev/reference/react/useState#ive-updated-the-state-but-logging-gives-me-the-old-value

### 이전 렌더링의 데이터를 참조하기

prop가 달라진 경우 일부 state만 재설정하고 싶을 수 있다. 이때는 렌더링 도중 `set` 함수를 호출한다.

```jsx
function MyComponent({ item  }) {
  const [foo, setFoo] = useState(null);
  const [bar, setBar] = useState(null);
   
  const [prevItem, setPrevItem] = useState(item);
  
  if (prevItem !== item) {
  	setPrevItem(item);
  	setBar(null);
  }

  // 생략...
}
```

React는 컴포넌트가 렌더링 도중에 자신의 `set` 함수를 호출하면(다른 컴포넌트의 `set` 함수를 호출하면 에러를 발생시킨다) `return`문으로 종료된 직후 반환된 JSX를 버리고 즉시 컴포넌트를 리렌더링한다. 자식들은 기존의 렌더링은 건너뛰고 최종 렌더링만 진행하게 된다.

> **출처**
>
> - https://react-ko.dev/reference/react/useState#storing-information-from-previous-renders

### 함수를 state의 값으로 저장하기

- `useState`에 전달된 함수는 초기화 함수로 취급하므로 해당 함수를 호출하여 초기값을 저장하려고 시도한다.
- `set` 함수에 전달된 함수는 업데이터 함수로 취급하므로 해당 함수를 호출하여 다음 렌더링의 state로 적용하려고 시도한다.

함수를 state의 값으로 지정하려면 해당 함수 객체를 반환하는 함수를 전달한다.

```jsx
const [fn, setFn] = useState(() => myFunction);

const handleClick = () => {
    setFn(() => myFunction);
}
```

## useContext

> **출처**
>
> - https://react-ko.dev/reference/react/useContext

`useContext`는 컴포넌트가 context를 읽고 구독할 수 있도록 해주는 React Hook이다.

```jsx
const value = React.useContext(Context);
```

### 매개변수 `Context`

`React.createContext`로 생성한 context 객체이다.

### 반환값

전달한 `Context`의 context 값을 반환한다.

- 트리에서 가장 가까운 상위 `<Context.Provider>` 컴포넌트의 `value` props에 지정된 값을 반환한다.
- context provider를 찾을 수 없다면 `createContext` 당시 전달했던 값을 반환한다.

반환된 값은 항상 최신이다. React는 context가 변경되면, 변경된 값을 받은 context provider부터 context를 읽는 모든 자식 컴포넌트까지 리렌더링한다(즉, 그 사이에 context를 사용하지 않는 컴포넌트도 리렌더링의 대상이다).

### React는 무엇을 기준으로 context가 변경되었다고 판단하는가?

React는 `Object.is`로 context provider에 전달되었던 이전 값과 다음 값을 비교한다.

### context는 어떻게 변경하는가?

context에 값과 세터 함수를 넘기면 된다.

```jsx
import React from 'react';

const UserContext = React.createContext(null);

function Profile() {
    const { user, setUser } = useContext(UserContext);
    
    return <div>{user.name}</div>;
}

function App() {
    const [user, setUser] = useState(null);
    
    return (
        <UserContext.Provider value={{ user, setUser }}>
        	<Profile />
        </UserContext.Provider>
    );
}
```

> **출처**
>
> - https://react-ko.dev/reference/react/useContext#updating-data-passed-via-context

### context 값이 객체인 경우 최적화하기

```jsx
function App() {
    const [user, setUser] = useState(null);
    
    function login(response) {
        setUser(response.user);
    }
    
    return (
        <UserContext.Provider value={{ user, login }}>
        	<Profile />
        </UserContext.Provider>
    );
}
```

`App`이 리렌더링될 때마다 `user`와 `setUser`가 새로운 객체가 되므로, 매 렌더링마다 `<UserContext.Provider>` 내부에서 `useContext(UserContext)`를 호출하는 컴포넌트까지 모두 리렌더링된다. `user`가 "실제로" 변경되지 않는 한 리렌더링이 되지 않도록 `user`와 `setUser`의 계산 값을 메모이제이션할 수 있다.

```jsx
function App() {
    const [user, setUser] = useState(null);
    
    const login = useCallback((response) => {
        setUser(response.user);
    }, []);
    
    const memoContext = useMemo(() => ({
        user, login
    }), [user, login]);
    
    return (
        <UserContext.Provider value={memoContext}>
        	<Profile />
        </UserContext.Provider>
    );
}
```

> **출처**
>
> - https://react-ko.dev/reference/react/useContext#optimizing-re-renders-when-passing-objects-and-functions

## useReducer

> **출처**
>
> - https://react-ko.dev/reference/react/useReducer

`useReducer`를 사용하면 컴포넌트에서 state 업데이트 로직을 컴포넌트 외부의 순수 함수로 분리할 수 있다. 인자와 반환 값의 형태가 조금 다른 것을 제외하면 `useState`와 기본 동작은 동일하다.

```jsx
const [state, dispatch] = React.useReducer(reducer, initialArg, init?)
```

### 매개변수

1. `reducer`: state가 업데이트되는 방식을 지정한 리듀서 함수(reducer function)이다. 순수 함수로, **이전 state**와 action을 인자로 받고 **다음 state를 반환**한다. action은 모든 값이 될 수 있으나 관례상 업데이트 로직을 구분하는 `type` 프로퍼티를 가진 객체이다.

   ```jsx
   function reducer(state, action) {
       switch(action.type) {
           case 'ADD_TASK': {
               return [...state, { id: action.id, text: action.text }];
           }
           case 'DELETE_TASK': {
               return state.filter(t => t.id !== action.id );
           }
       }
   }
   ```

2. `initialArg`: 첫 렌더링에서 state의 값으로 사용할 초기값이다. 

3. `init`(optional): 초기화 함수로, 이 함수를 전달하면 state는 `init(initialArg)`로 설정된다.

### 반환값

1. state 변수: 현재 렌더링 중인 state의 값을 저장하고 있다. 첫번째 렌더링 중의 값은 `initialArg` 또는 `init(initialArg)`와 일치한다.
2. `dispatch` 함수: action을 전달하여 리렌더링을 촉발할 수 있다.

### `disptach` 함수

`dispatch`에 전달된 action을 이용하여 다음 렌더링의 state 값을 계산할 수 있다. `dispatch` 함수는 현재 렌더링의 state 값을 바꾸지 않는다. 항상 한 렌더링 내의 state는 불변하며, state의 업데이트는 반드시 리렌더링을 촉발시킨다.

```jsx
const [state, dispatch] = useReducer(reducer, []);

function handleClick() {
    dispatch({ type: 'ADD_TASK', id: uuid(), '집 사기' });
}
```

### 기본 작동 방식

1. `dispatch`가 유일한 인자로 action을 전달받는다.
2. React가 `reducer`에 이전 state와 action을 넘겨 다음 state를 계산한다.

## useRef

> **출처**
>
> - https://react-ko.dev/reference/react/useRef

`useRef`는 컴포넌트가 렌더링에 필요하지 않은 값을 참조할 수 있게 하는 React Hook이다. 

```jsx
const ref = React.useRef(initialValue);
```

### 매개변수 `initialValue`

ref 객체의 `current` 프로퍼티의 값으로 사용할 초기값이다. 첫 렌더링 이후의 렌더링에서는 무시된다. 

### 반환값

```jsx
const ref = React.useRef(initialValue);
```

매 렌더링마다 동일한 ref 객체를 반환한다. 다음 프로퍼티를 가진다.

- `current`: 첫 렌더링부터 `initialValue`를 값으로 가진다. 수동으로 변경하지 않는 한 현재 값을 유지한다.

### ref란 무엇인가?

state의 변경은 리렌더링을 촉발시키고 이 변경은 렌더링 간에 유지된다. 지역 변수의 변경은 리렌더링을 촉발시키지 않으며 이 변경은 렌더링 간에 유지되지 않는다. *ref*는 변경이 리렌더링을 촉발시키지 않으면서 변경이 렌더링 간에 유지되어야 하는 값이다. ref는 일반 JavaScript 객체로, React가 그 변경사항을 알아차리지 못하며 `set` 함수와 달리 동기적으로 변경이 이루어진다.

> **출처**
>
> - https://react-ko.dev/learn/referencing-values-with-refs
> - https://react-ko.dev/learn/referencing-values-with-refs#best-practices-for-refs

### 언제 사용하는가?

렌더링에 필요하지 않지만 렌더링 간에 유지해야할 필요가 있는 값을 저장하기 위해 사용한다. 렌더링 중에는 ref를 사용하면 컴포넌트를 순수하게 유지할 수 없으므로, 렌더링 중에는 ref를 읽거나 쓰지 않도록 한다. 이벤트 핸들러나 Effect에서 사용하는 것이 적절하다.

```jsx
// 😣: 렌더링 중에는 ref에 접근하지 않는다.
function Foo() {
    ref.current = 13;
    return <div>{ref.current}</div>;
}

// 🥰: 이벤트 핸들러나 Effect에서 ref에 접근한다.
function Bar() {
    useEffect(() => {
        ref.current = 13;
    }, []);
    
    function handleClick() {
        ref.current = 14;
    }
}
```

> **출처**
>
> - https://react-ko.dev/reference/react/useRef#referencing-a-value-with-a-ref

#### DOM 노드 저장하기

ref에 DOM 노드를 저장하고 DOM 노드를 계속 조작할 수 있다. React에서 컴포넌트의 `ref` 프로퍼티에 ref 객체를 전달하면 된다.

```jsx
function MyInput() {
    const inputRef = useRef(null);
    
    function handleClick() {
        inputRef.current.focus();
    }
    return <input ref={inputRef} onClick={handleClick} />
}
```

1. React가 DOM 노드를 생성하고 화면에 그린다.
2. React가 `ref.current`에 DOM 노드에 대한 참조를 할당한다.

### 초기값 재생성 피하기

```jsx
function Video() {
    const playerRef = useRef(new VideoPlayer());
    // 생략
}
```

`useRef`에 함수 호출을 전달한다면, 이 함수는 매 렌더링마다 실행되나 반환값은 첫 렌더링에서만 사용한다. 이 경우 다음과 같이 ref의 값을 검사하여 초기화할 수 있다.

```jsx
function Video() {
    const playerRef = useRef(null);
    if (playerRef.current === null) {
        playerRef.current = new VideoPlayer();
    }
    // 생략
}
```

렌더링 중에 `ref.current`를 사용하는 것은 허용되지 않으나, 이 경우 결과가 항상 동일하고 첫 렌더링 때에만 조건이 만족되어 `ref.current`에 값을 할당하게 되므로 괜찮다.

> **출처**
>
> - https://react-ko.dev/reference/react/useRef#avoiding-recreating-the-ref-contents

### null 체크 피하기

```jsx
function Video() {
    const playerRef = useRef(null);
    
    function handleClick() {
        if (payerRef.current?.play) ref.current.play();
    }
    // 생략
}
```

정적 타입 검사에서 `null` 검사를 반복하게 된다면 다음과 같이 항상 값이 있음을 보장하는 패턴을 사용해볼 수 있다.

```javascript
function Video() {
    const playerRef = useRef(null);
    
    function getPlayer() {
        if (playerRef.current !== null) {
            return playerRef.current;
        }
        
        const player = new VideoPlayer();
        playerRef.current = player;
        return player;
    }
    
    function handleClick() {
        getPlayer().play();
    }
    
    // 생략
}
```

> **출처**
>
> - https://react-ko.dev/reference/react/useRef#how-to-avoid-null-checks-when-initializing-use-ref-later

### 여러 항목을 참조하기

여러 개의 항목에 대한 참조가 필요한 경우, 부모 DOM 노드에 대한 ref를 참조한 후 DOM API를 통해 하위 노드를 찾을 수 있다.

```jsx
// 😣: React Hook은 컴포넌트의 최상위 레벨에서만 사용할 수 있음
<ul>
    {items.map(item => {
        const ref = useRef(null);
        return <li ref={ref} key={item.id}>{item.title}</li>;
    })}
</ul>

// 🥰: ref.current.querySelectorAll('li')로 자식 DOM 노드를 참조하기
<ul ref={ref}>
    {items.map(item => <li key={item.id}>{item.title}</li>)}
</ul>
```

혹은 ref 콜백을 사용한다.

```jsx
function List({ items }) {
    const mapRef = useRef(null);
    
    function getMap() {
        if (mapRef.current === null) {
            mapRef.current = new Map();
        }
        
        return mapRef.current;
    }
    
    return (
        <ul>
            {items.map(item => (
                <li
                    ref={(node) => {
                        const map = getMap();
                        if (node) map.set(item.id, node);
                        else map.delete(item.id);
                    }}
                    key={item.key}
                >
                    {item.title}
                </li>))}
        </ui>
    );
}
```

1. React는 DOM 노드가 생성되면 해당 노드로 ref 콜백을 호출한다.
2. DOM 노드를 지울 때가 되면 `null`로 ref 콜백을 호출한다.

[ref 콜백](#ref-콜백)을 참고한다.

> **출처**
>
> - https://react-ko.dev/learn/manipulating-the-dom-with-refs#how-to-manage-a-list-of-refs-using-a-ref-callback

### 자식의 ref 접근하기

`forwardRef`를 사용하면 ref를 자식에게 전달할 수 있다.

```jsx
import React from 'react';

const MyInput = React.forwardRef((props, ref) => <input {...props} ref={ref} />)
function MyForm() {
    const inputRef = React.useRef(null);
    
    function handleInputClick() {
        inputRef.current?.focus();
    }
    
    return (
        <div>
            <button onClick={handleInputClick}></button>
            <MyInput ref={inputRef} />
        </div>
    );
}
```

> **출처**
>
> - https://react-ko.dev/learn/manipulating-the-dom-with-refs#accessing-another-components-dom-nodes

### 부모에게 노출할 ref 내용 사용자 정의하기

`forwardRef` 렌더 함수로 ref를 전달받은 자식 컴포넌트는 `useImperativeHandle` 훅을 사용하여 부모에게 노출할 ref의 내용을 사용자 정의할 수 있다.

> 출처
>
> - https://react-ko.dev/learn/manipulating-the-dom-with-refs#exposing-a-subset-of-the-api-with-an-imperative-handle

### 기본 작동 방식

React에서 업데이트는 두 단계로 이루어진다.

1. 렌더링하는 동안 React는 컴포넌트를 호출하여 화면에 표시할 것을 계산한다.
2. 커밋하는 동안 React는 DOM에 수정하여 변경 사항을 적용한다.

ref도 두 단계로 나누어보면 다음과 같다. DOM 노드를 참조하는 ref를 생각해보자.

1. 첫 렌더링 동안은 DOM 노드가 화면에 추가되지 않았으므로 `ref.current`는 `null`이다. 그 후 상태 변경으로 인한 렌더링 동안에도 DOM 노드는 변경 사항이 업데이트되지 않았다. 따라서 렌더링 동안에는 ref를 읽는 것이 적절하지 않다.
2. 커밋하는 동안 `ref.current`가 설정된다. DOM이 업데이트되기 전에는 `null`로 설정햇다가 업데이트된 직후 해당 DOM 노드로 다시 설정한다.

ref는 렌더링이 완료된 이후 side effect를 발생시키기 위해 사용하는 것이 일반적이다. 즉, 이벤트 핸들러나 Effect에서 접근한다.

> 출처
>
> - https://react-ko.dev/learn/manipulating-the-dom-with-refs#when-react-attaches-the-refs

### 조건부 렌더링에서 ref 사용하기

(TODO) 안 됨 ref 콜백 써라.

> 출처: https://medium.com/@teh_builder/ref-objects-inside-useeffect-hooks-eb7c15198780

## `ref` 콜백

`ref` 콜백은 JSX 태그의 `ref` 어트리뷰트에 전달하는 함수이다.

```jsx
<div ref={(node) => console.log(node)}></div>
```

- `node`: `null`이거나 DOM 노드이다. React는 ref가 연결될 때는 DOM 노드를 전달하고 ref가 분리될 때 `null`을 전달한다.
- `ref` 콜백은 아무것도 반환하지 않는다.

### 기본 동작 방식

1. DOM 노드가 화면에 추가되면 React는 해당 DOM 노드를 인자로 `ref` 콜백을 호출한다.
2. DOM 노드가 화면에서 제거되면 React는 `null`을 인자로 `ref` 콜백을 호출한다.
3. 전달된 `ref` 콜백이 동일한 함수를 참조하지 않는다면 컴포넌트를 렌더링할 때마다 콜백이 분리되었다가 다시 연결된다. 즉, 컴포넌트가 재렌더링될 때 `null`로 `ref` 콜백을 호출하고 DOM 노드로 새로운 `ref` 콜백을 호출한다. (따라서 이 경우 `ref` 콜백은 `useCallback`을 사용하는 것이 적절하다.)

예시로 알아보자.

```jsx
import React from 'react';

export function App() {
  const [state, setState] = React.useState(0);

  function handleClick() {
    setState(state + 1);
  }

  return <button ref={(node) => console.log(node)} onClick={handleClick}>{state}</button>
}
```

1. 첫 렌더링 이후 버튼이 화면에 추가된다. DOM 노드로 ref 콜백을 호출하여 `<button ></button>`이 출력된다.
2. 버튼을 누를 때마다 재렌더링이 실행된다. ref 콜백이 다시 계산되고, `null`로 ref 콜백을 호출하여 `null`이 출력된다. 커밋되면 DOM 노드로 ref 콜백을 호출하여 `<button ></button>`이 출력된다.

### ref 콜백과 `useCallback`

ref 콜백은 컴포넌트 렌더링마다 다시 생성되어 실행되므로, 이를 원하지 않는다면 `useCallback`으로 렌더링마다 ref 콜백이 새로 생성되지 않도록 한다.

```jsx
import React from 'react';

export function App() {
  const [state, setState] = React.useState(0);
  const refCallback = React.useCallback((node) => {
    console.log(node);
  }, []);

  function handleClick() {
    setState(state + 1);
  }

  return <button ref={refCallback} onClick={handleClick}>{state}</button>
}
```

1. 첫 렌더링 이후 버튼이 화면에 추가된다. DOM 노드로 ref 콜백을 호출하여 `<button ></button>`이 출력된다.
2. 버튼을 누를 때마다 재렌더링이 실행된다. ref 콜백이 다시 계산되지 않아 호출되지도 않는다.

## useImperativeHandle

`useImperativeHandle`은 `forwardRef`로 ref를 전달받은 경우, 부모에게 노출할 ref의 내용을 사용자 정의할 수 있도록 하는 React Hook이다.

```jsx
React.useImperativeHandle(ref, createHandle, dependencies?);
```

### 매개변수

1. `ref`: `forwardRef`로 전달받은 ref이다.
2. `createHandle`: 인자가 없으며, ref의 내용으로 노출할 객체를 반환하는 함수이다.
3. `dependencies`: `createHandle` 내부에서 참조하고 있는 반응형 값(props, state, 컴포넌트 내의 변수와 함수 등)을 나열한 배열이다. React는 매 렌더링마다 `Object.is`로 이전 값과 비교한 후 의존성에 변경이 생겼다면 `createHandle` 함수를 다시 실행하고 새로 생성된 핸들이 ref에 할당된다.

```jsx
import { forwardRef, useRef, useImperativeHandle } from 'react';

function Form() {
    const inputRef = useRef(null);
    
    function handleButtonClick() {
        inputRef.current?.focus();
    }
    
    return (
        <div>
            <MyInput ref={inputRef} />
            <button onClick={handleButtonClick}>클릭!</button>
        </div>
    );
}

const MyInput = forwardRef((props, ref) => {
    const inputRef = useRef(null);
    
    useImperativeHandle(ref, () => ({
        focus() {
            inputRef.current.focus();
        }
    }), []);
    
    return <input {...props} ref={inputRef} />;
})
```

> 출처
>
> - https://react-ko.dev/reference/react/useImperativeHandle

***

## useEffect

> **출처**
>
> - https://react-ko.dev/reference/react/useEffect

`useState`는 컴포넌트를 외부 시스템과 동기화하는 React Hook이다.

```jsx
React.useEffect(setup, dependecies?);
```

### 매개변수 

1. `setup`: Effect 로직이 포함된 함수이다. `setup` 함수는 반드시 함수를 반환하며, 이 함수를 **클린업 함수(cleanup function)**로 취급한다. 아무것도 반환하지 않으면 암묵적으로 빈 클린업 함수를 반환한다.
   
   기본적으로 `setup` 함수는 컴포넌트가 마운트될 때, 그리고 리렌더링이 완료될 때마다 실행된다. 클린업 함수는 컴포넌트가 언마운트될 때, 그리고 `setup` 함수가 실행되기 전마다 실행된다.
2. `dependecies`(optional): `setup`에 포함된 모든 반응형 값(relative value; 컴포넌트 내부의 props, state, 지역 변수)을 나열한 배열이다. React는 매 렌더링마다 `Object.is`로 가장 최근에 실행되었던 Effect의 의존성 배열과 비교한 후 의존성에 변경이 생겼다면 가장 최근에 실행되었던 Effect의 클린업 함수를 실행하고 현재 계산된 Effect를 실행한다.

### 반환값

`undefined`를 반환한다.

### 의존성을 선택할 수 없다

Effect의 의존성은 개발자가 아니라 React가 정한다. Effect가 사용하는 모든 **반응형 값(reactive value; 컴포넌트의 props, state, 지역 변수)**는 의존성 목록에 명시되어야한다. `eslint-plugin-react-hooks` ESLint 룰을 통해 오류를 알 수 있다.

> **출처**
>
> - https://react-ko.dev/reference/react/useEffect#specifying-reactive-dependencies
> - https://react-ko.dev/learn/lifecycle-of-reactive-effects#react-verifies-that-you-specified-every-reactive-value-as-a-dependency

### React는 무엇을 기준으로 의존성 배열이 변경되었다고 판단하는가?

React는 `Object.is`로 현재 계산된 Effect와 가장 최근에 실행되었던 Effect의 의존성 배열을 비교하여 같다고 판단하면 Effect를 실행하지 않는다.

```jsx
if (Object.is(prevDeps, curDeps)) {
  effect();
}
```

### 기본 작동 방식

React는 필요할 때마다 `setup` 함수와 `cleanup` 함수를 실행한다. 기본적으로 `setup` 함수는 렌더링이 완료될 때마다 실행되며, `cleanup` 함수는 `setup` 함수가 실행되기 전마다 실행된다. `dependecies` 배열을 어떻게 명시하느냐에 따라 실행이 달라진다.

> 이전 `cleanup` 함수는 가장 최근에 실행된 `setup` 함수가 반환하는 `cleanup` 함수이다. 만약 첫번째 렌더링과 두번째 렌더링에서 계산된 Effect의 의존성 배열이 같아 두번째 렌더링의 `setup` 함수가 실행되지 않았다면, 세번째 렌더링에서 의존성 배열이 달랐을 때 세번째 렌더링의 `setup` 함수를 실행하기 전 첫번째 렌더링의 `setup` 함수를 실행한다.

#### 1. 아무것도 전달하지 않은 경우

```jsx
useEffect(() => {
  /* setup */
  return () => { /* clenaup */ }
});
```

1. 컴포넌트가 마운트되면 `setup` 함수를 실행한다.
2. 컴포넌트가 리렌더링될 때마다 이전 `cleanup` 함수를 실행한 후 현재 `setup` 함수를 실행한다.
3. 컴포넌트가 언마운트되면 `cleanup` 함수를 실행한다.

#### 2. 빈 배열을 전달한 경우

```jsx
useEffect(() => {
  /* setup */
  return () => { /* clenaup */ }
}, []);
```

1. 컴포넌트가 마운트되면 `setup` 함수를 실행한다.
2. 컴포넌트가 언마운트되면 `cleanup` 함수를 실행한다.

#### 3. 의존성 배열을 전달한 경우

```jsx
useEffect(() => {
  /* setup */
  return () => { /* clenaup */ }
}, [a, b]);
```

1. 컴포넌트가 마운트되면 `setup` 함수를 실행한다.
2. 컴포넌트가 리렌더링될 때마다 의존성 배열을 `Object.is`로 이전 의존성 배열과 비교한다. 변경되었다면 이전 `cleanup` 함수를 실행한 후 현재 `setup` 함수를 실행한다.
3. 컴포넌트가 언마운트된다. `cleanup` 함수를 실행한다.

### React Effect는 브라우저가 화면을 그린 후 실행된다

1. React는 상호작용으로 발생한 Effect는 브라우저가 업데이트된 화면을 그리기 전에 실행한다. 그런데 어떤 경우, React가 Effect 내부의 state 업데이트를 처리하기 전 브라우저가 리페인트를 할 수도 있다.
2. React는 상호작용으로 발생하지 않은 Effect는 브라우저가 업데이트된 화면을 그린 후 실행한다.

브라우저가 화면을 그리기 전 Effect를 실행하고 싶다면 `useLayoutEffect`를 사용해야한다. [useLayoutEffect](#useLayoutEffect)를 참고한다.

> **출처**
>
> - https://react-ko.dev/reference/react/useEffect#caveats
> - https://react-ko.dev/reference/react/useEffect#my-effect-does-something-visual-and-i-see-a-flicker-before-it-runs

### 불필요한 의존성 제거하기

#### 이전 state를 기반으로 state 업데이트하기

```jsx
function ChatRoom() {
  const [messages, setMessages] = useState([]);
  
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (newMessage) => {
      setMessage([...messages, newMessage]);
    });
    
    return () => connection.disconnect();
  }, [messages]);
  
  // 생략...
}
```

이때 `messages`는 반응형 값이므로 Effect의 의존성 배열에 명시해야한다. 그렇다면 새로 매시지가 올 때마다 서버와 연결하고 연결을 끊는 로직이 반복될 것이다.

```diff
function ChatRoom() {
  const [messages, setMessages] = useState([]);
  
  useEffect(() => {
    const connection = createConnection();
    connection.connect();
    connection.on('message', (newMessage) => {
-    	setMessage([...messages, newMessage]);
+    	setMessage(messages => [...messages, newMessage]);
    });
    
    return () => connection.disconnect();
-	}, [messages]);
+	}, []);

  // 생략...
}
```

그렇다면 `set` 함수에 업데이터 함수를 전달하여 반응형 값에 의존하지 않을 수 있다.

> 출처
>
> - https://react-ko.dev/reference/react/useEffect#updating-state-based-on-previous-state-from-an-effect
> - https://react-ko.dev/learn/removing-effect-dependencies#are-you-reading-some-state-to-calculate-the-next-state

#### props로 전달받은 객체 의존성 없애기

```jsx
function ChatRoom({ options }) {
  const [messages, setMessages] = useState([]);
  
  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
  }, [options]);
  
  // 생략...
}
```

`option`은 객체이므로 내용이 같아도 `Object.is`에서 변경되었다고 판단할 수 있다. 이 경우 Effect 밖에서 객체로부터 원시값을 읽어 문제를 회피해볼 수 있다.

```diff
function ChatRoom({ options }) {
  const [messages, setMessages] = useState([]);

+	const { url, roomId } = options;
  useEffect(() => {
-		const connection = createConnection(options);
+		const connection = createConnection({ url, rommId });
		connection.connect();
-	}, [options]);
+	}, [url, roomId]);

  
  // 생략...
}
```

> 출처
>
> - https://react-ko.dev/learn/removing-effect-dependencies#read-primitive-values-from-objects

#### 렌더링 도중에 생성한 객체 의존성 없애기

```jsx
function ChatRoom() {
  const [messages, setMessages] = useState([]);
  
  const options = createOptions();
  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
  }, [options]);
  
  // 생략...
}
```

`options`는 매 렌더링마다 다시 계산되어, Effect도 매 렌더링마다 다시 실행된다. 이 경우 객체를 Effect 내부나, 아니면 아예 컴포넌트 외부로 옮겨 해결할 수 있다.

```diff
// 컴포넌트 외부로 옮기기
+ const options = createOptions();
function ChatRoom() {
  const [messages, setMessages] = useState([]);
  
-	const options = createOptions();
  useEffect(() => {
    const connection = createConnection(options);
    connection.connect();
-	}, [options]);
+	}, []);
  
  // 생략...
}
```

```diff
// Effect 내부로 옮기기
function ChatRoom() {
  const [messages, setMessages] = useState([]);
  
-	const options = createOptions();
  useEffect(() => {
+		const options = createOptions();
		const connection = createConnection(options);
		connection.connect();
-	}, [options]);
+	}, []);
  
  // 생략...
}
```

> 출처
>
> - https://react-ko.dev/reference/react/useEffect#removing-unnecessary-object-dependencies
> - https://react-ko.dev/learn/removing-effect-dependencies#does-some-reactive-value-change-unintentionally

### 서버와 클라이언트 서로 다른 컨텐츠 표시하기

Effect는 클라이언트에서만 실행되므로, 이를 이용해 서버와 클라이언트를 구분하고 서로 다른 로직을 수행할 수 있다.

```jsx
function MyComponent() {
  const [didMount, setDidMount] = useState(false);

  useEffect(() => {
    setDidMount(true);
  }, []);

  if (didMount) {
    // ... return client-only JSX ...
  }  else {
    // ... return initial JSX ...
  }
}
```

> 출처
>
> - (TODO) https://react-ko.dev/reference/react/useEffect#displaying-different-content-on-the-server-and-the-client
> - https://react-ko.dev/reference/react/useEffect#caveats

## useLayoutEffect

> **출처**
>
> - https://react-ko.dev/reference/react/useLayoutEffect

`useLayoutEffect`는 브라우저가 화면을 리페인트하기 전에 실행되는 버전의 `useEffect`이다. 렌더링에 레이아웃 정보를 사용하는 것을 그 목적으로 한다.

```jsx
React.useLayoutEffect(setup, dependencies?);
```

1. 초기 컨텐츠를 렌더링한다.
2. 이 렌더링 결과를 **브라우저가 화면에 리페인트하기 전에** 레이아웃을 측정한다.
3. 레이아웃 정보를 사용하여 최종 컨텐츠를 렌더링된다.

> **출처**
>
> - https://react-ko.dev/reference/react/useLayoutEffect#im-getting-an-error-uselayouteffect-does-nothing-on-the-server

### 매개변수

1. `setup`: Effect 로직이 포함된 함수이다. `setup` 함수는 반드시 함수를 반환하며, 이 함수를 **클린업 함수(cleanup function)**로 취급한다. 아무것도 반환하지 않으면 암묵적으로 빈 클린업 함수를 반환한다.

   기본적으로 `setup` 함수는 컴포넌트가 마운트되기 전, 그리고 리렌더링이 완료되기 전마다 실행된다. 클린업 함수는 컴포넌트가 언마운트되기 전, 그리고 `setupt` 함수를 실행하기 전마다 실행된다.

2. `dependecies`(optional): `setup`에 포함된 모든 반응형 값(relative value; 컴포넌트 내부의 props, state, 지역 변수)을 나열한 배열이다. React는 매 렌더링마다 `Object.is`로 가장 최근에 실행되었던 Effect의 의존성 배열과 비교한 후 의존성에 변경이 생겼다면 가장 최근에 실행되었던 Effect의 클린업 함수를 실행하고 현재 계산된 Effect를 실행한다.

> 출처
>
> - https://react-ko.dev/reference/react/useLayoutEffect#parameters

### 반환값

`undefined`를 반환한다.

### LayoutEffect는 클라이언트에서만 실행된다

서버 렌더링 중에는 실행되지 않는다. 서버에는 레이아웃 정보가 없기 때문이다.

> **출처**
>
> - https://react-ko.dev/reference/react/useLayoutEffect#im-getting-an-error-uselayouteffect-does-nothing-on-the-server

### LayoutEffect는 브라우저의 리페인트를 블로킹한다

React는 브라우저가 화면을 리페인트하기 전 `useLayoutEffect` 내부의 코드와 여기서 예약된 모든 state 업데이트가 처리되는 것을 보장한다.

즉, 화면에 나타나기 전 레이아웃 정보를 사용해서 무언가를 해야한다면 LayoutEffect를 사용한다. 예를 들어 툴팁을 렌더링한 후, 툴팁의 높이를 계산하여 다시 위치를 정해야한다면 이때 `set` 함수를 LayoutEffect에서 호출하고 사용자가 리렌더링을 눈치채지 못하도록 한다.

```jsx
const [tooltipHeight, setTootipHeight] = useState(0);	// 컴포넌트 마운트, 첫번째 렌더링

useLayoutEffect(() => {
  const { height } = ref.current.getBoundingClientRect();
  setTooltipHeight(height);	// 두번째 렌더링(리렌더링), layoutEffect에서 사용되므로 브라우저 리페인트를 막아 첫번째 렌더링이 화면에 그려지는 걸 막는다. 최종적으로 사용자에게 보여주는 것은 리렌더링의 결과이다.
}, []);
```

성능이 저하될 수 있으므로 가능하면 피하도록 한다.

> 출처
>
> - https://react-ko.dev/reference/react/useLayoutEffect#uselayouteffect-blocks-the-browser-from-repainting

## useMemo

> **출처**
>
> - https://react-ko.dev/reference/react/useMemo

`useMemo`는 리렌더링 사이의 결과를 캐싱하는 React 훅이다.

```javascript
const cachedValue = React.useMemo(calcuateValue, dependencies);
```

### 매개변수

1. `calcuateValue`: 캐시할 값을 반환하는 함수(calculation function)이다. 순수 함수이며 인자를 받지 않는다. 초기 렌더링 중에 호출하여 값을 저장하며, 리렌더링에서는 의존성의 변경이 있으면 호출하여 값을 저장한다.
2. `dependencies`: `calcuateValue`에 포함된 모든 반응형 값(컴포넌트 내부의 props, state, 지역 변수)을 나열한 배열이다. React는 매 렌더링마다 `Object.is`로 현재 의존성 배열과 이전 의존성 배열을 비교한 후, 변경이 생겼다고 판단되면 `calcuateValue`를 호출하고 새로 계산한 값을 저장한다.

### 반환값

1. 첫번째 렌더링 중의 값은 `calcuateValue`를 호출한 반환값이다.
2. 리렌더링 중의 값은 의존성이 변경되지 않았다면 마지막으로 저장된 값을 반환하고 변경되었다면 `calcuateValue`를 다시 호출한 반환값이다.

### 컴포넌트의 리렌더링 건너뛰기

기본적으로 컴포넌트가 리렌더링되면, 해당 컴포넌트의 모든 자식들도 재귀적으로 리렌더링된다. `memo`를 사용하면 컴포넌트의 모든 prop이 이전 렌더링과 같은 경우 리렌더링을 건너뛸 수 있다.

```jsx
import React from "react";

export const List = React.memo(function List({ items }) {
  return (
    <ul>
      {items.map((item) => (
        <li key={item.key}>{item.content}</li>
      ))}
    </ul>
  );
});
```

TODO: https://react-ko.dev/reference/react/memo

> **출처**
>
> - https://react-ko.dev/reference/react/useMemo#skipping-re-rendering-of-components
> - https://react-ko.dev/reference/react/useMemo#should-you-add-usememo-everywhere

### JSX 노드를 메모화하기

JSX 노드를 `useMemo`로 감쌀 수도 있다.

```jsx
export function TodoList({ theme, todos }) {
  const filtered = useMemo(() => filterTodos(todos), [todos]);
  const children = useMemo(() => <List items={filtered} />, [filtered]);
  
  return <div className={theme}>{children}</div>;
}
```

React는 기본적으로 컴포넌트를 리렌더링하면 자식 컴포넌트도 재귀적으로 리렌더링한다. 그러나 이젠 렌더링과 **동일한 JSX 노드**를 발견하면 컴포넌트를 리렌더링하지 않는다. `useMemo`로 감싼 JSX 노드는 의존성이 변경되지 않는 한 항상 **동일한 객체**를 반환한다.

> **출처**
>
> - https://react-ko.dev/reference/react/useMemo#memoizing-individual-jsx-nodes

### 의존성에 객체를 넣을 때 주의하라

```jsx
function Dropdown({ items }) {
  const options = { tag: "세탁기" };

  // 💥: 매 렌더링마다 options 객체가 생성되므로 계산 함수도 다시 실행된다.
  const visibleItems = useMemo(
    () => searchItems(items, options),
    [items, options]
  );

  // 생략
}

```

컴포넌트 내부 객체가 의존성에 포함될 경우, 컴포넌트가 렌더링될 때마다 객체도 새로 생성되어 의존성에 변경이 생긴다.

```diff
function Dropdown({ items, text }) {
  // 😊: options 객체가 메모화되어 text 변경시에만 변경된다.
-	const options = { tag: "세탁기", text };
+	const options = useMemo(() => { tag: "세탁기" }, [text]);

  const visibleItems = useMemo(
    () => searchItems(items, options),
    [items, options]
  );

  // 생략
}

```

이 경우 `options` 객체 자체를 메모화할 수 있다. `visibleItems`는 의도한 대로 동작할 것이다.

```diff
function Dropdown({ items, text }) {
-	const options = useMemo(() => { tag: "세탁기" }, [text]);

  const visibleItems = useMemo(
    () => {
+    	const options = { tag: "세탁기", text };
+   	return searchItems(items, options);
    },
    [items, text]
  );

  // 생략
}

```

가장 좋은 방법은 `options`를 계산 함수로 이동하는 것이다. `visibleItems`는 객체 `options` 대신 원시 값 `text`에 의존하게 된다.

> **출처**
>
> - https://react-ko.dev/reference/react/useMemo#memoizing-a-dependency-of-another-hook

###  함수 메모화하기

함수 역시 값이므로, `useMemo`는 함수를 반환할 수 있다.

```jsx
export function Page({ productId }) {
  const handleSubmit = useMemo(
    () => (formData) => {
      post(`product/${productId}`, formData);
    },
    [productId]
  );

  // 생략
}

```

shortcut으로 `useCallback`을 사용할 수 있다. [useCallback](#useCallback)을 참고한다.

```jsx
export function Page({ productId }) {
  const handleSubmit = useCallback(
    (formData) => {
      post(`product/${productId}`, formData);
    },
    [productId]
  );

  // 생략
}

```

> **출처**
>
> - https://react-ko.dev/reference/react/useMemo#memoizing-a-function

***

## useCallback

> 출처
>
> - https://react-ko.dev/reference/react/useCallback

`useCallback`은 리렌더링 사이에 함수 정의를 캐싱하는 React 훅이다.

```jsx
const cachedFn = React.useCallback(fn, dependecies);
```

### 매개변수

- `fn`: 캐시하려는 함수 값이다. 초기 렌더링 중에는 해당 함수를 반환한다. 리렌더링에서는 의존성의 변경이 있다면 렌더링 중에 전달된 함수를 반환하고, 의존성의 변경이 없다면 이전 렌더링의 함수를 반환한다.
- `dependencies`: `fn` 내에서 참조된 모든 반응형 값(컴포넌트 내부의 props, state, 지역 변수)을 나열한 배열이다. React는 매 렌더링마다 `Object.is`로 현재 의존성 배열과 이전 의존성 배열을 비교한 후, 변경이 생겼다고 판단하면 새로 계산한 함수를 저장한다.

### 반환값

1. 첫번째 렌더링에서는 `fn` 함수를 반환한다.
2. 리렌더링에서는 의존성이 변경되지 않았다면 마지막 렌더링에서 저장된 `fn` 함수를 반환하고 변경되었다면 렌더링 중에 전달한 `fn` 함수를 반환한다.

### `useMemo`와의 비교

- `useMemo`는 호출한 함수의 결과를 캐시한다. `useCallback`은 함수 자체를 캐시한다.
- 내부적으로 거의 동일한 듯하다. (TODO)

```jsx
function useCallback(fn, dependencies) {
  return useMemo(() => fn, dependencies);
}
```

### 메모된 콜백에서 state 업데이트하기

`useCallback` 내부에서 사용되는 반응형 값은 의존성 배열에 명시되어야한다. 즉, 다음과 같이 `set` 함수를 호출한다면 의존성 배열에 state가 명시되어야한다.

```jsx
import { useState, useCallback } from 'react';

function TodoList() {
  const [todos, setTodos] = useState([]);
  
  const handleAddTodos = useCallback(todo => {
    setTodos([...todos, todo]);
  }, [todo]);
  
  // ...
}
```

이러한 경우 `set` 함수에 업데이터 함수를 전달하여 의존성을 제거할 수 있다.

```diff
import { useState, useCallback } from 'react';

function TodoList() {
  const [todos, setTodos] = useState([]);
  
  const handleAddTodos = useCallback(todo => {
-		setTodos([...todos, todo]);
+		setTodos(todos => [...todos, todo]);
-	}, [todo]);
+	}, []);
  
  // ...
}
```

> 출처
>
> - https://react-ko.dev/reference/react/useCallback#updating-state-from-a-memoized-callback

### 커스텀 훅 최적화하기

커스텀 훅이 반환하는 함수는 모두 `useCallback`으로 감싸는 것이 좋다. 훅을 사용하는 쪽에서 코드를 최적화할 수 있기 때문이다.

> 출처
>
> - https://react-ko.dev/reference/react/useCallback

## useTransition

> 출처
>
> - https://react-ko.dev/reference/react/useTransition

`useTransition`은 UI를 블로킹하지 않고 state를 업데이트하는 React 훅이다.

```jsx
const [isPending, startTransition] = React.useTransition();
```

### 반환값

두 개의 값을 담은 배열을 반환한다.

- `isPending` 변수: pending 상태의 트랜지션이 있다면 `true`, 없다면 `false`이다.
- `startTransition` 함수: state 업데이트를 트랜지션으로 표시하는 함수이다.

### `startTransition` 함수

```javascript
startTransition(scope);
```

- `scope` 함수: 하나 이상의 `set` 함수를 호출하여 state를 업데이트하는 함수이다. **동기 함수**이며 매개변수를 가지지 않는다.

React는 `startTransition` 함수 호출 시 매개변수로 전달된 `scope` 함수를 즉시 실행한다. `scope` 함수 호출 중에 동기적으로 스케줄된 state 업데이트를 트랜지션으로 표시한다. timeout과 같이 비동기적으로 스케줄된 state 업데이트는 트랜지션으로 표시하지 않는다. 트랜지션은 논블로킹이며 원치 않은 로딩 indicator를 표시하지 않을 것이다.

> 출처
>
> - https://react-ko.dev/reference/react/useTransition#starttransition

### 트랜지션으로 표시한 state 업데이트의 특징

트랜지션으로 표시한 state 업데이트는 첫번째, UI를 논블로킹하지 않으며 두번째, 다른 state 업데이트에 의해 중단된다.

```jsx
import React, { useState } from 'react';

function TabContainer() {
  const [tab, setTab] = useState('recent');
  
  function selectTab(nextTab) {
    setTab(nextTab);
  }
  
  return (
  	<>
    	<button onClick={() => selectTab('recent')}>recent</button>
    	<button onClick={() => selectTab('popular')}>popular</button>
    	{tab === 'recent' && <RecentTab />}
    	{tab === 'popular' && <PopularTab />}
    </>
  );
}
```

예를 들어, `PopularTab`을 렌더링할 때 최소 `n`초가 걸린다고 하자. 이때 `popluar` 버튼을 누른다면 `PopluarTab`이 렌더링되기 전까지 UI가 멈추고 사용자 인터렉션에 반응하지 않는다. 하지만 `startTransition`으로 `set` 함수를 트랜지션으로 표시해보자.

```diff
-	import React, { useState } from 'react';
+	import React, { useTransition, useState } from 'react';

function TabContainer() {
+	const [isPending, startTransition] = useTransition();
  const [tab, setTab] = useState('recent');
  
  function selectTab(nextTab) {
+		startTransition(() => {
			setTab(nextTab);
+		})
  }
  
	// 생략...
}
```

state 업데이트는 트랜지션으로 표시되어 UI가 멈추지 않는다. 이때 다른 탭 버튼을 누르면 기존의 state 업데이트는 취소된다. 예를 들어, `popluar` 버튼을 누르고 바로 `recent` 버튼을 누른다고 하자.

1. 사용자가 `popluar` 버튼을 누른다. React는 트랜지션 내에서 `setTab('popluar')`를 처리하고 `TabContainer`를 리렌더링한다.
2. `TabContainer`를 리렌더링하는 도중 사용자가 `recent` 버튼을 누른다.
3. React는 트랜지션 내에서 `setTab('recent')`를 처리하고 `TabContainer` 컴포넌트를 리렌더링한다.

> 출처
>
> - https://react-ko.dev/reference/react/useTransition#examples

### 트랜지션 중에 pending 상태 보여주기

```jsx
import React, { useTransition } from "react";

function TabButton({ children, onClick }) {
  const [isPending, startTransition] = useTransition();

  if (isPending) return <b className="pending">{children}</b>;
  return (
    <button
      onClick={() =>
        startTransition(() => {
          onClick();
        })
      }
    >
      {children}
    </button>
  );
}
```

`useTransition`이 반환하는 boolean 변수를 사용하면 트랜지션이 진행 중임을 표시할 수 있다. 또한 원치 않은 경우 `Suspense` fallback을 보여주지 않을 수도 있다. 예를 들어 `PopluarTab`이 Suspense-enabled 데이터 소스를 사용하고, `TabContainer`가 `Suspense`로 감싸져있어도 fallback이 표시되지 않을 것이다.

```jsx
import React, { useState } from "react";

function TabContainer() {
  const [tab, setTab] = useState("recent");

  function selectTab(nextTab) {
    setTab(nextTab);
  }

  return (
    <Suspense fallback={"loading..."}>
      <TabButton onClick={() => selectTab("recent")}>recent</TabButton>
      <TabButton onClick={() => selectTab("popular")}>popular</TabButton>
      {tab === "recent" && <RecentTab />}
      {tab === "popular" && <PopularTab />}
    </Suspense>
  );
}
```

> 출처
>
> - https://react-ko.dev/reference/react/useTransition#displaying-a-pending-visual-state-during-the-transition
> - https://react-ko.dev/reference/react/useTransition#preventing-unwanted-loading-indicators

### 트랜지션 업데이트는 텍스트 입력에 사용할 수 없다

```jsx
const [text, setText] = useState('');

function handleChange(e) {
  stratTransition(() => {
    setText(e.target.value);
  });
}

return <input value={text} onChange={handleChange} />
```

트랜지션은 논블로킹이나 input 업데이트는 동기적으로 이루어져야하기 때문이다. 두 가지 해결 방법이 있다.

1. input의 state와 트랜지션으로 업데이트할 state를 각각 선언한다. 전자로 input을 제어하고, 후자로 렌더링 로직을 처리한다.
2. input의 state와 `useDeferredValue`로 지연된 값을 사용한다. [참고](#useDeferredValue)

> 출처
>
> - https://react-ko.dev/reference/react/useTransition#updating-an-input-in-a-transition-doesnt-work

### 비동기적으로 스케줄된 state 업데이트는 트랜지션으로 표시하지 않는다

`scope` 함수는 동기 함수여야하며, `scope` 함수 호출 중에 비동기적으로 스케줄된 state 업데이트는 트랜지션으로 표시하지 않는다.

#### `scope`가 동기 함수인 경우

예를 들어, `scope` 호출 중의 timeout으로 스케줄된 state 업데이트는 트랜지션으로 표시되지 않는다.

```js
startTransition(() => {
  setTimeout(() => {
    // 😣: startTransition 호출 *이후에* state를 설정한다
    setPage('/recent');
  }, 1000);
});
```

이와 같은 경우, `startTransition` 자체를 timeout으로 스케줄하여 해결할 수 있다.

```js
setTimeout(() => {
  startTransition(() => {
    // 😊: startTransition 호출 *중에* state를 설정한다
    setPage('/recent');
  });
}, 1000);
```

#### `scope`가 비동기 함수인 경우

`scope` 함수가 비동기 함수이면 `scope` 호출 중에 비동기적으로 스케줄된 state 업데이트는 트랜지션으로 표시하지 않는다.

```js
startTransition(async () => {
  await myAsyncFunction();
  // 😣: startTransition 호출 *이후에* state를 설정한다
  setPage('/recent');
});
```

이와 같은 경우, `scope` 함수 내부에서 비동기 로직을 꺼내어 `startTransition` 이전에 실행해야한다.

```js
await myAsyncFunction();
startTransition(() => {
  // 😊: startTransition 호출 *중에* state를 설정한다
  setPage('/recent');
});
```

> 출처
>
> - https://react-ko.dev/reference/react/useTransition#react-doesnt-treat-my-state-update-as-a-transition

### 컴포넌트 외부에서 트랜지션 시작하기

`useTransition`은 훅이므로 컴포넌트 내부 최상단에서 사용할 수 있다. 따라서 컴포넌트 내부에서 트랜지션을 시작하려면 `startTransition` 메서드를 사용한다.

TODO: https://react-ko.dev/reference/react/startTransition

> 출처
>
> - https://react-ko.dev/reference/react/useTransition#i-want-to-call-usetransition-from-outside-a-component

#### `scope` 함수는 지연되지 않는다

```js
console.log(1);
startTransition(() => {
  console.log(2);
  setPage('/recent');
});
console.log(3);
```

`set` 함수는 비동기적으로 작동하나, 트랜지션으로 표시되면 즉시 실행된다. 따라서 `1, 2, 3`을 출력한다.

> 출처
>
> - https://react-ko.dev/reference/react/useTransition#the-function-i-pass-to-starttransition-executes-immediately

## useDeferredValue

> **출처**
>
> - https://react-ko.dev/reference/react/useDeferredValue

`useDefferedValue`는 지연된 값을 사용할 수 있는 React 훅이다.

```js
const defferedValue = React.useDeferredValue(value);
```

### 매개변수 `value`

지연시키려는 값이다. 어느 타입의 값이든 될 수 있다. 원시값 혹은 컴포넌트 외부에서 생성된 객체를 전달해야 한다(렌더링 중에 생성한 객체를 전달하면 렌더링 할 때마다 값이 달라져 불필요한 백그라운드 리렌더링이 발생할 수 있기 때문이다).

### 반환값

1. 첫번째 렌더링 중의 값은 `value`와 일치한다.
2. 업데이트 중에는, React는 우선 이전 값으로 리렌더링을 시도한 후, 백그라운드에서 새로운 값으로 리렌더링을 시도한다. (백그라운드 리렌더링를 시도하는 중 새로운 업데이트가 발생하면 새로운 값으로 백그라운드 리렌더링을 처음부터 다시 시도한다.)

### 무엇을 기준으로 업데이트가 발생했다고 판단하는가?

React는 `Object.is`로 현재 렌더링하는 값과 새로운 값을 비교한다.

### fresh 컨텐츠를 로딩하는 동안 stale 컨텐츠를 보여주기

> 컴포넌트는 suspense-enabled 데이터를 사용한다. 즉, 컴포넌트는 해당 데이터를 로딩하는 동안 suspense된다. 이러한 컴포넌트를 `Suspense`로 감싸면 suspense된 동안 제공한 `fallback`을 대체되나 `useDefferedValue`를 사용하면 `fallback` 대신 지연된 값으로 렌더링된 컴포넌트를 보여줄 수 있다.

`TodoList` 컴포넌트가 Suspense-enabled 데이터를 사용한다. 즉, 해당 데이터를 로딩하는 동안 `TodoList`는 suspense된다.

```jsx
import { useState, useDefferedValue } from 'react';

function Page() {
  const [query, setQuery] = useState('');
  const defferedQuery = useDefferedValue(query);

  return (
    <>
      <input value={query} />
      <Suspense fallback={<h2>loading...</h2>}>
        <TodoList query={defferedQuery} />
      </Suspense>
    </>
  );
}

```

1. React는 `''`로 첫 렌더링한다.
2. 사용자가 `'a'`를 입력한다. React는 우선 이전 값인 `''`로 리렌더링한다.
3. React는 새로운 값인 `'a'`로 리렌더링을 시도한다. 이때 `TodoList`가 suspense되면 리렌더링을 포기하고 데이터 로딩을 기다린다.
4. React는 데이터가 로드된 후 새로운 값인 `'a'`로 리렌더링을 다시 시도한다.

> **출처**
>
> - https://react-ko.dev/reference/react/useDeferredValue#showing-stale-content-while-fresh-content-is-loading
> - https://react-ko.dev/reference/react/useDeferredValue#how-does-deferring-a-value-work-under-the-hood

### 컨텐츠가 stale한지 표시하기

```diff
import { useState, useDefferedQuery } from 'react';

function Page() {
  const [query, setQuery] = useState('');
  const defferedQuery = useDefferedQuery(query);
+	const stale = query !== defferedQuery;
  
  // ...생략
}
```

최신 값과 지연된 값을 비교하여 현재 표시되고 있는 컨텐츠가 stale한지 알 수 있다.

> **출처**
>
> - https://react-ko.dev/reference/react/useDeferredValue#indicating-that-the-content-is-stale

### 백그라운드 리렌더링은 중단될 수 있다

React는 백그라운드 리렌더링 중 또다른 업데이트가 발생하면 기존의 백그라운드 리렌더링을 중단하고 새로운 값으로 백그라운드 리렌더링을 처음부터 다시 시작한다.

> **출처**
>
> - https://react-ko.dev/reference/react/useDeferredValue#caveats

### UI 일부만 리렌더링 지연하기

`useDefferedValue`를 성능 최적화에 사용할 수 있다.

```jsx
function Page() {
  const [filter, setFilter] = useState('');
  
  return (
  	<input value={filter} onChange={e => setFilter(e.target.value)} />
    <SuperSlowList filter={filter} />
  );
}
```

가령, 사용자가 인풋에 값을 입력할 때마다 `Page`는 리렌더링되며 그 자식 컴포넌트들도 리렌더링된다. 그런데 `SuperSlowList`는 렌더링 속도가 상당히 느리므로 해당 컴포넌트를 리렌더링하는 동안 다른 UI도 블로킹된다. 이 컴포넌트에서 `SuperSlowList`의 리렌더링만 지연해보자.

1. `SuperSlowList`를 `memo`로 감싼다.

```jsx
const SuperSlowList = React.memo(function SuperSlowList({ filter }) {/* 생략 */})
```

2. `useDefferedValue`를 사용하여 `SuperSlowList`에 지연된 `filter` 값을 전달한다.

```diff
import { useState, useDefferedValue } from 'react';

function Page() {
  const [filter, setFilter] = useState('');
+	const defferedFilter = useDefferedValue(filter);
  
  return (
  	<input value={filter} onChange={e => setFilter(e.target.value)} />
    <SuperSlowList filter={defferedFilter} />
  );
}
```

`SuperSlowList`의 렌더링 속도는 빨라지지 않으나 `SuperSlowList`의 업데이트의 우선순위를 키 입력 이벤트로 인한 업데이트의 우선순위보다 낮출 수 있다.

#### 왜 `memo`로 감싸야하는가?

`filter`가 변경될 때마다 React는 `Page`는 리렌더링한다. 이 동안 `defferedFilter`는 이전 값을 가지나, 어쨌든 `Page`의 모든 자식 컴포넌트는 리렌더링을 시도한다. 그러므로 이전 props의 값과 렌더링 중인 props의 값을 비교하여 리렌더링을 건너뛸 수 있는 `memo`로 감싸야 `SuperSlowList`는 리렌더링을 건너뛸 수 있다.

> **출처**
>
> - https://react-ko.dev/reference/react/useDeferredValue#deferring-re-rendering-for-a-part-of-the-ui

### 백그라운드 리렌더링에서 Effect는 언제 발생하는가?

React는 백그라운드 리렌더링을 화면에 커밋한 이후 Effect를 실행한다.

> **출처**
>
> - https://react-ko.dev/reference/react/useDeferredValue#caveats

### 값을 지연하는 것과 디바운싱, 스로틀링의 차이는 무엇인가?

부모 컴포넌트가 제어 컴포넌트인 인풋과 목록을 가진다고 하자.

1. 디바운싱(debouncing)이란? 사용자가 타이핑을 멈출 때까지 기다렸다가 목록을 업데이트하는 것이다.
2. 스로틀링(throttling)이란? 정해진 시간마다(예: 최대 1초에 한 번) 목록을 업데이트한다.

`useDefferedValue`는 React 자체에서 제공하는 훅이고 사용자의 디바이스에 적응하므로 렌더링 최적화에 더 적합하다. `useDefferedValue`에 의해 수행되는 백그라운드 리렌더링(지연된 리렌더링)은 기본적으로 중단 가능하다. 디바운스나 스로틀링은 리렌더링을 캔슬하지 않으며 다만 업데이트를 지연하거나 일정 시간 동안 실행될 수 있는 업데이트의 횟수를 제한하는 것뿐이다.

아래는 디바운싱된 값을 제공하는 훅의 예시이다.

```jsx
import { useState, useEffect } from 'react';

export const useDebouncedValue = <T>(value: T, delay = 500) => {
  const [debouncedValue, setDebouncedValue] = useState<T>(value);

  useEffect(() => {
    const timerId = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(timerId);
    };
  }, [value, delay]);
  return debouncedValue;
};
```

디바운스나 스로틀링은 렌더링 중에 발생하지 않는 작업을 최적화하는 데 유용하다. 예를 들어, `useDefferedValue`는 네트워크 요청을 캔슬하지 않지만 디바운싱이나 스로틀링은 네트워크 요청을 더 적게 실행하게 해준다.

따라서 목적에 따라 사용하는 것이 옳다.

## useId

`useId`는 접근성 속성에 전달할 수 있는 고유 ID를 생성하는 React 훅이다.

```jsx
const id = React.useId();
```

### 반환값

특정 컴포넌트 내에서 특정 `useId`와 연관한 고유 ID 문자열을 반환한다.

### 목록에서 키를 생성할 때 사용하지 말아야한다

키는 데이터에서 생성되어야한다.

### 접근성 속성에 대한 고유 ID 생성하기

```jsx
import { useId } from 'react';

function PasswordField() {
  const passwordHintId = useId();
  
  return (
  	<>
    	<input type="password" aria-describedby={passwordHintId}/>
    	<p id={passwordHintId}>비밀번호는 최소 10문자 이상이어야해요</p>
    </>
  )
}
```

React에서 `id`는 고유해야하므로 접근성을 위해 고유한 ID를 생성할 수 있다.

```jsx
function MyApp() {
  const id = useId();
  
  return (
    <form>
      <label htmlFor={id + '-password'}>First Name:</label>
      <input id={id + '-password'} type="text" />
      <hr />
      <label htmlFor={id + '-confirm'}>Last Name:</label>
      <input id={id + '-confirm'} type="text" />
    </form>
  );
}
```

관련한 요소라면 접두사용으로 사용할 수도 있다.

> **출처**
>
> - https://react-ko.dev/reference/react/useId#generating-unique-ids-for-accessibility-attributes
> - https://react-ko.dev/reference/react/useId#generating-ids-for-several-related-elements

#### htmlFor과 aria-labelledby/aria-describedby의 차이는 뭘까?

TODO

### 왜 임의의 전역 변수를 사용하는 것보다 나을까?

전역 변수 `id++`를 사용할 수도 있는데, 왜 `useId`를 사용해야할까?

React는 서버 렌더링 중에 컴포넌트가 HTML 출력을 생성한다. 이후 클라이언트에서 hydration이 생성된 HTML에 이벤트 핸들러를 첨부한다. 한편 hydration이 작동하려면 클라이언트의 출력과 서버의 HTML이 일치해야한다.

클라이언트 컴포넌트가 hydration되는 순서가 서버 HTML이 생성된 순서와 일치하지 않을 수 있으므로, 카운터를 사용하면 클라이언트의 출력과 서버의 HTML이 일치하는지 보장하기 어렵다.

그러나 `useId`는 훅을 호출한 컴포넌트의 *부모 경로(parent path)*로부터 생성되므로 클라이언트와 서버의 트리가 동일하면 부모 경로는 렌더링 순서에 상관없이 일치할 것이다.

> **출처**
>
> - https://react-ko.dev/reference/react/useId#why-is-useid-better-than-an-incrementing-counter

### React 루트별로 접두사 공유하기

`createRoot`나 `hydrateRoot`에서 `identifierPrefix` 옵션에 접두사를 전달하면 해당 루트 내의 컴포넌트에서 호출된 `useId`는 접두사로 시작하는 고유 ID를 생성한다.

```jsx
import { createRoot } from 'react-dom/client';

function App() {
  const id = useId();
  
  // 생략....
}

createRoot(document.getElementById('rootA'), {
  identifierPrefix: 'app-A-'
}).render(<App />);

createRoot(document.getElementById('rootB'), {
  identifierPrefix: 'app-B-'
}).render(<App />);
```

두 애플리케이션은 같은 페이지 내에서 렌더링되어도 ID는 충돌하지 않는다.

> **출처**
>
> - https://react-ko.dev/reference/react/useId#generating-ids-for-several-related-elements

## useSyncExternalStore

> **출처**
>
> - https://react-ko.dev/reference/react/useSyncExternalStore

`useSyncExternalStore`는 외부 스토어를 구독할 수 있는 React 훅이다

```jsx
const snapshot = React.useSyncExternalStore(subscribe, getSnapshot, getServerSnapshot?);
```

### 매개변수

- `subscribe`: 스토어를 구독하는 함수이다. `callback` 함수를 인자로 받고 `unsubscribe` 함수를 반환한다. `callback` 함수는 스토어가 변경되면 호출되어야하며(이를 통해 컴포넌트가 리렌더링된다), `unsubscribe` 함수는 구독을 해제하는 함수여야한다. React는 새로운 `subscribe` 함수가 전달될 때마다 `subscribe`를 호출하므로, 컴포넌트 외부로 이동하거나 `useCallback`을 사용하여 메모이제이션한다.
- `getSnapshot`: 컴포넌트가 필요로 하는 스토어의 데이터의 스냅샷을 반환하는 함수이다. React는 Object.is로 `getSnapshot`의 반환값이 달라졌는지 비교하여 리렌더링한다. 따라서 함수가 호출될 때마다 항상 다른 값을 반환하면 컴포넌트가 렌더링될 때마다 다음 렌더링을 촉발하여 무한 루프에 빠지므로, 스토어가 변경되었을 때에만 이전 렌더링과 다른 값을 반환하도록 한다.
- `getServerSnapshot`(optional): 스토어에 있는 데이터의 초기 스냅샷을 반환하는 함수이다. 서버에서 HTML을 생성할 때와 여기서 렌더링된 컨텐츠가 클라이언트에서 hydrate할 때만 실행된다. 서버 스냅샷은 클라이언트와 서버 간에 동일해야하고, 서버에서 직렬화하여 클라이언트에게 전달한다. 이 함수를 전달하지 않으면 서버 렌더링 중 오류가 발생한다.

`subscribe`는 React가 전달한 `callback`이 스토어에 변경이 생길 때마다 실행되도록 구현해야한다. 그래야 앞으로 스토어에 변경이 생길 때마다 `callback`이 실행되어 React에게 스토어 데이터에 변경이 생겼음을 릴 수 있다. React는 `callback`이 실행되면  `getSnapshot`을 호출하여 이전 값과 비교하여 달라졌다면 컴포넌트를 리렌더링한다.

### 반환값

렌더링 중에 사용할 수 있는 스토어의 현재 스냅샷을 반환한다.

### 외부 스토어 구독하기

React는 React 외부 저장소에서 데이터를 읽어야하는 경우가 있다. React는 React 외부의 값이 변경되는지 알 수 없기 때문에 이때는 `useSyncExternalStore` 훅을 사용해야한다. 두 가지 경우가 있다.

1. React 외부에서 state를 보관하는 서드 파티 상태 관리 라이브러리
2. 변경 가능한 값을 노출하는 브라우저 API와 그 변경 사항을 구독하는 이벤트

#### 서드 파티 상태 관리 라이브러리와 동기화하기

```jsx
let nextId = 0;
const todos = [];
let listeners = [];

const todoStore = {
  addTodo() {
    todos = [...todos, { id: nextId++, content: `#${nextId}` }];
    emitChange();
  },
  subscribe(listener) {
    listeners = [...listeners, listener];

    return () => {
      listeners = listeners.filter((l) => l !== listener);
    };
  },
  getSnapshot() {
    return todos;
  },
};

function emitChange() {
  listeners.forEach((listener) => {
    listener();
  });
}

function MyApp() {
  const todos = useSyncExternalStore(
    todoStore.subscribe,
    todoStore.getSnapshot
  );

  return (
    <>
      <button onClick={() => todoStore.addTodo()}>todo 추가</button>
      <ul>
        {todos.map((todo) => (
          <li id={todo.id}>{todo.content}</li>
        ))}
      </ul>
    </>
  );
}

```

> **출처**
>
> - https://react-ko.dev/reference/react/useSyncExternalStore#subscribing-to-an-external-store

#### 브라우저 API 구독하기

브라우저는 `navigator.onLine` 속성으로 네트워크 연결이 활성화되어 있는지 표시한다. React가 알지 못하는 사이에 변경될 수 있으므로 `useSyncExternalStore`로 값을 읽어야한다.

```jsx
function getSnapshot() {
  retrn navigator.onLine;
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

function App() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  
  // ... 생략
}
```

> **출처**
>
> - https://react-ko.dev/reference/react/useSyncExternalStore#subscribing-to-a-browser-api

#### 사용자 정의 훅으로 추출하기

```jsx
export function useIsOnline() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  
  return isOnline;
}
```

> **출처**
>
> - https://react-ko.dev/reference/react/useSyncExternalStore#extracting-the-logic-to-a-custom-hook

### 서버 렌더링 지원하기

서버 렌더링 중엔 다음과 같은 문제가 생길 수 있다.

1. 서드파티 데이터 스토어에 연결할 때 서버와 클라이언트 사이에서 데이터를 일치시켜야한다.
2. 브라우저 전용 API에 연결할 때 서버 환경에는 해당 API가 존재하지 않아 작동하지 않는다.

이 경우 서버 렌더링 중 적절한 값을 반환하는 `getServerSnapshot`을 제공할 수 있다.

```diff
export function useIsOnline() {
	const isOnline = useIsOnline(
		subscribe,
		getSnapshot,
+		getServerSnapshot
	);
  return isOnline;
}

// 서버가 생성한 HTML에서는 언제나 true이다.
+function getServerSnapshot() {
+		return true;
+}
```

적절히 제공할 수 있는 초기값이 없다면 컴포넌트가 클라이언트에서만 렌더링되도록 강제하라.

```jsx
<Suspense fallback={<h2>loading...</h2>}>
	<OnlyClient />
</Suspense>

function OnlyClient() {
  if (type window === 'undefined') {
    throw new Error('<OnlyClient /> should only redner on the client.');
  }
}
```

> **출처**
>
> - https://react-ko.dev/reference/react/useSyncExternalStore#adding-support-for-server-rendering
> - https://react-ko.dev/reference/react/Suspense#providing-a-fallback-for-server-errors-and-server-only-content

### Redux는 어떻게 변경 사항을 React에게 알리는 걸까?

TODO

***

## lazy

> https://react-ko.dev/reference/react/lazy

```jsx
const MyComponent = lazy(load);
```

`lazy`를 사용해 최상위 레벨에서 지연된 컴포넌트를 선언할 수 있다. 지연된 컴포넌트는 첫 렌더링까지 컴포넌트 코드의 로딩이 지연된다.

### 매개변수 `load`

- `load`: Promise 혹은 thenable(`then` 메서드를 가진 Promise-유사 객체)를 반환하는 함수이며, resolve된 값은 유효한 React 컴포넌트 타입이어야한다. React는 컴포넌트의 첫 렌더링이 시작될 때까지 `load`를 호출하지 않는다. `load`를 처음 호출하면 resolve될 때까지 기다린 후, resolve된 값을 캐싱하고 렌더링한다(따라서 `load`는 한 번만 호출된다). reject되면 reject된 이유를 `throw`한다.

### 반환값

React 컴포넌트를 반환한다. 이 컴포넌트는 로딩되는 동안은 suspense된다.

### React.lazy와 Router.lazy의 비교

(TODO) https://remix.run/blog/lazy-loading-routes

## memo

> https://react-ko.dev/reference/react/memo

```jsx
const MemomizedComponent = memo(Component, arePropsEqual?);
```

`memo`를 사용해 props가 변경되지 않은 경우 컴포넌트의 리렌더링을 건너뛸 수 있다.

### 매개변수

- `Component`: 메모화하려는 React 컴포넌트이다.
- `arePropsEqual`(optional): 컴포넌트의 이전 props와 새로운 props를 인자로 받는 함수이다. 모든 props가 같으면 `true`를, 그렇지 않다면 `false`를 반환해야한다. React는 기본적으로는 `Object.is`를 사용해 props를 비교한다.

### 반환값

인자로 전달한 컴포넌트와 다른 새로운 컴포넌를 반환한다. 그러나 props가 변경되지 않는 한 리렌더링되지 않는다.

### state와 context가 변경되면 메모화된 컴포넌트는 리렌더링된다

메모화된 컴포넌트는 오로지 props가 변경되지 않는 한 리렌더링될 뿐, 내부 state와 사용한 context가 변경되면 리렌더링된다.

***

## createPortal

> 참고
>
> - https://react-ko.dev/reference/react-dom/createPortal

```jsx
import { createPortal } from 'react-dom';

<div>
	<MyComponent />
  {createPortal(children, domNode, key?)}
</div>
```

`createPortal`은 일부 자식을 DOM의 다른 부분에 렌더링한다. 이때 물리적인 위치만 변경될 뿐, 포털을 렌더링하는 부모 React 컴포넌트 하위의 자식 컴포넌트처럼 행동한다. 예를 들어 상위에서 제공하는 컨텍스트에 접근할 수 있고 이벤트도 물리적인 DOM 트리가 아니라 React 트리에 따라 자식에서 부모로 버블링된다.

### 매개변수

- `children`: React로 렌더링할 수 있는 모든 것이다.
- `domNode`: 이미 DOM 트리 내부에 존재하는 DOM 엘리먼트이다.
- `key`(optional): 포털의 키로 사용할 고유한 문자열 또는 숫자이다.

### 반환값

React 노드를 반환한다. React는 렌더링 출력에서 이 노드를 만나면 `children`을 `domNode` 안에 배치한다.

### DOM의 다른 부분으로 렌더링하기

```jsx
import { createPortal } from 'react-dom';

export default function MyComponent() {
  return (
    <div>
      <p>div가 부모이다!</p>
      {createPortal(<p>document.body가 부모이다!</p>, document.body)}
    </div>
  );
}

<body>
  <div id="root">
    <div>
      <p>div가 부모이다!</p>
    </div>
  </div>
  <p>document.body가 부모이다!</p>
</body>
```

원래라면 React 루트의 첫번째 자식 `div`의 하위에 물리적으로 배치되겠지만, `document.body` 하위에 물리적으로 배치된다.

## flushSync

> 출처
>
> - https://react-ko.dev/reference/react-dom/flushSync

```jsx
import { flushSync } from 'react-dom';

flushSync(() => {
    setState(1);
});
// 이후부터는 DOM이 업데이트되어있다.
```

`flushSync`는 전달된 콜백을 즉시 호출하고 내부의 모든 업데이트를 동기적으로 flush한다. 따라서 `flushSync`로 감싼 콜백이 실행된 직후 **DOM이 업데이트된다**.

### 매개변수 `callback`

함수이다. React는 `flushSync`가 호출되면 콜백을 즉시 호출하고 콜백 내부의 모든 업데이트(pending 업데이트, Effect, Effect 내부의 업데이트)를 동기적으로 flush한다. 업데이트가 `flushSync` 호출의 결과로 suspend되면 fallback이 다시 표시된다.

### 반환값

`undefined`를 반환한다.

### 상태를 동기적으로 DOM에 업데이트하기

> **출처**
>
> - https://react-ko.dev/learn/manipulating-the-dom-with-refs#flushing-state-updates-synchronously-with-flush-sync

```jsx
function handleAdd() {
    setTodos([...todos, { id: uuid(), text }]);
    listRef.current.lastChild.scrollIntoView();
}
```

새로운 todo가 추가될 때마다 스크롤을 가장 하단으로 내리고 싶을 수 있다. 그러나 `set` 함수는 동기적으로 실행되지 않으므로 스크롤은 한 항목 위로 스크롤된다. 이 경우 `set` 함수의 실행을 동기적으로 강제하면 된다.

```jsx
import { useState, useRef } from "react";

let nextId = 0;
export default function App() {
  const listRef = useRef<HTMLUListElement | null>(null);
  const [todos, setTodos] = useState<{ id: number; text: string }[]>(
    [...Array(50)].map((_, idx) => ({ id: nextId++, text: `${idx}` })),
  );
  const [text, setText] = useState("");

  function handleAdd(): void {
    setTodos([...todos, { id: nextId++, text }]);
    setText("");
    if (listRef.current && listRef.current.lastChild instanceof HTMLElement) {
      listRef.current.lastChild?.scrollIntoView();
    }
  }

  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <button onClick={handleAdd}>add</button>
      <ul ref={listRef}>
        {todos.map((todo) => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </div>
  );
}

```

`flushSync`로 감싼 코드가 실행된 직후 React는 DOM을 동기적으로 업데이트하도록 한다. 그래서 `listRef.current.lastChild`는 의도했던 대로 새로 업데이트된 todo를 표시한 DOM 노드를 가리키게 된다.

### 단, React가 React 상태를 동기적으로 업데이트하는 것은 아니다

```javascript
function handleAdd() {
    const newTodo = { id: nextId++, text: text };
    flushSync(() => {
        setText('');
        setTodos([ ...todos, newTodo]);      
    });
    console.log(todos.at(-1).text)
    console.log(listRef.current.lastChild.textContent);
}
```

이때 콘솔 결과는 같지 않다. 전자는 상태가 갱신되기 전 `todos`의 마지막 요소를, 후자는 상태가 갱신된 후의 `todos`의 마지막 요소를 가리킨다. React 자체는 상태를 비동기적으로 갱신하여 React는 다음 렌더링의 값에 접근하지 않는다. `flushSync`는 `react-dom`의 API로, DOM의 동기적 갱신을 보장한다. (TODO)

## createRoot

> **출처**
>
> - https://react-ko.dev/reference/react-dom/client/createRoot

```jsx
import ReactDOM from 'react-dom/client';

const root = ReactDOM.createRoot(domNode, options?);
```

`createRoot`로 브라우저 DOM 노드 안에 React 컴포넌트를 표시하는 루트를 만들 수 있다.

### 매개변수

- `domNode`: React 루트를 표시할 DOM 엘리먼트이다.
- `options`(optional): React 루트에 대한 옵션을 담은 객체이다.
  - `onRecoverableError`(optional): React가 오류로부터 자동적으로 복구될 때 호출할 콜백이다.
  - `identifierPrefix`(optional): `useId`로부터 생성된 ID에 사용할 문자열 접두사이다.

### 반환값

`render`와 `unmount` 메서드를 가진 객체를 반환한다.

### `render` 메서드

```jsx
root.render(reactNode);
```

React 루트에 `reactNode`를 표시한다.

- 매개변수 `reactNode`: react 루트에 표시할 React 노드이다.
- 반환값 `undefined`

### `render` 메서드의 동작

- `render` 메서드를 처음으로 호출하면 React 루트 내부에 이미 있는 HTML 컨텐츠를 지운다.
- `render` 메서드를 다시 호출하면 `set` 함수를 호출할 때처럼 이전 렌더링 트리와 비교하여 필요한 부분만 다시 만든다.

### `unmount` 메서드

```jsx
root.unmount();
```

`unmount` 메서드를 호출하여 루트 내부의 모든 컴포넌트를 언마운트하고 트리 내부의 이벤트 핸들러나 state를 모두 삭제하는 등 React를 루트로부터 떼어낸다.

- 매개변수를 받지 않는다.
- 반환값 `undefined`

### `unmount` 메서드의 동작

- `unmount` 메서드를 호출한 후에는 바인딩된 루트 객체의 `render` 메서드를 호출할 수 없다.
- 단, 동일한 DOM 노드에 다른 루트 객체의 `render` 메서드를 호출할 수는 있다.

## hydrateRoot

> **출처**
>
> - https://react-ko.dev/reference/react-dom/client/hydrateRoot

```jsx
import { hydrateRoot } from 'react-dom/client';

const root = hydrateRoot(domNode, reactNode, options?);
```

`hydrateRoot`는 서버사이드에서 이미 만들어진 HTML(예: `react-dom/server`로 생성한 HTML 컨텐츠)에 `reactNode`를 붙인다. hydration 과정을 거치면 HTML 완전히 인터렉티브한 애플리케이션이 된다. 이때 hydration 이전 렌더링 트리(서버사이드)와 hydration 이후 렌더링 트리(클라이언트사이드)는 동일해야 이벤트 핸들러가 다른 엘리먼트에 붙는 등 버그를 생성하지 않는다. 

### 매개변수

- `domNode`: 서버사이드에서 루트 엘리먼트로 렌더링된 DOM 엘리먼트이다.
- `reactNode`: 이미 렌더링된 HTML에 렌더링할 React 노드이다.
- `options`(optional): React 루트에 대한 옵션을 담은 객체이다.
  - `onRecoverableError`(optional): React가 오류로부터 자동적으로 복구될 때 호출할 콜백이다.
  - `identifierPrefix`(optional): `useId`로부터 생성된 ID에 사용할 문자열 접두사이다.

### 반환값

`render`와 `unmount` 메서드를 가진 객체를 반환한다.

### `render` 메서드

```jsx
root.render(reactNode);
```

`render` 메서드를 호출하여 hydrate된 React 루트 내부의 React 컴포넌트를 `reactNode`로 업데이트한다.

- 매개변수 `reactNode`: 업데이트해 표시할 React 노드이다.
- 반환값 `undefined`

### `render` 메서드의 동작

- 루트 hydrate가 완료되기 전에 `render` 메서드를 호출하면 React는 서버 렌더링된 HTML 컨텐츠를 지우고 전체 루트를 클라이언트 렌더링된 컨텐츠로 대체한다.

### `unmount` 메서드

```jsx
root.unmount();
```

`unmount` 메서드를 호출하여 루트 내부의 모든 컴포넌트를 언마운트하고 트리 내부의 이벤트 핸들러나 state를 모두 삭제하는 등 React를 루트로부터 떼어낸다.

- 매개변수를 받지 않는다.
- 반환값 `undefined`

### `unmount` 메서드의 동작

- `unmount` 메서드를 호출한 후에는 바인딩된 루트 객체의 `render` 메서드를 호출할 수 없다.

### hydration mismatch error 끄기

```jsx
export default function App() {
  return (
    <h1 suppressHydrationWarning={true}>
      현재 날짜: {new Date().toLocaleDateString()}
    </h1>
  );
}
```

엘리먼트의 컨텐츠나 속성이 서버와 클라이언트 사이드에서 다를 수밖에 없는 경우 엘리먼트의 속성`suppressHydrationWarning`을 `true`로 전달해 mismatch 경고를 끌 수 있다.

### 클라이언트 사이드인지 확인하기

클라이언트 사이드인지 `useEffect`를 사용해 확인할 수 있다. `useEffect`는 클라이언트 사이드에서만 실행되는 훅이기 때문이다.

```jsx
import React, { useState, useEffect } from 'react';

export function isClient() {
  const [isClient, setIsClient] = useState(false);
  
  useEffect(() => {
    setIsClient(true);
  }, []);
  
  return isClient;
}
```

***

## `<SctirctMode>`

> **출처**
>
> - https://react-ko.dev/reference/react/StrictMode

```tsx
<React.StrictMode>
  <App />
</React.StrictMode>
```

`<StirctMode>`는 **개발 환경에서만** 내부 트리에 다음과 같은 전용 동작을 활성화한다.

- 컴포넌트를 두 번 렌더링한다.
- Effect를 두 번 실행한다.
- deprecated된 API가 사용되는지 확인한다.

### 컴포넌트를 두 번 렌더링한다

React 컴포넌트는 순수 함수로 동일한 입력에 대해 항상 동일한 출력을 반환해야한다. 따라서 두 번 렌더링하면 동작이 변경되어 버그를 확인할 수 있다. 다음 함수들이 두 번 호출된다.

- 컴포넌트 함수 본문
- `useState`, `set` 함수, `useMemo`, `useReducer`에 전달한 함수
- `constructor`, `render`, `sholudComponentUpdate`와 같은 클래스 컴포넌트 메서드 일부

- React는 개발 환경에서 두 번 렌더링된다(초기화 함수와 업데이터 함수를 두 번 실행한다). 컴포넌트가 순수 함수인지 확인하기 위해서이다. 

> 출처
>
> - https://react-ko.dev/learn#why_alert_show_twice
> - https://react-ko.dev/reference/react/StrictMode#fixing-bugs-found-by-double-rendering-in-development

### deprecated API를 경고한다

다음 API를 사용하는 경우 경고한다.

- `findDOMNode`
- `UNSAFE_componentWillMount`와 같은 `UNSAFE_` 클래스 라이프 사이클 메서드
- 레거시 컨텍스트(`childContextTypes`, `contextTypes`, `getChildContext`)
- 레거시 문자열 refs(`this.refs`)

## `<Fragment>(<>)`

> 출처
>
> - https://react-ko.dev/reference/react/Fragment

```jsx
<React.Fragment>
	<div>첫번째 요소</div>
	<div>두번째 요소</div>
</React.Fragment>
```

`<Fragment>`를 사용하면 wrapper 노드 없이 엘리먼트를 그룹화할 수 있다. `<Fragment>...</Fragment>` 대신 `<>...</>`로 축약할 수 있다.

### Props

- `key`(optional): 축약 표현에는 사용할 수 없다.

### 실제 DOM에 영향을 주지 않는다

`<Fragment>`는 실제 DOM에 영향을 주지 않는다. 즉, wrapper node로 그룹화하지 않은 것과 같다 동일하다. 아래 1번과 2번은 서로 동일하다.

```jsx
/* 1번 */
<div>
  <Fragment>
    <p>첫번째 요소</p>
    <p>두번째 요소</p>
  </Fragment>
</div>

/* 2번 */
<div>
  <p>첫번째 요소</p>
  <p>두번째 요소</p>
</div>
```

### 언제 사용하는가?

wrapper node를 사용하길 원하지 않을 때 사용할 수 있다. 예를 들어 컴포넌트의 props에 여러 개의 엘리먼트를 전달하고 싶을 때 굳이 wrapper node를 추가하지 않아도 된다.

```jsx
function AlertDialog() {
  const buttons = (
    <>
    	<CancelButton />
    	<OKButton />
    </>
  );
  
  return <Dialog buttons={buttons} />;
}
```

## `<Suspense>`

```jsx
<React.Suspense fallback={<Loading />}>
	<Foo />
</React.Suspense>
```

`<Suspense>`는 자식 컴포넌트의 로딩이 완료될 때까지 fallback을 표시할 수 있다. 

> 출처
>
> - https://react-ko.dev/reference/react/Suspense

### 매개변수

- `children`: 렌더링하려는 실제 UI이다. 렌더링 중에 `children`이 suspense되면 Suspense 바운더리가 `fallback` 렌더링으로 전환된다. (suspense된 컴포넌트의 가장 가까운 Suspense 바운더리가 활성화된다.)
- `fallback`: 로딩이 완료되지 않은 실제 UI에 대신 렌더링되는 UI이다. `children`이 suspense되면 `fallback`으로 자동적으로 전환하고 데이터가 준비되면 `children`으로 돌아온다. 렌더링 중에 `fallback`이 suspense되면 가장 가까운 부모 Suspense 바운더리가 활성화된다.

### suspend된 렌더링의 state는 보존하지 않는다

처음 마운트가 가능하기 전 suspense된 렌더링에 대한 state는 보존하지 않는다. 컴포넌트가 로드되면 suspense된 트리를 처음부터 다시 시도한다.

### `startTransition`이나 `useDeferredValue`에 의한 업데이트는 fallback을 표시하지 않는다

Suspense가 트리에 대한 컨텐츠를 표시하고 있다가 다시 suspend되면, `startTransition`이나 `useDeferredValue`에 의한 업데이트가 아닌 경우만 fallback을 표시한다.

- `useDeferredValue`: 지연된 값을 사용하는 컴포넌트가 suspense된 경우, Suspense 바운더리가 활성화되는 대신 업데이트가 반영된 UI가 준비될 때까지 지연된 값(이전 값)을 표시한다.
- `startTransition`: 트랜지션으로 인한 업데이트에 컴포넌트가 suspense된 경우, Suspense 바운더리가 활성화되는 대신 업데이트가 반영된 UI가 준비될 때까지 기존의 컨텐츠를 보여준다. (Suspense-enabled route는 기본적으로 네비게이션 업데이트를 트랜지션으로 감싸고 있을 것이다.)

> 출처
>
> - https://react-ko.dev/reference/react/Suspense#showing-stale-content-while-fresh-content-is-loading
> - https://react-ko.dev/reference/react/Suspense#preventing-already-revealed-content-from-hiding

### 이미 표시된 컨텐츠를 숨겨야하는 경우 layout Effect를 클린업한다

React는 다시 suspense되어 이미 표시된 컨텐츠를 숨겨하는 경우 layout Effect를 클린업하고 컨텐츠가 준비되면 layout Effect를 다시 실행한다. 컨텐츠가 숨겨져 있는 동안에는 DOM 레이아웃을 측정하는 Effect가 실행되지 않도록 보장한다.

### 내부 최적화(TODO)

> React includes under-the-hood optimizations like *Streaming Server Rendering* and *Selective Hydration* that are integrated with Suspense. Read [an architectural overview](https://github.com/reactwg/react-18/discussions/37) and watch [a technical talk](https://www.youtube.com/watch?v=pj5N-Khihgc) to learn more.

### Suspense-enabled 데이터 소스만 Suspense 컴포넌트를 활성화한다

Suspense-enabled 데이터 소스만 Suspense 컴포넌트를 활성화한다. Suspense-enabled 데이터 소스는 다음을 포함한다.

1. Relay나 Next.js와 같은 Suspense-enabled 프레임워크를 사용한 데이터 fetching
2. `lazy`를 사용하는 지연-로딩 컴포넌트 코드

Suspense는 Effect나 이벤트 핸들러에서의 fetching을 감지하지 않는다.

> 출처
>
> - https://react-ko.dev/reference/react/Suspense#displaying-a-fallback-while-content-is-loading

### Suspense 바운더리를 중첩하는 경우의 동작

컴포넌트가 suspense되면 가장 가까운 부모 Suspense 바운더리가 활성화된다. 

```jsx
<Susepnse fallback={<GlobalSpinner />}>
  <Profile />
  <Suspense fallback={<FeedSkeleton />}>
    <Feed />
  </Suspense>
</Susepnse>
```

1. `Profile`이 로딩될 때까지 `GlobalSpinner`가 표시된다.
2. `Profile`의 로딩이 완료되면 `GlobalSpinner`에서 컨텐츠로 전환된다.
3. `Feed`가 아직 로딩되지 않은 경우, `Feed` 대신 `FeedSkeleton`가 표시된다.
4. `Feed`의 로딩이 완료되면 `FeedSkeleton`에서 `Feed`로 전환된다.

> 출처
>
> - https://react-ko.dev/reference/react/Suspense#revealing-nested-content-as-it-loads

### 네비게이션에서 Suspense 바운더리를 재설정하기

```jsx
<Profile key={queryParams.id} />
```

React는 트랜지션하는 동안에는 이미 표시된 컨텐츠를 숨기지 않으나 다른 매개변수를 가진 라우트로 이동하는 경우 fallback을 표시하는 것이 적절할 수 있다. 이 경우 매개변수를 트랜지션을 사용하는 컴포넌트의 `key` props로 전달하여 서로 다른 컨텐츠임을 알려줄 수 있다. 예를 들어 `@크리톤` 프로필 페이지에서 `@파이돈` 프로필 페이지로 트랜지션을 사용하여 업데이트되는 경우, `@파이돈` 프로필 페이지가 준비될 때까지 `@크리톤` 프로필 페이지를 보여주는 것보다 Spinner와 같은 fallback을 보여주는 것이 적절하다.

### 서버 에러나 server-only 컨텐츠에 fallback 제공하기(TODO)

> https://react-ko.dev/reference/react/Suspense#providing-a-fallback-for-server-errors-and-server-only-content
