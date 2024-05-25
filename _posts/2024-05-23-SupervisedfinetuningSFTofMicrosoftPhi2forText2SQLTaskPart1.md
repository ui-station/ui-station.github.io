---
title: "마이크로소프트 Phi2의 Text2SQL 작업을 위한 지도 학습 세부 조정 SFT 파트 1"
description: ""
coverImage: "/assets/img/2024-05-23-SupervisedfinetuningSFTofMicrosoftPhi2forText2SQLTaskPart1_0.png"
date: 2024-05-23 18:24
ogImage: 
  url: /assets/img/2024-05-23-SupervisedfinetuningSFTofMicrosoftPhi2forText2SQLTaskPart1_0.png
tag: Tech
originalTitle: "Supervised fine tuning (SFT) of Microsoft Phi2 for Text2SQL Task (Part 1)"
link: "https://medium.com/@divyeshjagatiya/supervised-fine-tuning-sft-of-microsoft-phi2-for-text2sql-task-part-1-d56b216311f0"
---


![image](/assets/img/2024-05-23-SupervisedfinetuningSFTofMicrosoftPhi2forText2SQLTaskPart1_0.png)

이 글에서는 우리만의 모델, LLM (Large Language Model)을 세밀하게 조정하여 자연어 텍스트에서 유효한 SQL 쿼리를 작성할 수 있는 기능을 추가할 것입니다.

한 단계씩 살펴보겠습니다.

- 소개
  - 사전 훈련된 모델 선택
  - 입력/출력 형식
- 데이터셋 준비
  - 정리 작업 진행
  - 하위 집합 생성
- (계속되는 내용은 Part 2에서)
- 결론

<div class="content-ad"></div>

# 소개

요즘, 트랜스포머 기반 모델이 자연어 처리 분야에서 많은 문제를 해결하고 있어요. 잘 알려진 예시로는 GPT, LLAMA, Mistral 등이 있습니다. 이 모델들은 특정 자연어 처리 문제를 해결하기 위해 입력으로 프롬프트를 사용합니다.

![이미지](/assets/img/2024-05-23-SupervisedfinetuningSFTofMicrosoftPhi2forText2SQLTaskPart1_1.png)

## 사전 학습된 모델 선택

<div class="content-ad"></div>

미리 훈련된 모델을 사용하여 시작해 보세요. 미리 훈련된 모델의 정의는 무엇일까요?

미리 훈련된 모델은 수천만 개 또는 수십억 개의 토큰을 사용하여 "다음 단어 예측" 목적으로 훈련된 모델입니다. 이 훈련 과정동안, 문장 내 단어의 구조와 의미를 학습합니다.

이 작업에서는 미러소프트/파이2 미리 훈련된 모델을 사용할 것입니다. 이 모델은 1.4 조 토큰으로 훈련되었으며, 27 억 개의 파라미터를 갖추고 있습니다. 이 모델은 SLM(작은 언어 모델)로 간주될 수 있습니다.

이 유형의 미리 훈련된 모델은 앞선 맥락을 기반으로 새로운 토큰을 생성할 수 있는 능력을 갖고 있습니다. 이 모델은 독립적인 질문, QA, 채팅 형식, 그리고 코드 생성과 같은 다양한 용도에 사용될 수 있습니다.

<div class="content-ad"></div>

이 모델을 QA 스타일로 텍스트2SQL 생성을 위해 미세 조정할 예정입니다.

## 입력/출력 형식

| | |
|------------------|----------------------------------|
| **input**        | User question                    |
| **output**       | SQL query                        |

질문은 다음과 같습니다: LLM은 사용자 질문에서 어떻게 SQL을 생성할까요?

<div class="content-ad"></div>

인간이라도 할 수 없어요. 적어도 테이블 구조에 대한 정보와 샘플 데이터가 필요한데, 그럼에도 불구하고 질문에 대한 SQL 쿼리를 해결할 수 있을 거에요.

LLM과 유사하게, 어떤 맥락을 제공해야 해요. 따라서 우리의 입력은 (맥락) + (사용자 질문)이고, LLM이 우리를 위해 SQL을 생성할 거에요.

그러니 데이터셋 수집 및 준비를 시작해 보고, 그 다음으로 세밀하게 조정해 봐요.

# 데이터셋 준비

<div class="content-ad"></div>

잠시 찾아보니 huggingface의 “gretelai/synthetic_text_to_sql” 데이터셋을 찾았어요. 제가 찾고 있던 작업에 가장 적합한 것 같아요. 데이터셋에 대해 더 많은 정보를 얻으려면 링크를 클릭해주세요.

```python
from datasets import Dataset, load_dataset

# 데이터셋 불러오고 원치 않는 열 제거하기
dataset = load_dataset("gretelai/synthetic_text_to_sql") \
    .remove_columns(['domain_description', 'sql_complexity_description',
                     'sql_task_type_description', 'sql_explanation', 'sql_task_type'])

dataset
```

```python
DatasetDict({
    train: Dataset({
        features: ['id', 'domain', 'sql_complexity', 'sql_prompt', 'sql_context', 'sql'],
        num_rows: 100000
    })
    test: Dataset({
        features: ['id', 'domain', 'sql_complexity', 'sql_prompt', 'sql_context', 'sql'],
        num_rows: 5851
    })
})
```

```python
dataset['train'][0]
```

<div class="content-ad"></div>

