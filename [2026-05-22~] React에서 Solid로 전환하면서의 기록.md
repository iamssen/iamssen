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

# 2026-06-25

Fork한 `eslint-plugin-solid`는 거의 Rule 관련 코드들만 남기고 전부 다 갈아엎은 것 같다. 배포 자동화도 해 놨고, 이제야 좀 볼 만하게 정리가 된 것 같다. [@ssen/eslint-plugin-solid](https://www.npmjs.com/package/@ssen/eslint-plugin-solid)

이제 Rule들을 하나씩 확인해 봐야 할 것 같다. Solid.js로 개발을 해 본 적도 없는데 ESLint 플러그인을 만들려니 상당히 난감하다.


# 2026-07-16

ESLint 플러그인은 일단 작업을 완료했다.

- [x] <https://github.com/iamssen/eslint-plugin-solid>
  - [x] Legacy ESLint 설정을 모두 제거하고 ESLint v10+ 지원으로 전환
  - [x] Solid.js 2.0 기준으로 Rule 변경
- [x] <https://github.com/iamssen/solidjs2-web-prototype>에서 실질 Web App 설정과 동작 테스트 완료

"아오 ㅆ 하기 싫어..."라면서 밍기적거리다가 Codex를 사용하게 되면서 작업이 급진전됐다.

초반 루틴이 잡히기까지는 Codex를 수동으로 운전(?)해 줄 필요가 있지만, 어느 정도 사용자 요청과 Agent 동작이 루틴화되기 시작한 이후에는 "나머지 작업은 알아서 진행해 봐"라는 요청만으로 모든 작업이 잘 진행되더라.

Agent 개발이 매직은 아닌 듯싶다. 기존의 논리적 작업 순서 설계 → 실행 → 검증 루프에서 실행과 검증의 부담이 크게 줄어드는 거지 "논리적 작업 순서 설계"라는 단계를 대체하지는 못한다. (뭐... 토큰을 펑펑 날리면 모르겠지만... ChatGPT Plus 최하 유료 요금제의 Codex 주간 사용량 20%만으로 작업을 끝냈으니 "논리적 작업 순서 설계" 수립에 인간이 적절하게 개입하고 실행, 검증 루프를 Agent에게 맡길 때 재실행 시도는 거의 없는 것으로 보인다.)

어쨌든... 드디어 Solid.js를 사용한 실제 개발 진행이 가능하게 되었다. ESLint 플러그인은 실제 개발을 진행하면서 점진적으로 수정해 나가면 될 것 같고...

뭐... 오랜만에 재밌는 작업이었다.

# 2026-07-22

<https://github.com/iamssen/solidjs2-web-prototype>에 Storybook 세팅을 추가했다.

언제나 생각하는 것이지만 Storybook은 무슨 스팸 같다. `npx create-storybook --type=solid` 실행했더니 온갖 것들이 덕지덕지... Vitest 연동, Playwright 연동 기능들은 도대체 왜 설치하는 거야?

[simple-grid]라는 간단한 Grid Component를 Codex를 사용해서 만들었는데, `AGENTS.md`에 요구사항을 좀 디테일하게 적긴 했지만 "아님 말고" 식으로 돌려봤던 게 결과가 너무 잘 나와서 좀 놀랐다.

Solid.js 개발이 React에 비해 자잘하게 번거로운 부분들이 있긴 하지만 대신 아주 만족스러운 부분이 있다. Component function이 딱 한 번만 실행된다는 것. 이건 정말 최고다. React 개발에서 제일 짜증나는 부분이 Component function이 미친 듯이 재실행되는 와중에 Memo를 여기저기 덕지덕지 쑤셔 넣어야 하는 건데, "딱 한 번만 실행된다"라는 동작 원리는 편안함 그 자체다. 속도 때문에 React Compiler니 뭐니 뇌절을 하고 있는 React에 점점 짜증이 나는 중이었어서 Solid.js의 단순함이 주는 편안함이 크게 다가오는 것 같다. 단순한데 심지어 빨라!

[simple-grid]: https://github.com/iamssen/solidjs2-web-prototype/tree/main/apps/app/components/simple-grid
