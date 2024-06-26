---
title: "비즈니스를 보호하기 위해 3가지 주요 LLM 보안 위협 완화하기"
description: ""
coverImage: "/assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_0.png"
date: 2024-05-18 21:02
ogImage:
  url: /assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_0.png
tag: Tech
originalTitle: "Mitigate 3 Major LLM Security Threats to Protect Your Business"
link: "https://medium.com/generative-ai/mitigate-3-major-llm-security-threats-to-protect-your-business-99be786a2c89"
---

![Image](/assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_0.png)

요즘 대형 언어 모델(LLMs)은 데이터 엔지니어링의 발전으로 그 발전을 이룩했으며, 데이터 분석 및 모델 생성의 기본 개념은 오랫동안 존재해왔습니다. 그럼에도, GPT-3와 같은 LLMs의 대중적인 채택은 이 기술들을 대중과 전문가들의 주요 관심사로 끌어올렸습니다.

이 통합은 스마트폰의 AI 번역 기능이나 온라인 회의 응용프로그램에서의 가상 비서와 같은 다양한 소비자 제품에서 명백하게 볼 수 있습니다.

LLM 채택에 대한 토의는 종종 잠재적인 직업 저해를 중점으로 하지만, 보안 전문가들은 이런 혁신 기술들과 함께 새로운 보안 취약점의 출현을 강조하고 있습니다.

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

<img src="/assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_1.png" />

요즘 빠르게 진행되는 기술 통합은 LLM의 보안 영향에 대한 중요한 우려를 불러일으킵니다. 오늘날, AI 통합의 널리 사용으로 인해 나타나는 주요 문제 및 공격 벡터를 검토하는 것이 중요합니다.

비즈니스가 LLM 보안 문제에 대해 보다 정보화되고 준비되도록 돕기 위해 저희는 Master of Code Global의 Application Security Leader인 Anhelina Biliak에게 몇 가지 통찰을 공유해 달라고 요청했습니다. 이 기사에서는 비즈니스가 대응 가능한 완화 전략과 함께 주요 LLM 취약성을 탐색할 것입니다.

# #1. 민감한 데이터 노출

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

LLM(언어 모델)을 사이버 보안 워크플로에 통합하는 것은 생산성 향상을 약속하며, 루틴 작업을 자동화하고 문제 식별을 돕고, 심지어 보고서 작성을 지원합니다. 이러한 시스템 내에서 민감한 데이터의 잠재적 노출은 중요한 보안 우려를 불러일으킵니다.

![이미지](/assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_2.png)

중요한 점은 공개 LLM(언어 모델)이 클라이언트 데이터의 직접적인 저장소로 사용되어서는 안 된다는 것을 강조하는 것이 핵심입니다. 이러한 모델은 의도치 않은 유출 가능성을 내포하고 있으며, 민감한 정보가 신중하게 작성된 프롬프트로 추출될 수 있습니다. 대신, 이러한 도구들은 연구 및 취약성 식별을 보조하는 강력한 도구로 취급하는 것이 가장 좋습니다. 프로젝트별 작업에 있어서, 그들은 지원적인 역할로 작용해야 합니다.

또한, LLM(언어 모델) 통합을 갖춘 최근에 출시된 제품도 최종 사용자들의 개인 정보 보호를 고려해야 합니다. 사람들은 여괄적인 응용 프로그램에 여권 번호나 기밀 의료 세부사항과 같은 개인 식별 정보(PII)를 입력할 수 있습니다. 이러한 데이터를 책임 있게 처리하여 잠재적인 도난을 예방하는 것이 중요합니다.

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

# 민감한 데이터 노출을 완화하는 전략

LLM과 상호 작용할 때 기밀 정보를 보호하는 핵심 단계는 다음과 같습니다:

- 견고한 데이터 산화. AI에 사용되는 교육 데이터셋은 민감한 기록의 추적을 제거하기 위해 면밀히 살균되어야 합니다. 개인화된 취약성이나 프로젝트 특정 세부 사항을 포함하지 않도록 주의하십시오.
- 최소 권한 원칙. 공개 LLM을 다룰 때, 필요한 최소한의 권한을 가진 가상의 사용자가 접근할 수 있는 데이터만 전송합니다. 이렇게 하면 모델의 지식이 compromise되었을 때 노출되는 정보를 최소화할 수 있습니다.
- 제어된 외부 접속. LLM이 외부 데이터베이스에 연결하는 능력을 제한하고 데이터 흐름 전체 과정에서 엄격한 접근 제어를 유지합니다.
- 정기적인 지식 감사. 시스템의 지식베이스를 정기적으로 감사하여 기밀 사항이 유지된 경우를 식별하고 해결합니다.

더 읽어보기: LLM에서 환각: 통합 전에 알아야 할 사항

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

# #2. 프롬프트 주입 공격

