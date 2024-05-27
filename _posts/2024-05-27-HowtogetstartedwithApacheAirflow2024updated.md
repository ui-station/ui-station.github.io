---
title: "Apache Airflow을 시작하는 방법 2024 최신 업데이트"
description: ""
coverImage: "/assets/img/2024-05-27-HowtogetstartedwithApacheAirflow2024updated_0.png"
date: 2024-05-27 16:38
ogImage: 
  url: /assets/img/2024-05-27-HowtogetstartedwithApacheAirflow2024updated_0.png
tag: Tech
originalTitle: "How to get started with Apache Airflow [2024 updated]"
link: "https://medium.com/@hugolu87/how-to-get-started-with-apache-airflow-2024-updated-c388fb5433e4"
---


<img src="/assets/img/2024-05-27-HowtogetstartedwithApacheAirflow2024updated_0.png" />

- 노트북을 열어주세요
- 터미널을 열어주세요
- 아래 코드를 실행해주세요

```js
pip uninstall apache-airflow
```

<img src="https://miro.medium.com/v2/resize:fit:534/0*iDdoQ91AdPvPcWGm.gif" />

<div class="content-ad"></div>

죄송해요 — 이걸 쓸 때는 금요일이었어요!

Orchestra를 시도해보세요 🚀

(검색 엔진 최적화를 위해 추가한 내용입니다)

# Apache Airflow은 쉽게 배울 수 있나요?

<div class="content-ad"></div>

아파치 에어플로우는 특히 초보자에게는 어느 정도의 도전이 될 수 있지만, 일부 필수적인 개념과 도구에 익숙해지면 더 쉬워집니다. 에어플로우를 배우기 위해 알아야 할 내용과 학습이 쉬운 또는 어려운 이유를 살펴보겠습니다:

# 전제 조건

- 파이썬: 에어플로우는 파이썬으로 작성되었으므로, 파이썬에 대한 탄탄한 이해가 필수적입니다.
- 명령줄 인터페이스 (CLI): 명령줄 사용에 대한 기본 지식은 에어플로우의 설치와 구성에 도움이 됩니다.
- SQL: SQL을 알고 있는 것은 에어플로우가 데이터베이스와 상호 작용하는 경우가 많으므로 유용합니다.
- 예약 개념: 작업 예약 및 워크플로 관리의 기본 개념을 이해하는 것이 중요합니다.

# 학습 곡선

<div class="content-ad"></div>

- 초기 설정: Airflow를 설정하는 것은 여러 구성 요소(Web 서버, 스케줄러, 메타데이터 데이터베이스 등) 때문에 약간 복잡할 수 있어요.
- DAGs (Directed Acyclic Graphs): Airflow의 핵심 개념은 DAGs를 중심으로 돌아갑니다. DAGs를 정의하고 관리하는 방법을 익히는 데는 시간이 필요하죠.
- Operators와 Hooks: Airflow는 작업을 정의하고 다양한 시스템에 연결하기 위해 operators와 hooks를 사용해요. 이들의 다양성과 사용 사례를 이해하는 것은 처음에는 압도될 수 있어요.
- 설정 및 배포: Airflow를 다른 환경(로컬, 온프레미스, 클라우드)에서 실행하도록 설정하는 것은 복잡성을 더합니다.

# 학습 자료

- 문서: 공식 Airflow 문서는 포괄적이며 좋은 시작점입니다.
- 온라인 튜토리얼과 강의: Udemy, Coursera 등에서 온라인 튜토리얼, 강의, YouTube 비디오들이 Airflow의 기본을 안내해줄 수 있어요.
- 커뮤니티와 포럼: 포럼, GitHub 이슈, Stack Overflow를 통해 Airflow 커뮤니티와 소통하면 일반적인 문제에 대한 가치 있는 통찰과 해결책을 얻을 수 있어요.
- 책: “Data Pipelines with Apache Airflow”와 같은 책들은 체계적인 학습을 제공할 수 있어요.

# 실용적인 팁

<div class="content-ad"></div>

- 간단히 시작하기: 간단한 DAG부터 시작하여 점차 복잡성을 높여가세요.
- 실습 중심: 더 많이 연습할수록 더 편안해질 거에요. 작은 프로젝트를 만들어보거나 예제를 복제해보세요.
- 예제 탐색: 예제 DAG 및 일반 패턴을 검토하면 많은 실용적인 통찰력을 얻을 수 있어요.
- 관리 서비스 사용: Apache Airflow 설정이 어렵게 느껴진다면 Google Cloud Composer나 Amazon Managed Workflows for Apache Airflow와 같은 관리 서비스 사용을 고려해보세요.

# 요약

- 학습 용이성: 중간 정도의 난이도로 Python 및 관련 도구에 익숙해야 합니다.
- 학습 경로: 문서로 시작하여 튜토리얼을 사용하고 커뮤니티와 정기적인 실습을 통해 참여하세요.
- 복잡성: 사용 사례에 따라 다를 수 있으며, 간단한 DAG가 쉽지만 복잡한 워크플로 및 배포에 더 많은 노력이 필요합니다.

