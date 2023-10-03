# Mapping Over Data
* React는 배열이 전달되면 각 항목을 가져와서 렌더링 한다.
* JSX 내 JS 내 JSX 형태로 작성하여도 정상적으로 동작한다.
    * JSX 컴파일러는 중첩된 JSX 호출을 문제 없이 처리할 수 있기 때문이다.
```js
<ul> // JSX
  {items.map(item => ( // JS
    <li>{item}</li> // JSX
  ))}
</ul>
```

# Keys
* `key`의 목적은 각 리액트 엘리먼트를 고유하게 식별하는 것이다.
* `fragments`에서 `key`를 사용하려면 긴 형태로 사용하여야 한다.
    * `<React.Fragment key={}></React.Fragment>`
* `key`의 유효범위는 해당 배열이다. 전역 범위가 아니다.

# Exercise
```js
const data = [
  {
    id: '001',
    description:
      'person with curly hair and a black T-shirt',
  },
  {
    id: '002',
    description: 'person wearing a hijab and glasses',
  },
  {
    id: '003',
    description:
      'person with short hair wearing a blue hoodie',
  },
  {
    id: '004',
    description:
      'person with a pink mohawk and a raised eyebrow',
  },
];

function App() {
  return (
    <div className="avatar-set">
      {data.map(({ id, description }) => (
        <Avatar
          key={id}
          src={`https://sandpack-bundler.vercel.app/img/avatars/${id}.png`}
          alt={description}
        />
      ))}
    </div>
  );
}
```
```js
function App() {
  return (
    <>
      <h2>Shopping cart</h2>
      <CartTable
        items={items.filter(({ inStock }) => inStock)}
      />
      <div className="actions">
        <button>Continue checkout</button>
      </div>

      <h2>Sold out</h2>
      <CartTable
        items={items.filter(({ inStock }) => !inStock)}
      />
    </>
  );
}

function CartTable({ items }) {
  // TODO: Map through “items”, creating 1 row
  // per item.

  return (
    <table className="shopping-cart">
      <thead>
        <tr>
          <th></th>
          <th>Title</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>
        {items.map((item) => (
          <tr key={item.id} className="cart-row">
            <td>
              <img
                className="product-thumb"
                src={item.imageSrc}
                alt={item.imageAlt}
              />
            </td>
            <td>{item.title}</td>
            <td>${item.price}</td>
          </tr>
        ))}
      </tbody>
    </table>
  );
}
```