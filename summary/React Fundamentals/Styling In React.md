* JSX를 JS로 컴파일하는 단계에서 번들러(webpack)는 JS가 아닌 파일 import를 지원한다.
* `.module.css` 형식의 파일이라면 JS 모듈처럼 import 되는데 아래와 같이 동작한다.
    * 모듈의 모든 CSS 클래스에 대해 더 길고 보장된 고유한 클래스 이름이 생성된다.
    * 생성된 고유한 클래스 이름을 사용하는 CSS가 문서의 <head>에 추가된다.
    * css 파일에 작성된 클래스에 생성된 고유한 클래스 이름이 매핑된 스타일 객체가 생성된다.
```js
{
  wrapper: "_components_Sidenote_module__wrapper",
  title: "_components_Sidenote_module__title",
}
```
* 각 이름이 고유한지 어떻게 알 수 있을까?
    * 파일 시스템을 이용한다.
    * 모든 CSS 클래스에는 파일의 전체 경로가 접두사로 사용되며, 같은 공간에 두 개의 파일이 존재할 수 없으므로 각 클래스가 고유하다는 것을 100% 확신할 수 있다.

# Exercise
```jsx
import styles from './Sidenote.module.css';

function Sidenote({ type, title, children }) {
  return (
    <aside className={`${styles.wrapper} ${styles[type]}`}>
      <h3 className={styles.title}>{title}</h3>
      <p>{children}</p>
    </aside>
  );
}

export default Sidenote;
```
```jsx
function Movie({ movie }) {
  return (
    <article className={styles.movie}>
      <div className={styles.thumbnailWrapper}>
        <img
          alt="Movie poster"
          src={movie.posterSrc}
        />
      </div>
      <div className={styles.textWrapper}>
        <h2>{movie.title}</h2>
        <p className={styles.synopsis}>
          {movie.synopsis}
        </p>
        <p>
          <strong>Rating:</strong>
          {' '}
          <span
            className={
              movie.rating >= 9
                ? styles.glowingReview
                : ''
            }
          >
            {movie.rating}
          </span>
        </p>
      </div>
    </article>
  );
}
```