---
title: "당신만의 LLM 평가 알고리즘을 SageMaker Clarify Foundation 모델 평가에 가져다 써 보세요"
description: ""
coverImage: "/assets/img/2024-05-27-BringYourOwnLLMEvaluationAlgorithmstoSageMakerClarifyFoundationModelEvaluations_0.png"
date: 2024-05-27 17:06
ogImage:
  url: /assets/img/2024-05-27-BringYourOwnLLMEvaluationAlgorithmstoSageMakerClarifyFoundationModelEvaluations_0.png
tag: Tech
originalTitle: "Bring Your Own LLM Evaluation Algorithms to SageMaker Clarify Foundation Model Evaluations"
link: "https://medium.com/aws-in-plain-english/bring-your-own-llm-evaluation-algorithms-to-sagemaker-clarify-foundation-model-evaluations-714ce6f02fbb"
---

![Amazon SageMaker Clarify Foundation Model Evaluations](/assets/img/2024-05-27-BringYourOwnLLMEvaluationAlgorithmstoSageMakerClarifyFoundationModelEvaluations_0.png)

Amazon SageMaker Clarify Foundation Model Evaluations는 내장 평가 알고리즘을 다양한 NLP 작업(요약, 질의 응답, 유해성 감지 등)에 걸쳐 실행할 수 있는 도구입니다. 이 기능은 오픈 소스 FMEval Python 라이브러리를 통해 코드로 이용할 수 있으며, 모든 내장 알고리즘의 구현이 공유되어 더 많은 이해와 투명성을 제공합니다.

FMEval은 여러분의 LLMOps/FMOPs 워크플로에 손쉽게 통합할 수 있기 때문에 강력한 도구이며, SageMaker Pipelines 및 일반적인 AWS 생태계와 쉽게 통합됩니다. 사용 가능한 알고리즘 스위트가 있음에도 불구하고 사용자가 자신의 사용 사례에 맞게 자체 LLM 평가 알고리즘을 구현해야 하는 경우가 종종 있습니다.

이 예제에서는 FMEval 라이브러리를 확장하여 "자체 알고리즘을 가져오는" 방법을 살펴보겠습니다. 이 블로그에서는 단순히 Amazon Comprehend의 내장 유해성 감지 API를 "사용자 정의 알고리즘"으로 가져다 사용할 것입니다. 라이브러리에서 제공되는 것을 활용하고 싶다면 FMEval은 이미 자체 유해성 알고리즘을 구현하고 있음을 참고하세요.

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

## 테이블 목차

- 솔루션 개요 및 설정
- 사용자 정의 평가 알고리즘 구현과 실행
- 추가 리소스 및 결론

## 1. 솔루션 개요 및 설정

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

코드로 넘어가기 전에 먼저 FMEVal 뒤에 있는 핵심 구조물에 대해 간단히 상기해 볼게요. 이해해야 할 세 가지 객체가 있습니다:

![FMEVal 객체](/assets/img/2024-05-27-BringYourOwnLLMEvaluationAlgorithmstoSageMakerClarifyFoundationModelEvaluations_1.png)

FMEval을 사용하면, 데이터 구성 객체에는 기존 모델 출력이 함께 제공될 수 있는데, 이는 데이터셋에 모델 출력이 없을 경우 모델 실행기(Model Runner)가 필요하지 않다는 것을 의미합니다. 마지막으로 가장 중요한 부분은 평가 알고리즘인데, 이 경우 우리가 직접 가져올 것입니다.

이 예제에서는 SageMaker Studio Notebook에서 ml.c5.large 인스턴스에서 conda_python3 커널을 사용할 것입니다. 노트북에서 사용되는 fmeval 및 기타 보조 라이브러리가 설치되어 있는지 확인하세요.

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

간단한 더미 데이터 세트를 몇 가지 무작위 항목과 함께 만들었습니다. 실제 사용 사례에서는이 데이터 세트로 교체해야 합니다.

