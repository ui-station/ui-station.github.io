---
title: "Amazon Redshift의 디자인을 이해하는 데 다시 8시간을 보냈어요 내가 발견한 것은 여기 있어요"
description: ""
coverImage: "/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_0.png"
date: 2024-05-23 13:50
ogImage:
  url: /assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_0.png
tag: Tech
originalTitle: "I spent another 8 hours understanding the design of Amazon Redshift. Here’s what I found."
link: "https://medium.com/data-engineer-things/i-spent-another-8-hours-understanding-the-design-of-amazon-redshift-heres-what-i-found-85c31a59fd19"
---

## 레드시프트 학술 논문으로부터의 모든 통찰: 2022년에 새롭게 태어난 아마존 레드시프트

- 역사와 배경
- 고수준 아키텍처
- 쿼리의 생애
- 코드 생성
- 컴파일 서비스
- 저장
- 컴퓨팅
- 통합

# 소개

나이가 들수록 많은 것들을 잘못 알고 있었다는 것을 깨달았습니다. 아마존 레드시프트에 대해 잘못 생각한 것이 하나입니다. 레드시프트에 갇혀 거의 일 년을 보낸 후 구글 빅쿼리를 처음 사용했을 때, 빅쿼리가 5배 이상 더 발전된 기술이고(특히 빅쿼리의 서버리스 경험 때문에), 레드시프트보다 더 진보했다고 스스로에게 이야기했습니다. 그 인상은 세 년간 지속되었습니다. 돌이켜보면, 나 자신을 비웃으며 왜 그렇게 순진했는지 의문을 제기합니다.

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

우수한 제품인 BigQuery, Redshift 또는 Snowflake와 같은 데이터베이스는 각각 하드웨어 제약 조건을 다루고 시스템 디자인 문제를 해결하는 고유한 방식이 있습니다. 어떤 데이터베이스가 더 빠른지 비교하는 대신에, 나는 그들의 내부 구현을 살펴가면서 가치 있는 것들을 배우는 것을 좋아해요. 이 기사는 Amazon Redshift에 대해 심층적으로 탐구한 결과물입니다 — 이전에 내가 무시했던 OLAP 시스템입니다.

이 기사에서는 학술 논문 "Amazon Redshift Re-invented (2022)"에서 대부분의 자료를 사용할 것이며, 추가 참고 문서는 기사 끝에 포함될 것입니다.

# 역사

Amazon Redshift는 클라우드를 위해 설계된 열 지향적 대규모 병렬 처리 데이터 웨어하우스입니다. 이 시스템은 대규모 병렬 처리 (MPP) 데이터 웨어하우스 회사 ParAccel에서 기술을 기반으로 구축되었으며, 이후 Actian에 인수되었습니다. 이 시스템은 이전 버전인 PostgreSQL 8.0.2를 기반으로 구축되었으며, Redshift는 그 버전에 변경을 가했습니다. 초기 프리뷰 베타판은 2012년 11월에 출시되었고, 전체 버전은 2013년 2월 15일에 제공되었습니다.

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

# High-level architecture

![img](/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_0.png)

Redshift 클러스터는 쿼리 실행을 처리하기 위해 여러 컴퓨팅 인스턴스로 구성됩니다. 각 클러스터는 단일 코디네이터 노드(=리더)와 여러 워커 노드를 가지고 있습니다.

데이터는 Amazon S3를 기반으로 하는 Redshift 관리 스토리지(RMS)에 저장됩니다. Redshift가 쿼리를 처리할 때, 데이터는 압축된 컬럼 지향 형식으로 로컬 SSD에 있는 컴퓨팅 노드에 캐싱됩니다. (저의 제한된 지식으로는 이것이 Snowflake 저장 계층과 유사하다고 생각됩니다.)

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

테이블 데이터는 여러 버킷으로 분할되어 모든 워커 노드에 분산됩니다. Redshift는 데이터의 특성에 기반하여 파티션 스키마를 적용할 수도 있고, 사용자가 명시적으로 라운드로빈 또는 해시와 같은 원하는 파티션 스키마를 선언할 수도 있습니다.

컴퓨팅과 스토리지 외에도 Redshift에는 다음과 같은 구성 요소가 있습니다 :

- AQUA는 FPGA를 활용하여 쿼리 성능을 가속화하는 레이어입니다.
- Compilation-As-A-Service는 생성된 코드(쿼리에서)를 위한 캐싱 서비스입니다.
- Amazon Redshift Spectrum을 사용하면 Redshift에서 S3의 데이터를 직접 쿼리할 수 있습니다.

# 쿼리의 생명주기

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

<img src="/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_1.png" />

아키텍처 구성 요소를 자세히 살펴보기 전에, Redshift 쿼리의 여정을 간단히 살펴보겠습니다:

