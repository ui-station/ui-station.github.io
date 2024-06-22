---
title: "데이터브릭스, 데브옵스 및 파이테스트"
description: ""
coverImage: "/assets/img/2024-06-19-DatabricksDevOpsandpytest_0.png"
date: 2024-06-19 12:24
ogImage: 
  url: /assets/img/2024-06-19-DatabricksDevOpsandpytest_0.png
tag: Tech
originalTitle: "Databricks, DevOps and pytest"
link: "https://medium.com/@rasmuslaursen/databricks-devops-and-pytest-858424b89383"
---


Databricks에서 코드 품질을 지속적으로 보장하고 DevOps 작업 프로세스에 통합하는 방법에 궁금증을 풀어 보셨나요? 더 이상 망설이지 마세요.

다음 예시에서는 Databricks에서 pytest와 DevOps를 사용하여 구현된 테스트 기능을 쉽게 시작하는 방법에 대해 살펴볼 것입니다.

# 목표

이 글을 마치면 Databricks에 구현된 함수를 테스트하고, DevOps에서 pull request가 제출될 때마다 테스트 스위트를 실행할 수 있게 될 것입니다. 아래에서 이를 어떻게 구현할지 살펴보세요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-DatabricksDevOpsandpytest_0.png" />

# DevOps에서 프로젝트 설정하기

Pytest는 두 가지 테스트 레이아웃을 지원하는데, 이 예시에서는 테스트가 애플리케이션 코드 외부에 배치되는 테스트 레이아웃을 사용할 것입니다. 이 분리는 나중에 데브옵스와 데이타브릭스 자산 번들을 사용하여 자동 릴리스를 다루는 기사에서 유용할 것입니다.

```js
project.toml
pipelines/
  pipeline_pytest.yml
src/
  functions/
    column_funtions.py
  soultion/
    demo_notebook.py
tests/
  test_column_funtions.py
```  

<div class="content-ad"></div>

이 예시에서는 src(소스 코드)와 tests(테스트)로 구성된 두 개의 주요 폴더를 갖춘 간단한 설정이 있습니다. src 폴더는 지원하는 함수를 포함하는 functions와 Lakehouse를 구현하는 노트북을 포함하는 solution 폴더로 구분됩니다.

column_functions.py에서는 주어진 열을 제곱하는 간단한 함수를 구현했습니다.

```python
# Databricks notebook source
def column_squared(df, columnname):
    df_squared = df.withColumn(columnname + "_squared", df[columnname] * df[columnname])
    return df_squared
```

test_column_functions.py에서는 column_functions.py의 함수 기능을 유효성 검사하는 간단한 테스트를 구현했습니다. 여기서 중요한 부분은 외부 데이터 소스나 스파크 세션에 의존하지 않고 독립적으로 유닛 테스트를 구현하고 있다는 것입니다. 입력과 예상 출력을 비교하기 위해 Databricks의 내장 기능을 사용합니다.

<div class="content-ad"></div>

```js
import pytest
from pyspark.sql import SparkSession
from pyspark.testing import assertDataFrameEqual
from src.functions.column_functions import column_squared

class TestColumnFuntions(object):
    def test_column_squared(self):
        spark = SparkSession.builder.getOrCreate()

        source_data = [("John", 25), ("Alice", 30), ("Bob", 35)]
        source_df = spark.createDataFrame(source_data, ["name", "age"])

        df_actual = column_squared(source_df,'age')

        expected_data = [("John", 25, 625), ("Alice", 30, 900), ("Bob", 35, 1225)]
        df_expected = spark.createDataFrame(expected_data, ["name", "age", "age_squared"])

        assertDataFrameEqual(df_actual, df_expected)
```

# DevOps 파이프라인

함수와 관련된 테스트를 구현한 후에는 이제 Azure DevOps에서 파이프라인을 설정할 수 있습니다.

파이프라인(pipeline_pytest.yml)에 대해 가상 환경을 Python용으로 생성하고 필요한 패키지를 설치한 다음 'tests' 디렉토리에서 pytest를 실행합니다. 이 경우 pytest-azurepipelines를 사용하여 pytest를 DevOps 파이프라인에 통합합니다. 이제 pytest는 'test_.py'로 시작하거나 '_test.py'로 끝나는 모든 테스트를 찾아 이 경로를 따라 이동합니다.

<div class="content-ad"></div>

```yaml
trigger:
  none

steps:
  - task: UsePythonVersion@0
    inputs:
      versionSpec: "3.9"
      addToPath: true

  - script: |
      python -m venv .venv
      source .venv/bin/activate
      python -m pip install --upgrade pip
      pip install numpy==1.22.4
      pip install pyspark
      pip install pandas
      pip install pyarrow
      pip install pytest-azurepipelines
      python -m pytest -vv tests
    displayName: "pytest"
```

이제 메인 브랜치에서 빌드 검증을 설정하여, 개발자가 풀 리퀘스트를 만들 때마다 정의된 테스트가 실행되도록 할 수 있습니다.

<img src="/assets/img/2024-06-19-DatabricksDevOpsandpytest_1.png" />

# 결론

<div class="content-ad"></div>

이 예시에서는 Databricks에서 구현된 기능에 대한 테스트 슈트를 설정하고 DevOps 워크플로에 통합하는 것이 얼마나 간단한지 살펴보았습니다. 이제 프로젝트에 필요한 테스트를 구현하여 지속적으로 고품질 코드를 제공할 수 있도록 만들어 보세요.