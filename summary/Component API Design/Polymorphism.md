# 요약
* 요소를 클릭하여 JS에서 동작을 수행할 수 있다면 버튼이어야 한다.
* 사용자를 새 페이지로 이동시키는 동작이 아니라면, 이 경우 앵커(<a>)여야 한다.
* 버튼처럼 보이고 싶지 않더라도 `<button>`을 사용해야 한다. CSS를 사용하면 내장된 버튼 스타일을 모두 제거할 수 있다.
* <button> 태그에 내장된 모든 사용성 이점을 다시 만드는 것보다 몇 가지 CSS 규칙을 제거하는 것이 훨씬 쉽다.
## Video Summary
* JSX는 lowercase로 태그 작성 시 HTML DOM Node로 인식하여 문자열로 인식한다.
* JSX에서 Pascalcase로 태그 작성 시 컴포넌트로 인식하여 변수로 인식하게 되고 파싱할 때 해당 변수의 값을 읽는다.
* 그러므로 아래와 같이 JSX가 작성이 된다면 Tag 변수를 사용하여 동적으로 태그를 설정할 수 있다.
```js
function LinkButton({
  href,
  children,
  ...delegated
}) {
  const Tag = typeof href === 'string'
    ? 'a'
    : 'button';

  return (
    <Tag
      href={href}
      className={styles.button}
      {...delegated}
    >
      {children}
    </Tag>
  );
}

// This code:
React.createElement(
  Tag,
  {
    href,
    className: styles.button,
    ...delegated
  },
  children
);

// Is the same as this code:
<Tag
  href={href}
  className={styles.button}
  {...delegated}
>
  {children}
</Tag>
```

## Exercise
### A List Component
```js
// List.js
import React from 'react';

import styles from './List.module.css';

const VALID_TAGS = ['ul', 'ol'];

function List({
  // ol | ul
  as: Tag = 'ul',
  className = '',
  children,
  ...delegated
}) {
  if (!VALID_TAGS.includes(Tag)) {
    throw new Error(
      `Unrecognized tag: ${Tag}. Expected: ${VALID_TAGS}`
    );
  }

  return (
    <Tag
      {...delegated}
      className={`${styles.wrapper} ${className}`}
    >
      {children}
    </Tag>
  );
}

export default List;
```

### Customized heading levels
```js
// SectionWithHeading.js
import React from 'react';

function SectionWithHeading({
  level,
  title,
  children,
}) {
  if (
    typeof level !== 'number' ||
    level < 1 ||
    level > 6
  ) {
    throw new Error(
      `Unrecognized heading level: ${level}`
    );
  }

  const HeadingTag = `h${level}`;

  return (
    <section>
      <HeadingTag>{title}</HeadingTag>
      {children}
    </section>
  );
}

export default SectionWithHeading;
```