- 쿼리는 먼저 리더 노드에 "안녕"이라고 말합니다. 여기서 구문 분석, 재작성 및 최적화됩니다.
- Redshift는 클러스터의 토폴로지를 사용하여 최적의 계획을 선택합니다. 계획 프로세스는 데이터 분포 정보도 활용하여 데이터 이동을 줄입니다.
- 계획 단계 후 Redshift는 실행 단계로 이동합니다. 계획은 개별 실행 단위로 분할됩니다. 각 단위는 이전 단위의 중간 출력을 사용합니다. Redshift는 각 단위에 대해 최적화된 C++ 코드를 생성 및 컴파일하고 이 코드를 네트워크를 통해 컴퓨트 노드로 전송합니다.
- 열 지향 데이터는 로컬 SSD에서 스캔되거나 Redshift 관리 스토리지에서 공급됩니다.

Redshift 실행 엔진은 성능을 향상시키기 위해 여러 최적화 기술을 적용합니다:

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

- zone-maps를 사용하는 것은 작은 해시 테이블이며 각 데이터 블록의 최소-최대 값을 저장합니다. (Snowflake와 BigQuery도 이렇게 합니다.)
- 스캔 연산은 Vectorization 및 SIMD(단일 명령, 다중 데이터) 처리를 사용합니다.
- 가벼운 압축 형식입니다.
- 블룸 필터
- 프리패칭
- Redshift의 AZ64 압축.

제가 Redshift 구성 요소에 대해 자세히 설명할 때 이러한 기술들을 다시 볼 수도 있습니다.

# 코드 생성

OLAP(On-Line Analytical Processing) 세계에서 쿼리 성능을 향상시키는 두 가지 주요 방법은 벡터화(Vectorization)와 코드 특수화(Code Specialization)입니다.

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

Vectorization의 주요 아이디어는 하나의 레코드를 처리하는 대신, 엔진이 일괄(벡터) 값으로 처리한다는 것입니다.

후자의 방식에서 엔진은 각 쿼리에 대해 코드를 생성하여 CPU 명령을 줄입니다. 코드 특수화를 적용하지 않는 시스템에서 각 연산자는 데이터 유형을 확인하고 입력 데이터 유형에 적합한 함수를 선택하기 위해 조건 블록(switch)을 통과해야 합니다. 코드 생성 방식은 실행 중에 해당 쿼리의 모든 연산자를 생성하기 때문에 이러한 과정을 피합니다.

Redshift는 코드 생성 방식을 적용했습니다. 시스템은 쿼리 계획과 실행 스키마에 특정한 C++ 코드를 생성합니다. 생성된 코드는 컴파일되고, 바이너리가 실행을 위해 컴퓨팅 노드로 전달됩니다. 각 컴파일된 파일은 물리적 쿼리 계획의 일부인 세그먼트라고 합니다.

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

코드 생성을 적용했음에도 불구하고 Redshift는 생성된 코드에 SIMD-벡터화된 데이터 스캔 레이어를 추가합니다. 벡터화된 스캔 함수는 미리 컴파일되며 (실시간으로 생성되는 것이 아닌) Switch 문으로 모든 데이터 유형을 처리합니다. 이는 Redshift가 더 나은 데이터 스캔 성능을 달성하고 각 쿼리에 대해 컴파일해야 하는 내장 코드 양을 줄이는 데 도움이 됩니다.

# 컴파일 서비스

위 섹션에서 알 수 있듯이 Redshift는 쿼리 실행을 위해 컴파일된 최적화된 객체를 사용할 것입니다. 이러한 객체는 로컬 클러스터 캐시에 캐싱되므로 동일하거나 유사한 쿼리가 실행될 때 마다 컴파일된 객체가 재사용되어 실행 시간이 더 빨라집니다. Redshift가 쿼리를 컴파일할 필요가 없어지기 때문입니다. 이 전략은 필요한 컴파일된 객체가 로컬 캐시에 있는 경우에만 성능을 향상시킵니다. 그렇지 않은 경우 Redshift는 코드를 생성해야 하므로 지연이 발생합니다.

<img src="/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_3.png" />

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

2020년에 Redshift는 컴파일 서비스를 소개했어요 (만약 마일스톤에 관해 틀린 정보를 전달했다면 이를 수정해 주세요). 이 서비스는 클러스터 리소스 대신 별도의 리소스를 사용해요. 컴파일 서비스는 컴파일된 객체를 외부 캐시에 캐싱하여 Redshift가 여러 클러스터에 대해 캐시된 객체를 제공할 수 있게 해줘요.

또한, 컴파일 서비스는 외부 컴파일 서비스의 병렬성을 활용하여 원하는 객체가 로컬 캐시나 외부 캐시에 없을 경우 코드를 더 빨리 컴파일할 수 있어요.

Redshift 뒤에 있는 사람들은 다음을 관찰했어요:

# CPU 친화적인 인코딩

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

