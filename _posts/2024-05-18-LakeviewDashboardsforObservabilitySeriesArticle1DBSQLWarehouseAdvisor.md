---
title: "Lakeview 대시보드를 통한 관측력 시리즈 - 기사 1 DBSQL 웨어하우스 어드바이저"
description: ""
coverImage: "/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_0.png"
date: 2024-05-18 16:24
ogImage:
  url: /assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_0.png
tag: Tech
originalTitle: "Lakeview Dashboards for Observability Series — Article #1 DBSQL Warehouse Advisor"
link: "https://medium.com/dbsql-sme-engineering/lakeview-dashboards-for-observability-series-article-1-dbsql-warehouse-advisor-f73ec8df1060"
---

표 태그를 Markdown 형식으로 변경해보세요.

![Lakeview Dashboard](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_0.png)

## 저자:

Cody Austin Davis

# Lakeview 대시보드 템플릿 시리즈 - 기사 1 - DBSQL Warehouse Advisor

## 소개

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

Lakeview 대시보드는 Databricks SQL에서 사용할 수 있는 새로운 가벼운 대시보드 도구입니다. 막 출시했음에도 불구하고 이미 놀라운 성능을 자랑하는데요. 추가 비용이나 관리 부담 없이 빠르게 대시보드를 개발하고 공유할 수 있는 기능을 제공합니다. 예를 들어, 대부분의 고객이 필요로 하는 가장 중요한 측면 중 하나는 데이터 플랫폼의 가시성입니다. 대부분의 고객이 특정 사용 사례에 대한 모니터링과 가시성을 필요로 하기 때문에 나는 Lakeview 템플릿 중 일부를 공개하여 누구나 가져다 쓰고 자신의 환경에서 사용할 수 있도록 했어요. 여러분의 노력을 아낄 수 있게끔 도와주고 Lakeview 대시보드가 무엇을 할 수 있는지 보여줄 수 있도록 했죠!

이 기사에서는 DBSQL Warehouse Advisor 템플릿을 공유하고 소개할 거에요. 대시보드의 4개 섹션에 대해 간단히 검토하고 사용하는 방법을 가르쳐 드리며 각 섹션을 통해 어떤 핵심 질문을 해결할 수 있는지 논의할 거에요.

먼저, 전반적으로, 이 대시보드가 무엇을 하는지와 어떤 부분에 초점을 맞추는지에 대해 알아볼까요. 공유될 여러 템플릿 중 첫 번째로, 템플릿을 언제, 어떻게 사용할지에 대한 맥락을 제시하고자 합니다.

DBSQL Warehouse Advisor Lakeview 대시보드는 DBSQL에서 데이터 웨어하우스에 대해 필요한 모든 정보를 제공합니다. warehouse_events, billing, 그리고 query_history (system table의 프라이빗 프리뷰, API는 이미 GA에서 사용 가능) 시스템 테이블을 자동으로 구문 분석하여 다음과 같은 작업을 지원합니다:

- 섹션 1 — 요금 모니터 — 시스템 내의 billing.usage 시스템 테이블을 모니터링하여 시간별 비용을 요약합니다. 웨어하우스가 너무 빨리 확장되는 시점을 보여주는 내장된 탄력 지표를 제공합니다. BI 워크로드는 특히 변동이 심할 수 있으므로, 부드러운 탄력 지표는 비용 경보에 대한 더 나은 측정 항목을 제공할 수 있습니다. 이 쿼리를 사용하여 경보를 설정할 수 있습니다!
- 섹션 2 — 크기 조정 모니터 — warehouse_events 시스템 테이블을 모니터링하여 웨어하우스의 크기 조정 비용과 효율성을 요약합니다. 변동이 심하고 고 동시성 워크로드의 사용량을 예측하고 추정하기 어려울 수 있습니다. 이 시각화를 사용하면 웨어하우스가 다른 크기 조정 수준에서 얼마나 시간을 보냈는지 요약하고 계획하기 쉬워집니다.
- 섹션 3 — 쿼리 성능 분석 — 이것은 매우 중요한 섹션입니다! (private preview 중인) query_history 시스템 테이블을 사용하여 웨어하우스의 전반적인 성능을 요약하고 문제가 되는 쿼리를 찾거나 병목 현상을 식별하며 SLAs를 추적하고 웨어하우스 시간이 어디에 소비되었는지 파악할 수 있습니다. 이 대시보드 하나에 고수준 모니터링부터 개별 쿼리 성능 튜닝까지 모두 담겨 있어요.
- 섹션 4 — 쿼리 비용/시간 할당 요약 — 웨어하우스를 사용하여 내부 또는 외부 고객에게 서비스를 제공하고, 소비 기반의 가격 모델에 따라 그들에게 요금을 청구해야 한다면, 이 섹션을 사용하여 쿼리, 사용자 및 쿼리 태그 수준에서 웨어하우스 시간을 할당할 수 있습니다! 데이터 제품 및 많은 하위 팀에 서비스를 제공하는 중앙화된 데이터팀에게 매우 중요합니다.

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

