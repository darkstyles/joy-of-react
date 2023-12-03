# Video Summary
Wordle 프로젝트에서 주요 상태는 추측 배열입니다. 여기에는 문자열 배열이 들어 있습니다.
```jsx
const [guesses, setGuesses] = React.useState([]);
// ['HELLO', 'WORLD']
```
하지만 UI를 렌더링하기 위해 필요한 데이터가 이것만이 전부는 아닙니다. 또한 추측에 포함된 모든 글자의 상태(특정 글자가 틀렸는지, 잘못 배치되었는지, 올바른지 여부)도 알아야 합니다.

저는 `Guess` 컴포넌트에서 이러한 상태를 계산합니다.
```jsx
function Guess({ value, answer }) {
  const result = checkGuess(value, answer);

  return (
    <p className="guess">
      {range(5).map((num) => (
        <Cell
          key={num}
          letter={result ? result[num].letter : undefined}
          status={result ? result[num].status : undefined}
        />
      ))}
    </p>
  );
}
```
checkGuess 함수는 "HELLO"와 같은 문자열을 객체 배열로 변환합니다.
```js
console.log(result);
/*
  [
    { letter: 'H', status: 'incorrect' },
    { letter: 'E', status: 'incorrect' },
    { letter: 'L', status: 'incorrect' },
    { letter: 'L', status: 'incorrect' },
    { letter: 'O', status: 'misplaced' },
  ]
*/
```
많은 학생들이 '이 계산을 제출할 때 하는 것이 더 합리적이지 않을까'라고 궁금해했습니다.

매번 렌더링할 때마다 결과를 다시 계산해야 하는 것은 낭비라고 생각하기 때문입니다. 사용자가 새로운 추측을 제출할 때마다 이전의 모든 추측에 대해 이 작업을 반복합니다!

만약 제출 시 이 작업을 수행하고 이 배열을 상태에 직접 저장하면 어떨까요?
```jsx
function handleSubmitGuess(tentativeGuess) {
  // Instead of storing the guess itself,
  // store the calculated result:
  const result = checkGuess(tentativeGuess, answer);

  const nextGuesses = [...guesses, result];
  setGuesses(nextGuesses);
}
```
이 접근 방식은 잘 작동하지만 두 가지를 자세히 살펴보고 싶습니다.
1. 성능 문제가 유효한지 여부
2. 어떤 접근 방식이 더 유지 관리하기 쉬운가

성능에 대해서는 모듈 2에서 설명한 대로 벤치마크를 설정했습니다. 결과는 다음과 같습니다:
```js
const start = Date.now()
const numberOfIterations = 100_000;

let result;
for (let i = 0; i < numberOfIterations; i++) {
  result = checkGuess(tentativeGuess, answer);
}

const totalTimeTaken = Date.now() - start;
console.log(totalTimeTaken / numberOfIterations);
```
결과에서 해당 객체 배열을 생성하는 데 걸린 시간(밀리초)을 알려줍니다. 한 번의 계산에 대한 현실적인 추정치를 얻기 위해 계산을 여러 번(10만 번) 반복합니다(그렇지 않으면 항상 0으로 반올림됨).

결과: 제 컴퓨터에서는 계산에 0.5마이크로초가 걸렸습니다.

참고로 100밀리초는 일반적으로 눈에 띄는 시간이 경과해야 하는 임계값으로 인정되는 시간입니다. 사용자가 추측을 제출하고 결과가 50밀리초, 75밀리초, 90밀리초 안에 표시되면 거의 즉각적으로 느껴지지만, 110밀리초가 걸리면 매우 짧은 지연 시간으로 느껴질 것입니다.

따라서 계산을 해보면, 사용자가 눈에 띄게 되려면 체크 추측 기능이 200,000배 더 느려져야 합니다.

물론 제 컴퓨터가 저가의 안드로이드 스마트폰보다 더 강력하긴 하지만 20만 배나 빠르지는 않습니다. 저는 이 'guess 계산' 프로세스가 성능에 눈에 띄게 영향을 미치는 상황은 없을 것이라고 확신합니다.

하지만 그것이 성능에 영향을 미친다면 어떨까요? 이 경우 모듈 3에서 설명한 메모 기술을 사용할 수 있습니다!

예를 들어 컴포넌트를 메모할 수 있습니다.
```jsx
const Guess = React.memo(
  function Guess({ value, answer }) {
    const result = checkGuess(value, answer);

    return (
      <p className="guess">
        {range(5).map((num) => (
          <Cell
            key={num}
            letter={result ? result[num].letter : undefined}
            status={result ? result[num].status : undefined}
          />
        ))}
      </p>
    );
  }
);
```
`Guess`를 순수 컴포넌트로 전환하면 `value`이나 `answer` props이 변경될 때만 다시 렌더링되도록 보장할 수 있습니다. 즉, `checkGuess` 계산이 필요할 때만 수행된다는 뜻입니다.

