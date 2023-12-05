# Video Summary
* 장점
    * 상태 업데이트 로직을 그룹화할 수 있다는 것입니다. Todo 상태를 변경하는 유일한 방법은 작업을 디스패치하는 것뿐입니다.
    * reducer 함수가 컴포넌트 외부에 있기 때문에 컴포넌트를 마운할 필요 없이 단위 테스트를 쉽게 설정할 수 있습니다.
* 기본적으로 `useReducer`는 상태 업데이트 로직에 사용할 수 있는 대체 구조를 제시합니다. 간단한 경우라면 상용구의 가치가 없을 수도 있지만, 좋은 구조로 나아가는 데 도움이 되며 복잡한 상황에서는 큰 도움이 될 수 있습니다.

## 왜 `reducer`라고 하나요?
"reducer"라는 이름은 자바스크립트 배열 메서드인 reduce에서 유래했습니다. 이 메서드를 사용하면 여러 항목의 배열을 하나의 최종 값으로 결합할 수 있습니다.

이 강좌에서는 `reduce` 배열 메서드를 다루지 않는데, 이는 제가 `reduce` 메서드 사용을 권장하지 않기 때문입니다. 예전에는 제가 가장 좋아하는 방법 중 하나였지만, 코드를 불필요하게 이해하기 어렵게 만든다는 것을 깨달았습니다. `forEach`와 같은 유사한 접근 방식을 사용하는 것과 비교했을 때 `reduce`가 코드 가독성을 향상시키는 상황을 아직 찾지 못했습니다.

예를 들어, `reduce`의 가장 일반적인 사용 사례는 숫자 배열을 더하는 것입니다. 대부분의 튜토리얼에서는 이 예제를 통해 `reduce`` 메서드를 소개하는데, 그 이유는 이 예제가 얼마나 유용한지 보여주기 때문입니다.
```js
function sumNumbers(list) {
  return list.reduce((acc, item) => {
    return acc + item;
  }, 0);
}
```
하지만 더 널리 알려진 `forEach` 루프를 사용하여 동일한 문제를 해결할 수 있습니다.
```js
function sumNumbers(list) {
  let sum = 0;
  list.forEach(item => {
    sum += item;
  });

  return sum;
}
```
`reduce` 방법에 대해 자세히 알아보거나 'reducer'라는 이름이 어디에서 유래했는지 알고 싶다면 Dave Ceddia의 블로그 게시물을 확인해 보세요. [What is a Redux Reducer?](https://daveceddia.com/usereducer-hook-examples/)

## reducer는 순수 함수여야 합니다.
reducer의 한 가지 문제점은 순수한 함수여야 한다는 점입니다.

순수 함수는 동일한 입력이 주어지면 항상 동일한 출력을 반환하는 함수입니다. 예를 들어 다음은 순수 함수입니다.
```js
function addNums(a, b) {
  return a + b;
}
```
`addNums(1, 1)`` 코드를 실행하면 매번 2를 반환합니다.

이와는 대조적으로, 여기 불순한 함수가 있습니다.
```js
function getRandomNumber() {
  return Math.random();
}
```
매번 임의의 값을 생성하기 때문에 `getRandomNumber()` 코드를 실행할 때마다 다른 결과가 나옵니다.
```js
getRandomNumber(); // 0.0564345954320411
getRandomNumber(); // 0.8482841158362815
```
useReducer 훅의 경우, reducer 함수는 순수해야 합니다. 동일한 입력이 주어지면 항상 동일한 출력을 반환해야 합니다.

이 실수를 하는 것은 놀라울 정도로 쉽습니다. 사실 저도 이 강의를 위한 동영상을 촬영하면서 이런 실수를 저질렀습니다. 😅

