---
title: "사용자 정의 테이블 함수 UDTF"
description: ""
coverImage: "/assets/img/2024-05-23-User-definedTableFunctionsUDTF_0.png"
date: 2024-05-23 15:41
ogImage: 
  url: /assets/img/2024-05-23-User-definedTableFunctionsUDTF_0.png
tag: Tech
originalTitle: "User-defined Table Functions (UDTF)"
link: "https://medium.com/@amandeep-singh-johar/user-defined-table-functions-udtf-f42af39cca9c"
---


```markdown
![이미지](/assets/img/2024-05-23-User-definedTableFunctionsUDTF_0.png)

Spark 3.5에서는 파이썬 사용자 정의 테이블 함수(UDTF)를 소개했습니다. 이것은 새로운 종류의 사용자 정의 함수입니다. 스칼라 함수는 각 호출에 대해 하나의 결과를 생성하는 반면, UDTF는 쿼리의 FROM 절 내에서 호출되며 전체 테이블을 출력합니다. UDTF 호출은 스칼라 식이나 완전한 입력 테이블을 나타내는 테이블 인수 중 어떤 것이든 사용할 수 있습니다.

![이미지](/assets/img/2024-05-23-User-definedTableFunctionsUDTF_1.png)

## 파이썬 UDTF 사용 이유
```

<div class="content-ad"></div>

만약 다양한 행과 열을 생성하면서 파이썬의 다양한 생태계를 활용하고 싶다면, Python UDTF가 이상적입니다.

## Python UDTF 대 Python UDF

Spark의 Python UDF는 입력으로 스칼라 값s 중 0개 이상을 받아들이고 단일 값을 반환하는 것이 설계되어 있습니다. 그에 반해, UDTF는 여러 행과 열을 반환할 수 있어 UDF의 기능을 더 확장시킬 수 있어 더 유연합니다.

## Python UDTF 대 SQL UDTF

<div class="content-ad"></div>

SQL UDTFs는 효율적이고 다재다능하지만, Python은 더 다양한 라이브러리와 도구를 제공합니다. 통계 함수나 머신 러닝 추론과 같이 고급 기술이 필요한 변환 또는 계산을 위해서는 Python UDTFs가 특히 유리합니다.

# LangChain과 함께 사용하는 UDTF

이전 예제는 기본적으로 보일 수 있지만, Python UDTFs를 LangChain과 통합하여 더 흥미로운 시나리오를 탐색해 봅시다.

```js
from langchain.chains import LLMChain
from langchain.llms import OpenAI
from langchain.prompts import PromptTemplate
from pyspark.sql.functions import lit, udtf

@udtf(returnType="keyword: string")
class KeywordsGenerator:
    """
    Generate a list of comma separated keywords about a topic using an LLM.
    Output only the keywords.
    """
    def __init__(self):
        llm = OpenAI(model_name="gpt-4", openai_api_key=<your-key>)
        prompt = PromptTemplate(
            input_variables=["topic"],
            template="generate a couple of comma separated keywords about {topic}. Output only the keywords."
        )
        self.chain = LLMChain(llm=llm, prompt=prompt)

    def eval(self, topic: str):
        response = self.chain.run(topic)
        keywords = [keyword.strip() for keyword in response.split(",")]
        for keyword in keywords:
            yield (keyword, )
```

<div class="content-ad"></div>

세부 정보:-

즐거운 학습하세요 🙂 !!!!!!