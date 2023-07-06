# study-react

(2023/07/05~)

- https://react.dev/

- https://react-ko.dev/

## One-way data flow

React는 컴포넌트 계층 구조를 따라 부모 컴포넌트에서 자식 컴포넌트로 내려 가는 단방향 데이터 흐름을 따른다. 기본적으로 자식 컴포넌트는 부모 컴포넌트의 데이터를 수정할 수 없으나 부모 컴포넌트가 props로 데이터를 수정할 수 있는 메서드를 전달하는 방식으로 역방향 데이터 흐름을 추가할 순 있다.

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

애플리케이션은 반드시 side effect를 동반한다. side effect는 렌더링이 완료된 후에 일어나는 변경이다. React에서 side effect는 대개 이벤트 핸들러 실행으로 발생한다. 이벤트 핸들러는 렌더링 중에 실행되는 것이 아니라 렌더링이 완료된 후 이벤트의 발생으로 실행된다. 그러니 이벤트 핸들러는 순수 함수가 아닐 수도 있다.

> **출처**
>
> - https://react-ko.dev/learn/keeping-components-pure#where-you-_can_-cause-side-effects

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

React Element 객체가 생성된다고 렌더링되진 않는다. 렌더링하는 함수에 전달해야한다.

> **출처**
>
> - https://react-ko.dev/reference/react/createElement#what-is-a-react-element-exactly

## Props

**props**는 부모 컴포넌트에서 자식 컴포넌트에게 전달하는 데이터이다. props는 변경 불가능한 값이다. props를 통한 데이터의 흐름은 단방향으로, 자식 컴포넌트에서는 부모 컴포넌트가 전달한 props를 바꿀 수 없다.

## State

**state**는 컴포넌트 내부의 데이터로 인스턴스 간에 공유되지 않는다. state는 컴포넌트 내부에서 변경 가능한 값으로, 컴포넌트는 자신의 상태를 변경할 수 있다. state의 변경은 렌더링을 촉발시키며 이 변경은 렌더링 이후에도 유지된다. 이와 달리 지역 변수의 변경은 렌더링을 촉발시키지 않으며 렌더링 이후에도 유지되지 않는다.

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
> ```jsx
> function Counter({ player }) {
>     const { count } = usePlayer(player);
>     return <div>{count}</div>
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

## Rendering

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

#### 첫 렌더링

```jsx
import Button from './Button.js';
import { createRoot } from 'react-dom/client';

