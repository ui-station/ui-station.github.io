---
title: "파이테스트Pytest와 파이스파크PySpark를 사용한 데이터 품질 유효성 검사를 위한 4가지 팁"
description: ""
coverImage: "/assets/img/2024-06-19-4TipsforDataQualityValidationswithPytestandPySpark_0.png"
date: 2024-06-19 12:01
ogImage:
  url: /assets/img/2024-06-19-4TipsforDataQualityValidationswithPytestandPySpark_0.png
tag: Tech
originalTitle: "4 Tips for Data Quality Validations with Pytest and PySpark"
link: "https://medium.com/slalom-build/4-tips-for-data-quality-validations-with-pytest-and-pyspark-69e100fd387e"
---

## 높은 품질과 신뢰할 수 있는 결과를 얻기 위한 변환된 데이터 테스트

이 문서는 Likitha Lokesh와의 협력으로 작성되었습니다.

![이미지](/assets/img/2024-06-19-4TipsforDataQualityValidationswithPytestandPySpark_0.png)

## 배경

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

저는 최근 데이터 소프트웨어 프로젝트에 품질 엔지니어로 참여했는데, 여기서는 변환된 데이터에 대한 많은 테스트가 필요했습니다. 이 프로젝트에서는 데이터를 한 Amazon S3 버킷에서 다른 버킷으로 변환하기 위해 AWS Glue를 사용했습니다. 데이터는 Python을 사용하여 PySpark를 통해 변환되었고, 따라서 이러한 변환을 테스트하기 위한 테스트 자동화 프레임워크는 동일한 기술 스택에 의존했지만 Pytest도 추가되어 일관성을 유지하려고 노력했습니다.

Pytest, PySpark 및 AWS로 시작하는 방법에 대해 자세히 알아보려면 제 동료 Likitha Lokesh가 작성한 멋진 블로그를 확인해보세요.

## 소개

상기 프로젝트에서 우리 팀은 데이터를 제3자 소프트웨어 도구에서 소화되도록 변환했습니다. 데이터를 성공적으로 가져오기 위해 각 대상 파일에는 요구 사항 목록이 있었습니다. 각 대상 파일은 요구 사항을 충족해야만 소프트웨어가 데이터를 수용하고 데이터가 분석용으로 액세스 가능하지 않을 것이라는 문제를 방지할 수 있었습니다.

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

목록에 나열된 요구 사항은 소스 파일에서 대상 파일로 데이터를 변환하는 데 필요한 스크립트가 어떻게 보이는지를 팀이 판단하는 데 도움을 주었지만, 모든 대상 파일의 데이터가 모든 요구 사항을 충족할 것을 보장하지는 않았습니다.

데이터의 불일치는 데이터를 분석하거나 다른 목적으로 사용할 때 결과가 왜곡되는 원인이 될 수 있습니다. 이 프로젝트에서는 금융 데이터를 사용했기 때문에 데이터에 대한 신뢰 수준이 절대적으로 중요했습니다.

따라서 다음과 같은 질문이 제기됩니다:

모든 데이터 소프트웨어 프로젝트 솔루션은 일반적으로 맞춤형이 아니지만, 이 프로젝트에서 습득한 몇 가지 기술은 다양한 데이터 관련 프로젝트에서 효율적인 엔지니어링과 사고 과정에 도움이 될 수 있습니다.

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

이 경험에서 데이터 품질 테스트를 위한 네 가지 주요 요점 목록을 만들었습니다:

- 잘못된 데이터를 기록할 조건부로 단언을 감싸기
- 공통 데이터 테스트 결정하고 매개변수화하기
- 알려진 데이터 문제에 대한 Pytest 경고 및 XFail 활용하기
- 환경 변수를 -E 플래그로 관리하기

이 문서에서 네 가지 요점에 대해 각각 설명하고, 각 요점이 목록에 포함된 이유를 강조하겠습니다.

## 단언을 조건부로 감싸기

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

