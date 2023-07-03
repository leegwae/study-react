# React Interview Questions

### React란 무엇인가

React는 웹이나 네이티브 앱에서 사용자 인터페이스를 작성하기 위해 사용하는 라이브러리이다. 컴포넌트를 기반으로 하여 독립적인 컴포넌트를 조립해 UI를 구축한다.

### React의 특징은 무엇인가

UI를 만드는 컴포넌트를 정의하고 재사용할 수 있다. 렌더링 과정에서 Virtual DOM을 사용한다. 컴포넌트 간 단방향식 데이터 플로우를 사용한다.

### React의 장점은 무엇인가

라이브러리이기 때문에, 애플리케이션에서 일부만 담당하고 다른 프레임워크와 함께 사용할 수 있다. 컴포넌트를 재사용할 수 있어 유지보수가 편리하다. 클라이언트 사이드 렌더링과 서버사이드 렌더링을 모두 지원할 수 있다. Virtual DOM을 사용하여 변화를 적용하는 비용이 적다.

#### TODO: 어떻게 다른 프레임워크와 사용가능한가

#### TODO: 어떻게 클라이언트 사이드 렌더링과 서버사이드 렌더링을 모두 지원하는가

#### TODO: Virtual DOM을 사용하면 왜 변화를 적용하는 비용이 적은가

### TODO: React의 단점은 무엇인가

### JSX란 무엇인가

JSX는 JavaScript XML의 약자로 JavaScript를 확장한 문법이다. React에서 컴포넌트를 만들 때 JavaScript 내에서 HTML과 유사한 마크업으로 작성할 수 있다. JSX는 자바스크립트 함수로 컴파일되고 이것이 브라우저에 마크업을 렌더링하는 JavaScript 함수, 곧 컴포넌트이다.

### React에서 컴포넌트란 무엇인가

React 컴포넌트는 브라우저에 마크업을 렌더링하는 JavaScript 함수이다. 각각의 컴포넌트는 독립적이며 재사용할 수 있다. React에서는 이 컴포넌트를 조립하여 애플리케이션을 구성한다.

### TODO: Virtual DOM이란 무엇인가

브라우저 렌더링 과정에 비교하여

https://velopert.com/3236

https://callmedevmomo.medium.com/virtual-dom-react-%ED%95%B5%EC%8B%AC%EC%A0%95%EB%A6%AC-bfbfcecc4fbb

### React에서 상태란 무엇인가

상태는 컴포넌트 내부의 데이터로 인스턴스 간에 공유되지 않는다. 상태는 컴포넌트 내부에서 변경 가능한 값으로, 컴포넌트는 자신의 상태를 변경할 수 있다. 이때 상태의 변화는 컴포넌트의 재렌더링을 촉발하며 재렌더링 후에도 변경 사항이 유지된다. (컴포넌트 내부의 지역 변수는 변경되어도 재렌더링을 촉발하지 않고 상태의 변경으로 인한 재렌더링 후에도 변경 사항은  유지되지 않는다.)

https://react-ko.dev/learn/state-as-a-snapshot

### React에서 props란 무엇인가

props는 부모 컴포넌트에서 자식 컴포넌트에게 전달하는 데이터이다. props를 통한 데이터의 흐름은 단방향으로, 자식 컴포넌트에서는 부모 컴포넌트가 전달한 props를 바꿀 수 없다. 즉, props는 변경 불가능한 값이다. (부모 컴포넌트가 데이터를 바꿀 수 있는 메서드를 props로 제공하고 자식 컴포넌트가 해당 메서드를 호출하는 형태로 소통할 수 있다.)

### React에서 state와 props의 차이는 무엇인가

상태는 컴포넌트 내부의 데이터로 변경이 가능하다. props는 자식 컴포넌트가 부모 컴포넌트로부터 전달받은 데이터로 변경이 불가능하다.

### React에서 prop drilling이란 무엇이고 어떻게 피할 수 있는가

