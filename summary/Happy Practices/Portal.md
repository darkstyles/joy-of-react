# Portal 사용법
* 원하는 DOM에 리액트 컴포넌트를 위치시킬 수 있다.
```jsx
import { createPortal } from 'react-dom';

function Modal({ children }) {
  return createPortal(
    // The React elements to render:
    <div className="modal">
      {children}
    </div>,
    // The target DOM container to hold the output:
    document.querySelector('#modal-root')
  );
}
```

# Portal 내부 들여다보기
```js
{
  "$$typeof": Symbol(react.portal),
  "children": {
    "$$typeof": Symbol(react.element),
    "type": {…},
    "key": null,
    "ref": null,
    "props": {…},
  },
  "containerInfo": div#modal-root,
  "key": null,
}
```
이 강좌에서는 React 엘리먼트가 UI의 일부에 대한 설명인 방법에 대해 이야기했습니다. 우리가 작성한 JSX는 React.createElement 호출로 컴파일되고, 이 호출은 설명형 객체("가상 DOM")를 생성합니다.

createPortal은 매우 유사합니다! React가 생성할 포털에 대한 설명을 생성합니다.

이 포털 객체는 엘리먼트를 "감싸고" 있으며, children 프로퍼티에는 텔레포트하려는 모든 React 엘리먼트가 포함되어 있습니다. 그리고 containerInfo 프로퍼티는 이 요소들을 호스팅할 DOM 노드에 대한 참조를 담고 있습니다.

다시 말해, createPortal 함수는 이 작업 중 어떤 것도 직접 수행하지 않습니다. 이 함수는 렌더링 프로세스에서 React가 사용하는 설명을 생성하여 렌더러가 앱의 이 조각을 다른 컨테이너에 주입해야 한다는 것을 알립니다.