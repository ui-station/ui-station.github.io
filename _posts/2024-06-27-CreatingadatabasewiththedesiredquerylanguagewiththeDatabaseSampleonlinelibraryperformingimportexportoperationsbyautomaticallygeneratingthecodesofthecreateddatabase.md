---
title: "DatabaseSample 온라인 라이브러리를 사용한 데이터베이스 생성 및 원하는 쿼리 언어와 함께 코드 자동 생성으로 importexport 작업 수행 방법"
description: ""
coverImage: "/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_0.png"
date: 2024-06-27 19:20
ogImage: 
  url: /assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_0.png
tag: Tech
originalTitle: "Creating a database with the desired query language with the DatabaseSample online library , performing import , export operations by automatically generating the codes of the created database"
link: "https://medium.com/@tolgaipek879/creating-a-database-with-the-desired-query-language-with-the-databasesample-online-library-c5b5c8f7db1f"
---


<img src="/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_0.png" />

# DATABASESAMPLE ONLINE LIBRARY란?

DatabaseSample Online Library는 다양한 오픈 소스 데이터베이스 샘플을 제공하는 온라인 라이브러리입니다. 우리의 목표는 개발자, 연구자 및 데이터베이스 관리 및 분석에 관심 있는 사람들을 위한 리소스를 제공하는 것입니다. 동시에, 샌드박스를 통해 우리의 데이터베이스를 설계함으로써 원하는만큼의 테이블을 생성할 수 있으며, 이러한 테이블을 자동으로 생성된 원하는 쿼리 언어로 가져오고 내보낼 수 있습니다. 또한 인공지능 지원 자동 데이터베이스 생성 옵션이 있습니다. 우리가 원하는 데이터베이스에 대해 인공지능에 세부 설명을 제공함으로써, 인공지능은 해당 테이블과 함께 원하는 데이터베이스를 생성하여 제시할 수 있습니다. 페이지 상단의 검색 창을 사용하여 특정 데이터베이스 샘플을 이름, 유형 또는 키워드로 검색할 수 있습니다. 또한 관련 샘플을 발견하기 위해 데이터베이스 페이지를 탐색할 수 있습니다.

<img src="/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_1.png" />

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

# 온라인으로 SANDBOX를 통해 새로운 데이터베이스를 만드는 단계:

![이미지](/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_2.png)

DatabaseSample를 통해 새로운 데이터베이스를 만들려면 먼저 오른쪽 상단의 시작하기 탭을 클릭하십시오. 그럼 SANDBOX 페이지로 리디렉션됩니다. SANDBOX는 무엇인지 설명한다면, 사용자가 가상 환경에서 자체 데이터베이스를 안전하게 만들고 보관할 수 있도록 합니다. 가상화된 데이터베이스는 일반적으로 공개되지 않습니다. 사용자별로 지정됩니다. 사용자에게 격리된 환경을 제공합니다. 일반적인 정의에 따르면, SANDBOX 페이지는 샘플 데이터범이 나열되고, 데이터베이스를 가져오고, 새 데이터베이스를 만드는 섹션입니다. 새 데이터베이스는 '새 데이터베이스' 옵션을 클릭하여 만들 수 있습니다. 이를 통해 쿼리를 실행하고 데이터베이스 작업을 테스트하며, 로컬 데이터베이스 인스턴스를 설정할 필요 없이 데이터를 살펴볼 수 있습니다.

원하는 쿼리 언어로 데이터베이스 코드를 작성하고, 데이터베이스가 자동으로 생성됩니다.

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

<img src="/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_3.png" />

새로운 데이터베이스를 만들려면 데이터베이스 이름을 왼쪽 상단에 입력하세요.

<img src="/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_4.png" />

# 데이터베이스 테이블 추가하기:

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

아래는 관계형 데이터베이스 테이블이 나열된 섹션입니다. 추가된 테이블은 쿼리 언어(DBML, PostgreSQL, MYSQL 및 MSSQL)를 사용하여 자동 코드 생성기로 내보내기 탭에서 사용할 수 있습니다. 그리고 원하는 데이터베이스 쿼리 언어로 코드를 작성하여 테이블을 자동으로 생성할 수 있습니다. 삭제할 테이블도 Clear 탭을 사용해 삭제할 수 있습니다. 테이블을 저장하는 옵션이 있습니다. 새 테이블을 만들려면 "Add table" 탭을 클릭하세요. 그런 다음 하나 이상의 필드를 추가하려면 "Add field after" 옵션을 클릭할 수 있습니다. 추가할 필드는 중요도 순서에 따라 "Move up" 또는 "Move Down"으로 정렬할 수 있습니다. 관련 필드는 "Remove field" 옵션으로 삭제할 수 있습니다. 테이블을 삭제하려면 "Delete table"을 사용하세요. "Commit"을 클릭한 후 테이블을 생성합니다.

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


![Image 8](/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_8.png)

![Image 9](/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_9.png)

![Image 10](/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_10.png)

To import any table into the database, we can first create our own tables automatically in our virtual environment by writing the relevant codes in any query language. We can import on 4 query languages as shown in the picture below.


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


![Opening Image 1](/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_11.png)

![Opening Image 2](/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_12.png)

![Opening Image 3](/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_13.png)

# OPEN SOURCE DATABASE EXAMPLES:


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

<img src="/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_14.png" />

<img src="/assets/img/2024-06-27-CreatingadatabasewiththedesiredquerylanguagewiththeDatabaseSampleonlinelibraryperformingimportexportoperationsbyautomaticallygeneratingthecodesofthecreateddatabase_15.png" />

여기에는 데이터베이스 샘플 온라인 라이브러리에서 유용할 만한 여러 유형의 오픈 소스 데이터베이스 예시가 있습니다. 데이터베이스를 원하는 대로 변경하거나 프로젝트에 정확히 적용할 수 있습니다. 더 많은 오픈 소스 데이터베이스 예시를 보려면 https://databasesample.com/databases를 방문해주세요.

이 글에서는 DatabaseSample 온라인 라이브러리에 관한 많은 정보를 전달하려고 노력했습니다. 읽어 주셔서 감사합니다. 만약 이 기사가 유용하다고 생각된다면 아래의 박수 버튼을 눌러보시는 건 어떨까요? 😉

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

## 자료: