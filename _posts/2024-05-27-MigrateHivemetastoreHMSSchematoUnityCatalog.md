---
title: "하이브 메타스토어 HMS 스키마를 유니티 카탈로그로 이관하기"
description: ""
coverImage: "/assets/img/2024-05-27-MigrateHivemetastoreHMSSchematoUnityCatalog_0.png"
date: 2024-05-27 17:16
ogImage: 
  url: /assets/img/2024-05-27-MigrateHivemetastoreHMSSchematoUnityCatalog_0.png
tag: Tech
originalTitle: "Migrate Hive metastore (HMS) Schema to Unity Catalog"
link: "https://medium.com/@data_engineering_0216/migrate-hive-metastore-hms-schema-to-unity-catalog-50ee65ab784f"
---


<img src="/assets/img/2024-05-27-MigrateHivemetastoreHMSSchematoUnityCatalog_0.png" />

HMS에서 Unity Catalog로의 이주 이야기에 오신 것을 환영합니다.

본 글에서는 HMS에서 Unity Catalog로의 이주 과정을 공유하고자 합니다. HMS를 Unity Catalog로 마이그레이션하기 위한 여러 도구들이 있음을 알고 있습니다. 특히 현재 시장에서 인기를 끌고 있는 UCX가 있습니다. 아직 UCX를 탐험해보지는 않았지만, 앞으로 UCX를 살펴볼 예정입니다.
UCX를 사용해보고 싶다면, https://github.com/databrickslabs/ucx 에서 확인하고 그 경험을 공유해주세요. 

본 글의 범위

<div class="content-ad"></div>

- 외부 테이블을 Unity 카탈로그로 이주합니다.

이 글에서 다루겠습니다.
- 관리형 테이블을 Unity 카탈로그로 이주합니다.
https://medium.com/@data_engineering_0216/migrate-managed-table-to-unity-catalog-ab4dbba9d6aa
- 뷰를 Unity 카탈로그로 이주합니다.
https://medium.com/@data_engineering_0216/migrate-views-from-hive-metastore-to-unity-catalog-7aac5ec1da50
- 메타데이터 기반 권한 관리
https://medium.com/@data_engineering_0216/unity-catalog-permissions-f1e6221cbc68
- https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/migrate

외부 테이블을 Unity 카탈로그로 이주합니다.

준비물

- 메타스토어 또는 카탈로그 관리자 권한
- Unity 카탈로그가 활성화된 Databricks 워크스페이스
- Unity 카탈로그가 활성화된 클러스터
- Databricks 접근 커넥터
- 마운트 지점과 동등한 스토리지 자격 증명 및 외부 위치(읽기 및 쓰기 권한 필요)
- 워크스페이스 또는 클러스터 수준에서 기본 카탈로그 설정: 선택 사항
클러스터 구성: spark.databricks.sql.initial.catalog.name gold_dv

<div class="content-ad"></div>

솔루션을 깊이 살펴보겠습니다. 사용자 정의 Python 함수인 migrate_tables_to_unity_catalog을 살펴봅시다. 이 코드는 외부 테이블을 하나의 (hive_metastore) 카탈로그에서 다른 카탈로그(Unity Catalog)로 동기화하는 함수를 정의합니다.

이 함수는 다음과 같은 매개변수를 사용합니다:

- src_ct_name: 원본 카탈로그의 이름.
- src_databases: 원본 카탈로그에서 가져온 데이터베이스 사전의 목록.
- dst_ct_name: 대상 카탈로그의 이름.
- exclude_databases: 마이그레이션에서 제외할 데이터베이스 이름의 목록.
- full_reset: 전체 리셋을 수행해야 하는지 여부를 나타내는 부울 플래그.

이 함수는 먼저 src_databases 매개변수에서 데이터베이스 이름 목록을 작성합니다. 제외할 데이터베이스가 있는 경우 해당 데이터베이스를 목록에서 필터링합니다.

<div class="content-ad"></div>

그럼, 각 데이터베이스를 처리하는 process_database 내부 함수를 정의합니다. full_reset이 True 인 경우, 대상 카탈로그의 데이터베이스를 삭제하고 새 데이터베이스를 생성한 후 소스 카탈로그에서 대상 카탈로그로 스키마를 동기화합니다. full_reset이 False 인 경우, 새 데이터베이스를 생성하고 스키마를 동기화합니다. full_reset이 제공되지 않은 경우, 재설정 모드를 요청하는 메시지를 출력합니다.

concurrent.futures.ThreadPoolExecutor를 사용하여 함수는 각 데이터베이스를 병렬로 처리하도록 제출합니다. 모든 futures가 완료되기를 기다리고 처리 중 발생한 예외를 처리합니다.

이 함수를 사용하려면 필요한 매개변수를 제공하고 함수를 호출해야 합니다.

