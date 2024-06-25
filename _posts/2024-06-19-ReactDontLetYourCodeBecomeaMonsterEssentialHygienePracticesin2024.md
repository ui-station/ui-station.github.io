---
title: "리액트 코드가 몬스터가 되지 않도록  2024년 핵심 위생 관행"
description: ""
coverImage: "/assets/img/2024-06-19-ReactDontLetYourCodeBecomeaMonsterEssentialHygienePracticesin2024_0.png"
date: 2024-06-19 11:25
ogImage:
  url: /assets/img/2024-06-19-ReactDontLetYourCodeBecomeaMonsterEssentialHygienePracticesin2024_0.png
tag: Tech
originalTitle: "React: Don’t Let Your Code Become a Monster — Essential Hygiene Practices in 2024"
link: "https://medium.com/quadion-technologies/react-dont-let-your-code-become-a-monster-essential-hygiene-practices-in-2024-5a058ff5c8f5"
---

리액트를 처음 사용해본 지 오랜 시간이 지났어요. 제가 jQuery, Knockout, Angular 등의 경험을 토대로 이 라이브러리가 혁명적이라는 것을 빨리 깨달았죠.

20개 이상의 중간 및 대형 프로젝트를 완료한 지금, 2024년 6월부터 시작하는 새로운 프로젝트에 대한 이상적인 도구와 관행에 대한 생각을 공유하고 싶어요.

[노트 1]
이 스택에서는 Next.js와 같은 전체 프레임워크를 사용하지 않는 것을 선호한다고 가정해요.

자세히 알아보도록 할까요?

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

## 시작점

- **Vite**: 놀랍도록 빠른 HMR과 고도로 최적화된 프로덕션 번들을 제공합니다. React와 TypeScript 템플릿을 선택할 수 있는 훌륭한 부트스트랩 유틸리티가 있어서 모든 것이 그대로 작동합니다.
- **TypeScript**: 2024년에는 모두 TypeScript를 사용할 거라고 기대합니다. 이 연구에서는 Microsoft가 정적 유형 시스템이 성숙한 실제 코드 베이스의 15%의 공개적인 버그를 방지할 수 있었을 것이라고 밝혀졌습니다. 또한, Airbnb 엔지니어는 이 컨퍼런스에서 TypeScript로 38%의 버그를 방지할 수 있었을 것이라고 말했습니다 (사후 분석에 따르면).

## 디자인을 아름답게 만들기

여기에는 많은 대안이 있습니다. 저는 Tailwind를 사용하여 자체 UI 컴포넌트를 만드는 것을 선호합니다. 특정 컴포넌트를 위해 몇 가지 라이브러리의 도움을 받습니다. 이 방법은 예를 들어 Material-UI를 사용하는 것보다 훨씬 작은 번들 크기를 갖는다는 것을 발견했습니다. 또한, 세부 사항을 통제할 수 있습니다. 단점은 UI 컴포넌트를 테스트하는 데 더 많은 시간이 걸릴 수 있지만, 다른 큰 라이브러리를 배우는 필요성을 피할 수 있습니다.

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- Tailwind CSS: 원래 쉬운, 깔끔하고 가장 좋은 문서 중 하나를 가진 원자적 CSS 클래스입니다.
- HeadlessUI: 더 복잡한 UI에 대해선 몇 가지 스타일이 없는 구성 요소를 사용하는 것을 선호합니다. Tailwind 크리에이터들이 만든 아름답고 아주 잘 테스트된 라이브러리입니다. 사용한 멋진 구성 요소로는 Dialogs, Transitions, Combobox 및 Dropdown Menu가 있습니다.
- 선택 사항: Classnames: 조건에 따라 CSS 클래스를 결합하는 작은 라이브러리입니다.
- 선택 사항: Storybook: UI 구성 요소를 격리하고 테스트하기 위해 사용하며 설치 중이라면 설명이 필요 없을 것입니다.

## 행동을 위한 라이브러리

선택은 어렵지만, 커뮤니티의 많은 부분에서 받아들여지고 오랫동안 사용된 라이브러리를 선택하겠습니다.

- Forms: react-hook-form은 여기서 명백한 리더입니다. 중첩된 구성 요소에서 입력을 추적하고 프롭드릴링을 피하기 위한 FormContext 기능이 정말 마음에 듭니다.
- Routing: react-router (아마도 react-router-dom만 설치하면 될 것입니다). 설명이 필요 없을 정도로 좋은 것입니다.
- API Queries: react-query는 다시 시도, 캐시 및 조건부 쿼리(“enabled” 파라미터 사용)와 같은 매우 멋진 기능이 많습니다. 잘 활용하면 응답을 처리하기 위해 useFetch 훅(React 문서에서 비권장)이 필요하지 않을 것입니다. 여기 TKDodo의 블로그에 특별 언급을 드리며, 실용적인 일반적인 사례를 설명하고 있습니다(지금은 유지자입니다).
- 상태 관리: Zustand가 Redux보다 우수하다고 설명하는 것을 보여주세요:

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

저에게는 충분해요. 오랫동안 Redux는 괜찮았어요. 말 그대로, Redux가 없었다면 이러한 새로운 도구들도 존재하지 않았을 거에요. 그렇지만 지금은 더 나은 개발자 경험을 제공하는 도구들로 같은 가능성을 가질 수 있어요.

## 테스트

