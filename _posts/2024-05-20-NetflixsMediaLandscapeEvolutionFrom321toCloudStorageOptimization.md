---
title: "Netflix의 미디어 랜드스케이프 진화 321에서 클라우드 스토리지 최적화로"
description: ""
coverImage: "/assets/img/2024-05-20-NetflixsMediaLandscapeEvolutionFrom321toCloudStorageOptimization_0.png"
date: 2024-05-20 16:50
ogImage:
  url: /assets/img/2024-05-20-NetflixsMediaLandscapeEvolutionFrom321toCloudStorageOptimization_0.png
tag: Tech
originalTitle: "Netflix’s Media Landscape Evolution: From 3–2–1 to Cloud Storage Optimization"
link: "https://medium.com/@netflixtechblog/netflixs-media-landscape-evolution-from-3-2-1-to-cloud-storage-optimization-77e9a19171ed"
---

by Esha Palta Vinay Kawade Ankur Khetrapal Meenakshi Jindal Peijie Hu Dongdong Wu Avinash Dathathri

# 소개

Netflix는 매년 다양한 콘텐츠를 제작합니다. 각 콘텐츠 제작 단계에서 넷플릭스는 다양한 콘텐츠 자산(이미지 시퀀스, 비디오, 텍스트 등)을 다양한 제작에서 확보합니다. 이러한 자산은 나중에 안전하게 Amazon S3 스토리지 서비스에 저장됩니다. 이 데이터의 상당 부분은 제작 중에 일시적으로 액세스되며 해당 콘텐츠 출시 시까지만 사용됩니다. 대부분의 자산은 해당 콘텐츠가 출시될 때까지 활성 또는 '핫' 스토리지 계층에만 유지되는 것이 목적입니다. 이 블로그에서는 사용자 액세스 패턴을 활용하여 스토리지의 효율성과 비용 효과를 스마트하게 최적화하는 방법을 탐색할 것입니다. 본 탐사에서는 다양한 AWS 스토리지 계층에 맞게 맞춤형 아카이브 및 삭제 전략의 비용 효율성을 명확히 검토하는 수명 주기 정책의 비용 분석에 대해 다루어볼 것입니다.

# Netflix의 콘텐츠 제작 및 스토리지 관행 개요

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

콘텐츠 제작의 다이내믹한 환경에서 전통 스튜디오들은 시험된 3/2/1 규칙에 오랜 기간 동참해왔습니다. 이 전략은 최소 두 가지 다른 유형의 미디어에 저장된 원본 카메라 영상과 오디오의 세 개 복사본 유지 및 한 개의 백업을 오프사이트에 보관하는 방식을 포함하고 있습니다.

제작 라이프사이클의 활동적인 부서로 들어가 미디어 저장 및 백업 규모를 이해해보겠습니다. LTO 테이프 백업은 제작 및 후반 제작 라이프사이클 동안 지속적으로 존재합니다.

![Production workflow](/assets/img/2024-05-20-NetflixsMediaLandscapeEvolutionFrom321toCloudStorageOptimization_0.png)

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

한 번 미디어가 카메라와 사운드 녹음기에서 추출되면, 디스크 파일로 변환되어 편집, 사운드 및 음악, 시각 효과 (VFX) 및 이미지 완성을 포함한 각 부서별 도구를 사용하여 조작됩니다. 이 미디어는 이 프로세스의 각 단계와 단계마다 포괄적인 백업 루틴을 거칩니다. 물리적 백업 및 아카이브를 기반으로 한 이 방법은 우연한 삭제, 공급 업체별 오류 및 자연 재해의 잠재적 위험을 줄이기 위해 설계되었습니다. 데이터 손실이 기획 및 촬영 단계에서 상당한 비용 손실로 이어질 수 있음을 명확히 인식하여 이러한 백업 프로세스의 중요성을 보여주고 있습니다.

