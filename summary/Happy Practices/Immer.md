# Immer

```jsx
function App() {
  const [numbers, setNumbers] = React.useState([0, 1, 2]);
  
  function handleClick() {
    const nextState = produce(numbers, (draftState) => {
      const nextNumber = numbers.length;
      draftState.push(nextNumber);
    });
    
    setNumbers(nextState);
  }
  
  return (
    <>
      <h2>Data contents:</h2>
      <div className="items">
        {JSON.stringify(numbers)}
      </div>
      
      <div className="actions">
        <button onClick={handleClick}>
          Add next number
        </button>
      </div>
    </>
  );
}
```
* `produce` 호출 시 동작은 아래와 같다.
```js
const state = {
  customer: {
    name: 'Daria Hakimi',
  },
  toppings: {
    pepperoni: true,
    anchovies: true,
    kale: true,
  },
};

const nextState = produce(state, (draftState) => {
  draftState.toppings.pepperoni = false;
});

const newState = {
  ...state,
  toppings: {
    ...state.toppings,
    pepperoni: false,
  },
};
```

# useImmerReducer
```jsx
import React from 'react';
import { useImmerReducer } from 'use-immer';

const initialState = { count: 0 };

function reducer(draftState, action) {
  switch (action.type) {
    case 'increment': {
      draftState.count++;
      return;
    }

    case 'decrement': {
      draftState.count--;
      return;
    }

    case 'reset': {
      return initialState;
    }
  }
}

function Counter() {
  const [state, dispatch] = useImmerReducer(reducer, initialState);

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>
        Increment
      </button>
      <button onClick={() => dispatch({ type: 'decrement' })}>
        Decrement
      </button>
      <button onClick={() => dispatch({ type: 'reset' })}>
        Reset
      </button>
    </>
  );
}
```

# useImmer
```jsx
import React from 'react';
import { useImmer } from 'use-immer';

function App() {
  const [person, updatePerson] = useImmer({
    name: 'Michel',
    age: 33
  });

  function becomeOlder() {
    updatePerson((draft) => {
      draft.age++;
    });
  }

  return (
    <div className="App">
      <h1>
        Hello {person.name} ({person.age})
      </h1>

      <button onClick={becomeOlder}>Older</button>
    </div>
  );
}
```