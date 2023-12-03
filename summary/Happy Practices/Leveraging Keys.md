# Leveraging Keys (키 활용)
* CSS keyframe 애니메이션은 다음과 같은 경우 최대한 빨리 실행됩니다.
    * DOM 노드가 처음 생성될 경우
    * CSS 클래스가 기존 DOM 노드에 추가되는 경우
## Video Summary
정답은 다음과 같습니다.
```jsx
// PriceDisplay.js
function PriceDisplay({ price }) {
  const formattedPrice = new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(price);

  return (
    <div className={styles.wrapper}>
     -<div className={styles.animated}>
     +<div key={price} className={styles.animated}>
       {formattedPrice}
      </div>
    </div>
  );
}
```

애니메이션 클래스를 적용하는 요소에 키를 추가하고 있습니다. 신기하게도 이렇게 하면 필요한 작업이 정확히 수행되고 코드가 완벽하게 작동합니다!
하지만 어떻게 작동할까요?
지금까지 이 강의에서는 다음 같은 시나리오에서 키를 사용하였습니다.
```jsx
function App() {
  return (
    <ul>
      {data.map(contact => (
        <ContactCard
          key={contact.id}
          name={contact.name}
          job={contact.job}
          email={contact.email}
        />
      ))}
    </ul>
  );
}
```
이것이 `키` 속성의 가장 일반적인 사용 사례입니다. 여기에 키를 추가해야 하며, 그렇지 않으면 콘솔 경고가 표시됩니다.
키를 사용하면 React가 특정 ID를 특정 React 엘리먼트 및 관련 DOM과 연결할 수 있습니다. 데이터가 변경되면 React는 키를 사용하여 DOM 노드의 내용을 편집하거나 노드의 순서를 바꾸거나 노드를 제거/재생성하는 등 어떤 DOM 조작이 필요한지 알아냅니다.
요약본에서는 설명하기 어려우니 이 동영상을 시청하시면 더 명확한 설명을 보실 수 있습니다!
`PriceDisplay` 컴포넌트에서 가격과 동일한 `키`를 설정하고 있습니다.
```jsx
<div key={price} className={styles.animated}>
  {formattedPrice}
</div>
```
가격이 0으로 시작한다고 가정해 보겠습니다. 즉, 키가 "0"과 같다는 뜻입니다(키는 문자열이어야 하므로 숫자 0은 문자열로 변환됩니다).

그런 다음 가격이 100으로 변경된다고 가정해 보겠습니다. 키가 "100"으로 업데이트됩니다.

키를 변경함으로써, 우리는 이것이 근본적으로 다른 엘리먼트라는 것을 React에 알리는 것입니다. 기존 DOM을 조정하는 대신 대체합니다. 현재 <div>가 DOM에서 제거되고 완전히 새로운 것이 만들어집니다. 그리고 새로운 노드이기 때문에 애니메이션이 다시 실행됩니다.

처음 이 접근 방식을 배웠을 때 저는 약간 교활하게 느껴졌습니다. 키는 배열을 렌더링하는 데 도움을 주기 위한 것이잖아요? 이런 식으로 키를 사용하면 문서화되지 않은 기능을 활용하고 있는 건 아닌지, 향후 React 버전에서 변경될 수 있는 기능을 활용하고 있는 건 아닌지?

전혀 그렇지 않습니다. 알고 보니 이런 종류의 문제를 해결하기 위해 React 팀에서 권장하는 접근 방식이 바로 이것입니다.

또한 이 방법은 어떤 영리한 트릭이 아니라 키를 원래 의도한 대로 사용하고 있습니다! 키는 우리가 흔히 생각하는 것보다 훨씬 더 일반적인 도구이며, 이것은 100% 유효한 사용 사례입니다.

