---
title: "마이크로소프트 패브릭과 데이타브릭스 유니티 카탈로그 - 통합 시나리오 해석하기"
description: ""
coverImage: "/assets/img/2024-06-19-MicrosoftFabricandDatabricksUnityCatalogunravelingtheintegrationscenarios_0.png"
date: 2024-06-19 12:25
ogImage:
  url: /assets/img/2024-06-19-MicrosoftFabricandDatabricksUnityCatalogunravelingtheintegrationscenarios_0.png
tag: Tech
originalTitle: "Microsoft Fabric and Databricks Unity Catalog — unraveling the integration scenarios"
link: "https://medium.com/@murggu/microsoft-fabric-and-databricks-unity-catalog-unraveling-the-integration-scenarios-a31a01297e76"
---

올해 첫 번째 Microsoft Fabric 커뮤니티 컨퍼런스가 개최되었습니다. 첫 번째 날 키노트에서 Fabric와 Databricks Unity Catalog (UC) 통합을 쇼케이스하는 두 가지 미리보기가 있었어요.

이전 블로그 게시물에서는 Databricks에서 OneLake로 쓰는 옵션 및 Fabric Spark에서 ADLS Gen2로 쓰는 옵션(Unity Catalog가 활성화된 클러스터가 아닌 경우)에 대해 알아보았습니다. 이 블로그 게시물은 Fabric + Unity Catalog 통합(Unity Catalog가 활성화된 클러스터와 관련된 다양한 시나리오를 밝히는 것을 목표로 합니다. Lakehouse 시나리오에 대한 자세한 내용은 Piethein의 게시물에서 확인해주세요.

- Unity Catalog 테이블을 OneLake 카탈로그로 동기화할 수 있을까요? 어떻게요?
- Unity Catalog가 활성화된 클러스터에서 OneLake로 쓸 수 있을까요?
- Unity Catalog를 OneLake와 통합할 수 있을까요? SQL 엔드포인트 / Fabric 데이터 웨어하우스에 대해 페더레이티드 쿼리를 실행할 수 있을까요?

참고: 본 글은 제 개인적인 경험과 견해를 반영한 것이며, Microsoft나 Databricks의 공식 입장을 대변하는 것은 아닙니다. 또한, 이 블로그 게시물은 잠재적인 시나리오를 개요로 제시하였지만, Fabric 로드맵이나 의도를 반영하는 것은 아닙니다. 미래에 모든 언급된 옵션이 운영되지는 않을 수 있습니다.

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

# Databricks Unity Catalog and Microsoft Fabric

통합 시나리오는 기본적으로 진입점에 따라 볼 수 있습니다. Unity Catalog 및 Fabric:

- Fabric에서 Unity Catalog에 액세스하기 (Fabric → Unity Catalog): 이 기능을 통해 사용자는 Fabric 내에서 Unity Catalog 카탈로그, 스키마 및 테이블에 원활하게 액세스할 수 있습니다.
- Unity Catalog에서 Fabric 활용하기 (DBX/Unity Catalog → Fabric): 이 기능을 사용하면 사용자는 Unity Catalog 내부에서 OneLake에 직접 액세스하고 SQL 엔드포인트 또는 Fabric 데이터 웨어하우스를 통해 연합 쿼리를 실행할 수 있습니다.

이러한 시나리오를 자세히 살펴보겠습니다.

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

# Fabric → Unity Catalog

## Fabric에서 Unity Catalog 사용하기

Fabric에서 Unity Catalog 테이블에 액세스할 수 있는 몇 가지 옵션이 있습니다. Fabric Spark에서 ADLS Gen2로 직접 읽기 및 쓰기도 가능합니다.

<img src="/assets/img/2024-06-19-MicrosoftFabricandDatabricksUnityCatalogunravelingtheintegrationscenarios_0.png" />

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

현재 옵션
UC 테이블에 바로 가기를 생성할 수 있는 옵션은 현재 두 가지 있습니다: 수동(지루한) 또는 반자동, 후자는 노트북을 통해 가능합니다. 반자동 방법을 사용하면 사용자들은 UC Delta 외부 테이블을 OneLake에 통합하여 바로 가기를 생성할 수 있습니다. 동기화를 위해 카탈로그와 스키마 이름을 지정하면, 해당 스키마 내 테이블에 대한 바로 가기가 Fabric 레이크하우스 내에 생성됩니다. 실행 유틸리티 노트북에 대한 추가 지침을 참조하세요.

```js
# configuration
dbx_workspace = "<databricks_workspace_url>"
dbx_token = "<pat_token>"
dbx_uc_catalog = "catalog1"
dbx_uc_schemas = '["schema1", "schema2"]'

fab_workspace_id = "<workspace_id>"
fab_lakehouse_id = "<lakehouse_id>"
fab_shortcut_connection_id = "<connection_id>"
fab_consider_dbx_uc_table_changes = True

# sync UC tables to lakehouse
sc.addPyFile('https://raw.githubusercontent.com/microsoft/fabric-samples/main/docs-samples/onelake/unity-catalog/util.py')
from util import *
databricks_config = {
    'dbx_workspace': dbx_workspace,
    'dbx_token': dbx_token,
    'dbx_uc_catalog': dbx_uc_catalog,
    'dbx_uc_schemas': json.loads(dbx_uc_schemas)
}
fabric_config = {
    'workspace_id': fab_workspace_id,
    'lakehouse_id': fab_lakehouse_id,
    'shortcut_connection_id': fab_shortcut_connection_id,
    "consider_dbx_uc_table_changes": fab_consider_dbx_uc_table_changes
}
sync_dbx_uc_tables_to_onelake(databricks_config, fabric_config)
```

가능한 미래 옵션

- Fabric에서 Unity Catalog 네이티브 항목: 하이브 메타스토어 메타데이터 이동과 유사하게, Unity Catalog 메타데이터를 Fabric 레이크하우스로 동기화하여 Unity Catalog 테이블에 액세스할 수 있게 합니다. 이 시나리오의 한 예시는 FabCon에서 시연되었는데, 사용자들이 Fabric UI를 통해 Unity Catalog 테이블에 직접 액세스하고 쿼리할 수 있는 것을 보여주었습니다.
- Fabric에 Unity Catalog 바로 가기: Dataverse 바로 가기와 유사하게, OneLake 바로 가기 UX는 잠재적으로 Unity Catalog 테이블에 대한 바로 가기 생성을 지원할 수 있을 것입니다.

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

참고: 델타 공유, Databricks에서 JDBC/ODBC, Fabric 데이터 파이프라인 Databricks 활동 등의 옵션은 여기에 언급되지 않았습니다.

# Databricks/Unity 카탈로그 → Fabric

## Databricks/Unity 카탈로그에서 Fabric 및 OneLake 사용하기

Unity 카탈로그는 클라우드 객체 저장소 연결(예: ADLS Gen2)을 활용하고 외부 데이터 시스템에 연결하여 연합 쿼리를 실행하는 다양한 방법을 제공합니다.

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

아래는 현재 옵션입니다. 사용자들은 UC 활성화된 클러스터에서 OneLake를 다음과 같이 사용할 수 있습니다: (i) Service Principal (SPN) 기반 인증을 사용하여 OneLake에 r/w, 그리고 (ii) SPN 인증을 사용하여 mount 지점으로 OneLake에 r/w.

```js
# spn을 사용한 r/w
workspace_name = "<워크스페이스_이름>"
lakehouse_name = "<레이크하우스_이름>"
tenant_id = "<테넌트_ID>"
service_principal_id = "<서비스_프린시펄_ID>"
service_principal_password = "<서비스_프린시펄_비밀번호>"

spark.conf.set("fs.azure.account.auth.type", "OAuth")
spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
spark.conf.set("fs.azure.account.oauth2.client.id", service_principal_id)
spark.conf.set("fs.azure.account.oauth2.client.secret", service_principal_password)
spark.conf.set("fs.azure.account.oauth2.client.endpoint", f"https://login.microsoftonline.com/{tenant_id}/oauth2/token")

# 읽기
df = spark.read.format("parquet").load(f"abfss://{workspace_name}@onelake.dfs.fabric.microsoft.com/{lakehouse_name}.Lakehouse/Files/data")
df.show(10)

# 쓰기
df.write.format("delta").mode("overwrite").save(f"abfss://{workspace_name}@onelake.dfs.fabric.microsoft.com/{lakehouse_name}.Lakehouse/Tables/dbx_delta_spn")
```

```js
# spn으로 Mount
workspace_id = "<워크스페이스_ID>"
lakehouse_id = "<레이크하우스_ID>"
tenant_id = "<테넌트_ID>"
service_principal_id = "<서비스_프린시펄_ID>"
service_principal_password = "<서비스_프린시펄_비밀번호>"

configs = {
    "fs.azure.account.auth.type": "OAuth",
    "fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
    "fs.azure.account.oauth2.client.id": service_principal_id,
    "fs.azure.account.oauth2.client.secret": service_principal_password,
    "fs.azure.account.oauth2.client.endpoint": f"https://login.microsoftonline.com/{tenant_id}/oauth2/token"
}

mount_point = "/mnt/onelake-fabric"
dbutils.fs.mount(
    source = f"abfss://{workspace_id}@onelake.dfs.fabric.microsoft.com",
    mount_point = mount_point,
    extra_configs = configs
)

# 읽기
df = spark.read.format("parquet").load(f"/mnt/onelake-fabric/{lakehouse_id}/Files/data")
df.show(10)

# 쓰기
df.write.format("delta").mode("overwrite").save(f"/mnt/onelake-fabric/{lakehouse_id}/Tables/dbx_delta_mount_spn")
```

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

알림: OneLake abfss 경로 또는 마운트 경로를 사용하여 외부 테이블을 만들 경우 UC에서 예외가 발생할 수 있습니다. 현재, OneLake를 기본 저장소로 사용하여 UC에 외부 테이블을 등록할 수 없습니다. 이는 잠재적인 미래 시나리오로 이어질 수 있습니다.

- INVALID_PARAMETER_VALUE: 클라우드 파일 시스템 스키마 누락
- 목록을 위한 SAS 토큰을 획득하지 못했습니다. 유효하지 않은 Azure 경로

잠재적인 미래 옵션
ADLS Gen2와 Azure Synapse와 유사하게, 미래에는 다른 옵션이 존재할 수 있습니다:

- 기본 관리 저장소로서 OneLake: Databricks는 Unity Catalog의 자동 활성화를 시작했습니다. 즉, 자동으로 ADLS Gen2와 같이 Databricks 관리 저장소로 Unity Catalog 메타스토어를 자동으로 프로비저닝합니다. 그러나 사용자는 Unity Catalog 메타스토어를 OneLake를 가리키도록하면서 사용자 관리 수준 저장소를 생성할 수도 있습니다. 참고: 아직 이 기능은 불가능합니다.
- 외부 위치로서 OneLake: 외부 위치는 카탈로그 및 스키마의 관리 저장소 위치를 정의하고, 외부 테이블 및 외부 볼륨의 위치를 정의하는 데 사용됩니다. 예를 들어, Spark에서 외부 테이블을 사용하는 경우 OneLake를 외부 위치로 활용할 수 있습니다. 참고: 아직 이 기능은 불가능합니다.
- 볼륨용 OneLake: 볼륨은 클라우드 객체 저장소 위치의 논리적 저장 볼륨을 나타내며 비 탭식 데이터셋에 대한 관리 방식을 추가합니다. ADLS Gen2와 같이 OneLake를 사용하여 외부 및 관리 볼륨을 생성할 수 있습니다. 참고: 아직 이 기능은 불가능합니다.
- 연합 Lakehouse: SQL 엔드포인트나 Fabric Data Warehouse의 데이터에 대한 읽기 전용 액세스는 UC 외부 카탈로그를 사용하여 미래 옵션으로 가능할 수 있습니다. 현재 Azure Synapse 및 SQL 액세스 인증은 사용자 이름/암호를 기반으로 하며 SPN은 아직 지원되지 않으므로 이 옵션은 아직 불가능합니다. 외부 카탈로그는 현재 객체 저장소를 지원하지 않으므로, 아직 OneLake/Lakehouse로의 외부 카탈로그 연결이 가능한지 여전히 불분명합니다.

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

표 태그를 Markdown 형식으로 변경해주세요.

## 잠깐, 액세스 정책은 어떻게 되나요?

다른 옵션으로는 Databricks의 ODBC를 SQL 엔드포인트로 사용하거나 Partner Connect( Power BI + Databricks) 및 스트리밍 옵션이 여기에 언급되지 않았네요.

계속해서 Unity 카탈로그 액세스 정책이 OneLake RBAC 및 OneSecurity와 어떻게 조화를 이루고 있는지, Unity 카탈로그에서 Fabric로 보안 및 액세스 정책을 이동하거나 그 반대로 이동할 수 있는지에 대해 살펴볼 수 있을 것입니다.

참고 문헌:

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

- Non-UC 시나리오: Databricks와 Fabric — OneLake 및 ADLS Gen2에 쓰기 | Aitor Murguzur 작성 | Medium
- Lakehouse 시나리오: Azure Databricks와 Microsoft Fabric 통합 | Piethein Strengholt 작성 | 2024년 6월 | Medium
- Databricks Unity Catalog를 OneLake와 통합 — Microsoft Fabric | Microsoft Learn
