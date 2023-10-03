# With an If Statement
* JSX가 반환문의 일부가 되어야 하는 규칙은 없으므로 `if`문을 통해 조건별로 변수에 할당하여 반환문에 포함할 수 있다.
* 요소의 속성의 값이 `undefined / null / false`라면 리액트는 DOM node 생성 시에 해당 속성을 제외한다.
* `if`문을 사용할 때의 단점은 로직을 함수 본문에 작성하고 해당 결과를 마크업에서 사용하게 되면 크고 복잡한 컴포넌트의 경우 어떻게 구조화되어 있는지 이해하기 어렵게 만들 수 있다.

# With &&
* `&&`을 제어 흐름 연산자라고 하는 이유는 if/else와 같이 항상 두 경로 중 하나를 선택하기 때문이다.
* `&&`연산자는 왼쪽 또는 오른쪽 결과를 반환한다.
    * 왼쪽 결과가 0이라면 true / false가 아닌 0을 반환한다.
* React는 false 또는 null과 같은 대부분의 falsy 값을 무시힌다.
    * 0과 NaN은 무시되지 않는다.
* `&&`연산자 사용 시에는 왼쪽에 항상 boolean 값을 사용하면 예외 상황을 피할 수 있다.

# With Ternary
* 삼항 연산자는 문이 아닌 표현식이다.
* `condition ? firstExpression : secondExpression`형식으로 사용한다.
    * `condition`이 참이라면 첫번째 표현식이 실행되고 거짓이라면 두번째 표현식이 실행된다.
* `console.log` 함수의 반환 값은 `undefined`이다.

# Showing and Hiding
* 조건에 따라 CSS를 변경하여 적용할 수 있다.
* 상황에 따라 DOM 노드를 제어할 지 CSS로 제어할 지 판단해서 사용하자.

# Exercise
* `VisuallyHidden`을 이용하여 스크린 리더가 읽을 수 있는 컨텐츠로 만들 수 있다.
* `aria-label`속성을 이용해서도 스크림 리더가 읽을 수 있지만 해당 속성은 버튼과 같은 대화형 요소에만 작용한다.
```jsx
import React from 'react';
const hiddenStyles = {
  display: 'inline-block',
  position: 'absolute',
  overflow: 'hidden',
  clip: 'rect(0 0 0 0)',
  height: 1,
  width: 1,
  margin: -1,
  padding: 0,
  border: 0,
};
const VisuallyHidden = ({ children, ...delegated }) => {
  const [forceShow, setForceShow] = React.useState(false);
  React.useEffect(() => {
    if (process.env.NODE_ENV !== 'production') {
      const handleKeyDown = (ev) => {
        if (ev.key === 'Alt') {
          setForceShow(true);
        }
      };
      const handleKeyUp = (ev) => {
        if (ev.key === 'Alt') {
          setForceShow(false);
        }
      };
      window.addEventListener('keydown', handleKeyDown);
      window.addEventListener('keyup', handleKeyUp);
      return () => {
        window.removeEventListener('keydown', handleKeyDown);
        window.removeEventListener('keyup', handleKeyUp);
      };
    }
  }, []);
  if (forceShow) {
    return children;
  }
  return (
    <span style={hiddenStyles} {...delegated}>
      {children}
    </span>
  );
};
export default VisuallyHidden;
```
```jsx
function ProfileCard({ profile }) {
  const numberOfBadges = profile.badges.length;

  return (
    <article className="profile-card">
      <header>
        <img
          alt={profile.imageAlt}
          src={profile.imageSrc}
        />

        <h2>{profile.name}</h2>
        <p className="joined">Joined {profile.joinDate}</p>
      </header>
      {numberOfBadges > 0 && (
        <ul
          className={
            numberOfBadges >= 3
              ? 'golden badge-list'
              : 'badge-list'
          }
        >
          {profile.badges.map(({ slug, label }) => (
            <li key={slug}>{label}</li>
          ))}
        </ul>
      )}
    </article>
  );
}
```