# Alternative approaches
## Video Summary
이 동영상에서는 다른 접근 방식을 설명합니다. 다음과 같이 수정한다면,
```jsx
function PriceDisplay({ price }) {
  const [applyAnimation, setApplyAnimation] = React.useState(false);

  React.useEffect(() => {
    setApplyAnimation(false);

    window.setTimeout(() => {
      setApplyAnimation(true);
    }, 0);
  }, [price]);

  const formattedPrice = new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(price);

  return (
    <div className={styles.wrapper}>
      <div className={applyAnimation ? styles.animated : undefined}>
        {formattedPrice}
      </div>
    </div>
  );
}
```

여기서 전략은 다음과 같습니다.
* 상태 변수인 `applyAnimation`을 사용하여 `styles.animated`가 <div>에 적용될지 여부를 제어합니다.
* 가격 속성에 종속된 효과를 추가합니다.
* 가격이 변경되면 `applyAnimation`을 `false`로 설정합니다. 한 프레임을 통과시킨 다음 다시 `true`로 설정합니다.

이 전략은 대부분 효과가 있지만, 간혹 UI에서 숫자가 너무 빨리 업데이트되는 경우가 있습니다.
하지만 그 외에는 이 접근 방식에 대해 어떻게 생각하시나요?

정신적인 부담이 상당히 크다는 점에 동의해 주셨으면 합니다. 실제로 무슨 일이 일어나고 있는지 이해하는 데 더 많은 시간을 할애해야 합니다.
(상태/이펙트에 익숙하고 키에 대한 이해도가 높다면 이 방식이 더 복잡하게 느껴지지 않을 수도 있습니다. 하지만 키에 익숙해지면 상황이 달라질 것입니다!)

또 다른 문제는 기존의 모범 사례에서 벗어나고 있다는 점입니다. 사용 효과 훅은 부작용을 처리하도록 설계되었습니다. 실제로는 일부 React 상태를 React 외부의 무언가와 동기화하는 경우가 많습니다. 예를 들면 다음과 같습니다.
* 네트워크를 통해 일부 데이터를 가져와서 React 상태를 데이터베이스의 일부 데이터와 동기화합니다.
* 윈도우 레벨 이벤트 리스너를 추가하여 React 상태를 윈도우의 스크롤 위치와 동기화합니다.

예시에서는 `applyAnimation` 상태 변수를 `plan` 상태 변수와 동기화하고 있습니다. React를 자체적으로 동기화할 때는 보통 가장 관습적인/최적인 방법은 아무것도 하지 않는 것입니다.

따라서 이러한 문제에 대해서는 핵심 접근 방식이 더 낫다고 생각합니다.

하지만 성능은 어떨까요? 키 접근 방식이 더 느려야 한다고 생각할 수도 있습니다. 결국 클래스명을 변경하는 대신 전체 DOM 노드를 파괴하고 다시 생성하는 것이니까요! 물론 하나의 속성를 조정하는 것이 완전히 새로운 DOM 노드를 생성하는 것보다 빠를 수는 있습니다!

여기에는 두 가지가 있습니다.
* 이 경우 두 옵션 모두 매우 빠르기 때문에 '즉각적'으로 반올림됩니다. 사용자는 두 접근 방식 간의 성능 차이를 인지할 수 없습니다. 따라서 실제로는 중요하지 않습니다.
* 이 대안적인 접근 방식에서는 DOM 조작을 덜 할 수 있지만, React 측에서 얼마나 더 많은 작업을 하고 있는지 생각해 보세요. `PriceDisplay`` 컴포넌트는 요금제가 변경될 때마다 세 번 렌더링됩니다. 핵심 접근 방식이 실제로 더 빠를 수도 있습니다!

# You Might Not Need an Effect
## props 또는 상태에 따라 상태 업데이트하기
```jsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');

  // 🔴 Avoid: redundant state and unnecessary Effect
  const [fullName, setFullName] = useState('');
  useEffect(() => {
    setFullName(firstName + ' ' + lastName);
  }, [firstName, lastName]);
  // ...
}
```
```jsx
function Form() {
  const [firstName, setFirstName] = useState('Taylor');
  const [lastName, setLastName] = useState('Swift');
  // ✅ Good: calculated during rendering
  const fullName = firstName + ' ' + lastName;
  // ...
}
```

## 비용이 많이 드는 계산 캐싱
```jsx
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');

  // 🔴 Avoid: redundant state and unnecessary Effect
  const [visibleTodos, setVisibleTodos] = useState([]);
  useEffect(() => {
    setVisibleTodos(getFilteredTodos(todos, filter));
  }, [todos, filter]);

  // ...
}
```
```jsx
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  // ✅ This is fine if getFilteredTodos() is not slow.
  const visibleTodos = getFilteredTodos(todos, filter);
  // ...
}

