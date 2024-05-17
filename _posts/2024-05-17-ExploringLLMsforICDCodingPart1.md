---
title: "ICD 코딩을 위한 LLM 탐험 - 파트 1"
description: ""
coverImage: "/assets/img/2024-05-17-ExploringLLMsforICDCodingPart1_0.png"
date: 2024-05-17 19:38
ogImage: 
  url: /assets/img/2024-05-17-ExploringLLMsforICDCodingPart1_0.png
tag: Tech
originalTitle: "Exploring LLMs for ICD Coding — Part 1"
link: "https://medium.com/towards-data-science/exploring-llms-for-icd-coding-part-1-959e48b58b9e"
---


## LLM(Large Language Model)을 활용한 자동 진단 코딩 시스템 구축

임상 코딩은 흔히 쓰이는 용어는 아니지만, 대부분의 국가에서 건강 관리 체계와 상호작용하는 모든 사람에게 중대한 영향을 미칩니다. 임상 코딩은 환자 건강 기록에서 의학 정보(진단 및 수술 등)를 표준화된 숫자 또는 알파벳 코드로 번역하고 매핑하는 것을 포함합니다. 이러한 코드는 청구, 건강 관리 분석 및 환자가 적절한 치료를 받을 수 있도록 하는 데 중요합니다.

![image](/assets/img/2024-05-17-ExploringLLMsforICDCodingPart1_0.png)

임상 코딩은 일반적으로 의료 전문가가 수행합니다. 이러한 코더들은 다양한 진단과 수술을 위한 특정 코드가 포함된 복잡하고 종종 계층적인 코딩 용어를 탐색합니다. 따라서 코더들은 사용된 코딩 용어에 대한 깊은 이해와 경험을 가져야 합니다. 그러나 문서를 수동으로 코딩하는 것은 느릴 수 있고, 오류가 발생할 수 있으며, 상당한 인적 전문 지식이 필요하여 병목 현상이 발생할 수 있습니다.

<div class="content-ad"></div>

심층 학습은 임상 코딩 자동화에 중요한 역할을 할 수 있습니다. 복잡한 의료 정보를 코드로 추출하고 번역함으로써, 심층 학습 시스템은 인간 중심 시스템 내에서 가치 있는 도구로 작용할 수 있습니다. 이러한 시스템은 코더들을 지원하여 대량의 데이터를 신속하게 처리하고 정확성을 향상시킬 수 있습니다. 이는 행정 업무를 간소화하고 청구 오류를 줄이며 환자 치료 결과를 향상시킬 수 있습니다.

이 첫 번째 부분에서는 ICD 코딩이 무엇인지, 자동 코딩 시스템이 효과적으로 극복해야 하는 다양한 도전에 대해 설명합니다. 또한 대용량 언어 모델(LLM)이 이러한 문제를 극복하는 데 효과적으로 활용할 수 있는 방법을 분석하고, 최근 논문에서 LLM을 효과적으로 활용한 알고리즘을 적용하여 ICD 코딩에 성공적으로 적용하는 방법을 설명합니다.

## 목차:

- ICD 코딩이란 무엇인가?
- 자동 ICD 코딩의 도전 요소는 무엇인가?
- LLM이 자동 ICD 코딩에 어떻게 도움이 될까?
- "Off-the-shelf 대용량 언어 모델을 이용한 자동 임상 코딩" 논문 탐색
- 논문에 설명된 기법 구현
- 결론
- 참고문헌

<div class="content-ad"></div>

# ICD 코딩이란 무엇인가요?

국제질병분류(ICD) 코딩은 세계보건기구에서 개발 및 유지보수하는 임상 용어 시스템입니다 [1]. 대부분의 국가에서 환자의 모든 진단, 증상 및 절차를 범주화하고 코딩하는 데 사용됩니다.

환자의 진단과 의료 절차를 기록하는 의료 기록은 ICD 코딩에 매우 중요합니다. ICD 용어는 대략 75,000가지 다른 코드로 구성된 트리 구조를 특징으로 하여 방대한 정보를 효율적으로 정리합니다. 이러한 문서를 정확하게 코딩하는 것이 중요합니다. 정확한 코딩은 적절한 청구를 보장하며 의료 분석 품질에 영향을 미치며 환자 치료 결과, 보상 및 의료 효율성에 직접적으로 영향을 줍니다.

