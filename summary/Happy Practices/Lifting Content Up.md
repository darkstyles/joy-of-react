# Exercise
## Art Store
* 완료 기준
    * <CartButton> 요소는 <헤더>가 아닌 앱 컴포넌트가 소유해야 합니다.
    * DOM 구조는 영향을 받지 않아야 합니다(장바구니 버튼은 여전히 <header> DOM 노드의 하위 노드여야 합니다).
    * 보너스: '키 활용하기' 단원에서 배운 내용을 사용하여 숫자가 바뀔 때마다 '페이드' 애니메이션이 다시 트리거되도록 코드를 업데이트합니다
```jsx
function App() {
  const [cartItems, setCartItems] = React.useState(
    []
  );

  function addToCart(item) {
    setCartItems([...cartItems, item]);
  }

  return (
    <>
      <Header
        actions={
          <CartButton
            numOfItems={cartItems.length}
          />
        }
      />

      <Shop
        paintings={DATA}
        addToCart={addToCart}
      />
    </>
  );
}

function Header({ actions }) {
  return (
    <header>
      <h1>
        Pintor Famoso
        <span className="artist-title">
          Abstract Expressionist
        </span>
      </h1>
      {actions}
    </header>
  );
}

function CartButton({ numOfItems }) {
  return (
    <a href="/" className="cart-button">
      <ShoppingCart />
      {numOfItems > 0 && (
        <span
          key={numOfItems}
          className="cart-number"
        >
          {numOfItems}
        </span>
      )}
    </a>
  );
}
```