요약을 보여줬으니 이제 섹션을 살펴보겠습니다. 함께 따라오고 싶다면 템플릿을 다운로드하고 대시보드를 환경에 가져와서 데이터웨어하우스 ID를 입력하면 됩니다!\* 템플릿을 가져오면 전체 대시보드 시각화와 대시보드를 구동하는 전체 백엔드 쿼리와 매개변수를 확인할 수 있습니다. 이 템플릿은 여러분의 특정한 사용 사례에 맞게 필요한 대로 수정하고 확장할 수 있습니다.

- \*참고: query_history를 위한 시스템 테이블 비공개 미리보기 상태에 있지 않은 경우, query history API를 사용하여 공개 미리보기가 될 때까지 해당 테이블을 생성할 수도 있습니다.

# 섹션 1 — 요금 모니터

![이미지](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_1.png)

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

이것은 DBSQL Warehouse Advisor의 청구 모니터입니다. 이 섹션은 간단합니다. Warehouse Id를 매개 변수에 입력하고 날짜 범위를 선택하여 DBUs 및 달러의 추이를 시간에 따라 볼 수 있습니다. 또한 해당 시간 프레임 내의 총 비용에 대한 큰 숫자 요약도 제공됩니다. 또한 DBSQL Warehouse의 목록 요금과 다른 경우에 사용할 클러스터 단위 가격 매개 변수도 있습니다.

또한 시간에 맞춰 부드럽게 조정된 사용량의 추세를 추적할 수도 있어, 일시적인 사용량 변동에 과도하게 반응하는 것을 피할 수 있습니다. 고동시 BI 워크로드에서 일반적인 것으로, 관리자들은 종종 주어진 시간 범위 내에서 사용량이 특정 %로 상승하는 추세를 지켜보고 경보를 울리고 싶어 합니다. 이 운동량 지표 시각화를 사용하여 이를 수행할 수 있습니다:

![이미지](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_2.png)

이것은 앞서 소개한 "높은 증가 % 장벽" 매개 변수와 결합됨으로써, 사용 패턴을 "높은 사용 증가 추세"로 판단하고 그것을 강조하여 빨간색으로 표시하는 임계값을 정의할 수 있습니다. 그런 다음 최근 값 쿼리에 LIMIT 1을 추가하여 비용 추세에 대한 경보를 설정하는 데 사용할 수 있는 정확한 기본 SQL 쿼리를 사용할 수 있습니다. 이 지표 차트는 "이전 기간보다 24시간 이동평균 증가량이 20% 이상 증가할 때 보여줘"라고 말합니다. 이는 이 기능이 종종 발생하는 일시적인 변동보다 지속적이고 심각한 비용 증가에 대한 경보를 보내는 데 좋은 방법입니다.

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

# 섹션 2 — 데이터웨어하우스 동시성 스케일링 요약

다음 섹션에서는 데이터웨어하우스 동시 사용량을 해석하는 데 도움이 되며, DBSQL의 "모니터링" 탭에있는 차트는 매우 가파르고 계획하기 어려울 수 있습니다. 특히 Serverless SQL을 사용할 때는 이 차트를 사용하기 어려울 수 있습니다...

![이미지](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_3.png)

...그리고 이를 예산 및 장기적인 워크로드 / 데이터웨어하우스 비용에 대한 기대로 전환하기 어렵습니다. 더구나, 클러스터 크기, 동시성 설정, 쿼리 성능 변경 시 어떤 일이 발생하는지 확인하기 어렵습니다. 이 탭을 사용하면 더 명확한 예측과 사용량에 대한 기대를 만들 수 있습니다.

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

![Dashboard](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_4.png)