Redshift은 디스크에 압축된 데이터를 저장합니다. LZO 및 ZSTD와 같은 일반적인 압축 알고리즘 외에도 Redshift는 AZ64 알고리즘과 같은 최적화된 유형별 알고리즘을 지원합니다. 이 알고리즘은 숫자 및 날짜/시간 데이터 유형을 다루는 것으로, 2019년에 Amazon에서 소개되었습니다. AZ64은 높은 압축 비율을 달성하고 성능을 향상시키도록 설계되었습니다. AZ64는 ZSTD와 비슷한 압축률을 달성하지만 빠른 압축 해제 속도를 갖추고 있습니다.

여기서 언급해야 할 멋진 점은 사용자가 AUTO 옵션(레드시프트가 데이터에 대한 압축을 자동으로 정의하도록 하는 옵션) 외에도 열 단위로 명시적으로 압축 체계를 정의할 수 있다는 것입니다. 게다가 한번 정의한 후에는 ALTER TABLE 절을 사용하여 압축 체계를 변경할 수 있습니다. 이것은 흥미로운 기능이라고 생각합니다. 사용자가 데이터에 대해 가장 이해하고 있으므로 유연한 압축 옵션을 허용하는 것이 데이터가 어떻게 저장되는지에 대한 더 나은 통제를 제공할 수 있을 것입니다. 그에 상응하여 더 많은 권한은 더 큰 책임을 의미합니다. 조심하지 않으면 나쁜 (압축) 선택이 성능과 비용에 악영향을 줄 수 있습니다. 내가 아는 한, Google은 BigQuery에서 이 기능을 허용하지 않습니다. Snowflake가 이를 지원하는지 알지 못하니 알고 계시면 의견을 남겨주세요.

# 적응형 실행

Redshift의 쿼리 엔진은 실행 통계에 따라 생성된 코드나 실행 속성을 조정하여 성능을 향상시키는 런타임 결정을 내립니다. 실행 중에 Bloom 필터를 사용하는 것은 Redshift의 동적 최적화의 대담한 예입니다.

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

# Amazon Redshift을 위한 AQUA

<img src="/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_4.png" />

Advanced Query Accelerator (AQUA)는 2021년에 Redshift에 의해 소개된 멀티 테넌시 서비스입니다. 이는 Redshift Managed Storage를 위한 캐싱 레이어 역할을 하며 복잡한 스캔 및 집계를 가속화합니다.

AQUA는 지역 서비스인 Amazon S3에서 데이터를 가져오는 레이턴시를 피하고 Redshift의 캐시 스토리지에 데이터를 채우는 필요성을 줄이기 위해, 클러스터의 핫 데이터(여러 번 액세스되는 데이터)를 로컬 SSD에 캐싱합니다. Redshift는 입력 쿼리로부터 해당 스캔 및 집계 작업을 감지하고 이를 AQUA로 푸시하여 캐시된 데이터로 처리합니다.

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

아마존의 사람들은 AWS의 Nitro ASIC를 활용한 커스텀 서버를 설계하여 압축 및 암호화를 가속화하고, FPGAs를 사용하여 필터링 및 집합 연산의 실행 속도를 향상시키도록 했습니다.

# 쿼리 재작성 프레임워크

Redshift는 또한 두 가지 목표를 가진 새로운 쿼리 재작성 프레임워크(QRF)를 소개했습니다:

- 연합, 조인 및 집계와 같은 연산의 실행 순서를 최적화하기 위한 재작성 규칙.
- 점진적 재료화 뷰 쿼리 및 유지 관리를 위한 스크립트 작성. (곧 다룰 예정입니다)

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

# 스토리지

이 섹션에서는 Redshift의 스토리지 레이어인 Redshift Managed Storage부터 동시성 제어까지 살펴볼 것입니다.

# Redshift Managed Storage (RMS)

RA3 클러스터 유형이나 Redshift 서버리스를 선택할 때, 데이터는 RMS에 저장됩니다. 이 저장 레이어는 Amazon S3를 기반으로 하며, 특정 년도 동안 다중 존에서 99.999999999%의 내구성과 99.99%의 가용성을 달성합니다. RMS를 사용하면 데이터가 컴퓨팅 노드에서 분리되어 저장되므로 고객은 컴퓨팅과 스토리지를 독립적으로 확장하고 비용을 지불할 수 있습니다. RMS는 S3를 기반으로 하기 때문에 데이터 블록 온도 및 블록화와 같은 최적화를 사용하여 높은 성능을 제공합니다.

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

RMS는 고대역 네트워킹을 제공하는 AWS Nitro 시스템 위에서 구축되었어요. RMS는 티어-1 캐시로 높은 성능의 SSD 기반 로컬 스토리지를 사용하고 있어요. Redshift는 자동으로 미세하게 데이터를 제거하고 지능적으로 데이터를 미리 가져오는 기술을 활용하여 로컬 SSD에서 최고의 성능을 얻으면서 S3의 무제한 확장성을 달성하고 있어요.

![이미지](/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_5.png)