상태가 여러 컴포넌트 간 공유되어야 할 경우, 가장 가까운 공통 조상 컴포넌트로 state를 끌어올려 props로 전달해줄 수 있다. 이때 prop drilling 문제는 props를 제공하는 컴포넌트와 사용하는 컴포넌트가 너무 멀리 떨어져있어 전달이 어렵고 컴포넌트를 변경하는 게 어려운 상황을 뜻한다. prop drilling 문제는 컴포넌트를 하위 컴포넌트로 쪼개지 않거나, 상태를 전역으로 올려 prop를 거치지 않고 상태를 사용할 수 있게 만들어 해결할 수 있다. 후자는 React의 Context API나, 외부 전역 상태 관리 라이브러리를 사용하면 된다.

### TODO: 컴포넌트에서 key는 어떻게 사용되는가

https://react-ko.dev/learn/managing-state#preserving-and-resetting-state

https://react-ko.dev/learn/rendering-lists#keeping-list-items-in-order-with-key

### TODO: 클래스와 함수형 중 무엇을 선택할 것이고 그 이유가 무엇인지 설명하라

https://react-ko.dev/learn/keeping-components-pure

### TODO: React는 상태 변화를 어떻게 감지하는가?

https://react-ko.dev/learn/state-a-components-memory#how-does-react-know-which-state-to-return

### TODO: React에서 렌더링 과정을 설명하라

https://react-ko.dev/learn/render-and-commit

https://react-ko.dev/learn/queueing-a-series-of-state-updates

1. DOM과 Virtual DOM의 차이에 대해 설명하라
2. 재조정에 대하여 설명하라
3. 컴포넌트에서 key는 어떻게 사용되는가?
4. React Context API에 대해 설명하라. 또 어떻게 사용되는가?
5. React의 라이프사이클을 설명하라
6. 클래스 컴포넌트에서 생명주기 메서드에 대해 설명하라
7. React hooks는 무엇인가?
8. React Hooks의 장점은 무엇인가?
9. state를 직접 변경하지 않고 setState를 사용하는 이유는?
10. useEffect로 componentWillUnmount를 구현할 수 있는가?
11. Class Component와 Function Component의 차이에 대해 설명하라
12. 클래스와 함수형 중 무엇을 선택할 것이고 그 이유가 무엇인지 설명하라
13. 최적화를 해본 경험이 있는가? 혹은 React에서 최적화를 어떻게 하는가?
14. useMemo, useCallback에 대해 설명하라
15. React는 메모이제이션을 어떤 방식으로 하는가 
16. Redux란 무엇인가?
17. React에서 상태 관리 라이브러란 무엇이고 목적은 무엇인가?
18. 상태관리 라이브러리 Redux, Mobx의 차이점에 대해서 설명하라.
19. Redux의 목적은 무엇인가?
20. Redux를 사용하는 이유는 무엇인가?
21. Redux의 장단점에 대해 설명하라
22. Context API와 Redux를 비교하라
23. 상태관리 라이브러리 중 무엇을 선택할 것인가?
24. 클라이언트 사이드 라우팅에 대해 설명하라
25. React에서 HOC란?
26. Creact-React-App이란 무엇인가?
27. React 애플리케이션을 styling하는 방식에는 무엇이 있는가?
28. 제어 컴포넌트와 비제어 컴포넌트의 차이는 무엇인가?
29. React에서 presentational component와 container component의 차이는 무엇인가?
30. 렌더링 동작 방식에 대하여 설명하라
31. React18 버전에 대해 설명하라.
32. useEffect와 useLayoutEffect에 대해 설명하라





## 참고

- [React 예상 면접 질문 리스트](https://velog.io/@ye-ji/React-%EC%98%88%EC%83%81-%EB%A9%B4%EC%A0%91-%EC%A7%88%EB%AC%B8-%EB%A6%AC%EC%8A%A4%ED%8A%B8)
- [[React\] 리액트 면접 질문 (기초 개념)](https://ahnanne.tistory.com/60)
- [React 인터뷰 대비 질문과 답변 15](https://velog.io/@dojunggeun/React-interview-questions-15)
- [[프론트엔드] FE 기술면접(React) 복기 모음](https://torimaru.tistory.com/35)
- [실제로 받은 프론트엔드 개발자 면접 질문 모음](https://xiubindev.tistory.com/119)
- [React 면접 질문과 답변 (난이도 상)](https://careerly.co.kr/comments/73555)
- [Top 40 ReactJS Interview Questions and Answers for 2023](https://www.simplilearn.com/tutorials/reactjs-tutorial/reactjs-interview-questions)
- https://github.com/sudheerj/reactjs-interview-questions