// 계산에 시간이 오래 걸린다면 캐시처리 한다.
function TodoList({ todos, filter }) {
  const [newTodo, setNewTodo] = useState('');
  const visibleTodos = useMemo(() => {
    // ✅ Does not re-run unless todos or filter change
    return getFilteredTodos(todos, filter);
  }, [todos, filter]);
  // ...
}
```
## How to tell if a calculation is expensive?
* 계산에 걸리는 시간값을 확인합니다.
```jsx
console.time('filter array');
const visibleTodos = getFilteredTodos(todos, filter);
console.timeEnd('filter array');
```
* 전체적으로 기록된 시간이 1밀리초 이상으로 상당히 길다면 해당 계산을 메모해 두는 것이 좋습니다.
* 그런 다음 `useMemo` 사용으로 총 기록 시간이 감소했는지 여부를 확인합니다.
* `useMemo`를 사용한다고 해서 첫 번째 렌더링이 더 빨리지지는 않고 불필요한 업데이트 작업을 건너뛰는데 도움이 될 뿐입니다.
* 가장 정확한 타이밍을 얻으려면 프로덕션용 앱을 빌드하고 사용자가 사용하는 것과 같은 기기에서 테스트하세요.
(엄격 모드가 켜져 있으면 각 컴포넌트가 한번이 아닌 두번 렌더링 되는 것을 볼 수 있습니다.)

## props 변경 시 모든 상태 초기화
```jsx
export default function ProfilePage({ userId }) {
  const [comment, setComment] = useState('');

  // 🔴 Avoid: Resetting state on prop change in an Effect
  useEffect(() => {
    setComment('');
  }, [userId]);
  // ...
}
```
```jsx
export default function ProfilePage({ userId }) {
  return (
    <Profile
      userId={userId}
      key={userId}
    />
  );
}

function Profile({ userId }) {
  // ✅ This and any other state below will reset on key change automatically
  const [comment, setComment] = useState('');
  // ...
}
```
```jsx
function List({ items }) {
  const [isReverse, setIsReverse] = useState(false);
  const [selectedId, setSelectedId] = useState(null);
  // ✅ Best: Calculate everything during rendering
  const selection = items.find(item => item.id === selectedId) ?? null;
  // ...
}
```

## 이벤트 핸들러 간 로직 공유
```jsx
function ProductPage({ product, addToCart }) {
  // 🔴 Avoid: Event-specific logic inside an Effect
  useEffect(() => {
    if (product.isInCart) {
      showNotification(`Added ${product.name} to the shopping cart!`);
    }
  }, [product]);

  function handleBuyClick() {
    addToCart(product);
  }

  function handleCheckoutClick() {
    addToCart(product);
    navigateTo('/checkout');
  }
  // ...
}
```
```jsx
function ProductPage({ product, addToCart }) {
  // ✅ Good: Event-specific logic is called from event handlers
  function buyProduct() {
    addToCart(product);
    showNotification(`Added ${product.name} to the shopping cart!`);
  }

  function handleBuyClick() {
    buyProduct();
  }

  function handleCheckoutClick() {
    buyProduct();
    navigateTo('/checkout');
  }
  // ...
}
```

## POST 요청 보내기 
```jsx
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Good: This logic should run because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  // 🔴 Avoid: Event-specific logic inside an Effect
  const [jsonToSubmit, setJsonToSubmit] = useState(null);
  useEffect(() => {
    if (jsonToSubmit !== null) {
      post('/api/register', jsonToSubmit);
    }
  }, [jsonToSubmit]);

  function handleSubmit(e) {
    e.preventDefault();
    setJsonToSubmit({ firstName, lastName });
  }
  // ...
}
```
```jsx
function Form() {
  const [firstName, setFirstName] = useState('');
  const [lastName, setLastName] = useState('');

  // ✅ Good: This logic runs because the component was displayed
  useEffect(() => {
    post('/analytics/event', { eventName: 'visit_form' });
  }, []);

  function handleSubmit(e) {
    e.preventDefault();
    // ✅ Good: Event-specific logic is in the event handler
    post('/api/register', { firstName, lastName });
  }
  // ...
}
```

## 계산의 연쇄
```jsx
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);
  const [isGameOver, setIsGameOver] = useState(false);

  // 🔴 Avoid: Chains of Effects that adjust the state solely to trigger each other
  useEffect(() => {
    if (card !== null && card.gold) {
      setGoldCardCount(c => c + 1);
    }
  }, [card]);

  useEffect(() => {
    if (goldCardCount > 3) {
      setRound(r => r + 1)
      setGoldCardCount(0);
    }
  }, [goldCardCount]);

  useEffect(() => {
    if (round > 5) {
      setIsGameOver(true);
    }
  }, [round]);

  useEffect(() => {
    alert('Good game!');
  }, [isGameOver]);

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    } else {
      setCard(nextCard);
    }
  }

  // ...
