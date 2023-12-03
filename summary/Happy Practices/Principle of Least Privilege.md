# Video Summary
제 솔루션에서는 `handleAddItem` 함수를 만들어서 하위 컴포넌트에 전달했습니다.
```jsx
function App() {
  const [items, setItems] = React.useState([]);

  function handleAddItem(label) {
    const newItem = {
      label,
      id: Math.random(),
    };

    const nextItems = [...items, newItem];
    setItems(nextItems);
  }

  return (
    <div className="wrapper">
      <div className="list-wrapper">
        ...
      </div>
      <AddNewItemForm
        handleAddItem={handleAddItem}
      />
    </div>
  );
}
```
많은 학생들이 왜 이런 식으로 구조화했는지 궁금해합니다. 상태 설정자 함수를 직접 전달하면 더 간단하지 않을까요?
```jsx
function App() {
  const [items, setItems] = React.useState([]);

  return (
    <div className="wrapper">
      <div className="list-wrapper">
        ...
      </div>
      <AddNewItemForm
        setItems={setItems}
      />
    </div>
  );
}

```
이 접근 방식도 효과가 있지만, 제가 이런 식으로 구조화한 데에는 이유가 있습니다. 그것은 제가 React를 사용하면서 배운 가장 중요한 정신 모델 중 하나인 `최소 권한의 원칙`과 관련이 있습니다.

이 용어는 보안 분야에서 유래한 것으로, 조직의 모든 구성원이 가진 액세스/인증의 양을 줄이는 것과 관련이 있습니다.

제가 은행 창구 직원으로 일한다고 가정해 봅시다. 제가 업무를 수행하려면 신규 고객의 계좌를 개설하거나 예금을 처리하는 등의 작업을 수행할 수 있는 권한이 필요합니다. 하지만 모기지를 발행하거나 스톡옵션을 사고파는 일은 할 수 없을 것입니다. 이러한 업무는 제 역할의 범위를 벗어나는 것이므로 컴퓨터 시스템에서 이러한 작업을 수행할 수 있는 권한을 부여해서는 안 됩니다.

이제 앱의 모든 구성 요소가 조직의 구성원이라고 가정해 봅시다.

`AddNewItemForm`` 컴포넌트는 현재 항목 목록의 끝에 새 항목을 푸시할 수 있어야 한다는 한 가지 작업을 수행합니다.

이 컴포넌트에 상태 설정 기능을 부여하면 그보다 훨씬 더 많은 권한을 부여할 수 있습니다. 예를 들어, 현재 항목을 모두 지울 수 있습니다
```jsx
// Erases all previously-saved items:
setItems([]);
```
또는 배열이 아닌 다른 것으로 설정하여 앱을 완전히 망가뜨릴 수도 있습니다/
```jsx
setItems(5);
```
대조적으로, 제 원래 솔루션에서 `AddNewItemForm`` 컴포넌트는 이러한 작업을 수행할 수 없습니다. 새 항목을 배열에 푸시하는 것만 할 수 있습니다.
```jsx
function AddNewItemForm({ handleAddItem }) {
  const [label, setLabel] = React.useState('');

  return (
    <div className="new-list-item-form">
      <form
        onSubmit={(event) => {
          event.preventDefault();

          handleAddItem(label);

          setLabel('')
        }}
      >
        ...
      </form>
    </div>
  );
}
```
이 컴포넌트는 레이블 대신 임의의 숫자를 사용하여 `handleAddItem`을 호출하는 등 여전히 문제를 일으킬 수 있지만, 파워가 훨씬 적습니다. 따라서 많은 문제를 일으키지 않습니다.

이제 이 모든 개념이 조금 이상하게 들릴 수도 있습니다. 컴포넌트는 지각이 없으니까요! 어느 쪽이든, setItems에 무엇을 전달할지 결정하는 것은 개발자입니다. 우리는 악의적으로 숫자를 전달하지 않습니다! 어디에서 setItems를 호출하는지가 중요한 이유는 무엇일까요?

이것은 규모가 클 때만 의미가 있는 것 중 하나입니다. 이 예제에서는 전체 애플리케이션의 코드가 150줄 미만입니다. 하지만 수십만 줄의 코드로 구성된 코드베이스에서 작업하고 있다면 어떨까요? 우리가 프로젝트에 참여한 50명의 개발자 중 한 명이라면?

이와 같은 실제 프로덕션 환경에서는 전혀 익숙하지 않은 코드베이스의 구석에 던져지는 경우가 많습니다. 어떻게 작동하는지 전혀 모르는 상태에서 `AddNewItemForm`` 컴포넌트를 조정해야 할 수도 있습니다!

이런 상황에서는 미묘한 버그가 발생하기 쉽습니다. 따라서 컴포넌트에 권한을 적게 부여할수록 나중에 발생할 수 있는 문제도 줄어듭니다.

# Exercise
## Todo List Application
* 완료 기준
    * CreateNewTodo 구성 요소는 목록에 새 할 일을 추가하는 한 가지 특정 방식으로만 상태를 수정할 수 있어야 합니다.
    * TodoList 구성 요소는 할 일을 완료/미완료로 전환하고 할 일을 삭제하는 두 가지 특정 방법으로만 상태를 수정할 수 있어야 합니다.
```jsx
function App() {
  const [todos, setTodos] = React.useState([]);

  const handleTodoCreate = (value) => {
    const newTodo = {
      value,
      id: crypto.randomUUID(),
    };
    setTodos([...todos, newTodo]);
  };

  const handleTodoUpdate = (id) => {
    setTodos(
      todos.map((todo) => {
        if (todo.id !== id) {
          return todo;
        }

        return {
          ...todo,
          isCompleted: !todo.isCompleted,
        };
      })
    );
  };
  const handleTodoDelete = (id) => {
    setTodos(todos.filter((todo) => todo.id !== id));
  };

  return (
    <div className="wrapper">
      <div className="list-wrapper">
        <TodoList
          todos={todos}
          deleteTodo={handleTodoDelete}
          toggleTodo={handleTodoUpdate}
        />
      </div>
      <CreateNewTodo createTodo={handleTodoCreate} />
    </div>
  );
}

function CreateNewTodo({ createTodo }) {
  const [value, setValue] = React.useState('');
  
  return (
    <div className="create-new-todo-wrapper">
      <form
        onSubmit={(event) => {
          event.preventDefault();

          createTodo(value);
          setValue('');
        }}
      >
        <label htmlFor="new-list-form-input">
          New item:
        </label>

        <div className="row">
          <input
            id="new-list-form-input"
            type="text"
            value={value}
            onChange={event => {
              setValue(event.target.value)
            }}
          />
          <button>Add</button>
        </div>
      </form>
    </div>
  );
}

function TodoList({ todos, deleteTodo, toggleTodo }) {
  return (
    <ol className="todo-list">
      {todos.map(({ id, value, isCompleted }) => (
        <li key={id}>
          <button
            className={`
              toggle
              ${isCompleted ? 'completed' : undefined}
            `}
            onClick={() => {
              toggleTodo(id);
            }}
            aria-label="toggle item"
          >
            {value}
            {isCompleted && (
              <VisuallyHidden> (Completed)</VisuallyHidden>
            )}
          </button>
          <button
            className="delete-btn"
            onClick={() => {
              deleteTodo(id);
            }}
          >
            <X />
            <VisuallyHidden>Delete Item</VisuallyHidden>
          </button>
        </li>
      ))}
    </ol>
  );
}
```

