---
title: "데이터브릭스에서 CDC를 활용해 민감한 데이터 보호하기"
description: ""
coverImage: "/assets/img/2024-05-17-ProtectingSensitiveDatawithCDCinDatabricks_0.png"
date: 2024-05-17 18:15
ogImage:
  url: /assets/img/2024-05-17-ProtectingSensitiveDatawithCDCinDatabricks_0.png
tag: Tech
originalTitle: "Protecting Sensitive Data with CDC in Databricks"
link: "https://medium.com/datareply/protecting-sensitive-data-with-cdc-in-databricks-b848951c75b4"
---

빅 데이터를 활용하여 비즈니스 지능적인 의사 결정을 하는 데 관심이 있는 모든 종류의 기업들에게 Databricks가 선호되는 선택지로 자리 잡고 있습니다. Spark의 창시자들에 의해 설립된 이 기업급 플랫폼은 데이터 애플리케이션을 개발하는 데 필요한 다양한 데이터 엔지니어링 및 ML 리소스를 통합합니다.

![이미지](/assets/img/2024-05-17-ProtectingSensitiveDatawithCDCinDatabricks_0.png)

Databricks가 제공하는 모든 기능을 설명하면 이 글이 너무 길어져서 읽기 어려워질 것입니다. 그들은 지속적으로 새로운 기능을 출시하고 현재 서비스를 개선하여 사용자 경험을 향상시키고 있습니다. 간략하게 설명하자면, 저희가 Secure CDC 애플리케이션에 적용한 Databricks의 일부 능력에 주로 초점을 맞출 것입니다. 그러기 전에, 먼저 CDC가 무엇을 의미하고 왜 중요한지 설명하겠습니다.

## 변경 데이터 캡처

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

전통적인 데이터베이스 엔진은 시스템에 들어오는 모든 트랜잭션을 최적으로 정확하게 등록하는 데 설계되어 있습니다. 특히 데이터베이스에 변경 사항을 도입하는 작업은 데이터 일관성을 유지하기 위해 순서대로 정확하게 적용되어야 합니다. 다른 한편으로, 분석 시스템은 기존 데이터로 복잡한 쿼리와 변환 작업을 실행하는 데 초점을 맞추며, 쓰기 성능보다 읽기 성능에 우선순위를 두기 때문에 동기화되지 않은 데이터 업데이트가 발생할 수 있습니다. 비구조화 또는 반구조화된 데이터 원본의 흡수 과정의 일환으로 데이터 일관성 제어가 종종 구현되어야 합니다. 데이터 수정을 올바르게 캡처하고 적용하는 이 메커니즘을 Change Data Capture (CDC)라고 합니다.

데이터브릭의 두 가지 주요 기능인 Delta Live Tables와 Autoloader가 CDC 파이프라인을 배포하는 데 도움이 되었습니다.

Delta Live Tables (DLT)은 실시간 데이터 처리 파이프라인을 만들기 위해 Spark 위에 구축된 강력한 선언적 프레임워크입니다. Spark는 Python, SQL, R, Scala라는 네 가지 다른 언어를 지원하지만, 이 글을 작성하는 시점에서 DLT 파이프라인은 Python 또는 SQL로만 설명할 수 있습니다.

DLT는 Autoloader를 활용하여 클라우드 위치에서 새 데이터를 자동으로 파이프라인으로 가져옵니다. Autoloader는 JSON, CSV, XML, PARQUET, AVRO, ORC, TEXT 및 BINARYFILE 형식의 여러 유형의 파일을 다양한 클라우드 제공 업체(S3, GCP, Azure 또는 DBFS)에서로드할 수 있습니다. 또한 파일 유형 및 클라우드 위치에 따라 사용자 지정 옵션을 지원하여 입력 데이터를 올바르게 읽고 포맷팅하는 과정을 간소화합니다. 저희는 Autoloader를 사용하여 파이프라인에 적용해야 하는 변경 사항을 캡처했습니다.

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

이제 실제 사용 사례로 들어가 봅시다.