# 자동 ICD 코딩에서 어떤 도전들이 있을까요?

<div class="content-ad"></div>

ICD 코딩은 효과적으로 작동하기 위해 자동화된 시스템이 극복해야 할 여러 가지 도전이 있습니다.

## ICD 코딩의 레이블 다양성:

중요한 도전 중 하나는 레이블의 광범위한 출력 공간입니다. ICD 코드는 많고 각 코드는 미세한 세부 사항에서 차이가 있을 수 있습니다. 예를 들어, 오른손에 영향을 주는 상태와 왼손에 영향을 주는 상태는 서로 다른 코드를 갖게 됩니다. 또한 의료 기록에서 드물게 나타나는 희귀 코드의 긴 꼬리가 존재하여, 이러한 코드를 학습하고 정확하게 예측하기 어렵게 만들 수 있습니다.

## 새로운 ICD 코드에 대한 적응:

<div class="content-ad"></div>

번거로우시겠지만, 테이블 태그를 마크다운 형식으로 변경해드릴게요!

전통적인 데이터셋인 MIMIC-III [2] 같은 경우는 종합적이지만, 종종 ICD 코드의 범위를 훈련 말뭉치에 포함된 코드로 제한합니다. 이 제한은 의료 기록에서 ICD 코드로의 딥러닝 모델을 다중 레이블 분류 문제로 처리하는 데 새로운 코드가 도입된 경우 모형 훈련 이후에 어려움을 겪을 수 있음을 의미합니다. 이는 재훈련이 필요하고 잠재적으로 어려울 수 있게 만듭니다.

## 정보 추출 및 문맥 활용:

또 다른 주요 과제는 의료 기록에서 정보를 정확하게 추출하고 문맥에 맞게 처리하는 것입니다. ICD 코딩은 근본적으로 정보 검색 문제로, 의료 기록에서 진단을 식별하는 것 뿐만 아니라 이러한 진단을 해당 ICD 코드로 올바르게 매핑하는 데 필요한 모든 보완 정보를 포착해야 합니다. 따라서 자동화된 시스템이 의료 기록에서 여러 진단을 추출하고 적절히 문맥화하여 ICD 코드로 정확하게 매핑되도록 하는 것이 중요합니다.

![이미지](/assets/img/2024-05-17-ExploringLLMsforICDCodingPart1_1.png)

<div class="content-ad"></div>

"Contextualization"란 여기서 무엇을 의미할까요? 의학 노트를 다룰 때 진단을 맥락에 맞게 처리하는 것은 관련 세부사항과 관련된 정보 — 예를 들어 영향을 받는 신체 부위 및 질환의 증상 — 를 연결하여 진단을 완전히 특성화하는 것을 의미합니다. 일반적으로 이 작업은 관계 추출로 참조됩니다.

# 대규모 언어 모델(LLMs)이 자동 ICD 코딩에 어떻게 도움이 되나요?

자동 ICD 코딩의 과제를 다룰 때, 대규모 언어 모델 (LLMs)은 이러한 문제에 대처하는 데 적합하며, 특히 새로운 레이블에 대한 적응성과 복잡한 정보 추출 작업을 관리하는 능력으로 인해 잘 역할을 합니다. 그러나 여기서의 포인트는 LLMs가 자동 ICD 코딩에 대한 최상의 해결책이거나 이러한 문제를 해결할 수 있는 유일한 해결책인 것을 주장하는 것이 아니라, 자동 ICD 코딩 시스템이 극복해야 하는 주요 과제들을 설정함으로써 LLMs의 능력을 최대한 활용하여 이를 해결할 수 있는지를 분석하는 것입니다.

## 새로운 및 드문 ICD 코드에 대한 적응:

<div class="content-ad"></div>