RMS는 S3로부터 데이터 접근을 향상시키기 위해 데이터 블록을 메모리에 넣고 로컬 SSD에 캐시하는 미리가져오기 메커니즘을 사용하고 있어요. RMS는 관련 블록이 로컬에서 사용 가능하도록 유지하기 위해 캐시 대체를 조정하면서 모든 블록에 대한 액세스를 추적해요. 로컬 SSD 위의 캐시 레이어인 메모리 디스크 캐시 크기는 쿼리의 성능과 메모리 요구 사항을 균형있게 조정할 수 있도록 동적으로 변경될 수 있어요.

테이블의 데이터는 데이터 슬라이스로 분할되어 논리적인 데이터 블록 체인으로 저장됩니다. 각 블록(크기 1MB)에는 식별, 테이블 소유권 또는 슬라이스 정보와 같은 정보가 포함된 헤더가 있어요. 블록은 메모리 내 구조인 슈퍼 블록을 사용하여 색인화돼요. 논문에 따르면 슈퍼 블록은 많은 파일 시스템과 유사한 특성을 가진 색인 구조입니다. 쿼리는 슈퍼 블록을 스캔하기 위해 존 맵을 사용하여 필요한 데이터 블록을 가져와요. 게다가 슈퍼 블록은 실행 중인 쿼리에 의해 처리된 데이터 블록을 위한 쿼리 추적 정보도 포함하고 있어요.

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

RMS는 Amazon S3로 트랜잭션을 동기화하여 여러 클러스터가 일관된 데이터에 액세스할 수 있게 해줍니다. 데이터는 쓰기 요청을 일괄 처리함으로써 다른 가용 영역에 걸쳐 S3에 쓰여집니다. 동시 클러스터는 동시 쓰기를 위해 필요할 때 요청되며 읽기는 스냅샷 분리에 의존합니다.

메인 클러스터에서 데이터가 삭제되면 Redshift는 해당 데이터가 더 이상 쿼리에 필요하지 않음을 보장하고, 이 데이터를 개체 저장소의 가비지 컬렉터용으로 표시합니다. 데이터가 Amazon S3에 백업되어 있기 때문에 SSD가 고장나도 데이터는 손실되지 않습니다.

Amazon S3는 또한 데이터 스냅샷을 저장합니다. 이러한 스냅샷은 복원 지점으로 작용합니다. Redshift는 전체 클러스터 데이터뿐만 아니라 개별 테이블의 데이터를 복원하는 것도 지원합니다. Amazon S3는 데이터 공유와 머신 러닝의 진실의 원천으로도 기능합니다.

# 데이터 메타데이터 분리

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

메타데이터와 데이터를 분리하면 Elastic Resize 및 Cross Instance Restore와 같은 프로세스를 구현하기가 더 쉬워집니다. 이 둘 다 메타데이터를 한 클러스터 구성에서 다른 클러스터 구성으로 이동해야 합니다.

Elastic Resize는 고객이 클러스터의 노드를 추가하여 성능을 향상시키거나 노드를 제거하여 비용을 절약할 수 있는 기능입니다. Cross-Instance Restore를 통해 사용자는 하나의 인스턴스 유형 클러스터에서 가져온 스냅샷을 다른 인스턴스 유형이나 다른 노드 수의 클러스터로 복원할 수 있습니다.

이 프로세스의 구현 세부 정보는 다음과 같습니다:

- 데이터의 사본이 Amazon S3에 저장되도록 보장합니다.
- 재구성하기 전 Redshift는 클러스터의 데이터를 고려합니다. 데이터 이동을 최소화하는 재구성 계획을 수립하여 균형 잡힌 클러스터를 생성합니다.
- 재구성하기 전에 Redshift는 데이터에 대한 카운트와 체크섬을 기록하고 완료 후 정확성을 검증합니다.
- 복원의 경우 Redshift는 테이블 수, 블록 수, 행 수, 사용된 바이트 및 데이터 분포의 카운트를 기록하고 스냅샷과 함께 저장합니다. 복원 후에 카운트와 체크섬을 검증하고 새 쿼리를 수락하기 전에 확인합니다.

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

다음 논문을 따르면:

## 로컬 저장소의 한계를 넘어서

Redshift는 무한한 확장성을 제공하기 위해 Amazon S3를 활용하며, 로컬 메모리와 SSD를 캐시로 사용합니다. (Snowflake와 같이).

클러스터는 각 데이터 블록의 액세스 횟수에 따라 작업 데이터 세트를 로컬로 유지합니다. Tiered 캐시는 이 정보를 추적하는 역할을 합니다. 캐시는 두 단계로 구성됩니다:

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

![image](/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_6.png)

- Low level: This level stores cold data blocks. Every time the query accesses a data block, the system increases the block’s reference count.
- High level: the cold blocks become hot (after being accessed multiple times), and the policy promotes data blocks to a high level.

During eviction, the reference count of each block is decremented. When the reference count reaches zero, the block will be moved down to the low level or entirely evicted from the cache.