```
```jsx
function Game() {
  const [card, setCard] = useState(null);
  const [goldCardCount, setGoldCardCount] = useState(0);
  const [round, setRound] = useState(1);

  // ✅ Calculate what you can during rendering
  const isGameOver = round > 5;

  function handlePlaceCard(nextCard) {
    if (isGameOver) {
      throw Error('Game already ended.');
    }

    // ✅ Calculate all the next state in the event handler
    setCard(nextCard);
    if (nextCard.gold) {
      if (goldCardCount <= 3) {
        setGoldCardCount(goldCardCount + 1);
      } else {
        setGoldCardCount(0);
        setRound(round + 1);
        if (round === 5) {
          alert('Good game!');
        }
      }
    }
  }

  // ...
```

## 애플리케이션 초기화하기
```jsx
function App() {
  // 🔴 Avoid: Effects with logic that should only ever run once
  useEffect(() => {
    loadDataFromLocalStorage();
    checkAuthToken();
  }, []);
  // ...
}
```
```jsx
let didInit = false;

function App() {
  useEffect(() => {
    if (!didInit) {
      didInit = true;
      // ✅ Only runs once per app load
      loadDataFromLocalStorage();
      checkAuthToken();
    }
  }, []);
  // ...
}
```
```jsx
if (typeof window !== 'undefined') { // Check if we're running in the browser.
   // ✅ Only runs once per app load
  checkAuthToken();
  loadDataFromLocalStorage();
}

function App() {
  // ...
}
```

## 상태 변경에 대한 상위 컴포넌트 알림
```jsx
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  // 🔴 Avoid: The onChange handler runs too late
  useEffect(() => {
    onChange(isOn);
  }, [isOn, onChange])

  function handleClick() {
    setIsOn(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      setIsOn(true);
    } else {
      setIsOn(false);
    }
  }

  // ...
}
```
```jsx
function Toggle({ onChange }) {
  const [isOn, setIsOn] = useState(false);

  function updateToggle(nextIsOn) {
    // ✅ Good: Perform all updates during the event that caused them
    setIsOn(nextIsOn);
    onChange(nextIsOn);
  }

  function handleClick() {
    updateToggle(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      updateToggle(true);
    } else {
      updateToggle(false);
    }
  }

  // ...
}
```
```jsx
// ✅ Also good: the component is fully controlled by its parent
function Toggle({ isOn, onChange }) {
  function handleClick() {
    onChange(!isOn);
  }

  function handleDragEnd(e) {
    if (isCloserToRightEdge(e)) {
      onChange(true);
    } else {
      onChange(false);
    }
  }

  // ...
}
```

## 부모에게 데이터 전달
```jsx
function Parent() {
  const [data, setData] = useState(null);
  // ...
  return <Child onFetched={setData} />;
}

