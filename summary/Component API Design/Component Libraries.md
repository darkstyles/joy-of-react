# 요약
* UI 프레임워크(Material UI) 사용을 추천하지 않는다.
  * 초기에 빠르게 UI를 구성할 수 있으나 작업하려는 디자인 형태와 UI 프레임워크에서 제공하는 컴포넌트 디자인과 다른 상황이 발생할 때가 있다.
    * 보통 이때 커스텀 옵션으로 해결하려고 많은 시간을 소비한다. 그러다 결국 포기하고 직접 해당 컴포넌트를 만드는 경우가 많다.
  * 여러 가지 스타일을 규정하지 않고 사용성과 접근성에만 초점을 맞춘 또 다른 카테고리의 도구가 있다.
    * Reach UI, Headless UI, Radix Primitives, React ARIA
  * 프로토타이핑이나 간단한 내부 도구를 만들 때는 UI 프레임워크를 사용하여도 괜찮다.
  * `Tailwind`는 즉시 사용 가능한 컴포넌트를 제공하지 않지만 디자인 토큰을 제공하여 개발자에게 일련의 가드레일을 제공한다.
  * `Chakra UI`는 기본적으로 스타일이 지정된 컴포넌트를 제공하지만 매우 미니멀하고 낮은 수준이며, 체크박스나 라디오 버튼과 같은 합리적인 기본값을 제공한다.