LLMs는 견고한 제로샷 및 퓨샷 학습 능력을 보여주며, 적은 예시와 프롬프트에서 제공된 지침으로 새로운 작업에 적응할 수 있습니다. 검색 증강 생성 (RAG)은 미세 조정 없이도 LLM이 새로운 작업에 적응하기 위해 더 많은 맥락 정보에 접근할 수 있는 패러다임입니다. 이는 특히 기존의 훈련 데이터셋에서 자주 나타나지 않을 수 있는 새로운 및/또는 희귀한 ICD 코드에 LLM을 조정하는 데 유용합니다. 이를 단지 몇 가지 설명 또는 사용 사례로부터 합니다.

## 맥락 정보:

LLMs는 임상 분야에서의 제로샷 관계 추출에서 효과적으로 확인되었습니다. 제로샷 관계 추출은 LLM이 해당 관계에 대해 이전에 구체적인 훈련을 받지 않고 텍스트에서 관계를 식별하고 분류할 수 있도록 합니다. 이를 통해 의료 코딩에서의 진단을 더 잘 맥락화하여 더 정확한 ICD 코드를 가져올 수 있습니다.

# "Automated clinical coding using off-the-shelf large language models" 논문 탐색하기:

<div class="content-ad"></div>

LLM을 ICD 코딩에 적용한 최근 연구를 탐색하다가, 특정한 세부 조정 없이 LLM을 활용한 ICD 코딩에 관한 매우 흥미로운 논문을 발견했습니다. 저자들은 LLM을 활용한 ICD 코딩을 위해 LLM-지도된 트리 탐색이라는 방법을 개발했습니다 [5].

## 이 방법은 어떻게 작동하나요?

ICD 용어는 계층적인 트리 구조입니다. 각 ICD 코드는 이 계층적 구조 내에 존재하며, 부모 코드는 더 일반적인 상태를 다루고, 자식 코드는 특정 질병을 상세히 설명합니다. ICD 트리를 탐색하면 더 구체적이고 세분화된 진단 코드로 이어집니다.

LLM-지도된 트리 탐색에서는 탐색이 루트에서 시작되고 LLM을 사용하여 탐색할 가지를 선택하며, 모든 경로가 고갈될 때까지 반복적으로 계속합니다. 실제로 이 과정은 트리의 임의의 수준에서 모든 코드의 설명과 의료 노트를 LLM에 프롬프트로 제공하고, 해당 의료 노트에 대한 관련 코드를 식별하도록 요청하는 것으로 구현됩니다. 각 인스턴스에서 LLM에 의해 선택된 코드는 더 구체적으로 탐색되고 조사됩니다. 이 방법을 사용하면 가장 관련성이 높은 ICD 코드가 식별되며, 이후 임상 노트에 대한 예측 레이블로 할당됩니다.

<div class="content-ad"></div>

예시를 통해 이를 명확히 해보겠습니다. ICD 코드 1과 ICD 코드 2라는 두 개의 루트 노드를 가진 트리를 상상해보세요. 각 노드는 코드를 특성화하는 평문 설명을 가지고 있습니다. 초기 단계에서 LLM에게 의학 노트와 코드 설명이 제공되고 의학 노트와 관련된 코드를 식별하도록 요청됩니다.

이 시나리오에서 LLM은 의학 노트와 관련이 있는 것으로 판단된 ICD 코드 1과 ICD 코드 2를 식별합니다. 알고리즘은 각 코드의 자식 노드를 조사합니다. 각 부모 코드는 더 구체적인 ICD 코드를 나타내는 두 개의 자식 노드를 가지고 있습니다. ICD 코드 1부터 시작하여, LLM은 ICD 코드 1.1과 ICD 코드 1.2의 설명을 사용하여 의학 노트를 기반으로 관련 코드를 결정합니다. LLM은 ICD 코드 1.1이 관련이 있다고 결론 내리고, ICD 코드 1.2는 관련이 없다고 판단합니다. ICD 코드 1.1에는 더 이상의 자식 노드가 없으므로, 알고리즘은 할당 가능한 코드인지 확인하고 문서에 할당합니다. 그 다음 알고리즘은 ICD 코드 2의 자식 노드를 평가합니다. LLM을 호출하여, ICD 코드 2.1이 관련이 있는 것으로 판단합니다. 이것은 간단화된 예시이며, 실제로는 ICD 트리는 광범위하고 깊기 때문에 알고리즘은 각 관련된 노드의 자식을 탐색하거나 트리의 끝에 도달하거나 유효한 탐색을 소진할 때까지 계속됩니다.