## 민감한 정보 보호

우리는 DLT 파이프라인 내에서 Change Data Capture를 사용하여 일반화된 GDPR(일반 개인정보 보호법) 준수 데이터 애플리케이션을 만들었습니다. GDPR 준수란 특정 조건이 충족되어야 한다는 것을 의미합니다:

- 인가된 사용자만 민감한 데이터에 질의할 수 있습니다.
- 민감한 데이터는 요청에 따라 데이터베이스에서 제거되어야 합니다.

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

<img src="/assets/img/2024-05-17-ProtectingSensitiveDatawithCDCinDatabricks_1.png" />

이러한 조건을 달성하기 위해 먼저 민감한 데이터를 식별하고 처리하여 무단 사용자가 액세스하지 못하게 했습니다. 이 개념을 익명화라고 합니다. 민감한 데이터를 익명화하기 위해 우리는 각 엔터티마다 고유한 암호화 키로 암호화하는 스파크의 내장 AES 함수를 사용했습니다. 두 번째 요구 사항을 위해 우리는 데이터베이스에서 제거해야 하는 엔터티의 암호화 키를 해싱하는 프로세스를 만들어서 더 이상 액세스할 수 없도록 했습니다. 심지어 인증된 사용자도 액세스할 수 없도록 처리했습니다. 이 프로세스에 대해 더 자세히 살펴보겠습니다.

## 워크플로우의 상세 설명

우리의 애플리케이션을 테스트하기 위해, 패키지의 처리를 위해 S3에 더미 고객 정보를 생성 및 저장하는 준비 노트북을 작성했습니다. 이 노트북은 또한 데이터베이스에서 민감한 정보를 삭제하길 요청한 고객 ID 목록 (기본적으로 삭제 요청을 하는 고객 ID 목록)을 저장하기 위해 동일한 클라우드 위치에 다른 폴더를 만들었습니다.

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

DLT 파이프라인의 주요 기능은 CDC를 사용하여 암호화된 고객 테이블을 업데이트하는 것입니다. 암호화 키 테이블에 대한 CDC를 사용합니다. 다음 그림에서 파이프라인의 표현을 확인할 수 있습니다:

![image](/assets/img/2024-05-17-ProtectingSensitiveDatawithCDCinDatabricks_2.png)

그림에서 보듯이 파이프라인에는 다음과 같은 요소들이 포함되어 있습니다:

- customers와 delete_requests 뷰는 Autoloader를 사용하여 해당 정보를 파이프라인으로 로드합니다.
- encryption_keys_v 뷰는 encryption_keys 테이블의 현재 상태를 쿼리하므로 적용할 변경 사항을 보다 쉽게 식별할 수 있습니다.
- new_keys 뷰는 customers와 encryption_keys_v 뷰를 비교하여 데이터베이스에 새로운 고객이 들어오는 것을 식별하고 이러한 새로운 고객을 위해 암호화 키를 생성합니다.
- delete_keys 뷰는 delete_requests 뷰를 읽어서 암호화된 고객의 암호화 키를 해시하는 고객을 식별합니다. 그런 다음, encryption_keys_v와 new_keys에서 가져온 이러한 암호화 키를 해싱합니다.
- keys_to_upsert 뷰는 delete_keys와 new_keys 뷰를 병합합니다.
- encryption_keys 테이블의 경우 DLT의 CDC apply_changes 함수를 사용하여 keys_to_upsert 뷰에서 레코드를 업서트합니다.
- quarantine 뷰는 마지막으로, encryption_keys 테이블의 해당 암호화 키를 사용하여 customer의 민감한 필드를 암호화합니다.
- 마지막으로, encrypted_customers는 파이프라인의 최종 출력 테이블입니다. quarantine 뷰를 구성합니다.

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

apply_changes 함수는 CDC를 처리하는 우리의 파이프라인의 핵심 구성 요소입니다. 다음과 같은 필수 인수를 받습니다:

