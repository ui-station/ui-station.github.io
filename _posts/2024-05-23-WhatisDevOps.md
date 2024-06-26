---
title: "데브옵스DevOps란 무엇인가요"
description: ""
coverImage: "/assets/img/2024-05-23-WhatisDevOps_0.png"
date: 2024-05-23 14:37
ogImage:
  url: /assets/img/2024-05-23-WhatisDevOps_0.png
tag: Tech
originalTitle: "What is DevOps?"
link: "https://medium.com/@patrick.conte/what-is-devops-a5c43df9f367"
---

## 시스템 엔지니어의 여정과 관점

![이미지](/assets/img/2024-05-23-WhatisDevOps_0.png)

# 소개

데브옵스란 무엇일까요? 그 질문은 특정 IT 분야에 뛰어들기 전에 스스로에게 묻고 진정한 의미를 이해하기 전까지 기억하는 질문이었습니다.

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

저는 멜버른, 호주에서 열린 VMware 사용자 회의에 참석했던 때를 떠올립니다. 여러 다른 VMware 애호가들과 함께 있었는데, VMware 및 협력사들이 개발 중인 흥미로운 제품들에 대해 더 알아보기 위해 열정적으로 참석했었습니다. 발표를 하는 신사의 이름은 KevOps로 매우 재미있는 이름이었다고 기억해요.

당시 시스템 엔지니어로서, 저는 데브옵스를 다음 단계로 여겼습니다. 상위 수준에서는 저에게 코드와 관련된 부분이라는 것은 알고 있었지만, 그 본질 자체가 이해되지 않아 솔직히 겁이 났어요. 처음으로 데브옵스를 이해할 때, 강력한 개발자여야 하고 높은 수준의 코딩 경험이 필요하다는 것을 이해했어요. 하지만 이것은 제가 잘하지 않았고 이 간극을 메우는 것은 불가능하다고 생각했어요. 하지만 더 깊이 파고들수록 더 많은 것을 발견하게 되었고, 결국은 저 스스로가 오르기 어려운 산처럼 보이던 그 직무까지 수행하고 있는 자신을 발견했습니다.

# 데브옵스의 간략한 역사

데브옵스가 무엇인지 알아가기 전에, 과거로 돌아가서 문제 상황을 살펴보겠습니다.

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

2007년 경, 개발 및 운영 팀이 산업 전반에 걸쳐 함께 일하는 방식에 문제가 있습니다. 서로 분리되어 있고 서로의 다음 목표가 일치하지 않는 경우가 많습니다. 개발자들은 운영팀이 서버를 관리해야 하는 많은 경쟁 우선 순위로 인해 배포를 제때에 할 수 없습니다. 이 모델로는 누구도 이길 수 없는 상황입니다.

여기에서 DevOps 용어가 만들어지고, 제가 소개할 때 이 운동이 가속화되기 시작했습니다. 저의 겸손한 의견으로는 IT 산업의 일부 분야를 혁신적으로 변화시키기 위해 필요한 문제 해결을 단호히 요청하는 움직임입니다.

# 그러면, DevOps란 무엇일까요?

우선, DevOps는 한 문장이나 진술로 설명할 수 없습니다. 사실, 이것은 여러 요소나 측면을 갖고 있는 것입니다. 어떤 렌즈를 통해 보느냐에 따라 달라지는 정의가 있습니다.

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

DevOps를 설명하는 것은 다음과 같을 수 있어요:

- 문화
- 프레임워크
- 기술적 방법론
- 엔지니어 유형

각각의 요소는 관점과 전문성에 따라 정의될 수 있어요. 이는 Agile이나 ITIL과 유사한데, 이러한 주제에 대한 이해는 당신의 역할과 책임에 따라 정말 중요해질 거예요.

집과 같이, DevOps는 강력한 기초 위에 구축되어 구조를 구현하고 마무리를 지어나갈 거예요. 아래 다이어그램은 DevOps가 문화적 수준에서 시작하여 기술을 살아 숨쉬게 만들어가는 과정을 도와줍니다:

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

![이미지](/assets/img/2024-05-23-WhatisDevOps_1.png)