## 핵심

- 이 방법은 LLM의 세밀한 조정이 필요하지 않습니다. 대신, 제공된 설명을 기반으로 LLM의 의료 노트를 상황에 맞게 이해하고 관련 ICD 코드를 동적으로 식별할 수 있는 능력을 활용합니다.
- 더 나아가, 본 논문은 LLM이 프롬프트에 관련 정보가 주어질 때 대규모 출력 공간에 효과적으로 적응할 수 있으며, macro-average 지표 측면에서 드문 코드에서 PLM-ICD [6]를 앞지를 수 있다는 것을 보여줍니다.
- 이 기술은 또한 파라메트릭 지식에 기초하여 의학 노트의 ICD 코드를 예측하도록 LLM에 직접 요청하는 기준선을 능가합니다. 이는 LLM을 임상 코딩 작업을 해결하기 위한 도구나 외부 지식과 통합하는 잠재력을 강조합니다.

<div class="content-ad"></div>

## 단점

- 알고리즘은 트리의 각 수준에서 LLM을 호출합니다. 이로 인해 트리를 탐색하는 동안 LLM 호출 횟수가 많아지며, ICD 트리의 광범위함이 이에 더해집니다. 이는 단일 문서를 처리하는 데 높은 대기 시간과 비용으로 이어집니다.
- 저자들이 논문에서 언급한 바와 같이, 관련 있는 코드를 정확하게 예측하려면 LLM이 모든 수준에서 부모 노드를 올바르게 식별해야 합니다. 한 수준에서 실수가 발생하더라도, LLM은 최종 관련 코드에 도달할 수 없게 됩니다.
- 저자들은 MIMIC-III와 같은 데이터셋을 사용하여 메소드를 평가할 수 없었습니다. 외부 서비스로의 데이터 전송을 금지하는 제한 사항으로 인하여 OpenAI의 GPT 엔드포인트와 같은 외부 서비스로의 데이터 전송이 불가능했습니다. 대신, 저자들은 CodiEsp 데이터셋 [7,8]의 테스트 세트를 사용하여 해당 방법을 평가했습니다. 해당 데이터셋은 250개의 의학 노트를 포함하고 있습니다. 이 데이터셋의 크기가 작은 것은 해당 방법이 대규모 임상 데이터셋에서의 성능을 아직 입증하지 못했음을 시사합니다.

# 논문에서 설명한 기술 구현하기

이 기술을 구현하여 그 작동 방식을 더 잘 이해해 봅시다. 논문에서 언급했듯이, 해당 논문은 평가를 위해 CodiEsp 테스트 세트를 사용합니다. 이 데이터셋은 스페인어 의학 노트와 이에 대응하는 ICD 코드로 구성되어 있습니다. 데이터셋에는 영어로 번역된 버전도 포함되어 있지만, 저자들은 스페인어 의학 노트를 GPT-3.5를 사용하여 영어로 번역하였으며, 이를 통해 사전 번역된 버전을 사용하는 것보다 성능이 약간 향상되었다고 주장했습니다. 이 기능을 복제하고 노트를 영어로 번역해 봅시다.

<div class="content-ad"></div>

```js
def construct_translation_prompt(medical_note):
    """
    Construct a prompt template for translating Spanish medical notes to English.
    
    Args:
        medical_note (str): The medical case note.
        
    Returns:
        str: A structured template ready to be used as input for a language model.
    """    
    translation_prompt = """You are an expert Spanish-to-English translator. You are provided with a clinical note written in Spanish.
You must translate the note into English. You must ensure that you properly translate the medical and technical terms from Spanish to English without any mistakes.
Spanish Medical Note:
{medical_note}"""
    
    return translation_prompt.format(medical_note = medical_note)
```