프롬프트 주입은 LLM(대형 언어 모델)을 이용한 앱에서 또 다른 중대한 문제로 부상하여, OWASP Top 10 등의 순위에서 상위권에 올랐습니다. 이 취약점은 공격자가 전략적으로 설계된 입력을 사용하여 LLM의 동작을 조작할 수 있게 하며, 데이터 유출, 무단 액세스, 시스템 전체의 무결성 침해에 이르기까지 다양한 위협을 초래할 수 있습니다.

![이미지](/assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_3.png)

이 위협 유형을 이해하기 위해서는 프롬프트를 LLM에 제공하는 지침으로 상상해보세요. 공격자는 이러한 지침과 관련 데이터를 신중하게 설계하여 도구가 원래의 프로그래밍을 무시하거나 의도와는 다르게 행동하도록 속일 수 있습니다. 이러한 조작은 현실 세계 응용 프로그램에서 광범위한 영향을 미칠 수 있습니다.

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

최근 삼성 데이터 누출 사건은 빠른 인젝션의 피해 가능성을 보여줍니다. 회사의 ChatGPT 사용 제한은 언어 모델에 의한 민감한 정보 노출 위험을 강조합니다. 이 사례는 LLM 구성 요소로써 다양한 솔루션과 시스템에서 점점 더 보편화되는 공격에 대한 이해와 대비의 중요성을 강조합니다.

# 텍스트를 넘어서: 멀티모달 프롬프트 인젝션

전통적인 텍스트 기반 프롬프트 인젝션에 초점이 맞춰져 있지만, 고급 기술은 계속 발전하고 있는 위협을 제시합니다. 공격자들은 이미지, 음성, 비디오와 같은 모데일티에 대한 인젝션을 통해 기본적인 보호장치를 우회하는 데 성공했습니다:

- 이미지: 이미지 처리 기능을 가진 LLM은 사진 내에 내장된 콘텐츠를 사용하여 속임수를 쓸 수 있습니다. 이는 시각 데이터로 위장된 악의적인 명령을 기존하는 창구를 엽니다.
- 음성: 음성 입력은 최종적으로 텍스트로 변환되므로, 공격자는 음성-텍스트 변환 LLM 구성 요소를 이용해 프롬프트 인젝션을 할 수 있습니다.
- 비디오: 진정한 비디오 기반 모델이 아직 널리 보급되지 않았지만, 비디오를 이미지와 오디오로 분해하는 것은 프롬프트 인젝션이 추후에 위험할 수 있다는 가능성을 시사합니다.

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

## 중요한 프롬프트 주입 방어 전략

성장하는 위협으로부터 AI 애플리케이션을 보호하기 위한 주요 전술을 살펴보겠습니다:

- 컨텍스트 인식형 프롬프트 필터링 및 응답 분석. 사용자 입력과 LLM 응답의 보다 광범위한 컨텍스트를 이해하는 지능적인 필터링 시스템을 통합하세요. 이는 미묘한 조작 시도를 감지하고 차단하는 데 도움이 됩니다.
- LLM 패치 및 방어 세부 조정. 잠재적인 취약점을 해결하고 LLM의 프롬프트 주입 공격에 대한 저항력을 향상시키기 위해 정기적인 업데이트 및 방어 세부 조정을 진행하세요.
- 철저한 입력 유효성 검사 및 살균 프로토콜. 사용자가 제공한 모든 프롬프트를 유효성 검사하고 살균하기 위해 엄격한 프로토콜을 수립하고 시행하세요. 이러한 조치는 악성 콘텐츠를 걸러내어 주입의 위험을 최소화하는 데 도움이 됩니다.
- 포괄적인 LLM 상호 작용 로깅. LLM과의 모든 상호 작용을 추적하기 위한 견고한 로깅 메커니즘을 구현하세요. 이를 통해 프롬프트 주입 시도를 실시간으로 감지하고 분석 및 완화를 위한 유용한 데이터를 제공할 수 있습니다.

## #3. LLM이 전통적인 웹 애플리케이션 취약점을 강화하는 방법

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

LLM을 고객을 대상으로 한 서비스에 통합하는 급발진은 새로운 위협 벡터를 만들어냅니다 - LLM 웹 공격입니다. 사이버 범죄자는 모델이 원래 데이터, API 및 사용자 정보에 액세스할 수 있는 특성을 활용하여 직접 수행할 수 없는 악의적인 조치를 취할 수 있습니다.

이러한 사건은 다음과 같은 목적을 가질 수 있습니다:

- 민감한 데이터 집합 추출. 프롬프트 내의 데이터, 훈련 세트 또는 액세스 가능한 API 내의 데이터를 대상으로 합니다.
- 악의적인 API 동작을 시작. 대규모 언어 모델은 악의적인 SQL 공격과 같은 동작을 수행할 수 있는 우발적인 프록시가 될 수 있습니다.
- 다른 사용자 또는 시스템 공격. 해커는 해당 도구를 사용하고 있는 다른 사람들에 대해 출발점으로서 LLM을 사용할 수 있습니다.

개념적으로, 많은 LLM 기반 공격은 서버 측 요청 위조(SSRF) 취약점과 유사점을 공유합니다. 두 경우 모두, 공격자는 서버 측 구성 요소를 조작하여 직접 액세스할 수 없는 시스템에 대한 사건을 촉진합니다.

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

