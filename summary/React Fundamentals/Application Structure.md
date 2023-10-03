# Index
* `index.js`파일은 일반적으로 실행될 첫 번째 코드이다.
* 이 파일은 리액트 애플리케이션을 렌더링하고 적상한 리액트 엘리먼트를 DOM 노드로 전환하는 역할을 한다.
* 보통 이 파일에서 `설정` 작업을 수행한다. 예를 들어 글로벌 CSS 파일을 가져오거나 오류 로깅 서비스를 설정한다.
* `index.js`는 애플리케이션의 설정 파일에 가깝기 때문에 일반적으로 많은 JSX를 렌더링 하지 않는다.

# App
* 프로젝트에는 일반적으로 `애플리케이션`의 줄임말인 App이라는 컴포넌트가 있다.
* App은 프로젝트의 기본 베이스 리액트 컴포넌트이며, 다른 모든 컴포넌트의 조상이다.
* App은 헤더와 푸터와 같은 `핵심`레이아웃을 관리한다.
* React Router와 같은 라우팅 솔루션을 사용하는 경우 이 파일이 최상위 경로로 포함되는 경우가 많다.
* App은 개발자에게 애플리케이션이 어떻게 구조화되어 있는지 보여줘야 한다.

# Modules
* 일반적으로 ES 모듈 시스템을 사용하여 애플리케이션을 여러 파일로 분할한다.