위 동영상에서 저는 이렇게 생긴 reducer 사용했습니다.
```js
function reducer(todos, action) {
  if (action.type === 'create-todo') {
    return [
      ...todos,
      {
        value: action.value,
        id: crypto.randomUUID(),
      },
    ];
  }
  // ✂️ Other actions omitted for brevity
}
```
이 `crypto.randomUUID()` 표현식은 매번 다른 값을 생성합니다. 결과적으로 동일한 입력으로 함수를 호출할 때 다른 출력을 얻게 됩니다.
```js
reducer([], { value: 'buy groceries' });
// -> [{ value: 'buy-groceries', id: 'c455ebae-db9f-42f7-8ec5-c3a0e169938c' }]

reducer([], { value: 'buy groceries' });
// -> [{ value: 'buy-groceries', id: '5f3c38bb-1b32-430e-8282-d489b546ac1b' }]
```
해결책이 있습니다: 액션을 통해 이러한 불순한 값을 reducer에 전달할 수 있습니다.
```js
function reducer(todos, action) {
  if (action.type === 'create-todo') {
    return [
      ...todos,
      {
        value: action.value,
        id: action.id,
      },
    ];
  }
  // ✂️ Other actions omitted for brevity
}


// We would then use it like this:
const action = {
  value: 'buy groceries',
  id: crypto.randomUUID(),
};

reducer([], action);
```
앞서 언급했듯이 실수하기 쉽지만 다행히도 대부분의 경우 해결 방법은 매우 간단합니다.

공식 문서에서 `useReducer` 훅에 대해 자세히 알아볼 수 있습니다.

# Switch / Case
마지막 예제에서 reducer는 여러 개의 if/else 문으로 구성되었습니다.
```js
function reducer(todos, action) {
  if (action.type === 'create-todo') {
    return /* ✂️ */;
  } else if (action.type === 'toggle-todo') {
    return /* ✂️ */;
  } else if (action.type === 'delete-todo') {
    return /* ✂️ */;
  }
}
```
Redux에서는 Switch/Case 구문이라는 더 일반적인 규칙이 있습니다.
위 Reducer Switch/Case를 사용하도록 재작성하면 다음과 같이 보입니다.
```js
function reducer(todos, action) {
  switch (action.type) {
    case 'create-todo': {
      return /* ✂️ */;
    }
    case 'toggle-todo': {
      return /* ✂️ */;
    }
    case 'delete-todo': {
      return /* ✂️ */;
    }
  }
}
```
switch() 괄호 안에 여러 개의 허용 가능한 값을 가질 수 있는 일종의 JS 표현식을 배치합니다. 이 경우 `action.type`의 값에 따라 서로 다른 로직으로 분기하고 싶습니다.

각 경우마다 일치하는 값이 주어집니다. 따라서 `action.type`이 "delete-todo"와 같으면 처음 두 개의 케이스 문은 건너뛰고 세 번째 케이스 문이 실행됩니다.

기능적으로는 위의 if/else 콤보와 완전히 동일합니다. 동일한 결과를 얻기 위한 다른 구문 옵션입니다.

하지만 이는 매우 일반적인 규칙입니다. 제가 본 거의 모든 리듀서에는 `action.type`의 값을 확인하는 Switch/Case가 포함되어 있습니다.

Switch/Case에는 몇 가지 주의해야 할 점이 있습니다.

## Added brackets
엄밀히 말하면, 위에서 보여드린 예시에는 불필요한 문법이 몇 가지 있는데, 각 대소문자 주위에 괄호가 필요하지 않습니다.
```js
switch (status) {
  case 'loading': // <-- No "{"!
    const showSpinner = !action.invisible;

    return {
      loading: true,
      showSpinner,
    };

  case 'ready':
    return {
      loading: false,
      data: action.data,
    }
}
```
중괄호를 추가하면 각 케이스에 대한 범위가 만들어집니다. 즉, 케이스 내에서 생성된 모든 변수는 해당 특정 케이스로 범위가 지정됩니다.

