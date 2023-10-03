* 정해진 수만큼 반복되는 작업은 숫자 배열을 만들어서 동일한 컴포넌트를 반복적으로 만들어서 사용한다.
    * lodash의 range 함수를 사용하자.
```jsx
function StarRating({ rating }) {
  return (
    <div className="star-wrapper">
      {range(rating).map((num) => (
        <img
          key={num}
          alt=""
          className="gold-star"
          src="https://sandpack-bundler.vercel.app/img/gold-star.svg"
        />
      ))}
    </div>
  );
}

// lodash - range
const range = (start, end, step = 1) => {
  let output = [];

  if (typeof end === 'undefined') {
    end = start;
    start = 0;
  }

  for (let i = start; i < end; i += step) {
    output.push(i);
  }

  return output;
};
```
```jsx
import { range } from './utils';

function Graph({ from, to }) {
  return (
    <div className="graph">
      {range(from, to + 1, 10).map((num) => (
        <div key={num} className="peg">
          {num}
        </div>
      ))}
    </div>
  );
}
```
```jsx
import { range } from './utils';

function Grid({ numRows, numCols }) {
  return (
    <div className="grid">
      {range(numRows).map((rowNumber) => (
        <div key={rowNumber} className="row">
          {range(numCols).map((colNumber) => (
            <div key={colNumber} className="cell"></div>
          ))}
        </div>
      ))}
    </div>
  );
}

export default Grid;
```