# 데브옵스란 문화

현재 GenAI가 핫한 주제인 가운데, ChatGPT 또는 Gemini와 같은 대형 언어 모델(Large Language Models, LLM)에 대해 익숙할 것입니다. ChatGPT에게 데브옵스란 무엇인지 물어보면 대략 이와 같은 답을 얻을 수 있습니다:

이 정의는 데브옵스의 문화적 측면을 잘 요약한 것입니다. 데브옵스는 개발팀과 운영팀 간의 장벽을 허물고 상호 유익한 관계를 구축하기 위해 함께 뭉치는 것을 목표로 합니다.

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

DevOps가 추구하는 문화 중 일부 맨트는 다음과 같습니다:

- 수동 및 번거로운 작업을 없애겠습니다.
- 자동화를 촉진하여 효율성과 민첩성을 높이겠습니다.
- 일관성을 위해 반복 가능한 패턴을 사용하겠습니다.
- 능력과 숙련도를 향상시키기 위해 협력을 촉진하겠습니다.
- 우리는 복잡함 대신 단숨함을 향한 태도로 새로운 도전에 대응하고 적응할 것입니다.

DevOps 모델을 준수하는 문화의 예를 살펴보겠습니다.

## DevOps 모델을 준수하지 않는 경우

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

운영팀:

- 소프트웨어 업데이트 설치와 같은 작업은 수동으로 처리됩니다.
- 반복 작업이 자동화되지 않습니다.
- 새로운 인프라 요청은 수동으로 처리됩니다.
- 소유 애플리케이션을 위한 개발자 코드는 수동으로 배포됩니다.
- 월간 주기 내에 작업을 완료하는 것에 대한 경쟁 우선순위가 있습니다.
- 작업은 선형적인 프로세스를 따르지 않으며 인간 에러의 영향을 받습니다.

개발팀:

- 운영팀에 크게 의존하므로 소유 애플리케이션 배포에 상당한 지연이 있습니다.
- 일부 배포를 적시에 테스트할 수 없습니다.
- 인프라에 대한 이해가 없거나, 있더라도 인프라에 액세스할 수 없습니다.
- 운영팀으로부터의 번거로운 피드백 루프로 배포 문제를 강조합니다.

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

DevOps가 해결하는 것을 정말로 이해하려면 어떤 나쁜 환경이 무엇인지 이해해야 합니다. 난 이상한 걸 말했나? 오늘도 이런 일이 발생하고 있고 DevOps를 도입해도 모든 문제가 해결되는 것은 아닙니다. 그러나 위의 예에서 보면 타임라인과 배달 속도에 많은 도움이 될 것입니다.

## DevOps 모델을 따른다

DevOps 팀:

- 인프라, 코드, 자동화 및 빌드 기술 간의 다양한 기술을 보유하고 있습니다.
- 문제를 검토하고 자동화를 통해 최선의 해결 방법을 한 번 찾은 후, 향후에도 그 자동화를 의존하여 자가 치유 및 수정합니다.
- 환경에 도입되는 변경 사항에 대해 패턴 기반 접근 방식을 사용하는 엄격한 프로세스를 따릅니다.
- 가능한 한 많은 수동 작업을 사용하지 않습니다.
- 변경 사항이 작고 점진적입니다.
- 피드백 루프가 강하며 팀원과의 관계를 구축하는 데 도움이 됩니다.

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

이 두 예시를 옆에 놓을 때, 개선 사항이 분명합니다. 이러한 사고 방식에 노출되었을 때, 여러분은 진정으로 데브옵스에서 무엇을 얻을 수 있는지 그 가치를 온전히 이해하게 됩니다.

데브옵스가 전혀 적용되지 않은 예시에서, 주요 키워드는 명확해야 합니다. 데브옵스의 본질은 가능한 만큼 많은 일을 자동화하는 것입니다. 그렇지만 모든 것을 자동화할 수는 없다는 점을 명심해야 합니다. 데브옵스는 여정입니다. 즉, 데브옵스 방법을 사용할 준비가 된 환경과 그렇지 않은 환경 사이에 명확한 구분을 가져야 합니다.