중괄호가 없으면 이와 같은 문제가 발생할 수 있습니다.
```js
switch (status) {
  case 'loading':
    const showSpinner = !action.invisible;

    return {
      loading: true,
      showSpinner,
    };

  case 'ready':
    // 🚫 Uncaught SyntaxError:
    // Identifier 'showSpinner' has already been declared
    const showSpinner = false;

    return {
      loading: false,
      showSpinner,
      data: action.data,
    }
}
```
첫 번째 경우에는 showSpinner 변수를 선언했지만, 스위치 전체가 하나의 큰 범위이기 때문에 다른 경우에 같은 이름의 변수를 만들려고 하면 오류가 발생합니다.

중괄호를 추가하면 이 문제가 해결됩니다.
```js
switch (status) {
  case 'loading': {
    const showSpinner = !action.invisible;

    return {
      loading: true,
      showSpinner,
    };
  }

  case 'ready': {
    // ✅ No problem!
    const showSpinner = false;

    return {
      loading: false,
      showSpinner,
      data: action.data,
    }
  }
}
```
제 멘탈 모델에서 각 사례는 자체적인 지점, 자체적인 미니 환경입니다. 중괄호를 추가함으로써 이 멘탈 모델에 현실을 일치시킵니다. let 또는 const로 생성된 모든 변수는 해당 브랜치로 범위가 지정됩니다.

## Fall-through
Switch/Case에 대해 완전히 당황스러운 점이 하나 있습니다.

다음 스니펫을 살펴보세요. 콘솔에 어떤 내용이 기록되기를 기대하시나요?
```js
const n = 2;

switch (n) {
  case 1: {
    console.log(1);
  }
  case 2: {
    console.log(2);
  }
  case 3: {
    console.log(3);
  }
  case 4: {
    console.log(4);
  }
}
```
n은 2와 같으므로 숫자 2가 기록될 것으로 예상할 수 있겠죠?

실제로 기록되는 내용은 다음과 같습니다.
```js
2
3
4
```
기본적으로 일치하는 케이스를 찾으면 해당 케이스 내의 코드가 모든 후속 케이스와 함께 실행됩니다!

이 동작을 방지하려면 각 대소문자를 특수 키워드 줄 바꿈으로 끝내야 합니다.
```js
switch (n) {
  case 1: {
    console.log(1);
    break;
  }
  case 2: {
    console.log(2);
    break;
  }
  case 3: {
    console.log(3);
    break;
  }
  case 4: {
    console.log(4);
    break;
  }
}
```
사용자와 같이 Switch/Case가 함수 내에 있는 경우, `return` 키워드를 사용하여 전체 함수를 빠져나갈 수 있는 또 다른 옵션이 있습니다.
```js
function reducer(todos, action) {
  switch (action.type) {
    case 'create-todo': {
      return /* ✂️ */;
    }

    case 'toggle-todo': {
      return /* ✂️ */;
    }

    case 'delete-todo': {
      return /* ✂️ */;
    }
  }
}
```