(Sounds like Python object’s reference count, huh?)

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

RMS는 클러스터 재구성 후 로컬 SSD에 데이터를 다시 채우는 데 티어별 저장소 캐시를 사용합니다 (예 : Elastic Resize). 이와 같은 시나리오에서 컴퓨팅 노드는 고객 쿼리에서 가장 자주 액세스될 것으로 예상되는 데이터 블록을 로컬 디스크에 채웁니다.

마지막으로, Redshift에는 메모리 내에서 가장 빈번하게 액세스되는 가장 뜨거운 블록을 유지하는 동적 디스크 캐시라는 또 다른 캐시 레이어가 있습니다. 또한 특정 쿼리에서 임시 블록을 저장합니다. 이 캐시는 메모리가 사용 가능할 때 자동으로 확장되고 시스템이 메모리 부족 상태가 되면 자동으로 축소됩니다.

# 점진적 커밋

비용을 절감하기 위해 RMS는 마지막 커밋과 비교하여 데이터 변경 사항만 캡처합니다. 이러한 변경 사항은 나중에 커밋 로그에 업데이트됩니다. Redshift의 로그 기반 커밋 프로토콜은 인메모리 구조를 영구 구조(디스크)로부터 분리하며, 각 슈퍼블록은 변경 사항의 로그입니다. 논문에서 가져온 내용:

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

이 로그 구조화된 메타데이터는 일관된 데이터에 접근하여 로그를 적용함으로써 동시성 확장 및 데이터 공유와 같은 기능의 비용을 줄입니다.

# 동시성 제어

Redshift는 다중 버전 동시성 제어를 구현하여 읽기 프로세스가 다른 읽기 요청으로 블록되는 것을 방지합니다. 쓰기 요청은 다른 동시 쓰기 요청에 의해만 차단될 수 있습니다.

각 트랜잭션은 트랜잭션이 시작되기 전에 모든 커밋된 트랜잭션에 의해 설정된 데이터베이스의 일관된 스냅샷을 볼 수 있습니다. 논문에서 Amazon은 스냅샷 격리 위에 Serial Safety Net (SSN)을 기반으로 한 새로운 디자인을 사용하여 엄격한 직렬화를 메모리 효율적인 방식으로 보장하는데 사용했습니다. 이 디자인은 이전에 커밋된 트랜잭션의 요약 정보만 사용하므로 엄격한 직렬화를 보장할 수 있습니다.

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

# 계산

아마존을 따라가는 레드시프트는 매주 수십억 개의 쿼리를 처리합니다. 사용자는 필요에 따라 계산 성능을 조절할 수 있는 다음 옵션 중 하나를 선택할 수 있습니다:

# 클러스터 크기 확장

![image](/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_7.png)

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

이 설정을 통해 고객은 필요에 따라 클러스터에서 컴퓨팅 노드를 추가하거나 제거할 수 있습니다. 데이터를 섞는 대신, Elastic Resize는 데이터 파티션 할당(메타데이터만)을 분배하여 데이터 파티션을 노드 간에 조직화되고 균형 있게 유지합니다. 크기를 조정한 후, 컴퓨팅 노드의 로컬 캐시(SSD)는 할당 정보에 따라 S3에서 데이터를 받아 채웁니다. (Redshift는 핫 데이터에 우선순위를 둠)

그러나 이로 인해 잠재적인 문제가 발생할 수 있습니다. 크기를 조정한 후, 각 노드가 담당하는 데이터의 수가 크기를 조정하기 이전과 다를 수 있으며, 이는 일관되지 않은 쿼리 성능을 초래할 수 있습니다. Redshift는 이를 다루기 위해 컴퓨팅 병렬성(작업자, 프로세스, 스레드 수)을 데이터 파티션과 분리합니다:

![이미지](/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_8.png)

- 컴퓨팅 병렬성이 `데이터 파티션의 수와 같은 경우, 개별 컴퓨팅 프로세스는 여러 데이터 파티션에서 작업합니다.
- 컴퓨팅 병렬성이 `데이터 파티션의 수와 다른 경우, 여러 컴퓨팅 프로세스가 개별 데이터 파티션에서 작업을 공유합니다.

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

Redshift은 공유 가능한 작업 단위 덕분에 이것을 달성합니다.

## 동시성 스케일링

이 구성은 OLAP 시스템의 전형적인 도전 과제 중 하나인 동시성을 다루는 데 도움을 줍니다. 사용자가 Redshift에서 더 많은 동시성 기능을 필요로 할 때 동적으로 확장됩니다.

![이미지](/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_9.png)

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

Concurrency Scaling을 사용하면 사용자는 쿼리를 제출하기 위한 단일 클러스터 활성 엔드포인트를 유지합니다. Redshift는 리소스가 완전히 활용되고 새로운 쿼리가 지속적으로 발생하는 것을 감지하면 자동으로 추가적인 Concurrency Scaling 컴퓨팅 클러스터를 추가합니다. 대기 중인 쿼리는 이러한 클러스터로 라우팅되어 처리됩니다. 또한 추가된 클러스터는 S3로부터 데이터를 로컬 디스크에 채웁니다.

