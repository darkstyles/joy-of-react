# Slots
* 이 패턴은 소비자에게 최대한의 제어권과 권한을 제공한다.
    * 위임된 프로퍼티를 사용하면 특정 요소에 추가 프로퍼티를 제공할 수 있다.
    * 다형성을 사용하면 특정 요소의 HTML 태그를 변경할 수 있다.
    * 슬롯을 사용하면 원하는 마크업을 제한 없이 제공할 수 있다.

## Exercise
### Icon Buttons
```js
import React from 'react';

import styles from './IconButton.module.css';

function IconButton({ icon, children }) {
  return (
    <button className={styles.wrapper}>
      <span className={styles.iconWrapper}>
        {/* Icon slot */}
        {icon}
      </span>
      <span className={styles.childrenWrapper}>
        {/* Children slot */}
        {children}
      </span>
    </button>
  );
}

export default IconButton;
```

### Stretch Goal: Restricting control
```js
// Solution 1: Cloning the element
import React from 'react';

import styles from './IconButton.module.css';

function IconButton({ icon, children }) {
  const clonedIcon = React.cloneElement(icon, {
    size: 20,
    strokeWidth: 1.5,
  });

  return (
    <button className={styles.wrapper}>
      <span className={styles.iconWrapper}>
        {clonedIcon}
      </span>
      <span className={styles.childrenWrapper}>
        {children}
      </span>
    </button>
  );
}

export default IconButton;
```
```js
Solution 2: Passing the component through props
import React from 'react';

import styles from './IconButton.module.css';

function IconButton({ icon: Icon, children }) {
  return (
    <button className={styles.wrapper}>
      <span className={styles.iconWrapper}>
        <Icon size={20} strokeWidth={1.5} />
      </span>
      <span className={styles.childrenWrapper}>
        {children}
      </span>
    </button>
  );
}

export default IconButton;
```

### 