데브옵스로의 전환을 위해 가장 중요한 요소는 문화입니다. 우리는 이를 받아들이고 온전히 수용해야 합니다. 문화가 없으면 우리는 단일한 토대나 공동의 기반을 갖지 못합니다. 데브옵스는 협업에 관한 것이며 개인이 성공하는 것이 아니라 팀이 성공하는 것임을 이해하는 것입니다. 약속을 견고하게 지키는 것이 무엇을 뜻하며, 데브옵스가 무엇을 의미하며 그 방법론이 우리에게 보여줄 수 있는 것에 대해 말하는 속담을 들어보셨을 것입니다.

문화에 동의한 후, 나머지는 바닥 규칙이 마련된 후에 쉽게 자리에 맞게 되는 것입니다.

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

# DevOps 프레임워크

DevOps를 프레임워크로 살펴볼 때, 우리는 더 많은 업무 방식을 고려합니다. 이는 다음을 포함합니다:

- 팀에게 DevOps가 의미하는 것은 무엇인가요?
- 이해해야 할 DevOps의 하위 개념은 무엇인가요?
- 우리 팀의 모든 사람이 성공을 위해 어떻게 설정되었는지 확인하는 방법은 무엇인가요?

이것들은 우리가 스스로 묻는 많은 다른 질문들 중 일부입니다. 이 프레임워크는 우리가 성공을 달성하고 현저한 혜택을 얻으면서 의미 있는 결과물을 제공하는 방식을 정의할 수 있도록 해줍니다.

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

오랜만에 만나서 반가워요!

데브옵스의 각 측면이 전문성을 갖고 있는 내용을 간단히 살펴보겠습니다:

![DevOps Expertise](/assets/img/2024-05-23-WhatisDevOps_2.png)

이것은 데브옵스 프레임워크 내에서 흔히 사용되는 일부 주요 개념을 샘플링한 것입니다. 물론 다른 것도 있지만, 여기서는 주요 개념을 단순히 설명하는 데에 초점을 맞추고 있습니다. 문화로서의 데브옵스 섹션과 마찬가지로 서로 유익한 전문분야를 식별하기 시작할 수 있을 겁니다.

본질적으로, 데브옵스는 이러한 다양한 전문분야를 결합하여 인프라 솔루션 및 애플리케이션을 배포하고 관리하며 유지보수하기 위한 목적을 달성하려는 프레임워크입니다. 이를 이루는 방법은 다음과 같습니다:

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

- SCM(소프트웨어 구성 관리) 및 CI/CD(지속적 통합/지속적 배포) 프로세스를 결합하여 인프라 기반 솔루션을 빌드하고 배포합니다. 보통 이를 GitOps 모델이라고 합니다.
- 정기적으로 반복되는 수동 작업을 자동화합니다.
- 수요에 따라 확장할 수 있는 용량이 탄탄하고 오류 허용이 가능한 무상태 아키텍처 개념을 활용합니다.
- 애플리케이션 변경에 반응하는 이벤트-주도 아키텍처를 생성합니다.
- 애자일성과 빠른 전달 속도를 향상시키기 위해 잘 알려진 패턴을 재사용합니다.

# 기술적 접근으로서의 DevOps

DevOps의 기술 세부 정보 및 기술 요소로 진입하기 전에, 직접 경험한 현실 시나리오를 한번 살펴보고 싶습니다.

2017년 특정 환경에서, DevOps에서 언급된 것과 유사한 수동 작업을 수행했던 기억이 나네요. DevOps 모델을 따르지 않았던 윈도우 중심의 환경이었고, 가상 환경으로 VMware를 사용했습니다. 서버는 SCCM을 사용하여 수동으로 패치되었습니다. 이 프로세스는 다음과 같았습니다:

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

- 운영팀 구성원이 목록에서 서버를 손에 든다.
- 관리 액세스를 사용하여 해당 서버에 로그인한다.
- SCCM 클라이언트를 시작한다.
- 업데이트를 설치한다.
- 다시 부팅한다.