현대 클라우드 스토리지 시스템의 등장은 스토리지 관행에서 패러다임 전환이라는 것을 알려줍니다. AWS S3와 같은 플랫폼은 11 9 이상의 내구성, 복원력 및 가용성을 자랑하는 높은 내구성을 제공합니다. 이 발전은 Netflix와 같은 미디어 회사가 데이터 아카이빙 및 삭제 정책과 같은 도구를 활용하여 그들의 스토리지 방법론을 재정의할 수 있도록 했습니다. 현장에서 촬영된 카메라와 사운드 시스템에서 캡처된 미디어는 인접한 데이터 센터 시설로부터 직접 Netflix의 클라우드 스토리지로 업로드되며, 백업이 필요하지 않습니다. 이 업로드 이후, 데일리, 편집, 시각 효과 및 이미지 완성과 같은 다양한 단계가 다운로드되어 수정되고, 최종 데이터 버전이 클라우드 스토리지로 전송될 수 있습니다. 이 구조는 콘텐츠 제작에 부합된 추적, 접근, 제어 및 확장성을 용이하게 합니다.

또한, 데이터 수명 주기 정책과 통합함으로써 저장 비용 절감과 데이터 관리를 위한 에너지 수요 감소, 이에 따라 탄소 발자국을 줄일 수 있습니다. 이러한 관행을 실행함으로써 기업은 에너지 소비를 줄이고, 결과적으로 환경 영향을 줄이면서 운영 효율성과 비용 효율성을 높일 수 있습니다.

이러한 전략을 도입하는 조직은 지속가능성에 기여하며, 데이터 성장을 더 효과적으로 관리할 수 있는 민첩성과 준비성을 높일 수 있습니다.

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

# 사용자 액세스 패턴 활용

넷플릭스의 미디어 콘텐츠 Orchestration의 핵심에는 중앙 집중식 자산 관리 플랫폼 (AMP)이 있습니다. 이 강력한 시스템은 제작 및 후기 단계에서 제작된 모든 미디어 자산을 지속하고 발견하는 데 전념되어 있습니다. 이 중앙 집중식 허브는 광범위한 미디어 콘텐츠 라이브러리의 관리와 접근성을 간소화하는 데 도움이 됩니다. 또한 우리는 소중한 통찰력을 추출하고 미디어 자산의 사용 및 액세스 패턴에 대한 상세한 보고서를 생성할 수 있습니다. 이 대국적인 시각은 콘텐츠가 어떻게 활용되는지에 대한 우리의 이해를 높이며, 정보에 기반한 의사 결정을 위한 기초를 마련합니다.

우리의 가설을 검증하기 위해, 발매 후 다양한 간격에서 사용자 및 애플리케이션에 의한 자산 액세스 속도를 조사했습니다. 조사 결과는 의미 있는 것들을 드러내었으며, 연관된 제목이 발매된 후 자산 사용량이 상당히 감소한다는 것을 밝혔습니다. 액세스 패턴의 인사이트:

![](/assets/img/2024-05-20-NetflixsMediaLandscapeEvolutionFrom321toCloudStorageOptimization_1.png)

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

Fif 2: 타이틀 발매 전후 자산 접근 패턴

이 트렌드는 여러 자산 유형에 걸쳐 상당히 일관성있게 발견되었습니다. 런칭 이후 자산에 대한 접근이 드물기 때문에 저장 비용을 최적화할 수 있는 매력적인 기회가 발생합니다. 예를 들어, 자산은 초기 런칭 후 6개월 후에 아카이브로 이동될 수 있습니다. 이 정책은 시간이 지남에 따라 더 많은 데이터를 축적함으로써 더 지능적으로 발전하고 정확도를 키울 수 있도록 설계되었습니다.

런칭 이후 자산에 대한 액세스가 매우 드문 점을 감안하면, 아카이브 준비가 된 자산을 AWS Glacier 스토리지로 이전할 수 있는 중요한 기회가 있습니다. AWS Glacier는 월간 저장 비용의 60% 낮은 비용으로 3-5시간 검색 시간을 제공합니다.