자, 이제 이런 경우 성능에 대해 걱정할 필요가 없다는 것을 납득하셨기를 바랍니다. 유지보수성 측면에서 가장 좋은 접근 방식이 무엇인지에 집중하면 됩니다. 어떤 접근 방식이 가장 작업하기 쉬울까요?

이 코드베이스를 처음 사용한다고 가정해 봅시다. 어떤 상태 모양이 더 이해하기 쉬울까요?
```js
// Option 1
['HELLO', 'WORLD', 'THIRD'];

// Option 2
[
  [
    { letter: 'H', status: 'incorrect' },
    { letter: 'E', status: 'incorrect' },
    { letter: 'L', status: 'incorrect' },
    { letter: 'L', status: 'incorrect' },
    { letter: 'O', status: 'misplaced' },
  ],
  [
    { letter: 'W', status: 'correct' },
    { letter: 'O', status: 'correct' },
    { letter: 'R', status: 'correct' },
    { letter: 'L', status: 'incorrect' },
    { letter: 'D', status: 'incorrect' },
  ],
  [
    { letter: 'T', status: 'incorrect' },
    { letter: 'H', status: 'incorrect' },
    { letter: 'I', status: 'incorrect' },
    { letter: 'R', status: 'misplaced' },
    { letter: 'D', status: 'incorrect' },
  ],
]
```
최소한의 데이터를 상태에 저장함으로써 상태를 개념화하기 쉽게 유지합니다. 상태는 데이터의 '순수한' 표현이라는 점에서 매우 우아한 면이 있습니다.

시간이 지남에 따라 앱은 점점 더 많은 기능을 추가하면서 점점 더 복잡해지는 경향이 있습니다. `Game` 컴포넌트에 10개의 상태 변수가 있을 때 추론하기가 더 쉽다면 더 좋지 않을까요?

이는 상태 업데이트에도 적용됩니다.

우리가 정규직으로 워들 작업을 하고 있다고 가정해 봅시다. 관리자가 저희 칸막이로 들어와서 "문제가 생겼습니다. 사용자들이 답답해하고 있습니다. 사용자가 이전 추측을 수정할 수 있게 해 주세요."라고 말합니다.

이 문제를 어떻게 해결해야 할까요? 상태가 문자열 배열이라면 다음과 같이 할 수 있습니다.
```jsx
function updateGuess(guessIndex, newValue) {
  const nextGuesses = [...guesses];
  nextGuesses[guessIndex] = newValue;

  setGuesses(nextGuesses);
}
```
이 함수는 다음과 같이 상태를 변경할 것으로 예상됩니다.
```jsx
console.log(guesses);
// ['HELLO', 'WORLD'];

updateGuesses(1, 'WORKS');
// Next state: ['HELLO', 'WORKS'];
```
상태는 문자열의 배열이라는 비교적 단순한 형태이므로 매우 간단한 변환이 가능합니다.

하지만 상태가 훨씬 더 복잡한 형태인 다른 접근 방식에서는 업데이트가 훨씬 더 어려워집니다. 복잡한 데이터 조작(배열 내의 배열 내의 객체를 배열 내에서 편집)을 수행하거나 해당 데이터를 생성하는 작업(checkGuess 함수)을 복제해야 하기 때문입니다.

파생된 데이터를 상태에 저장하면 상태를 변경할 때마다 해당 계산을 반복해야 한다는 뜻입니다. 이는 상당한 부담이 될 수 있습니다.

하지만 state에 최소한의 표현을 저장하면 상태를 훨씬 더 쉽게 변경할 수 있습니다. 상태가 변경되면 컴포넌트가 다시 렌더링되고 데이터를 도출하기 위한 모든 계산이 다시 실행되어 새 값이 자동으로 생성됩니다.

예외적인 경우도 있지만(곧 설명하겠습니다), 일반적으로 파생된 데이터를 state에 저장하지 않으면 작업이 훨씬 쉬워집니다.

# Exercise
## Compose Tweet
* 완료 기준
    * 남은 문자 수는 상태 변수에 저장되지 않고 메시지 상태에서 파생되어야 합니다.
    * 어떤 효과도 사용해서는 안 됩니다.
        * 보너스 질문입니다: 효과가 이 문제에 적합한 도구가 아닌 이유를 생각해 볼 수 있나요?
            * `messages`의 초기값에 텍스트가 있을 때 남은 문자 수에 대한 계산이 되지 않으므로 effect를 통하여 `messages`변경에 대한 남은 문자 수를 계산한다. 그렇게 되면 초기값으로 화면이 그려지고 남은 문자 수가 변경이 되면서 다시 한번 더 화면을 그리게 된다.