특히 해당 방식으로 패치해야 하는 여러 서버가 있는 경우에는 번거로운 프로세스였습니다. 이 모든 것의 귀하의 유일한 상처는? 이것이 월간 행사였다는 사실입니다.

이 예제는 DevOps가 개발 운영 측면에서 존재하기 전의 생활을 보여줍니다. 이는 환경을 유지하고 “불을 켜 있는” 과제 중 하나로써 정기적으로 수행되어야 하는 많은 작업 중 하나이기도 합니다. DevOps가 제공할 수 있는 가치를 실감하게 되면, 정기적으로 수동 작업을 수행하는 것은 시간의 대조적인 투자 수익이 매우 적은 것처럼 느껴집니다.

기술적 접근 방법으로서의 DevOps에 관한 것은, 이제 다양한 작업 및 동작을 수행하는 데 사용되는 실제 도구에 대해 깊게 들어가 시작합니다.

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

아래는 익숙할지도 모르는 몇 가지 개념과 각각의 응용 프로그램 목록입니다:

![이미지](/assets/img/2024-05-23-WhatisDevOps_3.png)

이것은 그림을 보여주기 위한 목록 샘플만을 제시한 것입니다.

이 도구들의 조합을 사용하여 환경을 운영하면, 플랫폼과 관련된 솔루션(대규모/확장된 환경에서 플랫폼을 지원)이나 특정 응용 프로그램을 구축할 수 있습니다. 이 다이어그램은 이러한 개념과 응용 프로그램을 실제 시나리오에 어떻게 적용할 수 있는지 보여줍니다:

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

![image](/assets/img/2024-05-23-WhatisDevOps_4.png)

**포인트 1 - 코드는 GitHub에 저장됩니다**

이를 통해 우리는:

- 전체 팀이 사용할 일관된 진실의 원천을 적절히 유지할 수 있습니다.
- 코드베이스의 버전을 생성하여 개발, 테스트, 프로덕션 등 다양한 환경으로 단계적이고 제어된 배포를 할 수 있습니다.
- 동료들로부터 제안된 변경 사항을 코드베이스에 도입하려고 하는 경우 동료들의 리뷰 및 승인 프로세스를 거치도록 합니다.
- 자동 배포를 위해 Buildkite와 통합합니다.

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

제 2점 — Terraform을 사용한 IaC

Terraform은 AWS에서 자원을 생성/대체/업데이트/삭제(CRUD)하는 데 사용됩니다. Github 및 Buildkite와 함께 Terraform을 사용하여 GitOps 모델을 구현함으로써 배포를 처리할 수 있습니다. 이는 다음을 보장합니다:

- 자원이 일관된 방식으로 배포됩니다.
- 빌드카이트만을 통해 Terraform을 사용하여 배포를 수행할 수 있도록 하여 이 프로세스에서 벗어나지 않습니다.
- 코드를 반복적으로 사용하여 매번 처음부터 작업하는 대신 개선만 하여 사용하는 DRY(Don’t Repeat Yourself) 모델을 촉진할 수 있습니다.

제 3점 — CI/CD를 위한 Buildkite 사용

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

Buildkite는 검증, 계획 및 배포를 수행하는 CI/CD 플랫폼으로 사용됩니다. Buildkite는 일관성을 유지하기 위해 GitHub를 소스로 사용할 것입니다.

4번 항목 — 배포 전 코드 검증 (CI/CD의 CI)

검증 및 계획 파이프라인이 배포 전에 실행되어 우리가 무엇을 기대해야 하는지 알 수 있습니다.

또한 이 단계를 강화하고 개발 환경에서 모의 배포를 수행할 수도 있습니다. 이렇게 하면 예기치 못한 문제가 도드라지며 접근 방식을 재고하고 조정해야 할 수 있습니다.

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

점 5 — Pull request 동료 리뷰

충분한 증거가 축적되면 동료에게 우리의 pull request를 검토해 달라고 요청할 수 있으며, 모든 게 제대로 된 경우 병합을 진행할 수 있습니다.

협업이 최고이며, DevOps의 문화 측면에서 언급된 대로 동료의 의견을 듣고 항상 배울 수 있고 조정할 수 있는 방법이 항상 있습니다.