Now that we have the evaluation corpus ready, let’s implement the core logic for the tree-search algorithm. We define the functionality in get_icd_codes, which accepts the medical note to process, the model name, and the temperature setting. The model name must be either “gpt-3.5-turbo-0613” for GPT-3.5 or “meta-llama/Llama-2–70b-chat-hf” for Llama-2 70B Chat. This specification determines the LLM that the tree-search algorithm will invoke during its processing.

Evaluating GPT-4 is possible using the same code-base by providing the appropriate model name, but we choose to skip it as it is quite time-consuming.

```js
def get_icd_codes(medical_note, model_name="gpt-3.5-turbo-0613", temperature=0.0):
    """
    Identifies relevant ICD-10 codes for a given medical note by querying a language model.

    This function implements the tree-search algorithm for ICD coding described in https://openreview.net/forum?id=mqnR8rGWkn.

    Args:
        medical_note (str): The medical note for which ICD-10 codes are to be identified.
        model_name (str): The identifier for the language model used in the API (default is 'gpt-3.5-turbo-0613').

    Returns:
        list of str: A list of confirmed ICD-10 codes that are relevant to the medical note.
    """
    assigned_codes = []
    candidate_codes = [x.name for x in CHAPTER_LIST]
    parent_codes = []
    prompt_count = 0

    while prompt_count < 50:
        code_descriptions = {}
        for x in candidate_codes:
            description, code = get_name_and_description(x, model_name)
            code_descriptions[description] = code

        prompt = build_zero_shot_prompt(medical_note, list(code_descriptions.keys()), model_name=model_name)
        lm_response = get_response(prompt, model_name, temperature=temperature, max_tokens=500)
        predicted_codes = parse_outputs(lm_response, code_descriptions, model_name=model_name)

        for code in predicted_codes:
            if cm.is_leaf(code["code"]):
                assigned_codes.append(code["code"])
            else:
                parent_codes.append(code)

        if len(parent_codes) > 0:
            parent_code = parent_codes.pop(0)
            candidate_codes = cm.get_children(parent_code["code"])
        else:
            break

        prompt_count += 1

    return assigned_codes
```

<div class="content-ad"></div>

우리는 논문과 비슷하게, ICD-10 트리에 액세스하는 simple_icd_10_cm 라이브러리를 사용합니다. 이를 통해 트리를 탐색하고, 각 코드에 대한 설명에 액세스하며 유효한 코드를 식별할 수 있습니다. 먼저, 트리의 첫 번째 수준에서 노드를 가져옵니다.

```js
import simple_icd_10_cm as cm

def get_name_and_description(code, model_name):
    """
    ICD-10 코드의 이름과 설명을 검색합니다.
    
    Args:
        code (str): ICD-10 코드.
        
    Returns:
        tuple: 형식화된 설명과 코드의 이름이 포함된 튜플을 반환합니다.
    """
    full_data = cm.get_full_data(code).split("\n")
    return format_code_descriptions(full_data[3], model_name), full_data[1]
```

루프 내부에서 각 노드에 해당하는 설명을 얻습니다. 이제 의료 노트와 코드 설명을 기반으로 LLM을 위한 프롬프트를 작성해야 합니다. 우리는 논문에서 제공된 세부 정보를 기반으로 GPT-3.5와 Llama-2용 프롬프트를 작성합니다.

```js
prompt_template_dict = {"gpt-3.5-turbo-0613" : """[사례 노트]:
{note}
[예시]:
<예시 프롬프트>
위식도 역류병
장전위

<응답>
위식도 역류병: 예, 환자에게 오메프라졸 처방함.
장전위: 아니오.

[작업]:
다음 ICD-10 코드 설명 각각을 고려하고 사례 노트에 관련 언급이 있는지 평가하십시오.
예시의 형식을 정확히 따르십시오.

{code_descriptions}""",

"meta-llama/Llama-2-70b-chat-hf": """[사례 노트]:
{note}

[예시]:
<코드 설명>
* 위식도 역류병
* 장전위
* 급성비인두염 [감기]
</코드 설명>

<응답>
* 위식도 역류병: 예, 환자에게 오메프라졸 처방함.
* 장전위: 아니오.
* 급성비인두염 [감기]: 아니오.
</응답>

[작업]:
예시 응답 형식을 정확히 따르십시오. (예) 판단하기 전에 전체 설명과 (예|아니오) 판단을 입력한 후에 새 줄을 추가하십시오. 
다음 ICD-10 코드 설명을 고려하고 사례 노트에서 관련 언급이 있는지 확인하십시오.

{code_descriptions}"""
}
```