```jsx
function ComposeTweet({ maxChars, handleSubmit }) {
  const [message, setMessage] = React.useState('');
  // const [charsRemaining, setCharsRemaining] =
    // React.useState(maxChars);

  const id = React.useId();
  const charsRemaining = maxChars - message.length

  // React.useEffect(() => {
  //   setCharsRemaining(maxChars - message.length);
  // }, [maxChars, message]);

  return (
    <form
      className={styles.wrapper}
      onSubmit={(event) => {
        event.preventDefault();
        handleSubmit(message);
        setMessage('');
      }}
    >
      <div className={styles.header}>
        <label htmlFor={id}>Compose Tweet:</label>
        <span
          className={styles.count}
          style={{
            color: getCharacterColor(
              charsRemaining
            ),
          }}
        >
          <VisuallyHidden>
            Characters remaining:{' '}
          </VisuallyHidden>
          {charsRemaining}
        </span>
      </div>

      <textarea
        id={id}
        className={styles.textarea}
        placeholder="What's happening?"
        required={true}
        value={message}
        onChange={(event) => {
          setMessage(event.target.value);
        }}
      />

      <div className={styles.footer}>
        <button className={styles.tweetBtn}>
          Tweet
        </button>
      </div>
    </form>
  );
}
```

## Checkout flow
* 완료 기준
    * 카트에서 품목을 제거하면 페이지 하단의 숫자(Subtotal / Taxes / Total)가 다시 계산됩니다.
    * 필요하지 않은 상태 변수는 모두 제거해야 합니다. 가능하면 상태를 파생하세요.

```jsx
function CheckoutFlow({
  items,
  taxRate,
  handleDeleteItem,
}) {
  const subtotal = calculateSubtotal(items);
  const taxes = subtotal * taxRate;
  const total = subtotal + taxes;

  return (
    <>
      <h1>Checkout</h1>

      <CartTable
        items={items}
        handleDeleteItem={handleDeleteItem}
      />

      <table className="checkout-totals">
        <tbody>
          <tr>
            <th scope="col">Subtotal</th>
            <td>${roundTo(subtotal, 2)}</td>
          </tr>
          <tr>
            <th scope="col">Taxes</th>
            <td>${roundTo(taxes, 2)}</td>
          </tr>
          <tr>
            <th scope="col">Total</th>
            <td>${roundTo(total, 2)}</td>
          </tr>
        </tbody>
      </table>
    </>
  );
}
```
## Thermostat
* 완료 기준
    * 온도 조절기의 현재 온도를 추적하려면 하나의 상태 변수를 사용해야 합니다.
    * up 및 down 버튼은 현재 온도를 1도씩 높이거나 낮춰야 합니다.
        * 참고: 현재 모드가 무엇이든 상관없습니다. "섭씨" 모드이든 "화씨" 모드이든, 이 버튼은 표시된 온도를 1도씩 증가/감소시켜야 합니다(예: 77°F에서 78°F로, 25°C에서 26°C로).
    * 헬퍼 함수 convertToCelsius 및 convertToFahrenheit를 사용하여 단위 간 변환을 할 수 있습니다.
```jsx
function Thermostat() {
  const [temperature, setTemperature] = React.useState(25);

  const [mode, setMode] = React.useState('celsius');

  function toggleMode() {
    const nextMode =
      mode === 'celsius'
        ? 'fahrenheit'
        : 'celsius';
    setMode(nextMode);

    const nextTemperature = Math.round(
      nextMode === 'celsius'
        ? convertToCelsius(temperature)
        : convertToFahrenheit(temperature)
    );

    setTemperature(nextTemperature);
  }

  function incrementTemperature() {
    setTemperature(temperature + 1);
  }
  function decrementTemperature() {
    setTemperature(temperature - 1);
  }

  return (
    <div className={styles.wrapper}>
      <div className={styles.logo}>Sugarfine</div>
      <div className={styles.digitalScreen}>
        {temperature}°
      </div>
      <div className={styles.controls}>
        <ToggleButton
          label="Mode"
          options={[
            {
              label: 'C',
              value: 'celsius',
            },
            {
              label: 'F',
              value: 'fahrenheit',
            },
          ]}
          selectedValue={mode}
          onChange={toggleMode}
        />
        <div className={styles.tempAdjustButtons}>
          <button
            className={styles.iconButton}
            onClick={decrementTemperature}
          >
            <ChevronDown size={32} />
            <VisuallyHidden>
              Decrease temperature
            </VisuallyHidden>
          </button>
          <button
            className={styles.iconButton}
            onClick={incrementTemperature}
          >
            <ChevronUp size={32} />
            <VisuallyHidden>
              Increase temperature
            </VisuallyHidden>
          </button>
        </div>
      </div>
    </div>
  );
}

export default Thermostat;
```