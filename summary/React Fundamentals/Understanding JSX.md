* JSX 에서 `react`를 import만하고 사용하니 제거해도 되지 않을까?
  * JSX 파일이 JS로 컴파일된 결과를 보면 JSX 구문이 `React.createElemnt` 변환되어 사용되는 것을 알 수 있다.
  * React 17에서 React 팀은 Babel과 다른 컴파일러에서 사용하는 새로운 "JSX 트랜스포머"를 도입하여 기본적으로 빌드 프로세스 중에 `react` import를 자동으로 삽입한다.
* Expression Slots를 사용하면 JSX 내에서 자바스크립트 표현식을 사용할 수 있다.
  * Expression Slots 내에서는 표현식만 동작하고 구문은 동작하지 않는다.
  * Expression Slots 에서 작성된 내용이 표현식만 유효한 이유는 js로 컴파일 하였을 때 하나의 children으로 전달되기 때문에 표현식만 동작하고 구문은 동작하지 않는다.
  * 표현식과 구문이 차이는 값을 생성 유무이다. 표현식은 값을 생성하고 구문은 값을 생성하지 않는다.
    * 표현식은 값을 생성하며, 값을 기록하거나 변수에 할당하거나 함수에 전달할 수 있다.
    * 구문은 변수를 선언하거나 루프를 실행하는 등 특정 작업을 수행하기 위한 명렬어이다.
```js
// This code...
const element = (
  <div>
    Items left to purchase: {shoppingList.length}
  </div>
);

// ...is equivalent to this code:
const compiledElement = React.createElement(
  'div',
  {},
  'Items left to purchase: ',
  shoppingList.length
);
```
* JSX 내에서 주석은 여러 줄 주석 구문(`/* */`)을 사용한다.
* 속성에 동적 값을 할당할 때는 중괄호를 이용하여 할당한다.
  * expression slots에 작성된 표현식은 js로 컴파일 시에는 실행되지 않고 복사만 되고 런타임에서 실행된다.
```js
const uniqueId = 'content-wrapper';

const element = (
  <main id={uniqueId}>
    Hello World
  </main>
);

const element = React.createElement(
  'main',
  {
    id: uniqueId,
  },
  'Hello World'
);
```
* HTML과 JSX 차이점
  * JSX에서는 예약어를 사용할 수 없다.
  * HTML5에서는 특정 태그에 닫는 태그가 없으나 JSX에서는 명확하게 닫는 태그를 작성하여야 한다. 이때 JSX에서는 `self-closing` 태그를 사용한다.
  * HTML은 태그명에 대해 대소문자를 구분하지 않으나 JSX는 대소문자를 구분한다. JSX는 대소문자로 DOM 태그인지 사용자 정의 컴포넌트인지를 구분한다.
  * JSX에서는 속성명은 camelCase로 사용한다. (단, Data와 ARIA 속성은 dash 사용을 유지한다.)
  * HTML의 내부 스타일은 문자열을 허용하지만 JSX는 객체만 허용한다.
    * JSX에서 모든 CSS 속성명은 camelCase를 사용한다. (단 브라우저별 속성은 PascalCase를 사용한다.)
    * 단위를 취급하는 속성값에 단위를 사용하지 않으면 리액트는 기본적으로 `px`로 단위를 설정한다.
* HTML은 기본적으로 요소간 개행을 인식하여 공백을 추가한다. JSX는 기본적으로 요소간 개행에 대한 공백을 처리하지 않는다.
  * JSX에서 요소간 공백을 추가하려면 expression slots을 통하여 공백 문자를 추가한다.
* Exercise
```js
const profileUrl = `/users/${message.author.handle}`;

const element = (
  <article style={{ filter: 'var(--shadow-elevation-high)' }}>
    <header>
      <img
        alt={message.author.avatarDescription}
        src={message.author.avatarSrc}
      />
      <a href={profileUrl}>{message.author.name}</a>
    </header>
    <p>{message.content}</p>
    <footer>
      Posted
      {' '}
      <time dateTime={message.published}>
        {formatDate(message.published)}
      </time>
    </footer>
  </article>
);
```
* 템플릿 언어와 JSX의 차이
  * 템플릿 언어는 사용자가 작성한 마크업을 HTML로 변환한다. 즉, HTML에는 이러한 기능이 내장되어 있지 않기 때문에 동적인 작업을 수행하려면 작은 사용자 정의 언어를 개발해야 한다.
  * JSX는 사용자가 작성한 마크업을 자바스크립트로 변환한다. 사용자가 포함하는 모든 로직을 해결하지 않고 "전달"한다.
  * JSX는 마크업을 작성할 때 자바스크립트의 모든 기능을 사용할 수 있다. 템플릿 언어처럼 소수의 헬퍼에 국한되지 않고 모든 자바스크립트 메서드를 사용할 수 있다.