* 개발자도 디자인을 보는 능력을 길러야 한다.
  * 디자인팀이 만든 디자인에 대해 비판적으로 생각하자.
  * 질문을 많이하자. 디자이너가 사물을 어떻게 구조화하였는지, 왜 그런 결정을 내렸는지을 이해하려고 하자.
    * [제품 및 디자인과의 효과적인 협업](https://www.joshwcomeau.com/career/effective-collaboration/)
  * 디자인 커뮤니티에서 필요한 디자인을 검색해보자.
    * [dribbble](https://dribbble.com/)
    * [behance](https://www.behance.net/)
    * [awwwards](https://www.awwwards.com/)
  * 3~4개의 디자인을 혼합하여 독특한 것을 만들 수 있다.
    * 예를 들어 한 사이트의 색 구성표는 다른 사이트에서, 일반적인 레이아웃과 간격은 다른 사이트에서, 타이포그래피 스타일은 세 번째 사이트에서 가져올 수 있다.
    * 실제 디자이너들도 이런 전략을 사용한다.
## Design systems
제품 중심 기업 중 타사 컴포넌트 라이브러리를 사용하는 기업이 극소수인 이유를 이해하려면 디자인 시스템에 대해 이야기해야 합니다.
디자인 시스템은 브랜드에 고유한 정체성을 부여하는 요소입니다. 디자인 시스템은 제품의 모양, 느낌, 작동 방식을 규정하는 규칙 모음입니다. 여기에는 색상과 타이포그래피 스타일에 대한 토큰은 물론 특정 UI 요소에 대한 심층적인 디자인이 포함됩니다.
디자인 시스템은 디자이너가 Figma나 Sketch와 같은 디자인 소프트웨어를 사용하여 구축합니다. 디자이너는 자신의 디자인을 기반으로 컴포넌트를 구현할 수 있도록 개발자인 당사에 이러한 문서를 제공합니다.
비유하자면 디자인 시스템은 레시피 북과 같습니다. 컴포넌트 라이브러리는 다진 토마토, 퓌레로 만든 당근 등 필요한 준비된 재료의 집합입니다. 그리고 웹 애플리케이션은 완성된 요리입니다.
문제는 서드파티 컴포넌트 라이브러리에는 자체 디자인 시스템이 내장되어 있다는 것입니다.
예를 들어 `Material UI`는 Google의 `Material Design` 시스템을 사용합니다. 이는 웹과 Android의 공식 Google 애플리케이션에서 사용되는 매우 구체적인 미학입니다.
서드파티 컴포넌트 라이브러리를 사용하면 스타일을 사용자 지정하거나 조정할 수 있지만, 이런 식으로 전체 디자인 시스템을 교체하는 것은 매우 어렵고 번거로운 일입니다. 이런 시도를 해본 여러 개발자와 이야기를 나눠본 결과 모두 후회하고 있습니다.
결론부터 말씀드리자면, 브랜딩/미학을 완벽하게 제어하고 싶어서 머티리얼 UI와 같은 "미리 스타일이 지정된" 컴포넌트 라이브러리에 의존하는 기술 회사는 거의 없습니다. 따라서 React 개발자로서 실제 작업에 가장 잘 대비할 수 있도록 이러한 도구도 사용하지 않습니다.
> <b>접근성 및 사용성(정보)</b>  
> 안타깝게도 웹에는 좋은 '표준 라이브러리'가 없습니다. 자동 완성, 도구 설명 또는 아코디언과 같은 기본 제공 HTML 태그가 없습니다.
> 이러한 저수준 구성 요소는 특히 접근성과 사용성을 고려할 때 의외로 구축하기가 까다로울 수 있습니다. 주의를 기울이지 않으면 화면 리더나 키보드를 사용하여 탐색하는 사람이 사용할 수 없는 애플리케이션을 만들게 됩니다.
> 이 모듈의 뒷부분에서 배우겠지만, 이를 위해 사용할 수 있는 특수 라이브러리가 있습니다. 접근성 중심 라이브러리인 Radix Primitives는 형평성을 최우선 관심사로 다루며, 디자인 시스템을 강요하지 않고 구성 요소의 "표준 라이브러리"를 채웁니다.

## Application structure
원더블록과 같은 퍼스트 파티 컴포넌트 라이브러리를 구축할 때는 컴포넌트 라이브러리가 자체 프로젝트가 되는 것이 일반적이며, 자체 깃 저장소, 자체 랜딩 페이지, 자체 문서가 있습니다.
이는 여러 프로젝트에서 컴포넌트를 공유해야 할 때 유용하지만, 솔직히 말해서 많은 마찰을 유발합니다.
이 강좌에서는 이 강좌 플랫폼과 같은 실제 제품에서 제가 사용하는 전략에 초점을 맞추겠습니다. 저는 모든 컴포넌트를 하나의 디렉터리에 보관합니다.
재미로 이 강좌 플랫폼에서 처음 몇 개의 컴포넌트 디렉토리를 스크린샷으로 찍어 스펙트럼에서 차지하는 위치에 따라 몇 개에 레이블을 붙였습니다:
![directory](https://github.com/darkstyles/joy-of-react/assets/3774171/ab6cbf29-2cf6-4e1b-bdc6-cc35a79ab732)
* `*` 보라색 별표가 하나 있는 컴포넌트는 가장 낮은 수준의 컴포넌트입니다.
* `**` 빨간색 별표가 두개 있는 컴포넌트는 조금 더 높은 수준의 컴포넌트로 `AccountDropdown`과 같은 것입니다. 
* `***` 노란색 별표가 세개 있는 컴포넌트는 `AccountPage`와 같은 모든 주요 컨텐츠를 렌더링하는 상위 수준의 컴포넌트입니다.

이렇게 모든 것을 한데 섞어 놓으면 혼란스러워 보일 수 있지만, `component library components`와 `그 외 모든 것` 사이에는 잘못된 이분법이 없습니다.
모든 컴포넌트는 스펙트럼의 한 부분이고, 모든 컴포넌트는 스펙트럼의 한 지점을 차지합니다. 필요한 모든 컴포넌트가 바로 내 손끝에 있고, 원하는 대로 자유롭게 구성하고 향상시킬 수 있습니다.  

[참고, 즐거운 React 파일/디렉토리 구조](https://www.joshwcomeau.com/react/file-structure/)