function Child({ onFetched }) {
  const data = useSomeAPI();
  // 🔴 Avoid: Passing data to the parent in an Effect
  useEffect(() => {
    if (data) {
      onFetched(data);
    }
  }, [onFetched, data]);
  // ...
}
```
```jsx
function Parent() {
  const data = useSomeAPI();
  // ...
  // ✅ Good: Passing data down to the child
  return <Child data={data} />;
}

function Child({ data }) {
  // ...
}
```
## 외부 스토어 구독
```jsx
function useOnlineStatus() {
  // Not ideal: Manual store subscription in an Effect
  const [isOnline, setIsOnline] = useState(true);
  useEffect(() => {
    function updateState() {
      setIsOnline(navigator.onLine);
    }

    updateState();

    window.addEventListener('online', updateState);
    window.addEventListener('offline', updateState);
    return () => {
      window.removeEventListener('online', updateState);
      window.removeEventListener('offline', updateState);
    };
  }, []);
  return isOnline;
}

function ChatIndicator() {
  const isOnline = useOnlineStatus();
  // ...
}
```
```jsx
function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

function useOnlineStatus() {
  // ✅ Good: Subscribing to an external store with a built-in Hook
  return useSyncExternalStore(
    subscribe, // React won't resubscribe for as long as you pass the same function
    () => navigator.onLine, // How to get the value on the client
    () => true // How to get the value on the server
  );
}

