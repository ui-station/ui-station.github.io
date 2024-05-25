---
title: "데이터베이스DBT 간단 정리"
description: ""
coverImage: "/assets/img/2024-05-23-DBTinaNutshell_0.png"
date: 2024-05-23 14:08
ogImage: 
  url: /assets/img/2024-05-23-DBTinaNutshell_0.png
tag: Tech
originalTitle: "DBT in a Nutshell🥜"
link: "https://medium.com/dev-genius/dbt-data-build-tool-in-a-nutshell-29028bc4e164"
---


이 게시물은 일반적으로 DBT(Data Build Tool)에 대한 내 이해와 누가 사용해야 하고 사용하지 말아야 하는지에 중점을 둘 것입니다. 앞으로의 게시물에서 더 자세히 다룰 예정입니다.

DBT(Data Build Tool)와 Snowflake❄️를 통해 비즈니스 문제 해결하기👨‍💻

![이미지](/assets/img/2024-05-23-DBTinaNutshell_0.png)

❖ Snowflake❄️ + DBT👨‍💻 프로젝트 전체 파트 1 : [링크](https://lnkd.in/gASCckRR)
❖ Snowflake❄️ + DBT👨‍💻 프로젝트 전체 파트 2 : [링크](https://lnkd.in/gMqfKZRW)
❖ Snowflake❄️ + DBT👨‍💻 프로젝트 전체 파트 3 : [링크](https://lnkd.in/g8BuWy66)

<div class="content-ad"></div>

◉ 참여하기:
👉 GitHub: [링크](https://lnkd.in/ggt3ZzUx)
🚀 기여하고, 복제하고, 공유하세요!

◉ YouTube 채널 찾기🎥: [링크](https://lnkd.in/esW5M3vb)

![이미지](/assets/img/2024-05-23-DBTinaNutshell_1.png)

- DBT란 무엇인가요?
- DBT는 무료인가요?
- DBT가 해결하고 있는 문제는 무엇인가요?
- ETL의 어느 부분에서 DBT를 사용하나요?
- DBT가 지원하는 데이터 어댑터(데이터 플랫폼)는 무엇인가요?
- DBT가 Databricks와 같은 기존 제품과 다른 점은 무엇인가요?
- DBT를 배우고 사용하는 방법은 무엇인가요?

<div class="content-ad"></div>

## DBT이란 무엇인가요?

DBT(data build tool)는 SQL 스크립트(.sql)와 YAML 스크립트(.yml)를 사용하여 워크플로우를 변환하는 데 사용되는 오픈 소스 도구(Core 버전)입니다.

![이미지](/assets/img/2024-05-23-DBTinaNutshell_2.png)

- SQL 스크립트는 CTE를 사용하여 데이터를 모듈화된 방식으로 변환하는 데 도움이 됩니다.
- YAML 스크립트는 스키마, 설명 및 열에 대한 테스트 규칙(Null이 아닌 값, 고유 값 등)을 정의하는 데 도움이 됩니다.

<div class="content-ad"></div>

## DBT은 무료인가요?

DBT에는 DBT 코어와 DBT 클라우드 두 가지 버전이 있습니다.

![이미지](/assets/img/2024-05-23-DBTinaNutshell_3.png)

- DBT 코어: CLI(Command Line Interface) 버전으로, 간단한 pip 명령어 `pip install dbt-core`를 사용하여 설치할 수 있습니다. 연결을 위해 어댑터(snowflakes, SQL Server)를 설치하려면 `pip install dbt-snowflake`를 사용하세요.
- DBT 클라우드: GIT 및 어댑터(데이터 소스)를 통합하고 드래그 앤 드롭 기능을 사용하여 워크스페이스를 구성할 수 있는 GUI(Graphical User Interface)를 제공하며, 모델(당신의 .sql 파일)을 예약할 수 있는 기능이 추가되어 있습니다.

<div class="content-ad"></div>

## DBT가 해결하려는 문제는 무엇인가요?

![DBTinaNutshell_4](/assets/img/2024-05-23-DBTinaNutshell_4.png)

원활한 협업:

- 협업을 위한 통합 플랫폼을 즐기세요.
- 효율적인 팀워크를 위한 CI/CD-Git 통합을 활용하세요.

<div class="content-ad"></div>

편리한 데이터 변환:

- 번거로움 없이 간단한 SQL 선택 문을 사용하여 데이터를 변환하세요.

테스트로 신뢰성 확보:

- 사용자 정의 테스트 케이스를 포함하여 데이터 변환을 테스트하세요.

<div class="content-ad"></div>

편리하게 배포하고 일정을 계획하세요:

- 코딩을 개발 및 프로덕션과 같은 다양한 환경에 배포하고 일정을 조정하세요.

간편하게 작업 문서화하세요:

- 간단한 .yml 파일로 전체 프로세스를 문서화하세요.
- 데이터 변환 여정에 대한 포괄적인 문서를 작성하세요.

<div class="content-ad"></div>

## ETL에서 DBT는 어느 부분에 사용되나요?

DBT는 다른 ELT✅ 방법을 사용합니다❌ ETL이 아닌데요, 여기서는 데이터 플랫폼으로 모든 데이터를 추출 및 로드하고 DBT를 사용하여 변형한 후 다양한 사용 사례를 위해 다시 데이터 플랫폼에 로드합니다.

<img src="/assets/img/2024-05-23-DBTinaNutshell_5.png" />

## DBT는 어떤 데이터 어댑터(데이터 플랫폼)를 지원하나요?

<div class="content-ad"></div>

지금은 다음 데이터 플랫폼을 직접 지원합니다:

- Snowflakes
- Google Big Query
- Data Bricks
- AWS Redshift
- Trino

![이미지](/assets/img/2024-05-23-DBTinaNutshell_6.png)

Trino는 직접적으로가 아닌 Trino를 통해 여러 데이터 소스에 연결하는 데 사용됩니다. 자세한 내용은 이미지를 참조하세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-DBTinaNutshell_7.png" />

## DBT와 Databricks와 같은 다른 기존 제품과의 차이점은 무엇인가요?

- DBT: DBT는 주로 데이터웨어하우스 내에서 데이터 변환 및 문서화 문제를 .sql 및 .yml 파일을 사용하여 CICD-git 환경에서 간단하고 효과적으로 처리하는 것에 중점을 둡니다.
- Databricks (유명한 예시): Databricks는 대용량 데이터 분석을 위한 협업 환경을 제공하는 통합 분석 플랫폼입니다. 데이터 엔지니어링, 머신 러닝, 협업 데이터 과학 기능이 포함되어 있으며 주로 분산 데이터 처리를 위해 Spark와 함께 사용됩니다.

## 어떻게 DBT를 학습하고 사용할 수 있을까요?

<div class="content-ad"></div>

- DBT를 배우기 시작하려면 해당 웹사이트에서 시작할 수 있어요. 총 16개의 다양한 코스가 초급, 중급 및 고급 수준으로 분류되어 있어요.

![DBTinaNutshell_8](/assets/img/2024-05-23-DBTinaNutshell_8.png)

- DBT를 더 잘 배울 수 있도록 많은 매체 기사와 유튜브 비디오들이 있어요 (DBT 콘텐츠에 대한 업데이트 받으시려면 지켜봐주세요! 다가오는 게시물 및 DBT 관련 비디오에 대한 업데이트를 받으시려면 저를 팔로우해주세요).

Leo Godin의 DBT 시리즈 게시물: 링크

<div class="content-ad"></div>

나에 대해 더 알아보기:

저는 데이터 과학 애호가🌺이며, 수학, 비즈니스 및 기술이 데이터 과학 분야에서 더 나은 결정을 내리는 데 어떻게 도움이 될 수 있는지에 대해 배우고 탐구하고 있습니다.

더 많은 내용 보기: https://medium.com/@ravikumar10593/

내 모든 핸들 찾기: https://linktr.ee/ravikumar10593

<div class="content-ad"></div>

나의 뉴스레터를 찾아보세요: [https://substack.com/@ravikumar10593](https://substack.com/@ravikumar10593)