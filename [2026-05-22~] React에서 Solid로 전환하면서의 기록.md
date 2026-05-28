# 2026-05-22
은퇴 후 딱히 하는 것 없이 취미 생활이나 하며 지내던 중, Solid.js에 대해 알게 되었다.

예전에 RxJS와 React를 결합해 Virtual DOM 렌더링을 과도하게 유발하지 않는 컨셉을 고민해 본 적이 있다. 하지만 어디서부터 어디까지 만들어야 할지 감이 오지 않을 정도로 큰 작업이 될 것 같아 관뒀었는데, Solid.js가 딱 그런 컨셉을 구현했더라.

더 이상 상업적 개발을 하지 않아 생산성을 위해 굳이 외부 라이브러리를 사용할 필요가 없어서 평소 UI 컴포넌트 라이브러리 없이 깡구현을 즐기는 편이다 보니 React 대신 Solid.js를 사용해 보는 것도 나쁘지 않을 것 같았다. (심심하기도 하고...)

Solid.js로 전환하면서 처음 맞닥뜨린 문제는 다음 두 가지였다.

1. Solid.js 2.0 Beta가 진행 중이며, API Breaking Change가 꽤 있음.
2. `eslint-plugin-solid` 관리가 안 되고 있음. 
  - ESLint v10 지원 안 됨 (메인테이너가 PR을 병합하지 않고 있는 상태)
  - Solid.js 2.0 규칙에 맞춰 추가로 구현할 게 있는지 확인해야 함

Solid.js 2.0이야 그냥 Beta 버전으로 설치해서 사용하면 되지만, `eslint-plugin-solid`는 좀 골치가 아프다. 일단 ESLint v10을 지원하는 PR을 가져와 직접 설치하는 방식으로 처리하긴 했는데, 2.0 규칙에 맞춰 추가적인 Rule까지 구현해야 한다면 꽤나 머리가 아파질 것 같다.

뭐... 일단 해보자.

# 2026-05-28

`eslint-plugin-solid`는 골치가 아파서 그냥 Fork를 했다.

<https://github.com/iamssen/eslint-plugin-solid/tree/feat/fork-package>

프로젝트 구조가 너무 복잡해서, 그냥 AI를 활용해 필수적인 부분만 남기고 다 날려버렸다. ESLint Rule과 Test 관련 코드보다 환경 세팅이 지나치게 복잡하게 되어 있었다. 이러니 프로젝트 관리가 버거워질 수밖에...

배포를 하자니 애매해서 일단은 `npm pack`으로 대충 사용 중이다. 