# 기회 규모

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

저희의 가설은 보관 자산을 더 저렴한 저장 공간에 보관한다면 비용 절감 가능성이 매우 크다는 것입니다. 저희의 재무 및 데이터 파트너들은 현재 데이터 규모, 성장률, 그리고 미래 비즈니스 사용 사례를 기반으로 서로 다른 모델과 추정치를 개발하는 데 도움을 주었습니다.

![Fig 3: 미디어 스토리지 비용 분석](/assets/img/2024-05-20-NetflixsMediaLandscapeEvolutionFrom321toCloudStorageOptimization_2.png)

상태 쿼: 계속해서 진행한다면, 예상 비용 부담이 급격히 증가할 것으로 예상됩니다.

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

단기적으로는 사용되지 않는 자산을 보관하여 비용 효율적인 S3 Glacier 유연한 검색 스토리지로 이전하는 것이 50% 이상의 절감을 가져올 것입니다.

장기적으로는 자산과 타이틀 라이프사이클의 다양한 단계(제작, 후기, 출시 이후 단계 등)에서 다양한 유형의 자산에 더 세부적인 라이프사이클 정책을 적용한다면 더 많은 절감을 이룰 수 있습니다.

# 데이터 라이프사이클 전략

데이터의 저장 계층을 결정하는 방법은 무엇일까요? 데이터 라이프사이클 관리의 첫 단계로 사용 패턴에 의존하는 간단한 전략을 선택했습니다. 사용에 근거한 이 접근은 단순하지만 상당한 가치를 제공합니다. 이 전략은 서비스에 프로그램이 출시된 후 특정 기간이 지난 후 자산을 낮은 비용의 아카이브 저장소로 이전하는 것을 포함하고 있습니다. 동시에 출시 후 임시 데이터는 정리되어 저장 리소스를 최적화하고 비용을 최소화합니다.

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

# 저장소 라이프사이클 관리 옵션 평가

저희 콘텐츠 중심 도메인에서는 Amazon S3가 광범위한 미디어 콘텐츠를 호스팅하는 기반 역할을 합니다. 하지만 우리는 기본적인 것을 넘어서 S3 상에 견고하고 높은 확장성을 갖춘 저장소 인프라 계층을 구축했습니다. 이 복잡한 프레임워크는 우리의 거대한 미디어 파일을 안전하고 효율적으로 저장, 정리, 추적하며 글로벌 분산 스튜디오의 요구를 충족시키기 위해 설계되었습니다.

자산 아카이빙 프로세스는 미래 참조를 위해 필요하지만 정기적으로 접근되지 않는 데이터를 기존의 고에너지를 사용하는 주 저장소 환경에서 더 에너지 효율적이고 저비용 아카이브로 전략적으로 이동하는 것을 포함합니다. 이 아카이빙 정책은 에너지 소비를 줄이고 고성능 저장 장치의 수명을 연장하는 데 기여합니다. 반대로, 삭제 정책은 오래된 또는 중복된 데이터를 체계적으로 삭제하고 저장 공간 요구 사항을 최적화하며 데이터 센터의 에너지 소비를 낮출 것을 목표로 합니다.

아카이빙 솔루션을 찾는 동안 우리는 AWS S3의 Intelligent Tiering을 평가했습니다. S3 Intelligent Tiering은 탁월한 기본 제품이지만, 우리가 원하는 데이터에 대한 사용자 정의 세밀한 조정 기능이 부족합니다. 접근 패턴 통계로부터 우리는 포스트 프로덕션 단계의 여러 플레이어가 우리 데이터에 접근하는 방법에 대한 훨씬 더 풍부하고 상세한 데이터 세트를 얻었습니다. 이 지식을 활용하여 S3 Intelligent Tiering이 더 저렴한 저장 등급으로 객체를 이동하는 데 30일 감시를 기다리는 대신 보다 저렴한 저장 등급으로 페타바이트의 데이터를 적극적으로 아카이브할 수 있습니다. 우리는 또한 이 지식을 활용하여 데이터를보다 적극적으로 삭제하고 더 많이 절약할 수 있었습니다.

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

