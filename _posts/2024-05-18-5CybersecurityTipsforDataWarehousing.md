---
title: "데이터 웨어하우징을 위한 5가지 사이버보안 팁"
description: ""
coverImage: "/assets/img/2024-05-18-5CybersecurityTipsforDataWarehousing_0.png"
date: 2024-05-18 18:08
ogImage:
  url: /assets/img/2024-05-18-5CybersecurityTipsforDataWarehousing_0.png
tag: Tech
originalTitle: "5 Cybersecurity Tips for Data Warehousing"
link: "https://medium.com/@odsc/5-cybersecurity-tips-for-data-warehousing-ea8ca94df084"
---

<img src="/assets/img/2024-05-18-5CybersecurityTipsforDataWarehousing_0.png" />

데이터 웨어하우징은 대규모 AI 및 기계 학습 애플리케이션을 훨씬 더 관리하기 쉽게 만듭니다. 모든 것을 한 곳에 가지고 있으면 더 빠르고 정확한 분석이 가능해지지만, 동시에 일부 보안 문제를 야기할 수도 있습니다. 이러한 대규모로 통합된 데이터베이스는 사이버 범죄자들에게 유혹이 되는 대상이므로 면밀한 보호가 필요합니다.

조직 간에도 데이터 웨어하우스 자체가 다양하듯이 특정 보안 시스템도 다양합니다. 그럼에도 불구하고 설정과는 상관없이 몇 가지 모범 사례를 도입해야 합니다. 고려해야 할 다섯 가지 주요 사이버 보안 팁을 소개합니다.

# 1. 데이터 익명화 및 암호화

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

데이터 창고에서 모든 데이터를 암호화하는 것이 첫 번째 단계입니다. 데이터에 높은 암호화 표준을 적용하면, 해커들이 액세스하더라도 그것이 쓸모없게 만들어질 것입니다. 홀모모르픽 암호화와 같은 새로운 기술은 당신이 데이터를 복호화하기 전에도 암호화된 데이터를 사용할 수 있도록 한 단계 더 나아간 것입니다.

사용하는 데이터 유형에 따라 데이터를 익명화해야 할 수도 있습니다. 이는 개인 식별자를 제거하여 개인 정보 침해를 방지하는 프로세스입니다. 실제 세계의 수치를 합성 데이터로 교체하는 것이 가장 안전한 방법이지만, 데이터가 실제 세계 사람들을 반영해야 하는 경우 역동적 익명화가 좋은 대안입니다.

## 2. 액세스 권한 제한

데이터 창고 사이버 보안의 다음 단계는 사용자의 액세스 권한을 제한하는 것입니다. 이 작업에 접근하는 가장 좋은 방법은 최소 권한 원칙(Least Privilege Principle, PoLP)을 따르는 것입니다.

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

PoLP(Principle of Least Privilege)는 작업을 올바르게 수행하기 위해 필요한 것만 액세스할 수 있어야 한다고 주장합니다. 기계 학습 모델과 작업하지 않는 직원은 기계 학습 훈련을 위해 구체적으로 데이터 웨어하우스에 액세스할 수 없어야 합니다. 마찬가지로, 데이터 과학자는 급여 데이터를 볼 수 없어야 합니다.

액세스 권한 제한은 두 가지 주요 이점이 있습니다. 첫째, 주어진 데이터 웨어하우스에 영향을 미칠 수 있는 사람 수를 줄임으로써 발생하는 74%의 데이터 침해와 관련된 인적 오류를 최소화합니다. 둘째, 공격자가 한 계정을 침해하면 측면 이동을 최소화합니다.

# 3. 인증 조치 향상

권한 제한은 신뢰할 수 있는 방법으로 누가 누구인지 판별할 수 있을 때에만 효과가 있음을 기억하세요. 따라서, PoLP를 강력한 인증 조치와 함께 실행해야 합니다. 가장 기본적인 수준에서는 다중 요소 인증(MFA)이 시행되어야 합니다.

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