# 컴퓨팅 분리

Redshift는 고객이 다른 Redshift 컴퓨팅 클러스터 및 AWS 계정 간에 데이터를 공유할 수 있도록 합니다. 컴퓨팅 클러스터는 단일 데이터 원본에 액세스할 수 있으며 ETL 파이프라인을 개발하거나 데이터 복사 비용을 부담할 필요가 없습니다.

사용자는 스키마와 테이블부터 사용자 정의 함수(UDF)까지 다양한 수준에서 데이터를 공유할 수 있습니다. 다른 사람과 데이터를 공유하려면 데이터의 소유자(생산자)가 먼저 데이터 공유를 생성하고 사용자에게 액세스를 부여합니다. Redshift는 IAM 정책과 메타데이터를 사용하여 인증 및 권한 부여를 구현합니다.

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

고객은 메타데이터 요청을 사용하여 공유된 객체에 대한 쿼리를 공유합니다. 공유 데이터에 액세스하는 것이 인가된 고객만 서비스를 제공받을 수 있습니다. 각 요청은 디렉토리 서비스 및 프록시 레이어를 통해 전달됩니다. 프록시는 요청을 인증하고 승인하며 메타데이터 요청을 적절한 프로듀서로 라우팅합니다. 고객 측에서 메타데이터를 수신한 후에는 RMS에서 원하는 데이터를 읽고 쿼리를 처리합니다. 공유 데이터를 쿼리할 때의 캐시 프로세스는 변경되지 않습니다: 공유 데이터는 클러스터에 캐시되며 이후의 쿼리에서는 로컬로 데이터를 읽어옵니다.

## 자동 튜닝 및 운영

첫날부터 Redshift는 전통적인 데이터 웨어하우징 시스템(로컬 서버 및 데이터 센터 구축)과 비교하여 많은 측면을 간소화했습니다. 그럼에도, 일부 유지 관리 및 튜닝 작업은 경험 많은 데이터베이스 관리자가 필요합니다: 사용자는 성능 향상을 위해 명시적으로 vacuum 프로세스를 예약하거나 분산 또는 정렬 키를 결정해야 합니다.

현재 Redshift는 고객 워크로드에 성능 영향을 미치지 않고 비주요 프로세스인 vacuuming, analyzing 또는 materialized views 새로고침을 자동으로 실행합니다. Redshift는 사용자 워크로드를 관찰하고 분석하여 성능 향상 기회를 식별하며, 예를 들어 워크로드에 대한 최적의 분산 및 정렬 키를 자동으로 지정하여 적용합니다.

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

또한, Redshift는 고급 예측 메커니즘을 활용하여 웜 풀을 통해 필요할 때 추가 노드를 가능한 빨리 사용할 수 있습니다. 이는 Snowflake의 사전 웜 워커 풀과 매우 유사하며, 노드 장애 또는 동시성 스케일링으로 인한 쿼리 대기 시간 및 다운타임을 줄입니다. 마지막으로, Amazon Redshift는 사용자 개입 없이 실행 및 스케일링이 쉬운 서버리스 옵션(예: Google BigQuery)을 제공합니다.

# 자동 테이블 최적화

분배 및 정렬 키와 같은 속성을 최적화하여 작업 부하의 성능을 최적화할 수 있습니다. 분배 키는 테이블 데이터가 클러스터 전체에 분배되는 방식을 나타내는 속성으로, 시스템이 병렬 리소스를 효율적으로 할당할 수 있도록 도와줍니다. 정렬 키는 하나 이상의 열을 기반으로 데이터를 정리하여 존 맵 인덱싱을 활용합니다. 존 맵은 데이터 단위의 최소값 및 최대값을 저장하는 인덱싱 구조로, 불필요한 데이터를 건너뛰는 데 매우 유용하며 데이터 정렬은 존 맵을 효율적으로 활용할 수 있습니다. (이건 BigQuery 클러스터링과 비슷하게 들리나요?)

![Amazon Redshift 디자인을 이해하는 데 또 8시간을 보냈어요. 여기서 발견한 것들](/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_10.png)

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

과거에는 이러한 키들이 사용자에 의해 명시적으로 정의되었습니다. 지금은 Redshift가 자동으로 Automatic Table Optimization (ATO)을 통해 이 프로세스를 처리합니다. ATO는 워크로드를 분석하여 최적의 분배 및 정렬 키를 추천합니다. 추천을 생성하기 위해 최적화된 쿼리 계획, 기본치, 및 예측 선택도와 같은 쿼리 실행 메타데이터를 주기적으로 수집합니다.

목표와 함께 추천된 키들:

