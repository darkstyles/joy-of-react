# Components
## 재사용 매커니즘
* HTML에서 기본적으로 재사용 매커니즘은 없으며, 여러 곳에서 부분 요소를 다른 HTML 문서에 삽입한다.
* CSS에서 재사용 매커니즘은 `class`이다.
* JavaScript에서 재사용 매커니즘은 `function`이다.
* React에서 재사용 매커니즘은 `component` 이다.
    * HTML을 위한 부분, CSS를 위한 클래스, 자바스크립트를 위한 함수가 아니라 이 세 가지를 모두를 묶은 컴포넌트를 생성하여 재사용 가능한 높은 수준의 UI 요소 라이브러리를 만들 수 있다.
    * 모던 리액트에서는 `hooks`이 있다.

# Basic Syntax
* React 컴포넌트는 하나 이상의 React 엘리먼트를 반환한다.
* React 컴포넌트는 대문자로 시작해야 한다. 이렇게 하면 JSX 컴파일러가 사용자 정의 React 컴포넌트를 렌더링하려는 것인지 알 수 있다.

# Props
* 전달되는 속성의 기본값을 설정하기 위한 두가지 방법이 있다.
    * `||` 연산자를 이용하여 전달된 속성의 값이 `falsy`인 경우 지정한 기본값을 할당한다.
    * 함수의 파라미터의 기본값을 설정하여 전달된 속성 값이 없을 경우 지정한 기본값을 할당한다.

# The Children Prop
* 열기 태그와 닫기 태그 사이에 무언가를 전달하면 React는 자동으로 해당 값을 `children` 속성에 할당하여 제공한다.
* `children`은 속성으로 전달될 때 예약어이다.
```js
// This element:
<div children="Hello world!" />

// …is equivalent to this one:
<div>
  Hello world!
</div>
```
* 열기 태그와 닫기 태그 사이에 넣어 값을 전달함과 동시에 `children`속성으로 값을 전달하게 되면 어떻게 될까?
    * 열기 태그와 닫기 태그 사이에 넣은 값이 `children`속성의 값으로 할당되어 제공된다.
    * React 팀에서는 props 객체의 children 값을 나중에 제공된 children 인수로 덮어쓰도록 처리했다.

# Exercise
```js
const ContactCard = ({ name, job, email }) => {
  return (
    <li className="contact-card">
      <h2>{name}</h2>
      <dl>
        <dt>Job</dt>
        <dd>{job}</dd>
        <dt>Email</dt>
        <dd>{email}</dd>
      </dl>
    </li>
  );
};

function App() {
  return (
    <ul>
      <ContactCard
        name="Sunita Kumar"
        job="Electrical Engineer"
        email="sunita.kumar@acme.co"
      />
      <ContactCard
        name="Henderson G. Sterling II"
        job="Receptionist"
        email="henderson-the-second@acme.co"
      />
      <ContactCard
        name="Aoi Kobayashi"
        job="President"
        email="kobayashi.aoi@acme.co"
      />
    </ul>
  );
}
```
```js
const Button = ({color, borderColor, children}) => {
  return (
    <button
      style={{
        border: '2px solid',
        color,
        borderColor,
        background: 'white',
        borderRadius: 4,
        padding: 16,
        margin: 8,
      }}
    >
      {children}
    </button>
  );
};

root.render(
  <div>
    <Button
      color="red"
      borderColor="red"
    >
      Cancel
    </Button>
    <Button
      color="black"
      borderColor="black"
    >
      Confirm
    </Button>
  </div>
);
```