# Exercise
## Art Store, Revisited
```jsx
function reducer(cartItems, action) {
  switch (action.type) {
    case 'add-to-cart': {
      return [...cartItems, action.item];
    }
  }
}

function App() {
  const [cartItems, dispatch] = React.useReducer(reducer, []);

  function addToCart(item) {
    dispatch({
      type: 'add-to-cart',
      item,
    });
  }

  return (
    <>
      <Header actions={<CartButton numOfItems={cartItems.length} />} />

      <Shop paintings={DATA} addToCart={addToCart} />
    </>
  );
}
```
## Counter 2.0, One Last Time
```jsx
function reducer(count, action) {
  switch (action.type) {
    case 'Increase slightly':
      return count + 1;
    case 'Increase a lot':
      return count + 10;
    case 'Reset':
      return action.initialVal;
    case 'Set to random value':
      return action.nextCount;
    case 'Decrease a lot':
      return count - 10;
    case 'Decrease slightly':
      return count - 1;
  }
}

function Counter({ initialVal = 0 }) {
  const [count, dispatch] = React.useReducer(
    reducer,
    initialVal
  );

  return (
    <div className="wrapper">
      <p>
        <span>Current value:</span>
        <span className="count">{count}</span>
      </p>
      <div className="button-row">
        <button
          onClick={() =>
            dispatch({ type: 'Increase slightly' })
          }
        >
          <ChevronUp />
          <span className="visually-hidden">
            Increase slightly
          </span>
        </button>
        <button
          onClick={() =>
            dispatch({ type: 'Increase a lot' })
          }
        >
          <ChevronsUp />
          <span className="visually-hidden">
            Increase a lot
          </span>
        </button>
        <button
          onClick={() =>
            dispatch({ type: 'Reset', initialVal })
          }
        >
          <RotateCcw />
          <span className="visually-hidden">Reset</span>
        </button>
        <button
          onClick={() => {
            const nextCount = clamp(
              Math.ceil(Math.random() * 100),
              1,
              100
            );
            dispatch({
              type: 'Set to random value',
              nextCount,
            });
          }}
        >
          <Hash />
          <span className="visually-hidden">
            Set to random value
          </span>
        </button>
        <button
          onClick={() =>
            dispatch({ type: 'Decrease a lot' })
          }
        >
          <ChevronsDown />
          <span className="visually-hidden">
            Decrease a lot
          </span>
        </button>
        <button
          onClick={() =>
            dispatch({ type: 'Decrease slightly' })
          }
        >
          <ChevronDown />
          <span className="visually-hidden">
            Decrease slightly
          </span>
        </button>
      </div>
    </div>
  );
}
```
## Gradient Generator, Revisited
```jsx
function reducer(state, action) {
  switch (action.type) {
    case 'add-color': {
      return {
        ...state,
        numOfVisibleColors:
          state.numOfVisibleColors + 1,
      };
    }

    case 'remove-color': {
      return {
        ...state,
        numOfVisibleColors:
          state.numOfVisibleColors - 1,
      };
    }

    case 'change-color': {
      const nextColors = [...state.colors];
      nextColors[action.index] = action.value;

      return {
        ...state,
        colors: nextColors,
      };
    }
  }
}
function App() {
  const [state, dispatch] = React.useReducer(
    reducer,
    {
        colors: [
            '#FFD500',
            '#FF0040',
            '#FF0040',
            '#FF0040',
            '#FF0040',
        ],
        numOfVisibleColors: 2,
    }
  );
  const { colors, numOfVisibleColors } = state;

  const visibleColors = colors.slice(
    0,
    numOfVisibleColors
  );

  const colorStops = visibleColors.join(', ');
  const backgroundImage = `linear-gradient(${colorStops})`;

  function addColor() {
    if (numOfVisibleColors >= 5) {
      window.alert(
        'There is a maximum of 5 colors'
      );
      return;
    }

    dispatch({ type: 'add-color' });
  }

  function removeColor() {
    if (numOfVisibleColors <= 2) {
      window.alert(
        'There is a minimum of 2 colors'
      );
      return;
    }

    dispatch({ type: 'remove-color' });
  }

  return (
    <div className="wrapper">
      <div className="actions">
        <button onClick={removeColor}>
          Remove color
        </button>
        <button onClick={addColor}>
          Add color
        </button>
      </div>

      <div
        className="gradient-preview"
        style={{
          backgroundImage,
        }}
      />

      <div className="colors">
        {visibleColors.map((color, index) => {
          const colorId = `color-${index}`;
          return (
            <div
              key={colorId}
              className="color-wrapper"
            >
              <label htmlFor={colorId}>
                Color {index + 1}:
              </label>
              <div className="input-wrapper">
                <input
                  id={colorId}
                  type="color"
                  value={color}
                  onChange={(event) => {
                    dispatch({
                      type: 'change-color',
                      value: event.target.value,
                      index,
                    });
                  }}
                />
              </div>
            </div>
          );
        })}
      </div>
    </div>
  );
}

```