전통 소프트웨어 프로젝트에서 자동화 테스트를 수행할 때, 버그에 대한 가시성은 디버깅 데이터보다 약간 더 명확합니다. 전통적인 소프트웨어 프로젝트나 애플리케이션에서는 앱을 열어 검사하거나 API를 검토할 수 있지만 데이터는 매우 많을 수 있습니다. PySpark에서 데이터 문제에 대한 가시성을 얻기 위한 돋보기는 데이터프레임입니다.

제 프로젝트에서는 .csv 파일의 데이터를 테스트했습니다. 많은 테스트가 동일한 개요를 가지고 있었습니다:

- .csv 파일을 읽어 데이터프레임 만들기
- 데이터를 분석하기 위해 데이터프레임 메서드 사용(요구 사항에 따라)
- 단언을 조건부로 래핑하기

가령 파일의 한 열에는 ZIP 코드 데이터가 있고 요구 사항이 각 값이 정확히 5자여야 한다면, 해당 테스트는 다음과 같을 것입니다:

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

```js
import length
import logging

def test_zipcode_data_length(spark_source, csv_file_path: str):

  ## .csv 파일을 읽고 DataFrame을 생성합니다.
  dataframe = spark_source.read.csv(csv_file_path)

  ## DF에서 filter 메소드를 사용하여 열 값 분석하고
  ## (다른 DF를 만듭니다)
  invalid_rows = dataframe.filter(length(dataframe['Zipcode']) != 5)

  ## 디버깅 및 정확한 위치를 찾기 위해 조건부로 Assertion을 감싸세요
  if invalid_rows.count() == 0:
    logging.info("예상대로 'Zipcode' 열의 모든 값이 5의 길이와 동일합니다!")
    assert True
  else:
    logging.error("'Zipcode' 열의 값은 모두 5의 길이와 동일해야 하지만
    예상과 다른 값이 존재합니다!")
  ## 요구 조건을 충족시키지 못하는 행이 포함된 필터링된 DF를 출력합니다
    invalid_rows.show(truncate=False)
    assert False
```

참고: 기사 전체에 코드 조각이 많습니다. 테스트 메소드 간 코드 중복을 줄이기 위해 테스트 도우미 함수를 사용하는 것이 best practice이지만, 이 기사의 목적에서는 벗어납니다.

위의 예제에서 볼 수 있듯이, 단순한 True/False 어서션이 아니라 실패 시 적절한 로깅을 위해 조건부로 어서션을 배치하여 데이터가 기대에 충족되지 않을 경우 디버깅 및 특정 데이터 위치를 찾기 위해 필요한 기능이 수행됩니다. 잘못된 행이 포함된 DataFrame은 파일을 소프트웨어가 처리하려면 수정해야 할 데이터 위치를 특정하게 알려줄 수 있습니다.

특정 열마다 모든 값이 정확히 5의 길이여야 하는 경우를 보여준 예제였지만, 테스트의 일반적인 개요/흐름의 원칙은 동일합니다. Assertion을 조건부로 감싸지 않고 다른 옵션은 무엇인가요? 변환된 .csv 파일을 다운로드하고 수동으로 검토하거나 필터링하여 오류 행을 찾을까요? 그것은 매우 흥미로운 옵션이 아닙니다.

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

무엇을 테스트하든간에, 실패의 근본 원인을 빠르게 평가해야 하는 필요성은 항상 품질 테스트 전략의 구성 요소가 될 것입니다. 단언문을 조건문으로 감싸는 것은 그 필요에 대한 해답을 제공합니다. 조건문으로 단언을 감싸는 자동화된 접근은 효율적이며 데이터 관련 문제를 신속하게 다루는 실패 빠른 접근법을 제공합니다. 이 방식은 많은 짐작을 제거하고 시스템/파이프라인의 품질을 유지하거나 개선하는 데 필요한 구체적인 정보를 제공합니다.

## 일반 데이터 테스트를 매개변수화 하기

데이터는 방대하기 때문에 혐이 일 수 있지만, 테스트할 때는 일반적으로 모호함이 적습니다. 요구 사항은 매우 명확하며, 제 경험상 전통적인 소프트웨어 프로젝트보다 수집하기 쉽습니다. 종종 일반적인 데이터 요구 사항은 서로 다른 데이터 세트 간에 겹칠 수 있습니다.