이 대시보드는 해석이 어려운 모니터링 탭을 자동으로 가져와 시각화로 변환하여 창고에서 N개의 동시 클러스터로 확장해야 하는 빈도를 더 명확하게 보여줍니다. 좀 더 자세히 살펴보면, 특정 시간 기간 동안 창고가 N개 클러스터로 확장되기까지 소요된 시간 및 총 시간 중 % 비율을 정확하게 볼 수 있습니다.

우리는 창고가 대략 80%의 시간 동안 1개 클러스터가 필요하고, 13%의 시간 동안 2개 클러스터가 필요하며, 대략 7%의 시간 동안 3개 클러스터, 그리고 1%의 시간 동안 4개 클러스터가 필요함을 명확히 볼 수 있습니다. 이를 통해 예측이 어려운 이러한 고비용 워크로드를 시간이 지남에 따라 얼마나 비용이 발생할지 예측하는데 도움이 되는 것은 물론, 이에 대한 변경 사항에 대해 계획하는 것도 더 쉽게 할 수 있습니다. 이 정보는 DBSQL의 어떠한 프로덕션 워크로드에 대비한 계획을 세우는 데 중요합니다.

워크로드를 최적화하거나 POC를 실행하거나 사용 사례를 추가할 경우, 이 대시보드를 사용하여 사용 패턴에 미치는 영향을 정확히 파악하여 미리 계획할 수 있습니다. 이 부분은 작지만 굉장히 유용합니다.

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

# 섹션 3 — 쿼리 성능 분석

이 섹션은 가장 크며, 고수준 성능 메트릭부터 실행 시간, 디스크 스피릴, 데이터 읽기 등의 쿼리 수준 세부 사항까지 모두 담고 있습니다. 이 섹션의 세부 사항은 전적으로 별도의 블로그를 작성할 수 있을 정도로 방대하므로, 이 글에서는 포함된 고수준 메트릭 및 이 섹션을 사용할 때 일반적으로 고려할 수 있는 몇 가지 이유에 초점을 맞출 것입니다.

이 섹션에서는 주로 쿼리 실행 시간과 대기 시간에 관심이 있습니다. 먼저 이러한 메트릭을 매우 고수준으로 하나의 큰 숫자 요약으로 살펴보고, 이후에는 SLA 차트와 함께 추세를 자세히 살펴볼 것입니다:

![LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_5](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_5.png)

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

![Lakeview Dashboards for Observability Series Article](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_6.png)

![Lakeview Dashboards for Observability Series Article](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_7.png)

쿼리 실행 시간 및 대기 시간 추이를 확인할 수 있습니다. 이제 데이터 웨어하우스가 언제 더 혼잡하거나 덜 혼잡해지는 지 쉽게 파악할 수 있어 문제를 빠르게 찾을 수 있게 되었습니다. 그러나 실제 워크로드에 대한 평균 실행 시간만 살펴본다고 해결되지 않습니다. 실제 SLA에 따라 쿼리 시간을 추적해야하며, 이를 위해 P90, P95 및 P99와 같은 표준 지표를 더 계산해야 합니다. 이 대시보드는 이러한 지표를 자동으로 계산하고 표시합니다. 위 시각적 자료를 통해 쿼리 실행 시간을 추적하여 쿼리 성능 문제가 발생하는 정확한 시기를 찾을 수 있습니다(중간에 큰 증가를 보았나요? 아마 확인해야할 이상 현상일 것입니다). 웨어하우스가 포화 상태에 있는지 추적하기 위해 동일한 방법으로 쿼리 대기 시간도 살펴볼 수 있고, 웨어하우스가 더 많은 동시성을 필요로 할 수도 있습니다. 실제로 여러분은 이 둘을 결합하여 사용 사례의 SLA 요구 사항에 맞게 클러스터 크기를 조절해야 합니다.

예를 들어, Databricks SQL로 구동되는 Downstream 데이터 애플리케이션이 있다고 가정해보겠습니다. 사용자들은 앱이 대부분의 쿼리를 1~2초 이내 또는 그 이하로 수행할 것으로 예상하며, 10초 이상 걸리는 것은 용인할 수 없습니다. 쿼리 실행 시간 차트의 P99 및 P95 백분위 지표를 확인하여 SLA 준수를 먼저 확인할 수 있습니다:

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

![image](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_8.png)