const root = createRoot(document.getElementById('root'))
root.render(<Button />);
```

`createRoot`로 생성한 루트(root)의 `render()`에 컴포넌트를 전달하면 해당 컴포넌트의 첫 렌더링을 촉발한다. `render()`에 전달된 컴포넌트를 루트 컴포넌트(root component)라고 한다.

#### 리렌더링

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

React에서 **렌더링(Rednering)**이란 컴포넌트를 호출하는 것이다. 렌더링은 재귀적이다. React는 중첩된 컴포넌트가 없고 무엇은 화면에 표시해야하는지 정확히 알기 전까지 컴포넌트를 렌더링하고, 그리고 해당 컴포넌트가 반환하는 컴포넌트를 렌더링하는 과정을 반복한다.

1. 첫 렌더링에서 React는 루트 컴포넌트를 호출한다. 이 동안 React는 컴포넌트에 대한 DOM 노드를 생성한다.
2. 리렌더링에서 React는 state 로 렌더링이 촉발된 컴포넌트를 호출한다. 이 동안 React는 이전 렌더링과 비교하여 변경된 속성을 계산한다. (계산만 하고 어떤 작업도 수행하지 않는다.)

### 단계 3: React가 DOM에 변경 사항을 커밋한다

React는 컴포넌트를 호출한 후 DOM을 수정한다.

1. 첫 렌더링 이후, React는 첫 렌더링 동안 생성한 DOM 노드를 `appendChild()` DOM API를 사용하여 화면에 표시한다.
2. 리렌더링 이후, React는 리렌더링 동안 계산된 값과 DOM을 일치하도록 한다. 이때 이전 렌더링과 최신 렌더링 간에 차이가 있는 경우에만 DOM 노드를 변경한다. 

### 최종적으로 화면에 표시: 브라우저 페인트

브라우저 렌더링(brwoser rendering)이란 렌더링이 완료되고 React가 DOM 업데이트한 이후 브라우저가 화면을 리페인트(repaint)하는 과정을 말한다. 문서에서는 "페인팅(painting)"으로 명시할 것이다.

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
2. 다음 렌더링 도중 `useState`를 만나면 해당하는 큐를 순회하며 `set` 함수를 꺼내 실행한다.
3. 최종 값(다음 렌더링의 state 값)을 계산한다.

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

일찍 리렌더링을 촉발하려면 `flushSync`를 사용한다. TODO: https://react-ko.dev/reference/react-dom/flushSync

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

`set` 함수에 전달된 값을 이용하여 **다음 렌더링의 state 값**을 지정할 수 있다. `set` 함수는 현재 렌더링 중인 state 값을 바꾸지 않는다. 항상 한 렌더링 내의 state는 불변하며, state의 업데이트는 반드시 리렌더링을 촉발시킨다.

- 함수인 경우, 이 함수는 업데이터 함수(updater function)로 취급한다. 업데이터 함수는 순수하며 결과만 반환해야한다. 업데이터 함수는 인자로 큐에 있는 이전 state의 값을 받는다.

  ```jsx
  setState(n => n + 1);	// 큐에 있는 이전 state + 1로 교체한다
  ```

- 그 외의 값은 경우, 다음 렌더링의 state는 해당 값이 된다.

  ```jsx
  setState(2);	// 2로 교체한다
  setState(state + 1); // 현재 렌더링 중인 state + 1로 교체한다
  ```

[Batching - 업데이터 함수 사용하기](when-using-updater function)를 참고한다.

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

React는 `Object.is`로 현재 렌더링 중인 state와 `set` 함수에 전달된 값을 비교하여 같다고 판단하면 state가 업데이트되었다고 판단하고 리렌더링을 촉발시킨다.

```javascript
if (Object.is(prevState, curState)) {
    triggerRender();
}
```

이에 따르면 state가 객체일 때, 다음 렌더링에 적용할 state 값으로 기존의 state 객체를 넘기면 같은 객체로 판별되어 리렌더링이 촉발되지 않는다.

### 왜 `set` 함수에 넘길 객체는 새로 생성해야하는가?

`set` 함수에는 다음 렌더링에 적용할 값을 전달한다. React는 이 값과 현재 렌더링 중인 값을 `Object.is`로 비교하여 같으면 업데이트를 무시한다. 이에 따르면, 객체의 프로퍼티 값을 수정해도 동일한 객체를 가리키고 있으므로 업데이트가 무시된다. 그러니 새로운 객체를 생성하여 넘기도록 한다.

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

### `set` 함수는 동기적으로 작동하지 않는다?

```jsx
const handleClick = () => {
    setValue('changed');
    console.log(value);	// unchanged
}
```

`set` 함수를 호출했는데도 로그에는 바뀐 값으로 출력되지 않아, 동기적으로 작동하지 않는다고 생각할 수 있다. 이것은 `set` 함수가 정말 `value`를 바꾼다면(`value = 'chagned'`) 동기적으로 작동하지 않는다는 말이 맞다. 그러나, `set` 함수를 호출하면 현재 렌더링 중인 state가 바뀌는 것이 아니라 다만 다음 렌더링의 state 값을 계산한다(다만 리렌더링을 촉발시킨다). 현재 렌더링 중인 state는 불변하며, 리렌더링이 촉발하여 다음 렌더링으로 넘어갔을 때 앞서 계산한 값을 현재의 state 값으로 저장한다.

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

### 이전 렌더링의 state를 렌더링 중에 참조하기

TODO: https://react-ko.dev/reference/react/useState#storing-information-from-previous-renders

### 함수를 state의 값으로 저장하기

- `useState`에 전달된 함수는 초기화 함수로 취급하므로 해당 함수를 호출하여 초기값을 저장하려고 시도한다.
- `set` 함수에 전달된 함수는 업데이터 함수로 취급하므로 해당 함수를 호출하여 다음 렌더링의 state로 적용하려고 시도한다.

함수를 state의 값으로 저장하려면 해당 함수 객체를 반환하는 함수를 전달한다.

```jsx
const [fn, setFn] = useState(() => myFunction);

const handleClick = () => {
    setFn(() => myFunction);
}
```

***

## `<SctirctMode>`

- React는 개발 환경에서 두 번 렌더링된다(초기화 함수와 업데이터 함수를 두 번 실행한다). 컴포넌트가 순수 함수인지 확인하기 위해서이다. 

> **출처**
>
> - https://react-ko.dev/learn#why_alert_show_twice
> - https://react-ko.dev/reference/react/StrictMode#fixing-bugs-found-by-double-rendering-in-development