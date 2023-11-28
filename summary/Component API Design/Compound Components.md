#  복합 컴포넌트
## A big trick
* 라이브러리에서 복합 컴포넌트 패턴을 선호하는 이유는 임포트 문이 단순해지기 때문이라고 생각한다. 드롭다운 컴포넌트 임포트를 한 번만 지정하면 모든 하위 컴포넌트가 제공되며 일일이 나열할 필요가 없다.
* 하지만 비용을 고려하면 이는 매우 작은 혜택에 불과하다.
    * 지역 코딩 부트캠프에서 React를 가르치면 의외로 많은 학생들이 서드파티 라이브러리에서 본 이상한 문법에 대해 질문하곤 했다.
    * 트리 쉐이킹과 같은 번들러 최적화에 액세스할 수 없다.
    * Next 13(아래 설명)과 같이 호환성 문제가 있을 수 있다.  

> <b>Issues with Next 13</b>
> Next 13 내에서 복합 컴포넌트를 사용하려고 하면 오류가 발생합니다.
> `Unsupported Server Component type: undefined`
> Next에는 특수한 모듈 프록시 로직이 있으며 컴포넌트가 이와 같은 속성을 가질 것으로 예상하지 않기 때문에 이런 일이 발생합니다.
> 안타깝게도 명명된 내보내기를 사용하여 컴파운드 컴포넌트를 "컴파운드 해제"하는 것 외에 다른 해결 방법은 알지 못합니다.

## DIY API design
```js
// 드롭다운 컴포넌트는 블랙박스다. 우리가 원시 데이터를 제공하면 이 컴포넌트가 우리를 위해 UI를 생성한다.
// 사용하기는 쉽지만 딱딱하다. 사용자 지정이 전혀 불가능하다.
<Dropdown
  label="Actions"
  options={[
    { href: '/change-email', label: "Change Email" },
    { href: '/reset-pwd', label: "Reset Password" },
    { href: '/delete', label: "Delete Account" },
  ]}
/>
```
```js
// 드롭다운 구성 요소는 IKEA의 가구와 비슷하다. 모든 부품이 제공되며 직접 조립해야 한다.
// 설정 작업이 조금 더 필요하지만 훨씬 더 유연하다. 다양한 방식으로 조각을 결합하거나 자체 하위 구성 요소로 대체할 수도 있다.
<Dropdown>
  <DropdownToggle>
    Actions
  </DropdownToggle>

  <DropdownMenu>
    <DropdownItem href="/change-email">
      Change Email
    </DropdownItem>
    <DropdownItem href="/reset-pwd">
    Reset Password
    </DropdownItem>
    <DropdownItem href="/delete">
    Delete account
    </DropdownItem>
  </DropdownMenu>
</Dropdown>
```

