---
title: "Azure Databricks와 Microsoft Fabric 통합하기"
description: ""
coverImage: "/assets/img/2024-06-19-IntegratingAzureDatabricksandMicrosoftFabric_0.png"
date: 2024-06-19 12:19
ogImage:
  url: /assets/img/2024-06-19-IntegratingAzureDatabricksandMicrosoftFabric_0.png
tag: Tech
originalTitle: "Integrating Azure Databricks and Microsoft Fabric"
link: "https://medium.com/@piethein/integrating-azure-databricks-and-microsoft-fabric-0030d3cf5156"
---

고지: 본 글은 Microsoft나 Databricks의 공식 입장이 아닌 저의 개인적인 경험과 견해를 반영하고 있습니다.

본 글은 고객 상호작용 중 자주 언급되는 핫 토픽인 Azure Databricks와 Microsoft Fabric의 조합과 통합에 대해 다룹니다. 두 서비스는 각자의 분야에서 최고 수준을 자랑합니다. Azure Databricks는 데이터 엔지니어링, 데이터 과학 및 머신 러닝 워크로드의 확장에 능합니다. 마찬가지로 Microsoft Fabric은 다양한 데이터 사용을 위한 간편성과 셀프 서비스 기능으로 빛을 발합니다. 보통 제기되는 핵심 질문은: 이 두 강자를 어떻게 통합할 수 있을까요?

현재 고려해야 할 다섯 가지 옵션이 있습니다. 이 글은 새로운 기능이 추가됨에 따라 발전할 수 있음을 염두에 두십시오.

- 보고 및 분석 레이어를 추가하여 Databricks를 활용한 아키텍처를 더욱 강화합니다.
- OneLake 골드 레이어를 통합하여 Databricks를 활용한 아키텍처를 보완합니다.
- Databricks가 모든 데이터를 OneLake에 기록하도록 합니다. 권장되지는 않지만 논의할 가치가 있습니다.
- V-ORDERED 활성화된 소비 레이어를 통해 Databricks를 확장합니다.
- 추가 구성 요소를 추가하여 Databricks 및 Microsoft Fabric의 데이터 처리 효율성을 향상시킵니다. 이는 좀 더 개인적인 접근입니다.

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

금일 제공되는 옵션은 다음 섹션에서 철저히 검토될 것입니다. 미묘한 차이점을 제공하고 장단점을 고려하며 관련 문서를 참조할 것입니다. 그러나 그보다 앞서, 두 강력한 도구를 활용하기로 선택하는 조직이 그 이유를 이해하는 데 도움이 됩니다.

## 왜 이 조합을 선택하는가?

조직은 Azure Databricks와 Microsoft Service Fabric을 결합하는 이유 때문에 이 조합이 제공하는 독특한 기능들을 선호합니다.

다양한 규모의 조직에서 선호하는 종합 데이터 처리, 분석 및 데이터 과학 플랫폼인 Azure Databricks는 긴 역사와 다양한 조직에서의 성공적인 채택으로 신뢰할 수 있는 플랫폼으로 자리매김했습니다. Spark의 창시자들에 의해 설립된 Databricks는 주로 엔지니어들을 위해 제공되며, 대규모로 Spark 워크로드를 관리하고 노트북 작성 및 복잡한 작업을 처리할 수 있는 플랫폼을 제공합니다.

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

마이크로소프트 패브릭의 매력은 그 간단함에 있습니다. 2023년에 출시되어 파워 BI에서 진화한 이 제품은 기존 파워 BI 사용자에게 쉬운 전환을 제안합니다. 사용자 친화적인 인터페이스, 통합 셀프 서비스 기능, 그리고 마이크로소프트 365와의 심플한 통합으로 비즈니스 사용자들의 특히 매력을 끌고 있습니다. 마이크로소프트 패브릭은 데이터 사용을 민주화하고 진입 장벽을 낮추기 위해 설계되어 있어, 모든 사용자에게 접근성 있는 플랫폼으로 인기를 끌고 있습니다.

본질적으로, Azure Databricks와 마이크로소프트 서비스 패브릭의 결합은 기술적, 비즈니스적 요구를 모두 충족하는 종합적인 솔루션을 제공하여, 많은 조직들 사이에서 인기를 얻고 있습니다.