점 6 — Pull request 병합 및 배포 (CI/CD의 CD 부분)

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

한 번 pull request가 승인되고 병합되면, 자동화가 실행되어 리소스가 배포됩니다.

GitOps 모델을 활용함으로써, 각 단계는 통제되며 비교적 예측 가능한 작업이어야 합니다. 이 모델을 계속 사용하여 환경에 변경 사항을 계속 적용할 수 있습니다.

DevOps는 당신이 가진 도구를 통해 쉽게 삶을 만드는 방법을 찾는 것입니다.

# 엔지니어로서의 DevOps

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

이전 레이어가 편하게 마련된 상태에서, 우리는 DevOps 하우스의 정상인 곳에 위치하게 되었습니다. DevOps 엔지니어들에게는 하위 레이어들이 강력한 기반을 제공하여 우리가 필요한 작업을 수행하고 전체 모델을 완성하는 데 도움이 됩니다.

그러나 다소 상반된 방식으로, DevOps 엔지니어로 레이블이 지어지는 것이 산업 전반에 깔끔하게 부합되지는 않습니다. 예를 들어, 온프레미스 환경에 DevOps 모델을 적용하는 것에는 문제가 없습니다. 많은 개념적 도구들이 온프레미스 동등물을 갖고 있습니다. 또한 그렇지 않더라도 SaaS(Software as a Service) 기반 제공품을 사용하여 온프레미스 환경에서 배포를 수행할 수 있습니다.

이 분야에서는 대부분 DevOps가 공용 클라우드에만 해당한다고 전체적으로 가정되어 왔지만, 그렇지 않습니다. DevOps 모델이 공용 클라우드에 편안하게 적용되지만, 온프레미스 환경에 적용할 수 있는 에지 케이스 시나리오들도 여전히 있습니다. 이것이 이 섹션의 요지로 이어지면서 누군가를 DevOps 엔지니어로 정의하기가 어렵다는 것을 알 수 있습니다.

기술적 접근 방식 섹션에서는 IaC 및 CI/CD와 같은 개념을 다루었습니다. 이러한 개념을 기반으로 하는 도구들은 산업 전반에서 다양한 형태로 나타날 것입니다. 이는 이러한 개념을 기반으로 하는 (어떤 면에서) 도구의 수가 매우 많기 때문입니다. 예를 들어, 회사 A의 DevOps 엔지니어로는 GitHub (SCM), GitHub Actions (CI/CD) 및 Terraform (IaC) 도구를 사용할 수 있지만, 회사 B의 DevOps 엔지니어로는 BitBucket (SCM), Bamboo (CI/CD) 및 CloudFormation (AWS의 IaC)을 사용할 수 있습니다.

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

공학 수준의 DevOps는 산업 전반에 걸쳐 일관성이 없다는 것을 설명하고 있습니다. 개인적으로, 몇 가지 환경을 경험해 보았는데, 이러한 환경은 DevOps 모델을 활용하지만 정확히 같은 도구를 사용하는 환경은 없습니다. 유사점이 많지만, 완전히 동일한 것은 아닙니다.

또 다른 좋은 예로는 DevOps 모델을 공개 클라우드 공간에 적용할 때를 들 수 있습니다. 비슷한 개념을 가지고 있지만 사용하는 용어 및 개념 적용 방식에서 다소 차이가 있습니다.

이로 인해 누군가를 DevOps 엔지니어로 규정하는 것이 어렵습니다. 실제로, DevOps 엔지니어 역할을 볼 때, 해당 회사와 해당 환경에 대한 역할일 것입니다. 여러 클라우드 및 여러 다른 기술 스택에 대해 이야기할 때 플랫폼 엔지니어가 관련될 수 있다는 주장도 나올 수 있습니다. 그럼에도 불구하고, DevOps 엔지니어라는 용어는 계속 사용되고, 이와 관련된 일정 수준의 모호성이 예상된다는 가정이 있습니다.

# 결론

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

어떠한 에셋을 가져오거나 동적으로 추가할 때 table 태그를 Markdown 형식으로 변경하세요.