우리는 SLA 준수에 대해 멋져 보입니다. 대부분의 쿼리 (P95)가 1초 미만으로 실행됩니다. 우리의 가장 나쁜 쿼리도 약 5초 정도로 우리의 최종 사용자가 허용할 범위 내에 있습니다. 이러한 숫자가 허용 가능한 수준을 초과하면 쿼리를 최적화하거나 창고가 포화되었는지 대기 시간 시각화를 확인할 수 있습니다:

![image](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_9.png)

이제 필요한 SLA 범위 내에 있습니다. 그러나 만약 우리의 P99가 3초였다면 어떨까요? 그럴 경우 문제가 생길 수 있습니다. 먼저 할 일은 위의 대시보드에서 쿼리 대기 시간을 살펴보는 것입니다. 대기 시간은 각 쿼리가 실행할 수 있는 슬롯을 기다리며 대기하는 시간입니다. SLA를 준수할 때는 대기가 괜찮지만 그렇지 않은 경우, 소중한 시간이 낭비되고 더 많은 동시성이 필요합니다! 이 시각화는 평균 쿼리 실행 시간과는 다르게 해석되며, 보통 대기 시간이 낮아야 합니다(~0초). 1초보다 훨씬 높아지면 쿼리가 쌓여서 대기열에 앉아있기 시작한다는 것을 의미하며, 이는 우리의 SLA에 영향을 미칩니다. 갱신된 3초 SLA를 고려한 우리 예시에서, 우리의 P99는 현재 5초이며, 이는 허용할 수 없는 수준입니다. 위 시각화에서 많은 쿼리가 거의 2초씩 대기한다는 것을 볼 수 있습니다! 동시 용량을 늘리는 것이 SLA에 더 가까워지는 데 도움이 될 수 있음을 보여줍니다 (DBSQL의 최소 최대 클러스터).

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

**기억하세요 — 원하는 SLA에서 시작해서 거꾸로 진행하세요.**

고수준 성능 지표 외에도 이 대시보드에는 많은 유용한 지표가 있습니다!

![대시보드 이미지](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_10.png)

우리는 쿼리 성능을 상태, 소스 및 문장 유형별로 시간에 따라 추세 분석할 수 있고, 심지어 쿼리 태그별 평균 쿼리 실행 시간도 모니터링할 수 있습니다. 대시보드에 내장된 기능이며, 다음 서명을 가진 템플릿된 코멘트를 찾습니다:

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

회사의 특정 사용 사례에 중점을 두어 전체 성능 분석에 도움을 줄 수 있습니다. 특히 ETL 워크로드에서 중요합니다.

더욱 심층적으로 파고들 수 있습니다. "쿼리 성능 상세" 하위 섹션으로 이동하여 쿼리에서 발생하는 일반적인 문제와 병목 현상을 식별할 수 있습니다. 대시보드에서는 자동으로 플래그를 계산하여 장기 실행 중인 쿼리, 스피룰이 발생한 쿼리, 그리고 이례적으로 높은 데이터 양을 읽는 쿼리를 식별하는 데 도움이 됩니다.

![image](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_11.png)

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

아래로 내려가면 각 쿼리당 할당된 비용을 계산하고, 쿼리 태그별로 그룹화할 수 있습니다 (기본적으로 위에서 정의되었지만, 필요에 맞게 이 템플릿을 변경할 수 있습니다!). 또한 성능 문제의 근본 원인을 확인하기 위해 쿼리 수준의 세부 정보를 전체 출력할 수도 있습니다. 대시보드에서는 사용자별 할당된 창고 비용, 사용자별 계산 사용량 % 및 사용자별 쿼리 수를 볼 수도 있습니다.

![image](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_12.png)

이것은 시작점으로서 제공되는 것일 뿐이므로, 여러분의 사용 사례에 맞추기 위해 이 대시보드를 가져오고, 탐색하고, 확장하는 데 자유롭게 사용하세요!

# 섹션 4 — 쿼리 비용 할당 요약

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

이 섹션은 외부 데이터 제품을 가진 고객이 자신들의 데이터 제품에 합리적인 가격 모델을 개발하기 위해 쿼리 수준의 비용 가시성을 원하는 경우에 대해 우리가 자주 받는 질문입니다.

![이미지](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_13.png)

