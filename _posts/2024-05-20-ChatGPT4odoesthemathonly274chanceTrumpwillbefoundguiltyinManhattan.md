---
title: "ChatGPT 4o가 수학 문제 풀기 맨해튼에서 트럼프가 유죄로 판결될 확률은 단 274 뿐이에요"
description: ""
coverImage: "/assets/img/2024-05-20-ChatGPT4odoesthemathonly274chanceTrumpwillbefoundguiltyinManhattan_0.png"
date: 2024-05-20 20:26
ogImage: 
  url: /assets/img/2024-05-20-ChatGPT4odoesthemathonly274chanceTrumpwillbefoundguiltyinManhattan_0.png
tag: Tech
originalTitle: "ChatGPT 4o does the math: only 2.74% chance Trump will be found guilty in Manhattan."
link: "https://medium.com/@rviragh/chatgpt-4o-does-the-math-only-2-74-chance-trump-will-be-found-guilty-in-manhattan-3a3844ead82c"
---


트럼프가 맨해튼에서 진행 중인 침묵금에 대한 재판에서 유죄인 것 같아요? 대부분의 맨해튼 사람들처럼 민주당 지지자라면 86% 확률로 유죄로 여기겠죠. 이 정보를 바탕으로, 이 사건을 다루는 십이 명의 맨해튼 배심원이 그를 유죄로 판단할 확률은 얼마정도일까요?

이 짧은 블로그 글은 수학, 법의학, 그리고 AI의 교차점에 관한 것입니다. 우리는 ChatGPT가 명확하게 제시된 수학 문제를 적용할 수 있는지 살펴볼 것이고, 그 방법론 사용의 타당성을 판단할 수 있는 능력을 확인할 거예요. 그 방법론을 기반으로 자체 계산을 수행하도록 지시한 후, 그 결과를 보겠습니다.

Politico 조사에서는 "공화당 지지자 중 14%만이 트럼프가 유죄라고 믿는다고 보고한 반면, 민주당 지지자 중 86%가 그 의견을 지지했다"고 발견했습니다. 이 확률 문제를 위해 맨해튼 재판에서의 십이 명 배심원이 모두 민주당 지지자이며 각각이 트럼프를 유죄로 고려할 확률이 인용된 것으로 가정해봅시다. 그럼 이들이 모두 유죄로 투표할 확률은 얼마인가요?

각 투표가 86%의 유죄 가능성을 가질 때 12 개의 유죄 투표 확률을 찾으려면 0.86을 12 제곱해야 합니다. 이는 16.36% 입니다.

<div class="content-ad"></div>

ChatGPT 4o가 프롬프트에 대해 어떻게 처리하는지 확인해 봅시다. 이 작업의 수학을 올바르게 수행했다고 보는 조건은 그 답이 16.36%이거나 0.1636 또는 유사한 숫자를 포함하는 경우입니다. 정확한 해결책이기 때문에, 우리는 그 답을 어떻게 얻었는지와 작성된 Python 코드를 살펴볼 것입니다.

# 결과

여기 저가 ChatGPT 4o에게 위 질문을 한 thread가 있습니다.

계산을 올바르게 수행하고 올바른 Python 코드를 생성했지만, 실제로 실행하지는 않았다고 합니다.

<div class="content-ad"></div>

```markdown
![Image](/assets/img/2024-05-20-ChatGPT4odoesthemathonly274chanceTrumpwillbefoundguiltyinManhattan_0.png)

따라서 실제 결과인 16.36%는 해당 답변에 나타나지 않습니다.

답변 끝에는 가능한 답변으로 "약 0.147 또는 14.7%"을 추측하며, 실제 숫자는 사실 16.36% 입니다.

약 10% 범위 내의 오차로 0.86을 12제곱한 값을 짐작하거나 직감할 수 있다면 상상해 볼만 하겠죠?
```  

<div class="content-ad"></div>

여기에서 제가 직접 실행한 코드입니다.

```
![ChatGPT 4o’s political analysis](/assets/img/2024-05-20-ChatGPT4odoesthemathonly274chanceTrumpwillbefoundguiltyinManhattan_1.png)

# ChatGPT 4o’s 정치 분석

대화를 이어가면서, 그 분석이 정확한 것을 지적합니다. 저는 모든 배심원이 민주당원일 것이라는 강력한 가정을 했다고 합니다. 또한, "사실에서는, 배심원의 결정은 집단 역학, 심리적 과정 및 서로와의 상호작용에 영향을 받을 수 있습니다. 따라서 독립 가정이 실제 재판 상황에서 유지되지 않을 수도 있습니다" 라고 언급하고 있습니다.
```

<div class="content-ad"></div>

다음에는 실제 코딩 기술을 테스트해보기로 했어요. 이렇게 물어봐 보았죠:

그룹 역학을 고려한 탄탄한 코드를 제시하며, 결과를 시뮬레이션하기 위해 몬테 카를로 방법을 사용합니다. 즉, 1만 개의 무작위 시행을 시뮬레이션하여 얼마나 많은 비유죄 결정이 나오는지 찾아냅니다. 내용의 실행이 요청되기 전까지 파이썬 코드를 실제로 실행하지는 않지만, 그 결과를 출력합니다:

![image](/assets/img/2024-05-20-ChatGPT4odoesthemathonly274chanceTrumpwillbefoundguiltyinManhattan_2.png)

![image](/assets/img/2024-05-20-ChatGPT4odoesthemathonly274chanceTrumpwillbefoundguiltyinManhattan_3.png)

<div class="content-ad"></div>

ChatGPT 4o가 머리 속에서 12차 다항식을 10% 이내로 추정했고, 그룹 역학 및 배심원 선정에 대해 모두 알고 있으며, 자체 몬테카를로 시뮬레이션을 위해 완벽한 파이썬 코드를 작성했습니다. 그리고 이 시뮬레이션은 ChatGPT 4o 자체가 고안한 역학에 기반한 만 천 번의 트라이얼에 대한 것입니다. 이러한 사실로 여러분은 그 결과를 신뢰할 수 있습니까?

지금까지 인간의 유도 없이는 이를 수행하지 않았겠지만, 그 수학적 역량은 무시할 수 없습니다. 그 시뮬레이션이 실제 세계를 정말로 모델링할 수 있는지는 앞으로 확인해봐야 할 문제입니다.

이 글에 대해 어떻게 생각하셨나요? 여러분의 코멘트를 읽어보고 싶습니다.