- 분배 키: 데이터 이동 비용을 최소화하기 위해 시스템은 특정 워크로드의 모든 테이블을 조사하여 추천해야 합니다.
- 정렬 키: 디스크에서 읽어야 하는 데이터 양을 줄이기 위해.

추천을 받은 후, Redshift는 고객들에게 두 가지 옵션을 제공합니다:

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

- 콘솔을 통한 수동 적용.
- 자동 백그라운드 워커가 권장 사항을 적용합니다. 이 워커는 설정을 점진적으로 적용하고 클러스터가 너무 바쁘지 않을 때에만 작업을 실행합니다.

# 자동 워크로드 관리

Redshift의 자동 워크로드 관리자(AutoWLM)는 입장 제어, 스케줄링 및 자원 할당을 담당합니다. 쿼리를 수신한 후 AutoWLM은 실행 계획과 최적화된 통계를 벡터 형식으로 변환합니다. 그런 다음 Redshift는 해당 벡터를 ML 모델로 넣어 컴파일 및 실행 시간을 예측합니다. Redshift는 모델의 결과를 사용하여 예측된 실행 시간을 기반으로 쿼리를 대기열에 넣습니다. 추정된 메모리 요구량(모델에 의해 예측됨)이 사용 가능한 메모리 풀에서 충족될 때만 쿼리가 실행 단계로 진행됩니다. 또한 자원 이용률이 너무 높다고 감지될 때 AutoWLM은 병행성 비율을 제한하여 쿼리 대기 시간을 피합니다.

AutoWLM은 스케줄링에 가중 라운드로빈 메커니즘을 활용하여 우선순위가 더 높은 쿼리를 우선적으로 더 자주 스케줄링합니다. 또한 SLA를 준수해야 하는 높은 우선순위 쿼리는 하드웨어 자원의 더 큰 할당을 받습니다. Redshift는 서로 다른 우선순위를 갖는 쿼리가 동시에 실행될 때 CPU 및 I/O를 지수함수적으로 감소하는 부분으로 분할합니다. 이는 높은 우선순위 쿼리를 낮은 우선순위 쿼리보다 지수함수적으로 빠르게 부스트합니다.

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

하위 우선순위 쿼리 뒤에 높은 우선순위 쿼리가 오면 AutoWLM은 상위 우선순위 쿼리에 공간을 확보하기 위해 하위 우선순위 쿼리에서 리소스를 회수합니다. 낮은 우선순위 쿼리에서 리소스 고갈을 방지하기 위해 시스템에 의해 리소스가 회수되는 확률이 각 빼앗애질 때마다 줄어듭니다. 결과적으로 리소스가 고갈되면 Redshift는 상위 우선순위 리소스를 제공하기 위해 하위 우선순위 쿼리를 대기열에 넣습니다.

# 쿼리 예측 프레임워크

위 섹션에서 언급했듯이 AutoWLM은 메모리 소비량이나 실행 시간과 같은 메트릭을 예측하기 위해 머신 러닝 모델을 사용합니다. Redshift의 쿼리 예측 프레임워크는 이러한 모델을 유지하는 역할을 합니다. 이 프레임워크는 Redshift 클러스터에서 실행되며 데이터를 수집하고 XGBoost 모델로 훈련을 하며 필요할 때 결과를 출력합니다. 클러스터에서 프레임워크를 실행함으로써 변화하는 워크로드에 빠르게 적응할 수 있습니다. 위에서 언급한 코드 컴파일 서비스도 최적화를 위해 쿼리 예측 프레임워크를 사용합니다.

# 자료화된 뷰

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

![Image description](/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_11.png)

SQL 뷰와 Materialize View (MV)은 쿼리 결과를 테이블처럼 나타내는 방법을 제공합니다. View와는 달리, MV는 데이터를 디스크에 물리적으로 유지하므로 MV에서 데이터를 쿼리할 때 실행 시간이 빨라집니다. Redshift는 MV 관리를 다음과 같이 자동화합니다:

- 기본 테이블의 변경 사항을 반영하여 필터, 선택, 그룹화 및 조인을 점진적으로 유지합니다.
- 유지 관리 시간을 자동화합니다: Redshift는 어떤 MV를 새로 고쳐야 하는지 감지합니다. 이는 쿼리 워크로드에서 MV의 유틸리티 및 MV 새로 고침 비용 두 가지 요소를 사용하여 수행됩니다.
- MV를 통해 쿼리를 자동으로 다시 작성하여 최적의 성능을 달성합니다. 점진적 유지 관리 및 쿼리 다시 작성은 "쿼리 다시 작성 프레임워크" 섹션에서 언급된 프레임워크를 사용합니다.

# 스마트 웜 풀

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

클라우드 시대에는 하드웨어 고장이 더 이상 예외가 아닙니다. 머신 고장으로 인한 성능 저하를 방지하기 위해 Redshift는 스마트 웜 풀 아키텍처(컴퓨팅 머신이 서비스되기 전에 미리 웜업됨)를 사용합니다. 이 아키텍처는 많은 프로세스에서 효율성을 제공합니다: 실패한 노드 교체, 클러스터 재개, 자동 동시성 스케일링...