이제 조직들이 종종 이 결합을 선택하는 이유를 알게 되었습니다. 이제 두 서비스를 어떻게 통합할 수 있는지 알아보겠습니다.

## 리포팅 및 분석 레이어를 추가하여 데이터브릭스 지원 아키텍처를 강화하기

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

첫 번째 디자인 고려 사항은 일반적인 Azure Databricks Medallion Lakehouse 아키텍처를 개선하는 데 관여합니다. 이 아키텍처는 Azure Data Lake Storage (ADLS) gen2, Azure Data Factory 및 Azure Databricks와 같은 서비스를 활용합니다. 이 설정에서 Databricks는 데이터 투입, 처리, 검증 및 보강의 모든 측면을 관리합니다. PowerBI는 일반적으로 보고와 분석적인 통찰을 전달하는 것을 포함한 나머지 작업을 처리합니다.

Microsoft Fabric을 포함한 Databricks 중심 아키텍처를 확장하여 자기 서비스 기능을 강화하고 비즈니스 사용자를 위한 사용자 경험을 향상시키는 것은 인기 있는 전략입니다. Databricks와 PowerBI에 새로운 기능과 능력을 갖추어 더욱 매력적이고 효율적인 경험을 제공한다고 생각해보세요.

Microsoft는 최근 Microsoft Fabric을 위한 '바로 가기'라는 새로운 기능을 소개했습니다. 이 기능은 다양한 소스에서 데이터를 읽어내어 데이터 중복을 제거하고 직접 데이터를 사용할 수 있게 하는 가벼운 데이터 가상화 엔진 역할을 합니다. 예를 들어, PowerBI를 사용할 때 PowerBI에 데이터를 복사하거나 가져오지 않고 필요한 데이터에 즉시 액세스할 수 있습니다.

우리가 이전에 이야기한 Databricks 중심 디자인과 관련해서, Databricks가 모든 데이터를 ADLS에 쓰기 때문에 ADLS Gen2 바로 가기 기능을 활용할 수 있습니다. 그러나 주의해야 할 중요한 고려 사항이 몇 가지 있습니다:

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

- 단축키를 사용하려면 패브릭 레이크하우스가 필요합니다. 이미 보유하고 있지 않다면, 하나를 만드는 것을 잊지 마세요.
- 테이블에 대한 바로 가기는 Delta Lake 형식의 데이터에만 액세스할 수 있습니다.
- 다브릭 관리 테이블 대신 외부 테이블에 대한 바로 가기를 가능한 한 사용하세요. 다음 설계 고려 사항을 논의할 때 이 부분에 다시 언급하겠습니다.
- 각 바로 가기는 단일 Delta 폴더를 참조할 수 있습니다. 그러므로 여러 Delta 폴더에서 데이터에 액세스해야 한다면, 각 폴더에 대해 개별적인 바로 가기를 만들어야 할 것입니다.
- 이러한 테이블 디렉토리에서 파일을 직접 조작하지 마세요. 대신, ADLS에서 Delta 파일을 읽는 읽기 전용 접근 방식을 사용하세요. 이 접근 방식에서 ADLS는 중간 저장소로 작동합니다. Databricks에서 테이블을 직접 읽지 않습니다.
- Lakehouse에 바로 가기를 만드는 것은 Fabric UI를 통해 수동으로 수행해야 합니다. 또는 REST API를 사용하여 모든 바로 가기를 자동으로 제공할 수 있습니다. 여기 튜토리얼 및 노트북 스크립트 링크가 있습니다.
- ADLS에서 데이터를 직접 읽을 때는 Unity 카탈로그의 보안 모델의 데이터 액세스 정책이 적용되지 않습니다.

Databricks와 Microsoft Fabric을 통합하는 과정에서 흥미로운 발전이 동행되고 있습니다! 이러한 기능들은 Microsoft Build 2024 컨퍼런스에서 발표되었습니다. 곧 Azure Databricks Unity Catalog를 Fabric과 통합할 수 있을 것입니다. Fabric 포털을 통해 새로운 Azure Databricks Unity Catalog 항목을 만들고 구성할 수 있을 것입니다. 이 단계를 거치면 Unity Catalog에서 관리되는 모든 테이블이 즉시 바로 가기로 업그레이드될 수 있을 것입니다. 이 예정된 통합은 Azure Databricks 데이터를 Fabric에 효율적으로 통합하여 Fabric 워크로드 전반에 걸쳐 원활한 운영을 지원할 것입니다. 이 새로운 기능의 데모는 여기에서 찾아볼 수 있습니다: https://www.youtube.com/watch?v=BYob0cGW0Nk&t=4434s

