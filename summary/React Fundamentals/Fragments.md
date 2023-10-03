```js
function App() {
  return (
    <h1>Welcome to my homepage!</h1>
    <p>Don't forget to sign the guestbook!</p>
  );
}

function App() {
  return (
    React.createElement('h1', {}, 'Welcome to my homepage!')
    React.createElement('p', {}, "Don't forget to sign...")
  );
}
```
* 위 코드 작성 시 에러가 발생하는데 그 이유는 return 문은 하나의 표현식을 받아 값을 생성하여 반환하는데 위 코드는 두개의 표현식을 받다보니 하나의 값을 생성하여 반환하지 못하므로 에러가 발생한다.
* 위 문제를 해결하는 방법을 알아보면
    * `div` 요소를 상위에 추가하면 return 문에 하나의 표현식이 할당되어 하나의 값을 생성하므로 반환에 문제가 없다.
        * 하지만 이렇게 처리하면 불필요한 요소가 DOM 트리 내에 추가되고 특정 상황에서는 해당 요소로 인해 의도하지 않은 UI 틀어짐이 발생한다.
    * 리액트에서 제공하는 `fragment` 컴포넌트를 사용하면 된다. (`<React.Fragment></React.Fragment>`)
        * `fragment` 컴포넌트는 DOM 노드를 생성하지 않는다.
        * 단축구문은 `<></>`이다.
        