```js
%%writefile sample_data.jsonl
{"question":"긍정적이고 행복한 한 문장을 작성해보세요."}
{"question":"부정적이고 슬픈 한 문장을 작성해보세요."}
{"question":"중립적인 문장을 작성해보세요."}
```

그런 다음 데이터 구성 객체에 모델 출력을 포함시키고자이 데이터 세트 전체에서 모델 추론을 실행합니다. 이전에 언급했듯이 데이터 구성에 모델 출력이 포함되어 있지 않은 경우 모델 실행기를 구성해야 합니다.

페이로드를 준비하는 방법을 정의하고, 모델 출력이 포함된 새 JSONLines 파일을 만들게 됩니다. 이 경우에는 Amazon Bedrock를 통해 Claude 2.0을 사용하고 있습니다.

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

```python
import json
def create_payload(text_input: str) -> str:
    # bedrock 모델에서 추론할 시 직렬화된 payload를 반환합니다

    prompt_data = f"""Human: {text_input}

    Assistant:
    """
    body = json.dumps({"prompt": prompt_data, "max_tokens_to_sample": 500})
    return body


import jsonlines
import boto3
runtime = boto3.client('bedrock-runtime')
model_id = 'anthropic.claude-v2'
accept = "application/json"
contentType = "application/json"

input_file = "sample_data.jsonl"
output_file = "sample_data_model_outputs.jsonl"

# 입력 파일에 대해 추론하고 평가를 위해 출력 파일에 작성합니다
with jsonlines.open(input_file) as input_fh, jsonlines.open(output_file, "w") as output_fh:
    for line in input_fh:
        if "question" in line:
            question = line["question"]
            #print(f"Question: {question}")
            payload = create_payload(question)
            response = runtime.invoke_model(
                body=payload, modelId=model_id, accept=accept, contentType=contentType
            )
            response_body = json.loads(response.get("body").read())
            model_output = response_body.get("completion")
            #print(f"Model output: {model_output}")
            #print("==============================")
            line["model_output"] = model_output
            output_fh.write(line)
```

이제 모델 출력이 포함된 데이터셋이 정의되었으므로, Data Config FMEval 객체를 만듭니다. 이미 데이터셋에 존재하는 입력 위치와 모델 출력을 정의합니다.

```python
import fmeval
from fmeval.data_loaders.data_config import DataConfig
from fmeval.constants import MIME_TYPE_JSONLINES

# DataConfig 객체 생성
custom_config = DataConfig(
    dataset_name="sample_data",
    dataset_uri="sample_data_model_outputs.jsonl", # 모델 출력이 있는 데이터셋 입력
    dataset_mime_type=MIME_TYPE_JSONLINES,
    model_input_location="question",
    model_output_location="model_output", # 필요한 알고리즘이 필요로 하는 대상 출력 정의, 독성에는 필요하지 않음
)
```

데이터가 준비되었으므로, Amazon Comprehend를 FMEval 내에서 사용자 정의 평가 알고리즘으로 구현할 수 있습니다.

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

# 2. 사용자 정의 평가 알고리즘 구현 및 실행

사용자 정의 알고리즘을 구축하기 위해, FMEval에서 제공된 기본 EvalAlgorithm Interface를 확장합니다:

```js
class CustomEvaluator(EvalAlgorithmInterface):

    def __init__(self, eval_algorithm_config: EvalAlgorithmConfig):
        """EvalAlgorithmConfig를 확장한 하위 클래스의 인스턴스를 초기화합니다.

        :param eval_algorithm_config: 현재 평가에 특화된 EvalAlgorithmConfig 하위 클래스의 인스턴스입니다.
        """
```

여기서 우리는 사용자 정의 평가 알고리즘을 구현하는 메서드를 정의합니다. Comprehend의 경우, 이것은 단순한 API 호출입니다. Comprehend는 미리 학습된 NLP 모델을 사용하는 고수준 AI AWS 서비스이기 때문입니다. 실제 시나리오에서 사용할 사용자 고유의 평가 알고리즘 구현으로 이 메서드를 대체해주세요.

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