<img src="/assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_4.png" />

## LLM이 익숙한 취약점을 악용하는 방법

LLM 통합은 새로운 위험 요인을 소개하는 한편, 기존의 웹 및 모바일 애플리케이션 취약점을 새로운 시각에서 새롭게 조명합니다. 사이버 범죄자들은 다음을 표적으로 할 수 있습니다:

- SSRF. LLM을 통해 데이터를 HTTP 요청을 통해 가져오는 경우 취약할 수 있습니다. 공격자들은 내부 호스트를 조사하거나 클라우드 메타데이터 서비스에 접근하여 광범위한 제어를 얻을 수도 있습니다.
- SQL Injection. 데이터베이스와 상호 작용하는 LLM은 입력 정제가 충분하지 않다면 취약할 수 있습니다. 해커들은 임의의 데이터베이스 쿼리를 실행하여 데이터를 훔치거나 수정할 수 있습니다.
- 원격 코드 실행 (RCE). LLM이 사용자가 제공한 코드 스니펫을 수락하고 실행하는 경우, 위협 요소는 악성 코드를 주입하여 기본 시스템을 손상시킬 수 있습니다.
- Cross-Site Scripting (XSS). 인공 지능 도구가 사용자가 입력한 정보에 따라 출력을 표시하는 웹 인터페이스가 있는 경우, XSS 공격 가능성이 있습니다. 사용자들은 세션 데이터나 기타 기밀 정보를 훔치는 악성 스크립트를 받을 수 있습니다.
- 보안되지 않은 직접 개체 참조 (IDOR). LLM이 객체(파일 또는 데이터베이스 레코드 등)와 상호 작용할 때 사용자 입력에 따라, 공격자들은 양적인 권한 없이 객체에 액세스하거나 수정하는 IDOR 결함을 악용할 수 있습니다.

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

이전의 보안 문제와 유사하게, 이러한 증폭된 취약점을 완화하는 것은 견고한 입력 필터링 및 유효성 검사, 제로 트러스트 아키텍처, 최소 권한 원칙 등과 같은 동일한 전략에 의존합니다.

Responsible Generative AI: Limitations, Risks, and Future Directions of Large Language Models (LLMs) Adoption를 참고하세요.

## LLM 보안의 도전과 미래

![이미지](/assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_5.png)

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

LLM 기술은 엄청난 속도로 발전하고 있습니다. 따라서 보안 관행에 적응할 수 있도록 유동적으로 유지하는 것이 중요합니다. 위험을 선제적으로 완화하기 위해 전문가들은 LLM에 특화된 취약점을 이해하고 효과적으로 대처할 수 있는 실용적인 도구가 필요합니다.

대규모 언어 모델의 미래를 안전하게 보호하기 위해 기존 프레임워크를 적응시키고, NLP를 위한 CVE와 같은 취약성 데이터베이스를 확대하며, 명확하고 벤더 중립적인 규제를 개발해야 합니다. 협력, 지속적인 연구, 그리고 선제적 사고 방식은 이러한 진화하는 도전에 대처하기 위한 중요한 열쇠입니다.

마스터 오브 코드 글로벌에서는 보안부서도 문제에 대한 연구를 계속하며, 당사의 AI 프로젝트의 안전을 보장할 수 있는 다양한 접근 방식을 탐색하고 있습니다. 예를 들어, 저희는 솔루션의 침투 테스트를 수행하고 엔지니어들에게 안전한 LLM의 사용과 통합에 대해 교육하는 포괄적인 교육 프로그램을 개발하고 있습니다.

또한 고객의 요구 사항에 따라 응용 프로그램의 가능한 위험 목록을 도입했습니다. 이를 통해 관리자들은 잠재적 위험을 인식하고 이에 대처하기 위한 필요 조치를 마련하여 사용자의 안전을 보장할 수 있습니다. 이는 모든 기업에게 최우선 과제입니다.

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

LLM 보안 우려가 혁신을 막지 못하도록 합시다. 우리와 협력하여 안전하고 믿을 수 있는 AI 애플리케이션을 구축하는 데 우리의 전문지식을 활용해 보세요. 귀하의 정확한 요구 사항을 논의하고 언어 모델의 전체 잠재력을 최대한 발휘하며 안전성을 최우선에 두기 위해 오늘 MOCG에 연락해 주세요.

LLM 보안을 높이고 사이버 위협에 앞서 나가세요. AI 안전성을 향상시키기 위해 준비가 되셨나요? 시작하려면 저희에게 연락하세요!

![이미지](/assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_6.png)

이 이야기는 Generative AI에서 발행됩니다. LinkedIn에서 저희와 연락하고 최신 AI 이야기에 대한 소식을 받아보려면 Zeniteq를 팔로우해 주세요. 함께 AI의 미래를 함께 만들어 봅시다!

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

<img src="/assets/img/2024-05-18-Mitigate3MajorLLMSecurityThreatstoProtectYourBusiness_7.png" />