![image](/assets/img/2024-05-23-Ispentanother8hoursunderstandingthedesignofAmazonRedshiftHereswhatIfound_12.png)

웜 풀은 사전 설치된 소프트웨어와 네트워킹 구성을 갖춘 EC2 컴퓨트 인스턴스 그룹입니다. 각 지역마다 AWS 가용 영역마다 별도의 웜 풀이 위치해 있습니다. 작업을 낮은 지연 시간으로 유지하기 위해서는 웜 풀에서 노드를 획득할 때 높은 히트율이 필요합니다. Redshift는 머신러닝 모델을 사용하여 특정 시점에서 필요한 EC2 인스턴스 수를 예측합니다. 시스템은 각 지역과 가용 영역에서 웜 풀을 동적으로 조정하여 인프라 비용을 절약합니다.

# 통합

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

내장형인 AWS Redshift는 Amazon 클라우드 서비스 생태계의 혜택을 크게 누립니다. 여기에는 Redshift의 통합 옵션 몇 가지가 있습니다.

- Amazon Redshift Spectrum을 사용하면 S3에서 데이터를 직접 쿼리할 수 있습니다. 이 기능은 대규모 Scale-Out 처리를 제공하여 Parquet, Text, ORC 및 AVRO 형식의 데이터를 스캔하고 집계합니다.
- Amazon Sagemaker와 함께하는 Redshift ML을 사용하면 SQL을 사용하여 기계 학습 모델을 학습하고 예측하는 것이 쉬워집니다. Redshift ML은 Amazon SageMaker를 활용하여 모델을 학습한 뒤 SQL 함수로 노출하고 사용자는 SQL을 사용하여 직접 사용할 수 있습니다
- Redshift Federated Query를 사용하면 Redshift가 고객의 OLTP 데이터베이스 (Postgres, MySQL 등)에 직접 연결하여 데이터를 가져올 수 있습니다. 이 편리한 기능으로 ETL 파이프라인을 통해 OLTP 출처에서 데이터를 추출할 필요가 없어집니다.
- 슈퍼 스키마리스 처리: SUPER 반구조화 형식은 스키마가 없는 중첩 데이터를 포함할 수 있습니다. SUPER 형식의 값은 Redshift 문자열 및 숫자 스칼라, 배열 및 구조체로 구성될 수 있습니다. 사용자는 SUPER 유형을 사용할 때 미리 스키마를 정의할 필요가 없습니다. Redshift의 동적 형식 지정은 중첩 데이터를 감지할 수 있습니다.
- Lambda와 함께하는 Redshift: Redshift는 AWS Lambda 코드를 지원하는 사용자 정의 함수(UDF)를 지원합니다. Lambda UDF는 Java, Go, PowerShell, Node.js, C#, Python 및 Ruby로 작성할 수 있습니다.

# 마무리

조금 긴 글이었죠? 이 글이 제 가장 긴 글입니다.

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

제목에 언급된 "8시간"에도 불구하고, 이 블로그를 마무리하는 데 거의 일주일이 걸렸어요. Redshift 논문은 많은 새로운 내용을 제공해서 조금 힘들었죠. 게다가, 이번에는 코드 특화 접근 방식을 선택한 OLAP 시스템을 연구한 것이 처음이라서 (이전에 공부한 시스템들은 모두 벡터화를 사용했어요: BigQuery, Snowflake, DuckDB).

코드 특화 외에도, Redshift의 주요 기능으로는 컴파일 서비스, Redshift 관리 스토리지, 그리고 데이터베이스 운영을 위해 머신 러닝을 적용한 것이 언급할만해요. 게다가, Redshift의 웜 풀 아키텍처는 Snowflake의 사전 웜 풀과 유사한데, 두 솔루션이 모두 컴퓨팅 스케일링의 지연 시간을 최소화하려고 노력하지만, Redshift의 경우는 머신 러닝 모델을 활용해서 작동한다는 점이 다르죠.

지금은 Redshift가 백그라운드 작업에 머신 러닝을 명시적으로 사용한다는 유일한 시스템인 것으로 보여요. Snowflake나 BigQuery는 이를 언급하지 않았죠. (제가 놓친 부분이 있다면 알려주세요)

이제 이만 헤어집니다. 다음 주에 또 만나요!

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

# 참고 자료

논문: Amazon Redshift Re-invented — 2022

문서: Amazon Redshift 공식 문서

PowerPoint 프레젠테이션: Amazon Redshift에 대한 심층 학습 및 모범 사례

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

내 뉴스레터는 매주 블로그 형식의 이메일로, 내가 더 똑똑한 사람들로부터 배운 것들을 정리해 두는 공간입니다.

그러니 나와 함께 배우고 성장하고 싶다면 여기에서 구독해 주세요: https://vutr.substack.com.