<div class="content-ad"></div>

의료 기록과 코드 설명에 기반한 프롬프트를 지금 만들어 보겠습니다. 프롬프트 및 코딩에서 우리에게 이점은 GPT-3.5 및 Llama 2 모두와 상호 작용하기 위해 동일한 openai 라이브러리를 사용할 수 있다는 것입니다. 단, Llama-2가 deepinfra를 통해 배포되어야 합니다. deepinfra는 LLM에 요청을 보내기 위한 openai 형식도 지원합니다.

```js
def construct_prompt_template(case_note, code_descriptions, model_name):
    """
    주어진 케이스 노트와 ICD-10 코드 설명을 평가하는 프롬프트 템플릿 구성
    
    Args:
        case_note (str): 의료 케이스 노트
        code_descriptions (str): ICD-10 코드 설명을 단일 문자열로 포맷팅
        
    Returns:
        str: 언어 모델에 입력으로 사용할 준비된 구조화된 템플릿
    """
    template = prompt_template_dict[model_name]

    return template.format(note=case_note, code_descriptions=code_descriptions)

def build_zero_shot_prompt(input_note, descriptions, model_name, system_prompt=""):
    """
    시스템 및 사용자 역할에 대한 제로샷 분류용 프롬프트 빌드
    
    Args:
        input_note (str): 입력 노트 또는 질의
        descriptions (list of str): ICD-10 코드 설명 리스트
        system_prompt (str): 선택적 초기 시스템 프롬프트 또는 지시
    
    Returns:
        list of dict: 각 메시지의 역할 및 내용을 정의하는 구조화된 사전 목록
    """
    if model_name == "meta-llama/Llama-2-70b-chat-hf":
        code_descriptions = "\n".join(["* " + x for x in descriptions])
    else:
        code_descriptions = "\n".join(descriptions)

    input_prompt = construct_prompt_template(input_note, code_descriptions, model_name)
    return [{"role": "system", "content": system_prompt}, {"role": "user", "content": input_prompt}]
```

프롬프트를 구성한 후, 이제 LLM을 호출하여 응답을 받겠습니다:

```js
def get_response(messages, model_name, temperature=0.0, max_tokens=500):
    """
    채팅-완성 API를 통해 지정된 모델로부터 응답을 획득
    
    Args:
        messages (list of dict): API 입력용 구조화된 메시지 목록
        model_name (str): 쿼리할 모델의 식별자
        temperature (float): 응답의 무작위성을 제어하는 값, 0이면 결정론적
        max_tokens (int): 응답의 토큰 수 제한
        
    Returns:
        str: 모델에서의 응답 메시지 내용
    """
    response = client.chat.completions.create(
        model=model_name,
        messages=messages,
        temperature=temperature,
        max_tokens=max_tokens
    )
    return response.choices[0].message.content
```

<div class="content-ad"></div>

좋아요, 우리가 출력물을 얻었어요! 이 응답으로부터, 이제 LLM이 추가적인 탐색을 위해 관련있는 노드들과 거부한 노드들을 식별하기 위해 각 코드 설명을 구문 분석합니다. 우리는 출력 응답을 새 줄로 나누고 각 응답을 분할하여 LLM의 각 코드 설명에 대한 예측을 식별합니다.