전반적으로 Apache Airflow에는 학습 곡선이 있지만 자원과 커뮤니티 지원이 많아 프로세스가 보다 원활해질 수 있습니다. 일관된 연습과 탐색을 통해 Airflow를 숙달하는 것은 분명히 가능합니다.

<div class="content-ad"></div>

# Apache Airflow을 시작하는 방법은?

오픈 소스라서 도커, 쿠버네티스를 배우고 다운로드하여 적극 활용해보세요:

Apache Airflow를 시작하려면 로컬 머신 또는 서버에 설치하고 설정하는 방법을 따르면 됩니다. 아래는 시작하는 데 도움이 되는 기본 가이드입니다:

# 1. Apache Airflow 설치

<div class="content-ad"></div>

## 준비 사항:

- Python: Python이 설치되어 있는지 확인해주세요 (버전 3.6 이상).
- 가상 환경: 충돌을 피하기 위해 가상 환경을 만드는 것이 좋은 습관입니다.

## 설치 단계:

- 가상 환경 만들기:

<div class="content-ad"></div>

```markdown
python -m venv airflow_env source airflow_env/bin/activate  # Windows에서는 `airflow_env\Scripts\activate`를 사용하십시오

- Apache Airflow 설치: Apache Airflow는 호환 가능한 종속성을 지정하기 위해 constraints 파일을 사용합니다. 아래 명령어를 사용하여 설치할 수 있습니다:

```bash
export AIRFLOW_VERSION=2.5.0
export PYTHON_VERSION="$(python --version | cut -d " " -f 2 | cut -d "." -f 1-2)"
export CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"
pip install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"
```

# 2. 데이터베이스 초기화
```

<div class="content-ad"></div>

Airflow은 작업 인스턴스 및 다른 메타데이터를 추적하는 데 데이터베이스를 사용합니다. Airflow를 실행하기 전에 데이터베이스를 초기화해야 합니다.

```js
airflow db init
```

# 3. 관리자 사용자 만들기

Airflow 웹 인터페이스에 액세스할 수 있는 관리자 사용자를 만듭니다.

<div class="content-ad"></div>

```js
에어플로우 사용자 생성 \
   --username admin \
   --firstname 당신의_이름 \
   --lastname 당신의_성 \
   --role Admin \
   --email 당신의_이메일
```

당신의 성, 이름 및 admin@example.com을 여러분의 정보로 대체하세요.

# 4. 에어플로우 웹 서버 및 스케쥴러 시작

- 웹 서버 시작:
```

<div class="content-ad"></div>

```js
# 첫 번째 터미널 창에서:
airflow webserver --port 8080

# 두 번째 터미널 창에서:
airflow scheduler
```

# 5. Airflow UI에 액세스하기

웹 브라우저를 열고 http://localhost:8080로 이동하세요. 이전에 생성한 관리자 자격 증명을 사용하여 로그인할 수 있습니다.

# 6. 첫 번째 DAG(Directed Acyclic Graph) 생성하기
```

<div class="content-ad"></div>

DAG은 실행하려는 작업을 관계와 종속성을 반영하는 방식으로 구성된 작업 모음입니다.

- DAG 파일 생성: dags 디렉토리에 새 Python 파일을 생성합니다 (일반적으로 ~/airflow/dags에 위치).

```python
# ~/airflow/dags/example_dag.py
from airflow import DAG
from airflow.operators.dummy_operator import DummyOperator
from datetime import datetime

default_args = {
    'owner': 'airflow',
    'start_date': datetime(2023, 1, 1),
    'retries': 1,
}

dag = DAG(
    'example_dag',
    default_args=default_args,
    description='간단한 예제 DAG',
    schedule_interval='@daily',
)

start = DummyOperator(task_id='start', dag=dag)
end = DummyOperator(task_id='end', dag=dag)

start >> end
```

- 파일 저장: 이 파일을 example_dag.py로 저장하세요.

<div class="content-ad"></div>

# 7. Airflow UI에서 DAG 확인하기

DAG 파일을 저장한 후 Airflow UI로 이동하면 새로운 DAG가 목록에 표시됩니다. 수동으로 트리거하거나 정의된 일정에 따라 자동으로 트리거될 수 있습니다.

# 추가 자료

- 공식 문서: Apache Airflow 문서
- 튜토리얼 및 가이드: 여러 온라인 튜토리얼이 특정 사용 사례 및 고급 구성에 도움이 될 수 있습니다.
- 커뮤니티 지원: Airflow의 Slack 채널이나 다른 커뮤니티 포럼에 가입하여 지원 및 네트워킹을 할 수 있습니다.

<div class="content-ad"></div>

이러한 단계를 따라하면 Apache Airflow를 설정하고 실행하여 자체 워크플로를 만들고 관리할 수 있을 것입니다.

## Airflow을 사용하려면 코딩이 필요한가요?

당연히 그렇죠. Python 및 OOP 지식이 필요합니다. 또한 CI/CD, 약간의 terraform 및 Kubernetes도 필요합니다!

## Apache Airflow를 배우는 데 얼마나 시간이 걸릴까요?

<div class="content-ad"></div>

너무 길어요. 궁금하다면, Orchestra를 사용해보세요. 누구나 사용할 수 있고 기업용 오케스트레이터 및 감시 도구의 모든 기능을 가지고 있지만 수고를 덜 수 있어요.