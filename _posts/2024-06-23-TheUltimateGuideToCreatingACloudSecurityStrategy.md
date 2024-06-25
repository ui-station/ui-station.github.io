---
title: "클라우드 보안 전략을 만드는 궁극적인 가이드"
description: ""
coverImage: "/assets/img/2024-06-23-TheUltimateGuideToCreatingACloudSecurityStrategy_0.png"
date: 2024-06-23 00:14
ogImage:
  url: /assets/img/2024-06-23-TheUltimateGuideToCreatingACloudSecurityStrategy_0.png
tag: Tech
originalTitle: "The Ultimate Guide To Creating A Cloud Security Strategy"
link: "https://medium.com/bugbountywriteup/the-ultimate-guide-to-creating-a-cloud-security-strategy-755c48faf355"
---

![Cloud Security](/assets/img/2024-06-23-TheUltimateGuideToCreatingACloudSecurityStrategy_0.png)

클라우드 보안은 처음에는 쉽지 않습니다.

저는 지난 20년간 이 산업에서 일한 경험이 있으며, 그 중 마지막 5년은 클라우드에 전념했습니다.

클라우드 보안 여정에서 가장 어려운 단계 중 하나는 클라우드 환경을 보호하기 위한 로드맵을 만드는 것입니다.

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

지난 몇 년 동안 클라우드 및 디지털 채택이 급증했으며, 적절한 로드맵이 없는 사이버 보안 팀은 후속 문제에 직면할 수 있습니다.

CIO들과 CISO들이 앉아서 자신들의 클라우드 워크로드를 안전하게 보호하기 위한 최상의 접근 방식을 논의할 때, 상당한 양의 자료에 물들게 될 것입니다. 그것은 상당히 짜증날 수 있습니다!

다양한 클라우드 구현 경험을 바탕으로, 나는 성공적인 클라우드 보안 구현을 위한 주요 성공 요소가 무엇인지 요약해 보기로 했습니다.

로드맵을 세 가지 기본 단계로 나누었습니다.

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

- 기초
- 실행
- 최적화

참고: 제 경험을 바탕으로 가능한 한 상세하게 작성하려고 노력했지만, 대부분의 회사에 적용하기에 너무 상세해서 실용적이지 않게 만드는 것은 피했습니다.

# 단계 1: 기초 다지기

클라우드 보안 프로젝트가 실패하는 가장 일반적인 이유 중 하나는 CISO가 단순히 온프레미스 모델을 클라우드에 그대로 복사하려는 것 때문입니다.

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

구름의 이해 부족은 매우 강력한 원천 기능이 무시되는 결과를 초래할 수 있습니다. 그러므로 여정을 시작하기 전에 적절한 기초를 마련하는 것이 매우 중요합니다.

다음은 몇 가지 중요한 기본 요소들입니다.

## A. 규정 환경 이해

클라우드 보안 여정을 시작하기 전에, 특정 지리에 대한 규정을 알아야 하는 것이 결정적인 첫 번째 단계입니다.

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

올바르게 처리하지 않으면 권한이 없는 데이터를 이동할 수 있으며 엄격한 규정 위반으로 엄격한 벌금을 부과 받을 수 있습니다.

![이미지](/assets/img/2024-06-23-TheUltimateGuideToCreatingACloudSecurityStrategy_1.png)

특정 국가는 자국 데이터를 자국 이외로 이동할 수 없도록하며 규정 미준수에 대해 엄중한 벌금을 부과합니다.

유익한 점은 대부분의 규정이 보안에 대한 최상의 실천 방법과 겹치기 때문에 적절한 프레임워크를 먼저 마련하면 나중에 작업량이 현저히 줄어듭니다.

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

