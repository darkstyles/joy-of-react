# Video Summary
아시다시피 각 카운터는 독립되어 있습니다. 깜짝 놀랐습니다! 같은 상태를 공유할 줄 알았는데요.

동일한 상태를 공유할 것이라고 생각한 이유는 React 엘리먼트가 하나뿐이기 때문입니다.

JSX를 제외하고 살펴보겠습니다.
```jsx
const counterElem = React.createElement(Counter);
// -> { type: Counter, props: {} }
```

createElement 메서드는 React 엘리먼트, 즉 일반 JS 객체를 생성합니다. 그런 다음 이 객체에 대한 참조를 변수 counterElem에 저장하고 반환된 코드에서 해당 변수를 두 번 참조합니다.

그렇다면 React 엘리먼트가 하나만 있다면 React 컴포넌트 인스턴스도 하나만 생성되지 않을까요?

여기서 이해해야 할 것은 "엘리먼트 생성"과 "컴포넌트 렌더링"은 완전히 다른 것이라는 점입니다.

조금 더 자세히 살펴봅시다. index.js 파일에 다음 코드가 있습니다:
```jsx
import App from "./App";

const container = document.getElementById('root');
const root = createRoot(container);

root.render(<App />);
```

App 엘리먼트({ type: App })를 생성하고 해당 엘리먼트를 React에 전달합니다. 이 엘리먼트를 렌더링하도록 React에 요청합니다.

이 강좌를 진행하는 내내 React 엘리먼트는 우리가 만들고자 하는 무언가에 대한 설명이라고 말씀드렸습니다. 이는 건축가의 설계도와 비슷합니다.