```js
def remove_noisy_prefix(text):
    # 문자 또는 숫자가 뒤따르고 점과 선택적 공백으로 시작하는 문자열의 제일 앞에 있는 숫자나 문자를 제거합니다.
    cleaned_text = text.replace("* ", "").strip()
    cleaned_text = re.sub(r"^\s*\w+\.\s*", "", cleaned_text)
    return cleaned_text.strip()

def parse_outputs(output, code_description_map, model_name):
    """
    모델 출력을 구문 분석하여 주어진 설명 매핑에 따른 ICD-10 코드를 확인합니다.
    
    Args:
        output (str): 확인을 포함하는 모델 출력입니다.
        code_description_map (dict): 설명과 ICD-10 코드의 매핑입니다.
        
    Returns:
        list of dict: 확인된 코드 및 해당 설명의 목록입니다.
    """
    confirmed_codes = []
    split_outputs = [x for x in output.split("\n") if x]
    for item in split_outputs:
        try:                
            code_description, confirmation = item.split(":", 1)
            if model_name == "meta-llama/Llama-2-70b-chat-hf":
                code_description = remove_noisy_prefix(code_description)

            if confirmation.lower().strip().startswith("yes"):
                try:
                    code = code_description_map[code_description]
                    confirmed_codes.append({"code": code, "description": code_description})
                except Exception as e:
                    print(str(e) + " Here")
                    continue
        except:
            continue
    return confirmed_codes
```

이제 루프의 나머지를 살펴봅시다. 지금까지 우리는 프롬프트를 구성했고, LLM으로부터 응답을 받았으며, 출력을 구문 분석하여 LLM에 의해 관련이 있다고 판단된 코드를 식별했습니다.

```js
while prompt_count < 50:
    code_descriptions = {}
    for x in candidate_codes:
        description, code = get_name_and_description(x, model_name)
        code_descriptions[description] = code

    prompt = build_zero_shot_prompt(medical_note, list(code_descriptions.keys()), model_name=model_name)
    lm_response = get_response(prompt, model_name, temperature=temperature, max_tokens=500)
    predicted_codes = parse_outputs(lm_response, code_descriptions, model_name=model_name)

    for code in predicted_codes:
        if cm.is_leaf(code["code"]):
            assigned_codes.append(code["code"])
        else:
            parent_codes.append(code)

    if len(parent_codes) > 0:
        parent_code = parent_codes.pop(0)
        candidate_codes = cm.get_children(parent_code["code"])
    else:
        break

    prompt_count += 1
```

<div class="content-ad"></div>

이제 예측된 코드를 반복하며 각 코드가 "leaf" 코드인지 확인합니다. 이는 코드가 유효하고 할당 가능한 ICD 코드임을 보증하는 것입니다. 예측된 코드가 유효하면 LLM이 그 의료 노트에 대한 예측으로 간주합니다. 유효하지 않으면 상위 코드에 추가하여 ICD 트리를 더 탐색하기 위해 자식 노드를 얻습니다. 더 이상 탐색할 상위 코드가 없을 경우 루프를 탈출합니다. 

이론적으로 의료 노트 당 LLM 호출 수는 임의로 높을 수 있으며, 알고리즘이 많은 노드를 탐색하는 경우 지연 시간이 증가할 수 있습니다. 저자는 의료 노트 당 최대 50회 프롬프트/LLM 호출로 처리를 종료하는 최대 수를 시행했습니다. 이 한계는 우리가 구현에서도 채택합니다.

## 결과

이제 GPT-3.5와 Llama-2를 LLM으로 사용하여 트리 탐색 알고리즘의 결과를 평가할 수 있습니다. 우리는 알고리즘의 성능을 마이크로-평균 및 매크로-평균 정밀도, 재현율 및 F1 점수를 통해 평가합니다.

<div class="content-ad"></div>

![ExploringLLMsforICDCodingPart1_2](/assets/img/2024-05-17-ExploringLLMsforICDCodingPart1_2.png)

구현 결과는 논문에 보고된 점수와 대략적으로 일치하지만 주목할 만한 차이점이 있습니다.

- 이 구현에서 GPT-3.5의 마이크로 평균 측정 지표는 보고된 값보다 약간 뛰어나지만, 매크로 평균 측정 지표는 보고된 값보다 조금 부족합니다.
- 마찬가지로 Llama-70B의 마이크로 평균 측정 지표는 보고된 값과 일치하거나 조금 뛰어나지만, 매크로 평균 측정 지표는 보고된 값보다 낮습니다.

