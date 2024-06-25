---
title: "Azure Data Factory에서 Databricks 작업 클러스터에 동적 태그 적용하기"
description: ""
coverImage: "/assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_0.png"
date: 2024-05-20 17:01
ogImage:
  url: /assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_0.png
tag: Tech
originalTitle: "Applying Dynamic Tags To Databricks Job Clusters in Azure Data Factory"
link: "https://medium.com/@kyle.hale/applying-dynamic-tags-to-databricks-job-clusters-in-azure-data-factory-e04bfd07b178"
---

아래는 Markdown 형식으로 변경한 텍스트입니다.

![이미지](/assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_0.png)

최근 몇 명의 고객이 Databricks 노트북 액티비티를 Azure Data Factory에서 실행할 때, 노트북을 실행할 작업 클러스터에 동적으로 태그를 설정하는 방법에 대해 물어봤어요.

다행히도 Azure Data Factory의 매개변수와 동적 콘텐츠 기능 덕분에 해결책을 상당히 쉽게 찾을 수 있어요.

# Prerequisites

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

아래는 Azure Databricks 워크스페이스에 액세스 권한, Azure Data Factory 인스턴스, 그리고 워크스페이스에서 작업을 만들고 실행할 수 있는 능력이 있다고 가정합니다.

# 링크드 서비스를 매개변수화하기

- Azure Data Factory에서 새로운 Azure Databricks 링크드 서비스를 만듭니다.
- 편집기의 매개변수 섹션에 동적으로 지정하고자 하는 각 태그 값에 대해 매개변수를 추가하세요. 이 예시에서는 CustomTag라는 매개변수를 추가하고 기본값을 설정했습니다.

![이미지](/assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_1.png)

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

3. 클러스터 사용자 정의 태그 섹션에서 태그 이름을 추가하고 값 필드를 선택하세요. 텍스트 상자 아래에 동적 콘텐츠를 추가할 링크가 나타납니다. 이를 클릭해주세요:

![Cluster custom tags](/assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_2.png)

4. 'Pipeline Expression Builder' 패널이 열립니다. 하단에는 이전에 생성한 'ClusterTag' 매개변수가 파라미터 목록에 있을 겁니다. 이 매개변수를 클릭하여 파이프라인 표현식에 삽입하세요. 확인을 클릭해주세요.

![ClusterTag parameter](/assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_3.png)

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

5. 클러스터의 나머지를 보통대로 구성하세요.

최종 연결된 서비스 구성은 다음과 같아야 합니다.

![Linked Service Configuration](/assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_4.png)

# 파라미터에 동적 값을 바인딩하세요

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

이제 우리가 연결된 서비스에서 태그 값에 대한 매개변수를 갖고 있으므로 해당 연결된 서비스를 사용하는 각 활동에 대해 값을 바인딩할 수 있습니다.

- 새로운 Databricks 노트북 활동을 만듭니다.
- 활동 구성 패널에서 Azure Databricks 탭을 참고하면 Linked Service 속성에 우리가 만든 매개변수가 포함되어 있음을 알 수 있습니다:

![Linked Service Properties](/assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_5.png)

3. 여기에는 세 가지 옵션이 있습니다:

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

- 이 속성값을 비워두면 클러스터는 태그의 값에 이전에 설정한 기본값을 사용합니다.
- 이곳에 정적 값을 지정할 수 있으며, 이 값은 태그의 값으로 매개변수를 통해 전달됩니다.
- 다시 동적 콘텐츠 옵션을 사용하여 이 매개변수에 대한 표현식을 제공할 수 있습니다. 전체 ADF 파이프라인으로 전달되는 매개변수, 파이프라인 자체의 이름, 파이프라인이 트리거된 날짜 또는 다른 활동의 출력값일 수 있습니다.

이 튜토리얼에서는 여기에 정적 값만 할당하겠습니다:

![image](/assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_6.png)

참고로 만일 우리 파이프라인에 여러 활동이 있다면, 각 활동에 대해 이 속성에 고유한 값을 할당할 수 있습니다.

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

수동으로 이 파이프라인을 실행한 후에는 Azure Databricks의 작업 클러스터로 사용자 정의 동적 태그 값이 전파된 것을 볼 수 있습니다:

![동적 태그 적용 예시](/assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_7.png)

이 태그는 클러스터에서 사용되는 Azure VM에도 전파되어, Azure 포털의 Azure 비용 관리 도구에서 청구 및 감사 목적으로 사용할 수 있습니다.

이제 Databricks 시스템 테이블에서 이 태그를 조회하여 클러스터와 관련 청구 사용 내역을 찾을 수도 있습니다.

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

아래는 Markdown 형식의 테이블입니다.

| 파일 이름                                                                     | 경로                                                                                      |
| ----------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| 2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_8.png | /assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_8.png |
| 2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_9.png | /assets/img/2024-05-20-ApplyingDynamicTagsToDatabricksJobClustersinAzureDataFactory_9.png |

이 내용이 Databricks에서의 컴퓨팅 및 파이프라인의 가시성 관리에 도움이 되기를 바랍니다. 피드백은 언제나 환영합니다!