S3 위에 위치한 저장소 인프라레이어는 두 가지 입력을 기반으로 다른 저장 클래스 간에 데이터를 이동할 수 있습니다.

- 엔드 유저 클라이언트 애플리케이션에서 지정한 정책.
- 접근 패턴 통계에서 유도한 정책.

# 저장소 라이프사이클 서비스 아키텍처

넷플릭스의 저장소 라이프사이클 관리 아키텍처를 간단히 살펴봅시다. 넷플릭스의 콘텐츠 생성을 고려할 때, 자산은 제작 중에 생성된 미디어 파일 집합을 나타냅니다. 고수준에서 저장소 라이프사이클 아키텍처는 다음 구성 요소로 구성되어 있습니다.

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

![Netflix's Media Landscape Evolution](/assets/img/2024-05-20-NetflixsMediaLandscapeEvolutionFrom321toCloudStorageOptimization_3.png)

**Fig 4: Storage Lifecycle Manager Architecture**

- **자산 관리 플랫폼**: Netflix에서 생산 및 후생산 단계의 미디어 자산 메타데이터를 저장하고 관리합니다. 자산 당 파일 수는 1개부터 1백만 개까지 다양합니다.
- **정책 관리자**: 자산 및 임시 미디어 파일을 포함한 저장 객체의 라이프사이클 정책을 관리합니다. 이 서비스는 파일 저장을 효율적으로 관리하기 위해 다양한 정책 기반 자동 및 임시 작업을 지원합니다.
- **콘텐츠 드라이브(Content Drive)**: 기존 파일 시스템 인터페이스를 사용하여 방대한 자산을 추적, 저장, 조직화, 관리하고 액세스 및 전송을 효과적으로 제어하기 위한 중앙화된 안전한 고도로 확장 가능한 솔루션을 제공합니다. 콘텐츠 드라이브는 미디어 파일의 상태에 대한 진리의 궁극적인 원천입니다. 임시로부터 우선순위가 높은 미디어 자산까지 다양한 미디어 자산은 시간이 지나거나 다른 버전에서 중요성을 갖습니다. 콘텐츠 드라이브는 데이터를 통해 우리의 이해를 높여주며, 자산 접근 패턴, 생산 관련 파일 수, 파일 크기 및 사용자 상호작용에 대한 통찰력을 제공합니다. 이 정보의 풍부함으로 생산 자산에 라이프사이클 정책을 정의하고 첨부하여 저장 풋프린트와 비용을 최적화할 수 있습니다.
- **저장 라이프사이클 관리자**: 저장 라이프사이클 작업을 처리하고 조정하는데 사용되는 워크플로를 처리합니다. 주요 워크플로는 다음과 같습니다:
  - Content Drive에 의해 관리되는 파일의 아카이빙을 AWS 아카이브 저장 계층으로 이동
  - 아카이브된 파일의 복원. 파일은 다시 아카이빙되기 전에 특정 기간 동안 S3 표준 계층에서 사용 가능합니다.
  - Content Drive에 의해 관리되는 파일의 삭제. 이것은 완전 삭제입니다.
- **S3 객체 관리자**: 미디어 작업을 최적화하기 위해 S3 상에 구축된 추상화 계층입니다.

이 게시물에서는 몇 가지 구성 요소를 높은 수준으로 다루고 다음 블로그 시리즈에서 자세한 아키텍처와 흐름을 다뤄 보겠습니다.

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

# 디자인 원칙

- 확장성

- 수십억 개의 파일을 처리할 수 있도록 설계되었으며, 모든 저장 객체가 아카이브하거나 삭제 대상이 될 수 있습니다.
- 쇼 종료 후 대규모 아카이빙과 같은 시나리오를 다룰 수 있는 쓰러러닝 허드 요청을 관리할 수 있습니다.

2. 내구성

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

- 저장 메타데이터의 데이터 무결성을 보장하고 안티 엔트로피 메커니즘을 활용합니다.
- 적어도 한 번의 활동 보고를 보장하여 신뢰성을 강화합니다.

3. 내구성

- 재시도 메커니즘을 포함한 아카이브/복원/제거 작업에 대한 보장을 제공합니다.
- 하루에 수천만 개의 파일 삭제 요구를 처리할 수 있는 기능으로 발전합니다.

4. 보안

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

- 지정된 응용 프로그램만 라이프사이클 작업을 트리거할 수 있도록 인가하여 안전한 환경을 유지합니다.

자산 라이프사이클 작업은 다음과 같습니다:

- 클라우드 응용 프로그램은 자산을 저장 백엔드에 업로드합니다.
- 제품 관리자와 응용 프로그램 소유자는 데이터 라이프사이클 정책을 정의하기 위해 Policy Manager API를 사용합니다. 해당 정책은 자산 유형에 적용되며, 예를 들어 제목 발매일로부터 180일 후에 일시적 자산을 삭제하거나 제작 후 30일 후에 최종 자산을 아카이브하는 등의 정책을 정의합니다.
- 정책 관리자는 자산에 대한 정책을 만들고 관리할 수 있습니다. 정책 엔진은 정책 정의를 평가하고 어떤 자산이 라이프사이클 작업의 대상이 되는지 판단합니다. 이후 정책 실행 워커에 대한 작업을 대기열에 추가합니다. 정책 실행 워커는 Content Drive를 대상으로 아카이브/복원/삭제와 같은 작업을 예약(생성)합니다.
- 정책 실행 시, Content Drive는 저장소 라이프사이클 관리자와의 라이프사이클 작업 실행을 예약합니다.
- 모든 라이프사이클 작업은 저장소 라이프사이클 관리자에 지속됩니다.
- 저장소 라이프사이클의 각 인스턴스에 대해 관리자는 실행할 자산을 평가하고 Content Drive에 실행 라이프사이클 이벤트를 전송합니다. Content Drive는 미디어 파일에 대한 충돌 작업 요청을 처리하기 위해 메타데이터 상태를 업데이트하며, 각 작업의 대상이 되는 자산 목록을 수집하고 이를 저장소 라이프사이클 관리자로 전송합니다.
- 필요한 처리량을 달성하기 위해 비동기 작업은 클러스터의 모든 노드에 균등하게 분산되며, 특정 작업을 처리하는 노드가 하나뿐입니다. 작업을 공유함으로써 병렬성을 달성하고, 이 솔루션은 데이터베이스 트랜잭션 충돌을 피하기도 합니다. Kafka를 사용하여 비동기 작업을 "소유"하는 노드로 이동합니다. Kafka의 리더 선출은 클러스터 전체에 균등하게 소유권을 설정하는 데 사용됩니다. Kafka는 최소 한 번의 메시징과 내구성 보장을 제공합니다.
- 저장소 라이프사이클 관리자는 S3 Object Manager를 사용하여 동시에 객체를 다른 저장 티어로 이동/삭제하고 비동기 완료/실패 이벤트를 기다립니다.
- 저장소 라이프사이클 관리자는 작업 완료를 모니터링하고 작업 완료 이벤트를 생성합니다. 실패한 이벤트는 작업 상태를 실패로 반영하기 전에 여러 번 다시 시도됩니다.
- S3 Object Manager는 작업 완료 이벤트를 처리하고 미디어 파일/폴더에 대한 메타데이터 상태 업데이트로 변환한 후 미디어 자산에 대한 라이프사이클 변경 완료 이벤트를 생성합니다. 스튜디오 응용 프로그램은 저장소 관리자에서 생성된 완료 이벤트에 가입하고 업무 업데이트를 최종 사용자에게 보냅니다.

# 저장소 아카이빙 통계

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

저장 수명주기 관리를 위해 Policy manager와 함께 생산 환경에서 자동화 작업을 시작했습니다. 이렇게 생긴 저장 수명주기 대시보드의 중요한 예시 몇 가지가 있습니다:

![그림 5: 하루에 아카이브된 파일 수(백만 단위)](/assets/img/2024-05-20-NetflixsMediaLandscapeEvolutionFrom321toCloudStorageOptimization_4.png)

![그림 6](/assets/img/2024-05-20-NetflixsMediaLandscapeEvolutionFrom321toCloudStorageOptimization_5.png)

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

Fig 6: Files Archived in a backfill job

# Netflix의 저장소 수명주기 진화

기존의 사용 패턴 및 콘텐츠 저장 및 보존 복잡성을 고려하여, Netflix는 많은 미디어 자산을 효율적이고 비용 효율적이며 지속 가능하게 관리하는 방법을 개척하려고 합니다. 곧 우리는 정책 관리자를 개선하여 모든 미디어 저장소 자산 및 임시 파일에 대한 데이터 수명주기 정책을 자동화할 계획입니다. 이 전략적인 움직임은 운영 효율성을 향상시키고 콘텐츠 관리 분야의 기술 발전을 선도하기 위한 저희의 약속과 일치합니다. 장기적으로는 데이터 수명주기 관리 솔루션을 자동화하고 확장하며, 우리의 목표는 충돌과 우선순위 뿐만 아니라 Netflix의 하이브리드 저장소의 데이터 수명주기 관리도 처리할 수 있는 정책 관리자를 개선하는 것입니다.

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

시스템화된 데이터 라이프사이클 관리는 높은 효율성, 비용 효율성 및 확장 가능한 저장 솔루션을 제공하기 위해 반드시 고려되어야 합니다. 이는 클라우드 또는 하이브리드 저장소를 위한 새로운 워크플로우의 설계나 아키텍처에 포함되어야 합니다.

Netflix에서 전형적인 실사 제작은 후속 제작 단계에서 단독으로 20,000개에서 80,000개의 에셋을 도출할 수 있으며, 이는 수백 테라바이트에 달하는 데이터량으로 이어집니다. 에셋 라이프사이클 정책에 따라 저렴한 저장 계층으로 아카이빙하여 약 70%의 비용 절감을 달성할 수 있습니다.

초기 단계에서의 성공을 고려할 때, 우리는 시스템의 능력을 향상시키고 있으며, 저장 계층과 정책 관리 계층을 모두 포함하여 두 가지 측면에서 에셋 라이프사이클 정책을 확대하고 있습니다:

- 제목 수명주기의 다른 단계에서 생성된 다양한 유형의 에셋에 대한 범위를 확장합니다.
- 완전한 라이프사이클을 정의하고 아카이빙되었을 수도, 아닐 수도 있는 에셋을 정리합니다.
- 정책 관리자를 확장하여 온프레미스, NFS, FSX, EBS 등 하이브리드 저장 환경의 데이터 계층화 및 라이프사이클 관리를 지원합니다.

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

# 감사의 말

Vinod Viswanathan, Sera Leggett, Obi-Ike Nwoke, Yolanda Cheung, Olof Johansson, Shailesh Birari, Patrick Prothro, Gregory Almond, John Zinni, Chantel Yang, Vikram Singh, Emily Shaw, Abi Kandasamy, Zile Liao, Jessica Gutierrez, Shunfei Chen 같은 멋진 동료들에게 특별히 감사드립니다.

# 용어

LTO: Linear Tape-Open의 약자로, 백업, 아카이빙 및 데이터 전송에 주로 사용되는 기술입니다.

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

VFX: 비주얼 이펙트의 약자로, 라이브 액션 미디어를 위해 비디오나 이미지를 수정하는 것을 포함합니다.

OCF: 오리지널 카메라 푸티지의 약자로, 필름 카메라에 의해 처음으로 촬영된 원본이며 편집되지 않은 콘텐츠를 나타냅니다.