앞서 언급했듯이, 이 구현은 몇 가지 미세한 차이점을 가지고 있어 최종 성능에 영향을 미칩니다. 이 구현이 원본 논문과 어떻게 다른지에 대한 보다 자세한 내용은 링크된 저장소를 참조해 주세요.

<div class="content-ad"></div>

# 결론

이 방법을 이해하고 구현하는 것은 여러 측면에서 나에게 매우 유익했습니다. 이를 통해 대규모 언어 모델(LLMs)의 강점과 약점에 대해 보다 세밀하게 이해할 수 있었고 임상 코딩 사례에서 그것을 구현할 수 있었습니다. 구체적으로, 코드에 관련된 중요한 정보에 동적으로 접근할 수 있는 경우 LLMs는 임상 문맥을 효과적으로 이해하고 관련 코드를 정확하게 식별할 수 있다는 것이 분명해졌습니다.

LLMs를 임상 코딩을 위한 대리자로 활용하는 것이 성능을 더욱 향상시킬 수 있는지 탐구하는 것이 흥미로울 것입니다. 생명공학 및 임상 텍스트에 대한 외부 지식 소스가 논문이나 지식 그래프 형태로 풍부하게 제공되는 상황에서 LLM 대리자는 의료 문서를 보다 세밀한 단위로 분석하는 워크플로에 활용될 수 있습니다. 또한 필요한 경우 외부 지식을 참고하여 최종 코드에 도달할 수 있도록 동적으로 도구를 활용할 수도 있습니다.

## 감사의 글

<div class="content-ad"></div>

이 방법을 평가하는 데 도움을 준 이 논문의 주 저자 Joseph에게 큰 감사를 표합니다!

- 참고 자료:

[1] https://www.who.int/standards/classifications/classification-of-diseases

[2] Johnson, A. E., Pollard, T. J., Shen, L., Lehman, L. W. H., Feng, M., Ghassemi, M., … & Mark, R. G. (2016). MIMIC-III, a freely accessible critical care database Sci. Data, 3(1), 1.

<div class="content-ad"></div>

[3] Agrawal, M., Hegselmann, S., Lang, H., Kim, Y., & Sontag, D. (2022). 대형 언어 모델은 소수의 적은 데이터로도 임상 정보를 추출합니다. arXiv 사전 인쇄 arXiv:2205.12689.

[4] Zhou, H., Li, M., Xiao, Y., Yang, H., & Zhang, R. (2023). 임상 관계 추출을 위한 LLM Instruction-Example Adaptive Prompting (LEAP) 프레임워크. medRxiv : 의학과학 사전 인쇄 서버, 2023.12.15.23300059. https://doi.org/10.1101/2023.12.15.23300059

[5] Boyle, J. S., Kascenas, A., Lok, P., Liakata, M., & O’Neil, A. Q. (2023, 10월). 상업용 대형 언어 모델을 사용한 자동 임상 코딩. NeurIPS 2023에서 Deep Generative Models for Health Workshop 발표.

[6] Huang, C. W., Tsai, S. C., & Chen, Y. N. (2022). 사전 훈련된 언어 모델로 자동 ICD 코딩하기: PLM-ICD. arXiv 사전 인쇄 arXiv:2207.05289.

<div class="content-ad"></div>

Miranda-Escalada, A., Gonzalez-Agirre, A., Armengol-Estapé, J., & Krallinger, M. (2020). CLEF (Working Notes), 2020에서 CodiEsp Track의 비영어 임상 사례에 대한 주석, 가이드라인 및 솔루션에 대한 개요.

Miranda-Escalada, A., Gonzalez-Agirre, A., & Krallinger, M. (2020). CodiEsp corpus: ICD10 (CIE10)로 코드화된 골드 표준 스페인어 임상 사례 - eHealth CLEF2020 (1.4) [데이터 세트]. Zenodo. https://doi.org/10.5281/zenodo.3837305 (CC BY 4.0)