function ChatIndicator() {
  const isOnline = useOnlineStatus();
  // ...
}
```

## 데이터 가져오기
```jsx
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1);

  useEffect(() => {
    // 🔴 Avoid: Fetching without cleanup logic
    fetchResults(query, page).then(json => {
      setResults(json);
    });
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```
```jsx
function SearchResults({ query }) {
  const [results, setResults] = useState([]);
  const [page, setPage] = useState(1);
  useEffect(() => {
    let ignore = false;
    fetchResults(query, page).then(json => {
      if (!ignore) {
        setResults(json);
      }
    });
    return () => {
      ignore = true;
    };
  }, [query, page]);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}
```
```jsx
function SearchResults({ query }) {
  const [page, setPage] = useState(1);
  const params = new URLSearchParams({ query, page });
  const results = useData(`/api/search?${params}`);

  function handleNextPageClick() {
    setPage(page + 1);
  }
  // ...
}

function useData(url) {
  const [data, setData] = useState(null);
  useEffect(() => {
    let ignore = false;
    fetch(url)
      .then(response => response.json())
      .then(json => {
        if (!ignore) {
          setData(json);
        }
      });
    return () => {
      ignore = true;
    };
  }, [url]);
  return data;
}
```

## 정리
* 렌더링 중에 무언가를 계산할 수 있다면 `Effect`가 필요하지 않습니다.
* 비용이 많이 드는 계산을 캐시하려면 `useEffect` 대신 `useMemo`를 추가하세요.
* 전체 컴포넌트 트리의 상태를 재설정하려면 다른 키를 전달하세요.
* 속성 변경에 대한 응답으로 특정 상태 비트를 재설정하려면 렌더링 중에 설정하세요.
* 컴포넌트가 표시되어 실행되는 코드는 `Effect`에 있어야 하고, 나머지는 이벤트에 있어야 합니다.
* 여러 컴포넌트의 상태를 업데이트해야 하는 경우 단일 이벤트 중에 수행하는 것이 좋습니다.
* 여러 컴포넌트의 상태 변수를 동기화하려고 할 때마다 상태를 위로 올리는 것을 고려하세요.
* `Effect`로 데이터를 가져올 수 있지만 경쟁 조건을 피하기 위해 `Effect cleanup`를 구현해야 합니다.

# Exercise
## Pricing Woes
```jsx
// PriceDisplay.js
import React from 'react';
import styles from './PriceDisplay.module.css';

function PriceDisplay({ id, price }) {
  const formattedPrice = new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(price);

  return (
    <div className={styles.wrapper}>
      <div key={id} className={styles.animated}>
        {formattedPrice}
      </div>
    </div>
  );
}

export default PriceDisplay;
```
## Toonie Clicker, revisited
```jsx
function App() {
  const [numOfCoins, setNumOfCoins] = React.useState(0);

  return (
    <div className={styles.wrapper}>
      <main>
        <BigCoin
          numOfCoins={numOfCoins}
          setNumOfCoins={setNumOfCoins}
        />
        {numOfCoins > 0 && (
          <div className={styles.floatingNumWrapper}>
            <FloatingText key={numOfCoins}>+2</FloatingText>
          </div>
        )}
      </main>
      <footer>
        Your coin balance:
        <strong>{numOfCoins}</strong>
      </footer>
    </div>
  );
}

export default App;
```
## Stretch goal: Only showing on increment
```jsx
function App() {
  const [numOfCoins, setNumOfCoins] = React.useState(0);
  const [numOfPiggyBanks, setNumOfPiggyBanks] = React.useState(0);

  const [floatingTextKey, setFloatingTextKey] = React.useState('initial');

  function clickCoin() {
    setNumOfCoins(numOfCoins + 2);
    setFloatingTextKey(crypto.randomUUID());
  }

  function buyPiggyBank() {
    setNumOfCoins(numOfCoins - PIGGY_BANK_COST);
    setNumOfPiggyBanks(numOfPiggyBanks + 1);
  }

  return (
    <div className={styles.wrapper}>
      <main>
        <BigCoin handleClickCoin={clickCoin} />

        {floatingTextKey !== 'initial' && (
          <div className={styles.floatingNumWrapper}>
            <FloatingText key={floatingTextKey}>+2</FloatingText>
          </div>
        )}

        <button
          disabled={numOfCoins < PIGGY_BANK_COST}
          className={styles.shopItem}
          onClick={buyPiggyBank}
        >
          Buy Piggy Bank
          {numOfPiggyBanks > 0 && `(${numOfPiggyBanks})`}
        </button>
      </main>
      <footer>
        Your coin balance:
        <strong>{numOfCoins}</strong>
      </footer>
    </div>
  );
}

export default App;
```
```jsx
function App() {
  const [numOfCoins, setNumOfCoins] = React.useState(0);
  const [numOfPiggyBanks, setNumOfPiggyBanks] = React.useState(0);

  // We can calculate how many coins the user has earned by
  // adding the value of all owned piggy banks to their current
  // coin balance:
  const totalCoinsEarned =
    numOfCoins + numOfPiggyBanks * PIGGY_BANK_COST;

  function buyPiggyBank() {
    setNumOfCoins(numOfCoins - PIGGY_BANK_COST);
    setNumOfPiggyBanks(numOfPiggyBanks + 1);
  }

  return (
    <div className={styles.wrapper}>
      <main>
        <BigCoin
          numOfCoins={numOfCoins}
          setNumOfCoins={setNumOfCoins}
        />

        {totalCoinsEarned > 0 && (
          <div className={styles.floatingNumWrapper}>
            {/*
              Use this derived value as the key, since
              it won't change when the user buys a
              piggy bank!
            */}
            <FloatingText key={totalCoinsEarned}>
              +2
            </FloatingText>
          </div>
        )}

        <button
          disabled={numOfCoins < PIGGY_BANK_COST}
          className={styles.shopItem}
          onClick={buyPiggyBank}
        >
          Buy Piggy Bank
          {numOfPiggyBanks > 0 && (
            `(${numOfPiggyBanks})`
          )}
        </button>
      </main>
      <footer>
        Your coin balance:
        <strong>{numOfCoins}</strong>
      </footer>
    </div>
  );
}

export default App;
```