- target: 업데이트할 테이블이며, 우리의 경우에는 encryption_keys 테이블입니다.
- source: 적용할 변경 사항을 포함하는 테이블 또는 뷰입니다. 여기서는 keys_to_upsert 뷰를 사용합니다.
- keys: 행을 업데이트하거나 삽입할 기본 키입니다. 여기서는 고객 ID를 사용할 것입니다.
- sequence_by: 처리할 변경 사항의 순서를 정렬하는 열입니다. 이벤트가 등록된 타임스탬프를 사용합니다.

이 함수는 column_list와 같은 출력 열을 유지하는 추가적인 선택적 인수를 지원하며, 레코드를 삭제하는 조건을 받는 apply_as_deletes와 같은 선택적 인수를 지원합니다. (우리의 경우에는 사용하지 않았습니다).

## 후처리 작업

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

지금까지 민감한 데이터의 암호화와 데이터베이스로부터의 삭제에 대해 다뤘습니다. 하지만 이 데이터에 권한이 있는 사용자에게 액세스를 부여하는 방법은 무엇일까요? 다음과 같이 진행됩니다: 우리는 사용자 쿼리에 따라 암호화된 또는 해독된 데이터를 검색하는 동적 뷰를 생성하는 후 처리 작업을 만들었습니다. 이 후 처리 작업은 키를 해싱한 후 encryption_keys 테이블을 초기화하는 작업도 수행합니다. 이 마지막 단계는 앞의 테이블 버전이 쿼리될 경우 해싱된 후에도 암호화 키를 복구할 수 있는 델타 라이브 테이블의 시간 이동 기능을 제한하기 위해 필요했습니다.

우리의 파이프라인의 주요 구성 요소를 설명했으니, 이제 최종 결과를 살펴보겠습니다.

## 아키텍처 개요

DLT 파이프라인의 순차 실행과 후 처리 작업을 보장하기 위해 우리는 두 구성 요소를 포함하고 종속성을 정의하는 전체적인 워크플로우(이름: GDPR Job)를 만들었습니다. 우리 애플리케이션의 마지막 구성 요소로, AWS Lambda Functions와 Databricks의 Statement Execution API를 사용하여 사용자에게 출력 데이터를 제공하는 API를 개발했습니다. 마지막으로, 이러한 Databricks 및 AWS 리소스의 배포는 Terraform 및 Terragrunt를 사용하여 스크립팅되고 매개변수화되었습니다.

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

다음 이미지를 통해 최종 애플리케이션을 더 잘 이해할 수 있습니다:

![보호되고 있는 민감한 데이터와 CDC를 사용한 Databricks](/assets/img/2024-05-17-ProtectingSensitiveDatawithCDCinDatabricks_3.png)

## 마무리 말

Terraform와 Terragrunt를 활용하여 CDC 지원이 포함된 이식 가능한 GDPR 준수 ETL 파이프라인 템플릿을 생성할 수 있었습니다. 통합 및 QA 테스팅을 위해 우리 애플리케이션을 위한 테스트 스위트를 작성했는데, 이는 사전 작업 노트북과 유사합니다. 이 노트북은 서로 다른 시나리오를 테스트하기 위해 데이터를 해당 경로에 저장한 후 GDPR 작업을 트리거하고 출력물이 예상과 일치하는지 확인합니다.

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

요약하자면, Databricks가 안전하고 견고한 데이터 처리 애플리케이션을 개발하는 데 필요한 모든 구성 요소를 제공한다는 것을 보여드렸습니다. 그러나 이 프로젝트의 실행은 간단하지 않았습니다. 호환성 문제와 통합 문제가 있었는데 이는 우리가 원래의 아키텍처를 수정하여 해결해야 했습니다. 다행히, 이러한 문제들 중 많은 것들이 이미 Databricks에 의해 해결되었거나 향후 배포를 위해 노력 중이라는 점입니다.

## 참고문헌

- Databricks Delta Live Tables를 사용한 변경 데이터 캡처 간소화
- Databricks Delta Live Tables로 PII 방화벽 구축하기