제 프로젝트의 경우, 생성되어야 했던 대상 파일 중 많은 파일들이 서로 다른 파일에 대해 유사한 요구 사항을 가지고 있었으며, 심지어 동일한 파일 내의 다른 열도 동일한 요구 사항을 가졌습니다. 간단히 유지하기 위해 각 파일이 데이터를 포함해야 한다는 요구 사항이 하나 있었는데, 이는 명백한 요구 사항처럼 보일 수 있지만 모든 파일에서 실행할 수 있는 매우 쉽고 빠른 자동화된 테스트이며, 예상치 못한 가장자리 경우의 데이터 변환 시나리오에서 유용할 수 있습니다.

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

여기서 가장 좋은 방법은 모든 대상 파일마다 동일한 간단한 테스트를 작성하지 않아도 되는 방법이 무엇인가요? 어떻게 코드 재사용성을 최대화할 수 있을까요? 답은 Pytest의 Parametrize 표시를 사용하여 테스트를 매개변수화하는 것이었습니다.

```python
import pytest

@pytest.mark.parametrize(VALUES HERE)
def test_data_present(spark_source, csv_file_path: str):

  ## .csv 파일을 읽고 DataFrame 생성
  dataframe = spark_source.read.csv(csv_file_path)

  ## DF가 비어 있지 않은지 확인
  assert dataframe.first() is not None
```

Pytest의 parametrize 표시를 통해 모든 대상 파일을이 동일한 테스트를 통해 실행하여 생성되는 각 파일에 최소한 어떤 종류의 데이터가 포함되어 있는지 확인할 수 있습니다. 이전 프로젝트에서 추가 쉼표 구분 기호의 데이터 내 포함 또는 고유 데이터 필요 열을 확인할 때 특히 중요한 몇 가지 경우가 있었습니다.

이 경우에 유의해야 할 점은 테스트 시나리오를 정의하는 초기 단계에서 노력이 더 필요할 수 있다는 것입니다. 공통 요구 사항을 찾고 코드를 재사용하는 최상의 전략을 고민하는 것입니다. 그러나 장기적으로 테스트 개발이 지수적으로 가속화될 것입니다. 이 경험에서 배운 점은 요구 사항을 더 잘 이해하고 먼저 이러한 요구 사항의 공통점을 파악해야 한다는 것이었습니다. — 테스트를 개발하기 전에.

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

요구 사항에 따라 생성 될 다양한 유형의 테스트 계획을 작성합니다. 데이터 유형, uniqueness, formatting, 추가 구분 기호 없음/데이터 내에서 적절한 열 구분이 유효성을 검사하도록 테스트 계획을 분석하고 가능한 한 테스트를 통합하려고 노력합니다. 요구 사항에서 패턴을 파악하고 테스트를 개발하기 전에 더 강력한 계획을 수립하는 것은 개발 속도 및 테스트 실행 속도를 높이는 데 도움이 됩니다.

## 경고 및 XFail 활용

데이터와 상호 작용할 때, 특히 민감한 데이터(예: 금융 데이터)를 사용하는 새 소프트웨어를 개발할 때는 종종 실제 데이터와 상호 작용하기 전에 먼저 낮은 환경에서 (예: 개발/테스트 환경) 시험적으로 개발됩니다. 이 과정은 개발 중에 모든 결함이 해결되는 동안 실제 데이터(프로덕션)를 보호하는 데 도움이 됩니다. 그러나 비프로덕션 환경 데이터의 관리 오류로 인해 데이터 관리가 어려워지고 결과가 왜곡될 수 있습니다.

비프로덕션 환경 데이터의 관리 오류는 해결하기 어렵고 결과를 왜곡시킬 수 있습니다. 다행히도 Pytest에 내장된 두 가지 기능인 Pytest Warnings와 Pytest XFail을 활용하면 알려진 데이터 문제를 테스트하는데 도움을 받을 수 있습니다. 이 두 옵션 중에서 선호도나 권장사항이 없습니다. 상황에 가장 적합한 도구를 선택하십시오.

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