데이터 사용을 위해 Microsoft Fabric을 사용하는 확장된 Databricks 중심 아키텍처는 Databricks에 아주 만족한 고객들 사이에서 흔히 관찰됩니다. 이들 고객은 이미 Databricks를 사용하여 레이크하우스를 구축하는 데 상당한 시간과 자원을 투자하였으며, 이를 계속 활용할 계획입니다. Microsoft Fabric는 Delta 형식을 사용하는 레이크하우스 접근 방식의 강점과 다재다능성을 인식합니다. 이는 데이터 소비에 최적화된 레이어를 추가하여 (기존) 아키텍처를 향상시킬 수 있게 해줍니다. 이를 통해 조직은 Databricks 중심 설정에 데이터 사용을 위한 추가적인 레이어를 특별히 추가함으로써 기존 아키텍처를 강화할 수 있습니다.

## OneLake 골드 레이어를 통합하여 Databricks가 가능한 아키텍처를 칭찬하세요.

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

두 번째 디자인은 초기 디자인 패턴을 수정하여 OneLake 골드 레이어를 구조에 통합합니다. 이것은 Azure Databricks의 Azure Blob 파일 시스템 (ABFS) 드라이버 덕분에 가능합니다. 이 드라이버는 ADLS와 OneLake를 모두 지원합니다. 아래에 이 접근 방식의 그림을 보실 수 있고 MS Learn 페이지에서 Notebook 예제를 찾을 수 있습니다.

이 구조 내에서 전반적인 워크플로 및 데이터 처리 단계 — 데이터 수집, 처리, 유효성 검사, 데이터 보강 — 는 크게 변경되지 않습니다. 모든 것은 Azure Databricks 내에서 관리됩니다. 핵심 차이점은 이제 데이터 사용을 위한 데이터가 Microsoft Fabric에 더 가까워졌다는 것입니다. 왜냐하면 Databricks는 데이터를 OneLake에 저장된 Gold 레이어에 기록하기 때문입니다. 이것이 최선의 방법이며 이것이 왜 유익한지 궁금할 수 있습니다.

중요한 점은 이 통합 방식이 Databricks에서 공식적으로 지원되지 않는다는 것이며, 데이터 관리에 영향을 미칩니다. 이에 관해 다음에 자세히 다룰 것입니다. 더 많은 정보를 원하시면 Databricks 문서를 참조해 주세요.

Databricks는 관리형 테이블과 외부 테이블 두 가지 유형의 테이블을 구분합니다. 관리형 테이블은 기본적으로 생성되며 Unity Catalog에 의해 관리되며 수명주기 및 파일 레이아웃을 제어합니다. 외부 도구를 사용하여 이러한 테이블에서 파일을 직접 조작하는 것은 권장되지 않습니다. 이에 반해, 외부 테이블은 메타스토어, 카탈로그 또는 스키마에 지정된 관리형 저장 위치 외부에 데이터를 저장합니다.

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

그래서, 문서에서 제공한 지침을 기반으로, 이 접근 방식을 사용하여 OneLake에 직접 쓰여진 모든 테이블은 외부 테이블로 분류하는 것이 권장됩니다. 이는 데이터가 메타스토어의 범위 바깥에서 관리되기 때문입니다. 그 결과, 이러한 테이블의 관리는 Fabric 내부와 같은 다른 곳에서 수행해야 합니다. 이 방식의 동기는 다음과 같을 수 있습니다:

첫째, OneLake에 데이터를 물리적으로 저장하는 것은 Microsoft Fabric 내에서 성능을 향상시킵니다. 이는 OneLake 테이블이 특히 조인 및 집계와 관련된 쿼리에 최적화되어 있기 때문입니다. 그에 반해, ADLS Gen2를 통해 바로가기를 통해 데이터를 읽는 경우, 이러한 작업을 포함하는 쿼리에 대해 성능이 느릴 수 있습니다.