저는 최근에 건축가 설계도가 여러 단계의 세부 사항으로 구성되어 있다는 것을 알게 되었습니다.
[건축 설계 단계](https://github.com/darkstyles/joy-of-react/assets/3774171/5b9dc53b-4a0a-47be-a387-70580647f3fe)

우리가 만든 요소인 { type: App }은 레벨 0과 같습니다. React가 렌더링할 실제 UI에 대해서는 아무 것도 알려주지 않습니다!

더 세분화하기 위해 React는 App 컴포넌트를 렌더링해야 합니다. App 함수는 애플리케이션의 보다 세분화된 그림인 React 엘리먼트를 반환합니다. 그 모습은 다음과 같습니다:
```jsx
const counterElem = { type: Counter, props: {} };

const appSketch = {
  type: 'div',
  props: {},
  children: [
    counterElem,
    counterElem
  ],
};
```
이것은 레벨 2 스케치일 수 있습니다. 자식이 두 개인 div가 필요하다는 것은 알지만 그 자식이 정확히 무엇인지 알 수 없습니다!

그런 다음 React는 자식 위에 매핑하고 동일한 계획을 재귀적으로 따릅니다: 카운터 컴포넌트를 렌더링하여 세부 사항을 채웁니다.
```jsx
const appSketch = {
  type: 'div',
  props: {},
  children: [
    {
      type: 'button',
      props: {
        onClick: function () {},
      },
      children: 0,
    },
    {
      type: 'button',
      props: {
        onClick: function () {},
      },
      children: 0,
    },
  ],
};
```
각 counterElem이 같은 객체에 대한 참조라는 사실은 중요하지 않습니다. React는 이를 고려하지 않습니다! 대신, 이 단일 설명은 여러 컴포넌트를 렌더링하기 위해 여러 번 사용됩니다.

맨 위에 있는 <App />부터 시작해서 앱을 자세히 살펴봤습니다. React 엘리먼트가 컴포넌트를 참조할 때마다 해당 컴포넌트를 렌더링하여 전체 그림을 채웠습니다. 스케치가 완성되었습니다!

여기서 멋진 점은 실제 UI에 대한 정확한 설명이 있다는 것입니다. 브라우저에서 살펴보면 다음과 같이 보입니다.
```jsx
<div>
  <button>
    0
  </button>
  <button>
    0
  </button>
</div>
```
JS 오브젝트가 이 마크업을 어떻게 완벽하게 설명하는지 보셨나요?

과거에 React 팀은 이것을 가상 DOM이라고 불렀습니다. 각 컴포넌트는 애플리케이션의 한 조각을 설명하는 JS 청크를 생성합니다. 컴포넌트를 접할 때 렌더링하면 모든 슬라이스를 조합하여 원하는 UI의 세부적인 스케치를 얻을 수 있습니다.

(이 용어가 혼란스럽다는 이유로 React 팀에서 사용을 중단한 것 같습니다. 우리는 이 강좌의 모듈 5에 이르렀고, 이제야 이 용어를 이해할 수 있는 충분한 컨텍스트에 도달했습니다!).

이 모든 작업을 수행한 후 React는 필요한 DOM 노드를 생성하여 이러한 변경 사항을 "커밋"합니다.

다른 예를 하나 더 살펴봅시다. 이 예는 정반대의 사례이지만 기본 아이디어는 동일합니다.

다음은 코드입니다.
```jsx
import React from 'react';

function App() {
  const [color, setColor] = React.useState(null);

  return (
    <div>
      {color ? (
        <Counter color={color} />
      ) : (
        <Counter />
      )}

      <label htmlFor="color-picker">
        Select color:
        <input
          id="color-picker"
          type="color"
          value={color}
          onChange={(event) => {
            setColor(event.target.value);
          }}
        />
      </label>
    </div>
  );
}

function Counter({ color }) {
  const [count, setCount] = React.useState(0);

  return (
    <button
      style={{ color }}
      onClick={() => setCount(count + 1)}
    >
      {count}
    </button>
  );
}

export default App;
```
`Counter` 컴포넌트에는 이제 `color` props이 있고, 그 값을 변경하는 제어된 색상 입력이 있습니다.

하지만 여기서 조금 흥미로운 점이 있습니다.
```jsx
{color ? (
  <Counter color={color} />
) : (
  <Counter />
)}
```
첫 번째 렌더링에서는 색상이 null이므로 false 분기가 사용되며, <Counter />가 렌더링됩니다. 사용자가 새 색상을 선택하면 색상이 #123123과 같은 문자열이므로 true 분기가 대신 선택됩니다.

여기에 문제가 있습니다: false 분기에서 true 분기로 전환할 때 `Counter` 컴포넌트 인스턴스를 제거/재생성할까요? 아니면 기존 인스턴스를 재사용할까요?

제 원래의 직감은 인스턴스를 제거하고 다시 생성할 것이라는 것이었습니다. 결국, JSX의 다른 덩어리이니까요!

하지만 React는 코드가 어떻게 구조화되어 있는지에 대한 가시성이 없습니다. 리턴하는 것만 볼 수 있습니다.

스케치해 봅시다. 첫 번째 렌더링에서 우리는 다음과 같이 보이는 React 엘리먼트를 반환합니다.
```jsx
const firstSketch = {
  type: 'div',
  props: {},
  children: [
    {
      type: Counter,
      props: {}
    },
    // ✂️ Ignoring the `<label>` since it doesn't change
  ]
}
```
그런 다음 사용자가 색상을 변경한다고 가정합니다. 새 스케치는 다음과 같이 표시됩니다:
```jsx
const secondSketch = {
  type: 'div',
  props: {},
  children: [
    {
      type: Counter,
      props: {
        color: '#123123',
      }
    },
    // ✂️ Ignoring the `<label>` since it doesn't change
  ]
}
```
React가 알 수 있는 유일한 차이점은 새로운 `color` props이 추가되었다는 것입니다. 그리고 props은 항상 바뀌기 때문에 일반적으로 props이 바뀔 때 컴포넌트 인스턴스를 제거하거나 다시 만들지 않습니다!

이것도 반직관적인 상황 중 하나입니다. 우리의 뇌는 "하지만 이건 다르잖아! JSX의 다른 덩어리라고요!"라고 말합니다. 하지만 우리가 지금까지 배워온 것처럼 React 엘리먼트는 우리가 원하는 것을 설명하는 것입니다. 그 설명의 정체성은 중요하지 않습니다.

이 부분은 우리가 이해하기 어렵습니다. 저는 React를 오랫동안 사용해 왔지만 여전히 당황스럽습니다! 몇 달 후에 이러한 아이디어를 확고히 하기 위해 다시 한 번 살펴볼 가치가 있는 강의 중 하나일 것입니다.