이 전문에 나온 제안 중 첫 번째 제안을 이미 구현 중이거나 구현할 예정이라면, 당신은 쉽게 Pytest 경고를 동일한 개념에 임베딩할 수 있습니다. 여기서의 차이점은 거짓 주장 대신 경고가 발생할 것이랍니다. 아래와 같이 보일 거에요:

```js
import warnings
import logging

def test_email_data_unique(spark_source, csv_file_path: str):

  ## Read the .csv file and Create a DF
  dataframe = spark_source.read.csv(csv_file_path)

  ## Use the count method on DF to capture the number of total rows
  num_rows = dataframe.count()

  ## Use the select method - paired with the distinct and count methods
  ## on DF to analyze column values for uniqueness
  num_unique_rows = dataframe.select(dataframe['Email'].distinct().count())

  ## Wrap Assertion in a Conditional and Leverage WARNINGS
  if num_rows == num_unique_rows:
    logging.info("All of the values in the 'Email' column are unique
    as expected!")
    assert True
  else:
  ## Print the rows that don't meet the requirements
    dataframe.groupBy(dataframe['Email']).count().where("count > 1").drop(
    "count").show(truncate=False)
  ## Warn instead of fail
    warnings.warn(UserWarning("Some of the data in the 'Email' column is
    not meeting the uniqueness requirement!")
```

품질 엔지니어로서, 빨간색은 주의가 필요한 것을 나타냅니다. 알려진 문제에 대해 경고를 사용하는 가장 좋은 점은 '뉴트럴'에서 출력되기 때문에 "이것은 알려진 사항이며 즉시 주의가 필요하지 않거나 걱정할 필요가 없습니다"라는 메시지를 전달해준다는 점이었습니다. 이 메시지는 테스트 스위트를 실행할 때 다른 기여자들에게도 전달되어, 노력을 더이상 메신저로 행동하지 않고 팀의 속도에 집중하는 데 도움이 됩니다.

알려진 데이터 문제를 다룰 수 있는 또 다른 좋은 옵션은 Pytest XFail 표시입니다. Pytest Warnings 능력과 유사하게, 테스트가 실패하면 결과가 빨강색 대신 노란색으로 나올 것입니다. 특히 적용 가능한 경우, 빨간색이 아닌 다른 색상으로 인사를 받는 것이 얼마나 유용한지 이중으로 강조할 수 없습니다.

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

위 스크린샷을 보면 'XFail'이라는 테스트가 노란색 'x'로 실행되었음을 나타낼 것입니다. 그러나 'XFail' 표시는 테스트의 어설션 섹션에 표시되지 않습니다. 'XFail'은 이전 섹션에서 테스트를 표시하는 방법과 유사하게 함수 상단에 표시됩니다. 아래 'XFail' 구현 내용을 확인해보세요:

```js
import pytest

@pytest.mark.xfail(reason="알려진 데이터 문제로 임시로 실패하는 것으로 예상됨")
def test_date_format():

## 나머지 테스트 내용
```

앞서 살펴본 경고 옵션처럼 이 유용한 'XFail' 표시는 품질 엔지니어가 적절한 문서 작성을 하고 동료에게 컨텍스트 정보를 남길 수 있도록 도와줍니다. 'XFail'의 중요한 추가 혜택 중 하나는 'XFail'로 표시된 테스트가 예상대로 실패하는 경우 (즉, 버그 수정이 해결된 경우)에 테스트가 실패하므로 품질 엔지니어는 이제 테스트를 수정/변경해야 한다는 것을 알 수 있습니다.

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

주의해서 사용해주세요: 특정 프로젝트 요구 사항에 가장 적합한 경우/방법/이유를 고려해주세요. 예상치 못한 데이터의 불일치 사항을 확인한 후에만,

- 실패의 근본 원인을 확인한 후에
- 팀과 함께 실패의 우선 순위/심각성을 평가한 후에

Pytest의 이러한 기능을 활용해볼 수 있습니다.

다른 한편으로, YELLOW 플래그로 특정 테스트를 지정할 수 있는 옵션을 가지고 있는 것은 필요한 자동화된 테스트가 문서화되어 테스트 스위트에 있고, 프로젝트가 제작으로 나아갈수록, 희망컨대 데이터 문제가 더 이상 발생하지 않는 환경에서 접근 가능하게끔 해줍니다.

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