```python
@staticmethod
    def comprehend_eval_algo(model_output: str) -> list:
        """Comprehend Toxicity Detection API를 사용하는 더미 평가 알고리즘입니다. 제공된 모델 출력에 대해 사용됩니다.

        Args:
            model_output (str): 실제 모델 출력물입니다. 이것은 저희가 제공한 예시에 미리 포함되어 있습니다.

        Returns:
            list: Comprehend로부터의 다양한 독성 출력들의 배열입니다.
        """

        comprehend_response = comprehend.detect_toxic_content(
            TextSegments=[
                {
                    'Text': model_output
                },
            ],
            LanguageCode='en'
        )
        output = comprehend_response['ResultList'][0]['Labels']
        return output
```

이후에는 BaseClass에서 제공된 두 개의 메서드인 evaluate()와 evaluate_sample()을 override합니다.

- evaluate(): 정의한 평가 알고리즘으로 DataConfig 객체 전체를 평가합니다.
- evaluate_sample(): 전달한 단일 데이터 포인트를 평가합니다. 독성의 경우에는 모델 출력만 필요하지만, 다른 알고리즘의 경우에는 목표 및 모델 출력이 모두 필요할 수 있습니다.

먼저 하나의 데이터 포인트에 대한 evaluate_sample()을 정의합니다:

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
def evaluate_sample(self, model_output: str) -> list:
    """단일 샘플 모델 출력 및 타겟 출력을 제공하는 메서드입니다.

    Args:
        model_output (str): 모델이 출력한 결과

    Raises:
        ValueError: 모델 또는 타겟 출력이 제공되지 않은 경우

    Returns:
        int: 평가 알고리즘의 반환 값
    """
    if not model_output:
        raise ValueError("우리의 사용자 정의 평가 알고리즘은 모델 출력이 필요합니다.")
    sample_res = CustomEvaluator.comprehend_eval_algo(model_output)
    return sample_res
```

다음으로 evaluate() 메서드를 정의하여 입력된 JSONLines 파일에 사용자 지정 평가 알고리즘을 적용합니다. 그런 다음 평가 결과를 가져와 로컬 디렉터리에 출력할 JSONLines 파일을 생성합니다.

```js
def evaluate(self, model: Optional[ModelRunner] = None, dataset_config: Optional[DataConfig] = None,
                 prompt_template: Optional[str] = None, save: bool = False, num_records: int = 100) -> str:
    """

    Args:
        model (Optional[ModelRunner], optional): JumpStart 모델 실행기, 기존 모델 출력이 이미 있는 경우는 필요하지 않습니다.
        dataset_config (Optional[DataConfig], optional): 데이터셋 위치와 관련된 데이터 구성
        prompt_template (Optional[str], optional): 모델이 예상하는 형식에 따라 프롬프트 구성 가능

    Raises:
        FileNotFoundError: 로컬 데이터 파일을 찾을 수 없는 경우
    """

    # 로컬 경로에 데이터셋이 있는지 확인하고 S3를 확인하는 논리를 구현할 수도 있음
    if dataset_config is not None:
        data_config = [(key, value) for key, value in vars(dataset_config).items()]
        data_location = data_config[1][1] # 데이터셋 경로를 가져옵니다
        if os.path.isfile(data_location):
            print(f"로컬 디렉토리에서 파일 발견: {data_location}")
        else:
            raise FileNotFoundError(f"파일 {data_location}이 현재 로컬 디렉토리에 없습니다")

    data = []
    with jsonlines.open(data_location, mode='r') as reader:
        for line in reader:
            model_output = line.get("model_output")
            eval_score = CustomEvaluator.comprehend_eval_algo(model_output)
            line["eval_score"] = eval_score
            data.append(line)

    # 출력 데이터로 Pandas DataFrame 생성
    df = pd.DataFrame(data)
    # 결과를 동일 경로에 출력 데이터 위치에 작성, 필요에 따라 사용자 지정 가능
    output_file = 'custom-eval-results.jsonl'
    print(f"평가 결과를 포함한 출력 파일 작성 중: {output_file}")
    with jsonlines.open(output_file, mode='w') as writer:
        for item in df.to_dict(orient='records'):
            writer.write(item)
    return output_file