```python
import concurrent.futures


def migrate_tables_to_unity_catalog(src_ct_name, src_databases, dst_ct_name, exclude_databases, full_reset):
    """
    한 카탈로그에서 다른 카탈로그로 관리되는 모든 테이블을 복사합니다.

    매개변수:
        src_ct_name (str): 원본 카탈로그의 이름.
        src_databases (list): 원본 카탈로그에서의 데이터베이스 딕셔너리 목록.
        dst_ct_name (str): 대상 카탈로그의 이름.
        exclude_databases (list): 마이그레이션에서 제외할 데이터베이스 이름 목록.
        
    반환:
        None
        
    예외:
        None

    예시:
        src_ct_name       = "hive_metastore"
        src_databases     = spark.sql(f"SHOW DATABASES IN {src_ct_name}").collect()
        dst_ct_name       = "uc_dv"
        exclude_databases = ["poc", "temp_tbd", "default"]
        migrate_managed_tables_to_unity_catalog(src_ct_name, src_databases, dst_ct_name, exclude_databases)
        
    """
    
    list_of_db = []
    for db in src_databases:
        dbName = db['databaseName']
        list_of_db.append(dbName)
    if exclude_databases:
        databases = [x for x in list_of_db if x not in exclude_databases]
    else:
        databases = list_of_db

    print(databases)

    def process_database(db, full_reset):
       
        if full_reset == True:
            drop_db = f"DROP DATABASE IF EXISTS {dst_ct_name}.{db} CASCADE"
            display(spark.sql(drop_db))
            create_db = f"CREATE DATABASE IF NOT EXISTS {dst_ct_name}.{db}"
            display(spark.sql(create_db))
            query = f"SYNC SCHEMA {dst_ct_name}.{db} from {src_ct_name}.{db}" # SYNC SCHEMA uc_dv.gold from hive_metastore.clean

            print(query)
            display(spark.sql(query))
        elif full_reset == False:
            create_db = f"CREATE DATABASE IF NOT EXISTS {dst_ct_name}.{db}"
            display(spark.sql(create_db))
            query = f"SYNC SCHEMA {dst_ct_name}.{db} from {src_ct_name}.{db}" # SYNC SCHEMA uc_dv.gold from hive_metastore.clean

            print(query)
            display(spark.sql(query))
        else:
            print("재설정 모드를 제공해주세요")
        
    with concurrent.futures.ThreadPoolExecutor() as executor:
        futures = [executor.submit(process_database, db, full_reset) for db in databases]
        # 모든 futures가 완료되기를 기다림
        for future in concurrent.futures.as_completed(futures):
            try:
                # 각 future의 결과를 가져옴
                result = future.result()
            except Exception as e:
                # 발생한 예외 처리
                pass
```

<div class="content-ad"></div>

아래 코드는 다음 작업을 수행합니다:

```js
src_ct_name       = "hive_metastore"
src_databases     = spark.sql(f"SHOW DATABASES IN {src_ct_name}").collect()
dst_ct_name       = "uc_dv" #spark.sql("SELECT current_catalog()").collect()[0]['current_catalog()']  
exclude_databases = ["poc", "temp_tbd", "default"]
full_reset         = False

migrate_tables_to_unity_catalog(src_ct_name,src_databases,dst_ct_name,exclude_databases,full_reset)
```

- "src_ct_name" 변수를 정의하여 값 "hive_metastore"를 할당합니다.
- Spark를 사용하여 src_ct_name 카탈로그 내의 데이터베이스 목록을 검색하는 SQL 쿼리를 실행하고 결과를 src_databases 변수에 할당합니다.
- Spark를 사용하여 현재 카탈로그를 검색하는 SQL 쿼리를 실행하고 결과를 dst_ct_name 변수에 할당합니다.
- 마이그레이션 프로세스에서 제외될 데이터베이스 이름을 포함하는 "exclude_databases" 목록을 정의합니다.
- 값이 False인 부울 변수 "full_reset"을 정의합니다.
- migrate_tables_to_unity_catalog 함수를 src_ct_name, src_databases, dst_ct_name, exclude_databases 및 full_reset 매개변수로 호출합니다. 이 함수는 소스 카탈로그에서 대상 카탈로그로 테이블을 마이그레이션하는 역할을 합니다.

위의 Python 함수는 hive_metastore의 외부 테이블을 unity Catalog로 몇 분 안에 마이그레이션하는 데 유용합니다.

<div class="content-ad"></div>

위 코드를 Databricks 노트북에 복사하여 Adf나 Databricks 일정으로 매일 실행하면 Unity Catalog로 완전히 마이그레이션할 때까지 도움을 줄 수 있습니다. 예를 들어, hive_metastore에 새 테이블을 추가하면 일정이 자동으로 새로운 테이블을 Unity Catalog에 동기화합니다.

Unity Catalog로의 마이그레이션 여정에 도움이 되기를 바라며, 궁금한 점이 있으시면 언제든지 물어보세요.