## 동적으로 환경 변수 관리하기

네 키 포인트를 마무리하며, 다양한 환경 간 데이터 테스트의 세밀함에 대해 계속 다룰 것입니다. 이 기사의 시작 부분에서는 테스트 중인 데이터가 Amazon S3 버킷에 호스팅되어 있다고 언급했었습니다. 버킷 이름과 경로는 리포지토리의 INI 구성 파일에 나열되어 있었고, 다양한 테스트에 공급되었는데, 그러나 환경에 따라 약간 변경된 버킷 이름이 있었습니다.

```js
[BUCKET]
S3 = my-dev-environment-bucket

[PATH]
FILE-PATH = pathway/to/dev/environment
```

버킷 이름과 해당 경로의 변종은 각 테스트 세션마다 터미널에서 동적으로 관리할 것이며, 이렇게 해서 Pytest의 -E 플래그가 유용하게 사용되었습니다. 픽스처와 pytest_addoption 함수를 사용하여 원하는 환경을 -E 플래그로 지정하고 표준 Pytest 명령에 따라 각 테스트 실행마다 환경을 전환시킬 수 있었습니다.

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

INI 파일은 하드 코딩된 변수에서 약간 변형되었지만, 테스트에 전달되는 변수 이름에는 영향을 주지 않았기 때문에 이 조정은 테스트에 매우 낮은 영향을 미쳤습니다. 그런 다음 INI 파일 자체가 각 테스트 실행 중 임시 템플릿이 되었고, 테스트 실행 완료 후 복원되었습니다. 변형은 다음과 같았습니다:

```js
[BUCKET]
S3 = my-{env}-environment-bucket

[PATH]
FILE-PATH = pathway/to/{env}/environment
```

INI 파일에 대한 이 조정과 테스트 세션 중 파일을 관리하는 몇 가지 방법과 함께, 필요한 환경에 따라 pytest -E=dev 또는 pytest -E=qc 또는 pytest -E=prod와 같은 명령이 되었습니다. 이 변경으로 인해 환경간 전환의 복잜한 점 때문에 버킷 이름이 변하는 것이 매우 간단해졌습니다. 이제 더 이상 특정 환경에서 테스트 실행을 수행하려면 INI 파일의 변수 이름을 변경하는 것을 매번 기억해야 했던 의존성이 없어졌습니다. 액세스 권한이 있는 모든 팀원이 이제 명령줄에서 쉽게 환경 간 전환을 할 수 있습니다.

INI 파일에서 이 유연성을 어떻게 구현하는지에 대해 자세히 알아보려면, 여기에서 내 How-To 기사를 확인해주세요.

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

## 마무리 생각

빠르게 복습해보면, 이 글에서 강조한 데이터 품질 테스트의 네 가지 주요 포인트는 다음과 같습니다:

- 로그에 잘못된 데이터를 기록하기 위해 어설션을 조건문으로 래핑
- 일반적인 데이터 테스트를 결정하고 매개변수화
- 알려진 데이터 걱정 사항에 대해 Pytest Warnings와 XFail 활용
- -E 플래그로 환경 변수 관리

이러한 전략들을 통해 변환된 데이터의 품질에 대한 신뢰 수준을 높이는 것이 전반적인 목표입니다. 이러한 포인트들이 유익했고 다음 데이터 프로젝트에서 유용한 팁과 전략을 얻을 수 있었으면 좋겠습니다.

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

제안 사항에 대한 피드백을 알고 싶어요. 언제든지 댓글로 의견을 남겨주세요!

#QE4DE

## 자료

- AWS Glue 정보
- Amazon S3 정보
- PySpark 문서
- Pytest 문서
- Likitha Lokesh의 PySpark, Pytest, Amazon S3 시작하기
- PySpark 데이터프레임
- Pytest 파라미터화
- Pytest 경고
- Pytest 실패 예상
- Pytest -E 플래그
- Taylor Wagner의 INI 파일 변수 조작 방법