둘째, OneLake에서 데이터를 관리하는 것은 Microsoft Fabric 내에서 보안 조치를 적용하는 데 유용합니다. 예를 들어, OneLake 테이블은 역할 기반 액세스 제어 (RBAC)를 사용하여 보안할 수 있어 데이터 액세스를 관리하는 과정이 단순해집니다. 그러나 ADLS Gen2를 사용할 경우, ADLS Gen2 저장소 계정의 권한을 처리해야 하므로 더 복잡할 수 있습니다.

셋째, OneLake 테이블은 정책에 따라 관리될 수 있어 데이터가 규정에 따라 사용되도록 보장하기가 더 쉽습니다. 예를 들어, 다른 곳에 있는 도메인과 테이블을 (외부적으로) 공유할 때입니다.

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

데이터를 읽는 것 이외에도 Microsoft Fabric 내에서 새로운 데이터를 생성하는 것을 고려해 볼 수 있습니다. 만약 이것이 여러분의 계획 중 일부라면, 다가오는 기능이 매우 흥미로울 수 있습니다. 곧 Fabric 사용자들은 Unity 카탈로그를 통해 Azure Databricks에서 lakehouses와 같은 데이터 항목에 액세스할 수 있게 될 것입니다. 데이터는 여전히 OneLake에 남아 있겠지만, Azure Databricks에서 해당 데이터의 계보 및 다른 메타데이터에 직접 액세스하고 보는 능력을 갖게 될 것입니다. 이 향상된 기능은 Fabric에서 Databricks로 다시 데이터를 읽는 것을 용이하게 할 것입니다. 예를 들어, Azure Databricks의 Mosaic AI를 활용해 AI를 활용하려는 경우 Microsoft Fabric에서 다시 읽음으로써 가능할 것입니다. 이 기술은 아마도 Lakehouse Federation일 것입니다. 이 내용에 대한 자세한 정보는 이 비디오의 해당 부분에서 확인할 수 있습니다: https://youtu.be/BYob0cGW0Nk?t=4125

마지막으로, 모든 통합 및 데이터 처리를 Databricks에서 처리하고 소비 레이어를 Fabric에서 관리하는 전략은 각 응용 프로그램 영역에서 가장 좋은 기능을 활용할 수 있도록 하는 편리함을 기관들에게 제공합니다. 이 접근 방식은 데이터 처리의 최적 성능과 보안을 보장합니다.

## Databricks가 모든 데이터를 OneLake에 기록하도록 설정 (권장되지 않음)

Databricks를 OneLake와 통합하는 경험을 바탕으로, OneLake가 ADLS Gen2와 동일한 API를 지원한다는 것을 알고 있습니다. 이를 감안해 보면, 가상의 설계 가능성을 고려해 보겠습니다: 모든 Medallion 레이어를 OneLake에 저장하는 것입니다. 이 가능할까요? 알아보도록 하죠.

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

이 접근 방식에 대한 인센티브는 신규 배포로부터 비롯될 수 있습니다. 여기서의 목표는 데이터 엔지니어링 작업을 효율적으로 확장하면서 Microsoft Fabric을 사용하여 모든 계층에서 데이터 사용 및 소비에 대한 설계 간결성과 셀프 서비스를 장려하는 데 Databricks의 기본 기능을 활용하는 것입니다.

유감스럽게도, 이 설계는 효율적인 데이터 관리에 적합하지 않습니다. 이 구성은 각 워크스페이스 계층이 Microsoft Fabric의 자체 Lakehouse 엔터티를 필요로 하기 때문에 작업공간이 증가함에 따른 관리 오버헤드로 이어질 수 있습니다. 이 증식은 데이터 공유 시 통제, 메타데이터 관리 및 협업 오버헤드와 같은 추가적인 도전 과제를 야기할 수 있습니다. 또한 Databricks는 관리형 테이블을 사용할 때 이 접근 방식을 지원하지 않습니다. 따라서, 비록 이 아키텍처가 이론적으로 매력적으로 보일 수 있지만, 저는 최선의 실천으로 이를 사용하는 것을 강력히 비추합니다.

## V-ORDERED 활성화 소비 계층으로 Databricks 확장

