* `react` 종속성에서 핵심 React 라이브러리를 가져오고, `react-dom`에서 createRoot 함수를 가져온다.
* React 자체는 "플랫폼애 대한 지식이 없어도 기능을 사용할 수 있다"라고 보기 때문에 핵심 react 패키지가 있고, 그 다음에는 플랫폼별로 다른 렌더러가 있다.
  * react-dom for the web
  * react-native for mobile (iOS / Android) or desktop (Windows / macOS) applications
  * react-three-fiber for 3D scenes using WebGL and Three.js
* `React.createElement` 는 3개 이상의 인수를 받는 함수이다.
  * 생성할 요소의 유형
  * 요소가 갖기를 원하는 속성
  * 요소의 콘텐츠, 요소가 하위 요소로 가져야 하는 내용
* render 함수
```js
function render(reactElement, containerDOMElement) {
  const element = document.createElement(reactElement.type);

  element.textContent = reactElement.children;
  for (const key in reactElement.props) {
    const value = reactElement.props[key];
    element.setAttribute(key, value);
  }

  containerDOMElement.appendChild(element);
}
```