데이터셋이 무엇인지 감을 잡기 위해 하나의 샘플을 살펴봅시다. 여기서 우리는 "sql_context," "sql_prompt," 그리고 "sql" 필드를 사용할 것입니다.

- sql_context: 테이블 생성 및 삽입 문장
- sql_prompt: 사용자 쿼리
- sql: 대상 쿼리

(sql_context + sql_prompt)가 입력이 되고, (sql)이 대상 생성이 됩니다.

```js
{'id': 5097,
 'domain': 'forestry',
 'sql_complexity': 'single join',
 'sql_prompt': '각 영업사원이 판매한 총 목재 양을 영업사원별로 정렬하여 나타내시오.',
 'sql_context': "CREATE TABLE salesperson (salesperson_id INT, name TEXT, region TEXT); INSERT INTO salesperson (salesperson_id, name, region) VALUES (1, 'John Doe', 'North'), (2, 'Jane Smith', 'South'); CREATE TABLE timber_sales (sales_id INT, salesperson_id INT, volume REAL, sale_date DATE); INSERT INTO timber_sales (sales_id, salesperson_id, volume, sale_date) VALUES (1, 1, 120, '2021-01-01'), (2, 1, 150, '2021-02-01'), (3, 2, 180, '2021-01-01');",
 'sql': 'SELECT salesperson_id, name, SUM(volume) as total_volume FROM timber_sales JOIN salesperson ON timber_sales.salesperson_id = salesperson.salesperson_id GROUP BY salesperson_id, name ORDER BY total_volume DESC;'}
```

<div class="content-ad"></div>

## 정리를 해봅시다

이 데이터셋은 합성 데이터입니다. 유효하지 않은 문맥이나 SQL 쿼리를 가질 수 있습니다. 이러한 레코드를 찾아 제거해봅시다. 쓰레기를 넣으면 쓰레기가 나온다는 말이죠.

다음 조건에 따라 유효한 데이터를 확인할 것입니다:

- SQL 문맥과 SQL 쿼리는 SQL Lite 데이터베이스에 유효해야 합니다.
- 테이블은 샘플 레코드를 가져야 합니다.
- SQL 쿼리를 실행한 후에 결과를 얻을 수 있어야 합니다.

<div class="content-ad"></div>

```js
import sqlite3

def check_all_tables_have_values(row, debug=False):

    # 테이블에 레코드가 있어야 함
    if row['sql_context'].find('INSERT INTO') == -1:
        return False

    try:
        db = sqlite3.connect(":memory:")
        cur = db.cursor()
        cur.executescript(row['sql_context'])
        res = cur.execute(row['sql']).fetchall()
        if debug: print(res)
        # print(res, len(res))
        return len(res) > 0
    except:
        # print("Error while run query")
        return False

dataset = dataset.filter(lambda x : check_all_tables_have_values(x))
dataset
```

```js
DatasetDict({
    train: Dataset({
        features: ['id', 'domain', 'sql_complexity', 'sql_prompt', 'sql_context', 'sql'],
        num_rows: 53478
    })
    test: Dataset({
        features: ['id', 'domain', 'sql_complexity', 'sql_prompt', 'sql_context', 'sql'],
        num_rows: 3133
    })
})
```

보시다시피 데이터의 약 46%가 제거되었습니다. 이것은 SQL Lite와 호환되지 않거나 데이터가 없을 수 있습니다.

## 하위 집합 만들기```

<div class="content-ad"></div>

그럼,이 초기 실험을 위한 데이터셋이 훨씬 큽니다. 이를 위해 그 중 일부를 만들어 보겠습니다.

다음과 같이 14개 도메인과 3가지 SQL 복잡성 수준으로 데이터셋을 만들 것입니다:

```js
SELECTED_SQL_COMPLEXITY = ['basic SQL', 'aggregation','single join']

SELECTED_DOMAINS = [
    "technology", "sports", "logistics", "space", "energy",
    "finance", "agriculture", "justice", "retail", "media",
    "education", "healthcare", "fashion", "music"
]

def filter_by_sql_task_type_and_domains(row):
    return row['sql_complexity'] in SELECTED_SQL_COMPLEXITY \
         and row['domain'] in SELECTED_DOMAINS

dataset = dataset.filter(lambda x : filter_by_sql_task_type_and_domains(x))
dataset
```

```js
DatasetDict({
    train: Dataset({
        features: ['id', 'domain', 'sql_complexity', 'sql_prompt', 'sql_context', 'sql'],
        num_rows: 6713
    })
    test: Dataset({
        features: ['id', 'domain', 'sql_complexity', 'sql_prompt', 'sql_context', 'sql'],
        num_rows: 408
    })
})
```

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-SupervisedfinetuningSFTofMicrosoftPhi2forText2SQLTaskPart1_3.png" />

여기에는 14개의 도메인 데이터셋이 있으며, 각 도메인은 훈련 데이터에 적어도 300개의 샘플이 있습니다. SQL 복잡성은 "기본 SQL"의 50%, "단일 조인"의 20%, 그리고 "집계"의 30%로 분포됩니다.

# (계속, 파트 2로 이어집니다)

# 결론

<div class="content-ad"></div>

이 문서에서는 SLM이 무엇인지 알아보고 fine-tuning을 통해 Text2SQL 작업을 어떻게 해결할 것인지에 대한 아이디어를 얻게 됩니다.

데이터셋에 대해 작업을 진행했으며, 다음 (제2부) 글에서 실제 fine-tuning 프로세스를 수행할 것입니다.