이 차트에서는 이미 쿼리를 어떻게 할당할지에 대해 작업이 이미 진행되었습니다. 당신은 warehouse_id를 정의하고 쿼리 범위를 선택하며 보고 싶은 "파레토 지점"을 정의할 수 있습니다. 이것은 당신에게 당신의 데이터 웨어하우스 전체 계산 시간의 상위 X%를 구성하는 쿼리들을 볼 수 있도록 합니다. 예를 들어, 위의 시각화는 SQL 쿼리가 이 시간대에 웨어하우스 계산 시간의 약 20%를 차지한 것을 보여줍니다. 파레토 분석을 통해 당신은 당신의 웨어하우스 활용도의 가장 큰 부분을 차지하는 쿼리들을 살펴볼 수 있으며, 작고 차이점이 없는 "long tail" 쿼리들을 걸러낼 수 있습니다.

요약 뿐만 아니라 대시보드에는 데이터 제품에 대해 세부적인 쿼리 수준 리포트가 포함되어 있어, 당신의 데이터 제품에 대한 세부적인 가격 책정을 할 수 있습니다:

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

<img src="/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_14.png" />

이 보고서에서는 시간이 어디에 사용되는지 쉽게 확인할 수 있으며, 심지어 엔드 유저와 태그별로도 분석할 수 있습니다! 사용자 실행 분석은 이 쿼리를 실행하는 각 사용자의 실행 횟수와 오류율을 제공합니다. 이 정보는 워크로드에 대한 매우 투명한 비용 할당을 갖도록 필요한 모든 세부 정보를 제공합니다.

주의해야 할 중요한 몇 가지 정의가 있습니다:

데이터 웨어하우스 실행 시간 = 시간 창 동안의 모든 쿼리 실행의 총 실행 시간

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

쿼리 할당 비용 = 쿼리 실행 시간 / 데이터베이스 실행 시간

# DBSQL Warehouse Advisor — DBT Version

DBSQL Advisor 템플릿 외에도, DBT 데이터베이스에서 DBSQL로 푸시된 쿼리의 DBT 메타데이터를 이해하는 DBT 버전도 있습니다. 이를 통해 사용자들은 DBT 모델 및 개별 노드 ID에 대한 추가 정보를 즉시 얻을 수 있습니다.

![이미지](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_15.png)

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

이제 쿼리 분석의 섹션 3과 4에서는 dbt 대상, 프로필 이름 또는 노드 ID와 같은 친숙한 메타데이터로 성능 및 쿼리 할당 지표를 분석할 수 있습니다. 이를 통해 DBT 모델에 대한 리소스가 정확히 어디에 사용되고 있는지 확인할 수 있습니다.

![이미지1](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_16.png)

![이미지2](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_17.png)

![이미지3](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_18.png)

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

이 대시보드를 사용하면 DBSQL의 Lakeview와 함께 DBT 파이프라인을 이해하고 모니터링하는 데 상당히 도움을 받을 수 있습니다! 내부 분석부터 완전한 데이터 제품 제작까지, 데이터 플랫폼에 대한 완전한 제어 및 시각성을 갖게 됩니다.

# 다음 단계

이 시리즈에서 제공하는 4가지 대시보드 템플릿 중 첫 번째로, 데이터브릭스 사용자들이 시스템 테이블과 Lakeview 대시보드를 통해 일반적이고 강력한 사용 사례에 대해 최대한 활용할 수 있도록 지원하는 것이 목표입니다. 우리가 제공하는 탁월한 기능 뿐만 아니라, 이러한 템플릿은 훌륭한 참조 자료가 되어 앞으로 구축하려는 대시보드에 흥미로운 디자인 패턴으로 사용될 수 있습니다!

대시보드를 지원하는 SQL 논리 및 시각/매개변수 선택의 원천에 대해 깊이 파고들어 설명을 듣고 싶다면, 의견을 남겨주시면 감사하겠습니다!

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

## Lakeview Templates:

- DBSQL Warehouse Advisor
- DBT Version — DBSQL Warehouse Advisor

## 사용 방법:

- 위 링크에서 JSON 파일을 다운로드합니다.
- 데이터브릭 워크스페이스의 “대시보드” 탭으로 이동합니다.
- 오른쪽 상단 모서리에 있는 파란색 “대시보드 만들기” 버튼 옆의 화살표를 클릭합니다.
- “파일에서 대시보드 가져오기”를 선택하고 다운로드한 JSON 템플릿을 업로드합니다.
- 대시보드를 로드하고 사용 중인 데이터 웨어하우스 ID를 입력합니다!

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

![Image](/assets/img/2024-05-18-LakeviewDashboardsforObservabilitySeriesArticle1DBSQLWarehouseAdvisor_19.png)