MFA는 여러 방법으로 실행할 수 있지만, 모든 방법이 동일한 수준의 보안을 제공하는 것은 아닙니다. 예를 들어 SMS 기반 인증은 이메일 인증보다 더 안전합니다. 특정 장치에 액세스가 필요하기 때문입니다. 생체 인증은 암호보다 해킹이 더 어려울 수 있지만, 공격자가 생체 데이터에 액세스하면 변경할 수 없으므로, 민감한 창고에는 이상적이지 않을 수 있습니다.

# 4. 데이터 분류 및 조직화

데이터 웨어하우징 보안에서 놓치기 쉬운 하지만 여전히 중요한 단계는 데이터를 분류하는 것입니다. 조직화는 사이버 보안 문제보다는 작업 문제처럼 보일 수 있지만, 중요한 보안적 영향을 많이 미칩니다.

먼저, 볼 수 없는 것은 안전으로 보호할 수 없습니다. 보안 소프트웨어 사용자의 약 60%가 데이터의 40% 미만만 분석한다고 합니다. 이는 중요한 취약점을 놓칠 수 있거나 침해를 인식하지 못할 수 있음을 의미합니다. 조직의 부재는 시각성을 제한하기 때문에 데이터를 분류하여 그룹으로 구성하여 보다 철저한 취약점 분석과 빠른 사고 대응을 가능하게 해야 합니다.

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

분류는 엑세스 권한을 정제하는 데도 도움이 됩니다. 데이터를 사용 또는 민감도에 따라 정렬하면 누가 액세스할 수 있는지 결정하고 해당 정책을 시행하는 데 도움이 됩니다. 또한 행동 생체 인식을 구현할 수 있어 이로 인해 평상시에는 액세스할 수 없는 데이터에 접근하는 경우 경고를 받을 수 있습니다.

# 5. 창고를 면밀히 모니터링하십시오

이러한 변경 사항을 시행한 후 데이터 창고를 지속적으로 모니터링해야 합니다. 어떤 방어 기법도 100% 효과적일 수는 없지만, 신속한 대응은 침해 사건 발생 시 피해를 최소화할 것입니다. 새로운 위협에 대응하거나 실시간 보안 사건에 대응할 수 있는 유일한 방법은 지속적인 모니터링을 통해 가능합니다.

인공지능과 자동화는 여기서 꼭 필요합니다. 24시간 수동 모니터링은 많은 보안 인력이 필요합니다. 대부분의 기관에게는 선택사항이 아닙니다. 세계적으로 노동력 수요가 증가하더라도 사이버 보안 직원은 340만 명이 부족합니다. 자동화된 네트워크 모니터링은 실시간 사건 제한 및 부족한 보안 직원을 보완하기 위한 경고를 제공할 수 있습니다.

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

# 데이터 웨어하우징 보안에 대한 주의가 필요합니다

데이터 웨어하우스는 한 대의 큰 데이터베이스를 보호하는 것이 여러 개의 자원에 분산하는 것보다 쉽기 때문에 보안이 개선됩니다. 그러나 동시에, 그 크기 때문에 눈에 띄는 관심을 끄는 경우가 있습니다, 특히 민감한 정보를 저장하는 경우에는 더욱 그렇습니다.

이러한 위험 요소들을 고려하여 데이터 웨어하우징 사이버 보안은 필수적입니다. 기존의 보안 시스템에 다음 다섯 가지 모범 사례를 통합하여 데이터 웨어하우스를 가능한 한 안전하게 유지하십시오.

최초 게시물: OpenDataScience.com

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

OpenDataScience.com에서 데이터 과학 관련 기사를 더 읽어보세요. 초보자부터 고급 수준까지의 튜토리얼과 안내서를 만나보실 수 있습니다! 매주 목요일마다 최신 소식을 받아보고 싶으시다면 여기를 클릭하여 주간 뉴스레터를 구독해보세요. 또한 Ai+ 트레이닝 플랫폼을 통해 언제 어디서든 데이터 과학을 학습할 수 있습니다. ODSC 이벤트에 참석하고 싶으신가요? 다가오는 이벤트에 대해 더 알아보고 싶으시다면 여기를 클릭해주세요.