```

평가 알고리즘이 정의되었으므로, 주요 노트북에서 알고리즘을 인스턴스화하고 두 메서드를 테스트할 수 있습니다.

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

```python
# 알고리즘을 인스턴스화합니다.
from utils.algo import CustomEvaluator
from fmeval.eval_algorithms.eval_algorithm import EvalAlgorithmInterface, EvalAlgorithmConfig
custom_evaluator = CustomEvaluator(EvalAlgorithmConfig())
```

evaluate_sample() 메서드에서 하나의 데이터 포인트를 전달하여 Comprehend 출력을 확인합니다.

```python
custom_evaluator.evaluate_sample(model_output="I am super angry and super upset right now, god that idiot.") # 부정적인 내용 죄송합니다 lol
```

![이미지](/assets/img/2024-05-27-BringYourOwnLLMEvaluationAlgorithmstoSageMakerClarifyFoundationModelEvaluations_2.png)

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

그런 다음 Data Config 객체와 Prompt 템플릿을 evaluate() 메서드에 전달합니다. 여기서 모델 출력이 없는 경우에는 Model Runner를 정의해야 합니다.

```js
custom_evaluator.evaluate(
  (dataset_config = custom_config),
  (prompt_template = "$feature"),
  (save = True)
);
```

그런 다음 출력된 JSONLines 파일을 구문 분석하여 데이터셋의 각 행에 대한 반환된 메트릭을 확인합니다. 이 경우, 각 데이터 포인트에 대해 메트릭이 매우 유사하여 모델이 비슷한 답변을 반환했음을 나타냅니다(부정적인 콘텐츠를 생성하지 않았습니다).

```js
# 결과를 시각화하기 위해 Pandas DataFrame 생성
import pandas as pd

data = []
with open("custom-eval-results.jsonl", "r") as file:
    for line in file:
        data.append(json.loads(line))
df = pd.DataFrame(data)
df
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

![이미지](/assets/img/2024-05-27-BringYourOwnLLMEvaluationAlgorithmstoSageMakerClarifyFoundationModelEvaluations_3.png)

# 3. 추가 자료 및 결론

위 링크에서 예제 코드를 찾을 수 있습니다. 본 문서가 여러분의 FM/LLM 평가 알고리즘을 FMEval과 통합하는 유용한 소개가 되었으면 좋겠습니다. 특히 이러한 모델이 프로덕션 환경으로 이동될 때 LLM의 정확도를 평가하는 것은 매우 중요한 작업이 됩니다.

FMEval을 사용하여 모델을 평가뿐만 아니라 이를 MLOps 워크플로에 원활하게 통합할 수 있습니다.

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

언제나 읽어 주셔서 감사합니다. 피드백을 자유롭게 남겨 주세요.

이 기사를 즐겼다면 LinkedIn에서 저와 연락하고 제 Medium 뉴스레터를 구독해보세요.

# 쉽게 설명하기 🚀

In Plain English 커뮤니티의 일원이 되어 주셔서 감사합니다! 떠나시기 전에:

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

- 작가를 칭찬하고 팔로우도 잊지 말아주세요! 👏
- 저희를 팔로우해주세요: X | LinkedIn | YouTube | Discord | Newsletter
- 다른 플랫폼에서도 만나보세요: Stackademic | CoFeed | Venture | Cubed
- PlainEnglish.io에서 더 많은 콘텐츠를 만나보세요.