HIPAA, PCI DSS 또는 SOC 2와 관련하여, 귀하의 법률 부서와 적절한 부문의 dos 및 don`ts를 완전히 숙지하기 위해 상호 협력하는 것이 중요합니다.

매년 종일 감사를 하는 데 지치는 사이버 보안 팀에게 한 가지 놀라운 소식은 대부분의 클라우드 제공업체가 그들을 위해 많은 일을 처리한다는 것입니다.

AWS, Azure 및 Google은 모두 매년 수백 개의 로컬 및 글로벌 인증을 실행하는 여러 제3자 프로그램을 보유하고 있으며, 이러한 인증은 수수료 없이 요청할 수 있습니다.

AWS artifact는 AWS에 대한 수백 개의 보고서에 액세스할 수 있는 예시 중 하나입니다.

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

<img src="/assets/img/2024-06-23-TheUltimateGuideToCreatingACloudSecurityStrategy_2.png" />

## B. 공유 책임 모델 이해

공유 책임 모델은 클라우드에서 무언가를 구현하기 전에 미리 알아둬야 할 가장 중요한 것 중 하나입니다.

클라우드에서 보안은 고객과 클라우드 제공업체가 함께 작업하여 환경을 안전하게 유지해야 하는 공유 책임이 되므로 이를 미리 알아두는 것이 중요합니다.

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

기반이 거의 다 마무리되었지만, 데이터와 애플리케이션에 대한 통제를 시행하여 귀하의 영역이 규정 준수를 하고 있는지 확인해야 합니다.

AWS는 구름의 보안을 책임지고, 귀하는 구름 안에서의 보안을 담당합니다.

![이미지](/assets/img/2024-06-23-TheUltimateGuideToCreatingACloudSecurityStrategy_3.png)

이것은 사용하는 모델에 따라 달라질 수 있습니다 (완전히 관리되는 모델, IaaS 또는 플랫폼 등). 클라우드 제공업체는 귀하가 선택한 모델에 따라 일을 더 맡기거나 적게 맡을 수 있습니다.

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

<img src="/assets/img/2024-06-23-TheUltimateGuideToCreatingACloudSecurityStrategy_4.png" />

## C. 팀을 병렬적으로 강화하세요

CISO이고 클라우드 보안 여정을 시작하는 경우, 팀 내에서 클라우드 기술을 구축하는 것이 중요한 기본 단계입니다.

외부 컨설턴트에만 의존하지 마세요. 그들은 프로젝트가 끝나면 대개 떠나가고 내부 팀은 매일 운영을 맡게 됩니다.

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

팀원들은 투자가 이루어지고 있기 때문에 이를 신뢰의 표시로 인식할 것입니다.

# 단계 2: 클라우드 보안

이제 클라우드에 대한 견고한 기본 지식과 규제 승인(희망적으로!)이 확보되었으니, 클라우드 환경을 안전하게 유지하는 방법을 살펴볼 차례입니다.

말씀드린대로, 온프렘에서 사용 중인 도구 세트를 그대로 복사하려고 하지 마세요. 항상 네이티브 클라우드 서비스를 먼저 사용하려고 노력해야 합니다.

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

이 단계는 팀에서 가장 많은 노력과 스트레스를 유발할 수 있는 단계 중 하나입니다.

이 단계에서 가장 중요한 두 가지는 벤치마킹과 클라우드 보안 모델을 만드는 것입니다.

## A. 벤치마킹

클라우드에서 보안 상태를 즉시 파악하는 가장 좋고 빠른 방법은 보안 모베스트 프랙티스에 대한 벤치마킹을 활성화하는 것입니다.

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

좋은 소식은, 구글, Azure 및 AWS와 같은 공급 업체가 이미 사전 구성된 벤치마크를 제공하여 환경을 측정할 수 있습니다.

첫 날부터 CIS 벤치마크를 활성화하여 클라우드 내에서 쉽고 빠른 보안 성과를 얻는다면, CISO를 기쁘게 만들 수 있는 좋은 방법일 것입니다.

아래는 각 메이저 프로바이더에 대한 도구입니다:

- AWS Security Hub
- Azure Security Center (Microsoft Defender로 변경됨)
- Google 규정 준수 센터

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

그 외에도, 예산이 있는 경우에는 가시성을 얻을 수 있는 제3자 도구가 있습니다.

참고: 1일차의 높음 / 중간 수의 갯수에 대해 걱정하지 마세요. 모든 환경에 정상입니다. 하지만 아무것도 놓치지 않도록 위험 추적이 프로세스로 구현되어 있는지 확인해 주세요.

## B. 클라우드 보안 모델 구축하기

벤치마크가 활성화되었다면, 이제는 환경을 위한 고수준 보안 프레임워크를 구현하는 시기입니다. 아래는 중점을 두어야 하는 주요 영역입니다:

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

- 신원 제어: 클라우드에서의 신원은 방화벽입니다. 그래서 이를 우선으로 생각해야 합니다. 그냥 MFA를 활성화하고 끝내지 마세요. 대신 신원을 위한 적절한 보안 생태계를 구축해야 합니다. 단일 사인온 시스템과 연결하여 클라우드에서 별도의 신원 집합을 관리할 필요가 없도록 하는 것이 가장 좋은 방법입니다.

- 암호화: 암호화 제어는 클라우드로 들어가는 PCI, PII와 같은 민감한 데이터를 어떤 규정에 맞춰야 하는지에 따라 다를 것입니다. 데이터가 정지 상태에 있을 때와 전송 중에 대한 암호화 제어를 알아두세요. AWS 및 다른 클라우드 제공업체는 암호화 키를 다루는 뛰어난 관리 서비스를 제공하며, 이를 통해 내부에서 HSM을 관리하는 번거로움을 줄일 수 있습니다.

- 로깅 및 경보: 클라우드에서 로깅 및 경보를 지나치게 하는 것은 매우 쉽습니다. 경보를 너무 적게 만들면 중요한 데이터를 놓치게 될 수 있고, 너무 많이 만들면 대응팀을 분주하게 만들어 경보 피로를 야기할 수 있습니다. 좋은 점은 이미 벤치마킹을 활성화했다면, 이러한 항목 중 많은 것을 경보로 변환하고 해당 사항을 추가하기만 하면 됩니다.

- 작업 부하 보호: 클라우드 워크로드를 실행할 때 VM, 컨테이너 및 클러스터를 보호하고 안전하게 유지해야 합니다. VM은 안전한 이미지에서 시작해야 합니다. 컨테이너 이미지는 시작하기 전에 검사돼야 하며, 실행 시 보호는 전반적으로 제공되어야 합니다. 클라우드를 위한 최소 요구 사항으로 만드세요.

- 위협 인텔리전스: 클라우드에서 가장 멋진 점 중 하나는 클라우드 제공업체 덕분에 얼마나 많은 위협 인텔리전스에 접근할 수 있는지입니다. Azure, Google 및 AWS는 고객이 혜택을 받는 위협 인텔리전스 기술에 수십억을 투자하고 있습니다. 이 데이터는 클라우드 서비스에 공급되어 공격의 조기 탐지를 가능케 합니다. 서비스는 초기에 활성화하여 첫날부터 학습을 시작하고 사전에 조치를 취할 수 있는 기준 값을 생성할 수 있도록 하세요.

# 단계 3: 클라우드 최적화

이 단계는 클라우드 제어에 대한 자신감을 키워나갈 때이며, 더 전략적인 작업에 집중할 수 있는 시기입니다. 이 단계에서 살펴볼 몇 가지 주요 영역은 아래와 같습니다:

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

- 알림을 자동으로 해결하도록 설정하여 보안 팀이 더 생산적으로 작업에 집중할 수 있도록 합니다.
- 기존 알림 논리를 세부 조정하면 무엇이 작동하고 무엇이 그렇지 않은지 알 수 있습니다.
- 이전 단계에서 부여된 클라우드 권한을 정리합니다. 지금쯤 누가 어떤 권한이 필요한지 알 수 있고 그에 맞게 조정할 수 있습니다.
- Slack과 같은 협업 도구를 통해 도구 세트를 확장하면 보안 프로세스의 효율성을 크게 높일 수 있으며, 이메일 문화에서 벗어날 수 있습니다.

## A. 리스크 검토

첫 날부터 리스크 추적기를 유지해야했지만, 이제는 리스크 데이터베이스를 신중히 살펴보고 무엇을 유지할지와 관리부서가 수용해야 하는 것을 결정해야 합니다. 실용적으로 생각하고 완벽한 100% 완료된 리스크 추적기를 얻을 수 없음을 깨닫는 것이 중요합니다.

수정할 수 있는 것은 추적하고, 수정할 수 있는 것은 닫아야 합니다.

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

그것은 주요 단계를 마무리하고 성공적인 클라우드 보안 여정으로 나아가게 도와줍니다.

더 많은 세부 정보를 원하시면, 아래에서 만든 비디오를 확인해보세요.

![이미지](/assets/img/2024-06-23-TheUltimateGuideToCreatingACloudSecurityStrategy_5.png)

타이머 이즈랄은 핀테크 산업에서 사이버 보안 및 IT 리스크 관리 분야에서 20년 이상의 국제적 경험을 보유한 다중 수상 경력을 지닌 정보 보안 리더입니다. 타이머에게는 링크드인이나 그의 유튜브 채널 "클라우드 보안 가이"에서 연락할 수 있으며, 거기에서는 클라우드 보안, 인공지능 및 일반적인 사이버 보안 진로에 관한 조언을 정기적으로 게시하고 있습니다.