- 단위 테스트: 위대한 Kent Dodds가 만든 react-testing-library은 사용자가 상호작용하는 방식과 동일하게 컴포넌트를 렌더링하고 테스트할 수 있어요.
- 테스트 러너: Vitest은 Jest와 완벽하게 호환되지만 훨씬 더 빨라요. Vite 프로젝트와 아주 잘 맞아요.
- E2E 테스트: Playwright는 Cypress보다 빠르답니다. 자체 크로미엄 버전을 만들어 브라우저 테스트를 초고속으로 최적화했어요. 어떤 것이 더 나은 UI나 문서를 가지고 있는지 결정하기 어려워요. 이러한 e2e 테스트는 매우 안정적이라고 생각해요. Playwright는 또한 API 요청을위한 모의 전략을 제공해요.

## 코드 품질 도구

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- ESLint/Prettier: 이 도구들이 정말 유용하다는 것을 발견했습니다. 좋고 명확한 린트 도구가 없는 언어로 전환할 때 그들이 그리워집니다. 코드를 더 멋지게 표시하는 것을 넘어, 수많은 오류를 방지할 수 있습니다. 저에게는 필수입니다.
- 선택 사항: Husky: 커밋마다 일부 린팅을 실행하는 것이 멋집니다
- 선택 사항: Commitlint: 메시지 규칙

## 코드 품질 관행

- React 오류 및 경고를 신속하게 인식하고 고치세요: 그들을 무시하면 앱이 언젠가는 충돌합니다. 빨리 수정하는 것이 좋으므로 콘솔에서 무슨 일이 일어나는지 주의하세요. 또한 해결책을 Google에서만 찾지 말고 학습 기회를 활용하세요.
- 컴포넌트를 작게 유지하세요: React는 코드를 컴포넌트화하는 것을 가능하게 하는 프레임워크입니다. 이를 활용하세요! 제 경험상 이상적인 컴포넌트 크기는 200줄 미만입니다. 300줄인 컴포넌트는 특별한 경우에만 허용됩니다. 그 이상은 큰 컴포넌트로 간주됩니다.
  큰 컴포넌트는 읽기, 리팩터링, 테스트하기 어렵습니다. 컴포넌트 크기가 그 크기를 초과하면, 두 개 이상의 컴포넌트로 리팩터링하고 사용자 정의 훅을 사용하세요.
- 지속적 통합(CI)을 설정하여 푸시할 때마다 테스트를 실행하세요
- 트렁크 기반 개발을 구현하고 브랜치 지옥을 피하세요

## 개발자 경험

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

- 절대 경로 임포트: 우리는 "../../../utils" 대신에 "@utils"를 쓰는 것이 더 좋다고 인정합시다.
- kebab-case 파일 이름: 만약 당신이나 여러분의 팀원 중 일부가 다른 OS에서 작업한다면, 문제가 발생할 수 있습니다. 맥은 대소문자를 무시하지만, 윈도우와 리눅스는 그렇지 않기 때문입니다.

![이미지](/assets/img/2024-06-19-ReactDontLetYourCodeBecomeaMonsterEssentialHygienePracticesin2024_0.png)

- npm, pnpm, bun, 그리고 yarn에 대해: 각각이 좋은 대안이 될 수 있습니다. 그러나, pnpm과 bun이 호환성 문제를 겪을 수 있다는 것을 발견했고, 반면에 yarn이 npm보다 속도가 빠른 경우가 많습니다. 그래서 나는 yarn을 선택할 것입니다.
- VS Code & 스니펫: 많은 코드 에디터를 사용해봤지만, 단언컨대 승자는 VS Code입니다. 모든 OS와 매우 호환성이 뛰어나며, 아주 아주 빠릅니다. 멋진 UI를 가졌으며, 커스터마이징 기능이 훌륭합니다. 오토세이브 시 어떤 작업을 수행할지 구성하는 것이 정말 대단합니다. 나는 린팅 및 임포트 정리를 선호합니다. 스니펫에 대해, 나는 TypeScript React 컴포넌트를 만들기 위한 사용자 정의 스니펫과 Custom Hooks를 위한 다른 하나를 가지고 있습니다.
  가장 좋아하는 확장 프로그램 중 일부는 ESLint, Prettier, Tailwind CSS, GitLens, Vitest, React Refactor, Error Lens입니다.
- 스크럼: 아마도, 다음 스토리는 스크럼에 관한 것일 것입니다. 제가 몇 년 경험 중 본 좋고 나쁜 실천법에 대해 이야기하고 싶습니다. 그러나 대부분의 프로젝트에서 동작하는 몇 가지 방법에 대해 말씀드릴 것입니다:

## 결론

<!-- ui-station 사각형 -->

<ins class="adsbygoogle"
style="display:block"
data-ad-client="ca-pub-4877378276818686"
data-ad-slot="7249294152"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>

이 React 스택은 개발자 행복과 빠른 앱, 그리고 깔끔함을 중요시합니다. Vite는 빠르고 TypeScript는 코드를 견고하게 유지하며, Tailwind CSS는 쉽게 스타일을 입힐 수 있고, Zustand는 어플리케이션 상태를 예측할 수 있는 방법을 제공합니다. 하지만 당신이 선택하는 개발 방법이 정말 중요합니다. 이것들이 성공적인 프로젝트와 평범한 프로젝트를 구분짓는 요소가 될 것입니다.
그리고 기억하세요, 이것들은 일부 제안일 뿐입니다! 최고의 도구는 당신이 무엇을 구축하느냐에 따라 다릅니다. 그래서 자신을 가장 멋지게 느끼게 하는 것을 선택하세요.

내가 알아야할 다른 라이브러리나 방법을 사용하고 있나요?

당신의 의견을 듣고 있어요.

더 알고 싶으세요? 도와줄 필요가 있으세요? 문의해주세요!
www.quadiontech.com