다음 설계 고려사항은 Microsoft Fabric의 사용과 V-Order 기능을 활용하는 데 더 중점을 둘 것입니다. 이 기능은 파케이 파일 형식에 대한 라이트 타임 최적화로, Microsoft Fabric 컴퓨팅 엔진(예: Power BI)에서 빠른 데이터 읽기를 가능케 합니다.

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

Databricks와 Microsoft은 오픈 소스 열 기반 파일 형식인 Delta Lake를 채택하기로 선택했습니다. 그러나 Microsoft은 V-Order 압축의 추가 레이어를 통합했는데, 이것은 최대 50%의 더 많은 압축을 제공합니다. V-Order는 오픈 소스 parquet 형식과 완전히 호환되며, 모든 parquet 엔진이 일반 parquet 파일처럼 읽을 수 있습니다.

참고로 V-Order를 적용하여 Fabric의 유지 관리 기능을 활용하면 V-Order가 없는 테이블에 적용할 수 있습니다.

V-Order는 Microsoft Fabric에 중요한 이점을 제공하는데, 특히 Power BI 및 SQL 엔드포인트와 같은 구성 요소에게 도움이 됩니다. 예를 들어, Power BI를 사용하여 데이터 쿼리 중에 뛰어난 성능을 유지하면서 실시간 데이터에 직접 연결할 수 있습니다. 데이터 소스의 변경 사항이 Power BI에 즉시 반영되므로 새로 고침을 기다릴 필요가 없어집니다.

V-Order로 최적화된 테이블을 사용하는 것은 현재 Microsoft Fabric에게만 제한되어 있습니다. Databricks는 아직이 기능을 통합하지 않았습니다. 따라서 그런 경우에는 V-Order로 최적화된 테이블을 활용하기 위해 Microsoft Fabric 내의 서비스를 활용해야 합니다.

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

먼저 실버(Silver)와 골드(Gold) 단계 사이의 Databricks를 통한 처리 단계가 V-Order 최적화가 필요하지 않을 경우에도 여전히 중요하다는 주장이 가능하다는 점을 감안해 볼 수 있습니다. 이 부분은 반복적으로 보일 수 있지만, Databricks를 이용한 지속적인 데이터 처리를 가능하게 하는 타당한 선택지입니다.

다른 주목할만한 측면은 왜 조직이 이 설계를 선택하는지에 대한 것이며, 이는 여러 테이블 간의 거래 일관성을 유지하는 것입니다. 특히, 이러한 일관성을 Gold에서 유지하는 것은 매우 중요합니다. 현재 Spark는 개별 테이블에 대한 트랜잭션만 지원합니다. 따라서, 테이블 간에 데이터 불일치가 있는 경우 보상 조치를 통해 해결해야 할 수 있습니다. 예를 들어, 구매 주문에 대한 세 가지 테이블에 영향을 미치는 변경 사항을 하는 경우, 이러한 변경 사항을 하나의 트랜잭션으로 묶을 수 있습니다. 이것은 해당 테이블을 쿼리할 때 모든 변경 사항이 있거나 전혀 없을 것임을 의미합니다. 이러한 무결성 문제는 다수의 테이블에 걸쳐 복잡한 트랜잭션을 관리할 수 있는 환경의 중요성을 강조하며, Delta Lake 위에서 이를 지원할 수 있는 유일한 플랫폼은 Microsoft Fabric Warehouse입니다. 자세한 내용은 여기에서 확인할 수 있습니다.

위 이미지에서 나타난 업데이트된 아키텍처에서는 Synapse Engineering이 이제 실버(Silver)에서 골드(Gold)로의 처리 엔진으로 작동합니다. 이 접근 방식은 모든 테이블이 V-Order 최적화되도록 보장합니다. 추가로, 트랜잭션 기능이 필요한 사용 사례를 위해 Synapse Warehouse가 추가되었습니다. 그러나 이러한 아키텍처 변경 사항은 데이터 엔지니어가 서로 다른 독특한 데이터 처리 서비스를 탐색해야 한다는 의미입니다. 따라서 모든 팀에게 명확한 지침을 제공하는 것이 중요합니다. 예를 들어, Databricks의 기본 기능인 AutoLoader와 Delta Live Tables를 활용한 데이터 품질 검증을 위해 브론즈(Bronze) 및 실버(Silver)에 대한 원칙을 수립하고, 골드(Gold)에서는 Microsoft Fabric와만 연동되는 소비특화 통합 로직 구축에 초점을 맞출 수 있습니다.

## 추가 구성 요소를 추가하여 Databricks 및 Microsoft Fabric의 데이터 처리 효율성 향상하기

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

우리 이전 토론에서는 엔지니어들이 다른 데이터 처리 서비스를 탐험해야 하는 과제에 대해 논의했습니다. 이 문제는 메타데이터 주도 접근법과 DBT와 같은 템플릿 프레임워크를 채택하여 해결할 수 있습니다. 새로운 아키텍처에서는 Databricks와 Microsoft Fabric에 추가 구성 요소를 결합했습니다. 이 변경 사항에 대해 자세히 알아보도록 하겠습니다.

Databricks 측면에서는 메타데이터 주도 프레임워크(메타데이터 저장소), Great Expectations 및 Data Build Tool (DBT)를 추가했습니다. 메타데이터 주도 프레임워크는 작성하고 유지해야 하는 코드 양을 크게 줄일 수 있습니다. 다중 노트북을 생성하는 대신 이 접근 방식을 통해 모든 데이터의 수집 및 유효성 검사를 위한 범용 파이프라인을 활용할 수 있으며 Great Expectations라는 다른 오픈 소스 프레임워크로 데이터를 수집하고 유효성 검사할 수 있습니다. 이 접근 방식은 메타데이터 저장소에서 읽어와 다른 스크립트를 동적으로 호출함으로써 달성됩니다. 이 접근 방식에 대해 더 알고 싶다면 이 주제에 대한 또 다른 블로그 글을 추천합니다.

다음으로 DBT에 대해 이야기해 봅시다. 이 오픈 소스 명령줄 도구인 데이터 빌드 툴(DBT)은 Python으로 작성되었습니다. 그 강점은 SQL의 SELECT 문과 유사한 구문을 사용하여 템플릿을 활용해 변환을 정의하는 범용 인터페이스를 제공하는 데 있습니다. Databricks는 dbt-databricks 패키지를 통해 지원됩니다. DBT와 Databricks를 사용하는 데 대한 자세한 정보를 원한다면 이 주제에 대한 다른 블로그 글을 읽는 것을 권장합니다.

Microsoft Fabric 측면에서도 DBT가 중요한 역할을 할 수 있습니다. Microsoft Fabric 내에서 Synapse Warehousing을 위해 dbt-fabric 또는 Synapse Spark을 위해 dbt-fabricspark 중에서 선택할 수 있습니다. 이 템플릿 접근 방식의 장점은 개발자가 모든 데이터 변환 사용 사례를 위한 단일 프론트엔드에 익숙해지기만 하면 두 서비스를 모두 활용할 수 있다는 점입니다. 이 방법론은 프로세스를 간소화하고 효율성을 높일 수 있습니다.

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

## 결론

Azure Databricks와 Microsoft Fabric의 통합은 조직에 수많은 혜택과 가능성을 제공합니다. Azure Databricks의 유연성과 확장성이 Microsoft Fabric의 간편하고 사용자 친화적인 기능과 결합되면 모든 계층에서 데이터 사용 및 관리를 혁신적으로 개선할 수 있습니다. Databricks 중심 아키텍처를 Microsoft Fabric 레이어로 향상시키거나 성능 및 보안을 향상시키기 위해 아키텍처에 OneLake 골드 레이어를 통합하기까지 여러 아키텍처 디자인 선택지가 있습니다.

또한, Microsoft Fabric의 V-Order 최적화 소개와 추가 구성 요소 사용은 데이터 처리 효율성을 현격히 향상시키고 간소화할 수 있습니다. 그러나 이러한 조합 또는 통합은 서비스 탐색과 유연성, 데이터 보안 및 격리를 균형있게 고려해야 할 수도 있습니다.

요약하자면, Azure Databricks와 Microsoft Fabric의 통합은 Microsoft Build 2024 Conference에서 발표된 흥미로운 혁신과 함께 빅 데이터 처리 워크로드를 위한 희망찬 미래를 암시합니다.
