---
title: "2024년 데이타브릭스 데이터  AI 서밋에서의 소감들"
description: ""
coverImage: "/assets/img/2024-06-19-ReflectionsfromDatabricksDataAISummit2024_0.png"
date: 2024-06-19 12:18
ogImage:
  url: /assets/img/2024-06-19-ReflectionsfromDatabricksDataAISummit2024_0.png
tag: Tech
originalTitle: "Reflections from Databricks Data + AI Summit 2024"
link: "https://medium.com/@haricharanshivram/reflections-from-databricks-data-ai-summit-2024-a59a870ceabd"
---

![Reflections from Databricks Data + AI Summit 2024](/assets/img/2024-06-19-ReflectionsfromDatabricksDataAISummit2024_0.png)

# 개요:

2024년 Databricks Data + AI Summit에 참석한 것은 눈을 뜨게 하는 경험이었어요. 놀라운 주제 발표에서부터 분과 세션, 그리고 연구 내용까지 깊게 들여다보며, 이번 세미나는 오늘날의 디지턈 환경에서 높은 품질의 데이터와 AI의 변혁적인 힘을 강조했어요.

이 개인적인 관점에서, 나에게 정말 기억에 남는 이벤트로 만든 몇 가지 주요 포인트와 영감을 주는 이야기들을 탐구해보겠어요.

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

# 주요 연설:

데이터브릭스는 이러한 연설들이 대부분 자가 마케팅과 자기 홍보에 관한 것이라는 내 생각을 바꿨어.

- 나는 첫 번째 주요 연설에 마음이 없이 참석했어. 시간에 맞춰 도착했음에도 불구하고, 객석에는 16,000명 이상이 참석하여 수용 능력을 초과했어. 두 연설 모두 놀라운 발표와 데모로 이뤄진 롤러코스터 경험이었어 (나는 다음 섹션에서 조금 더 자세히 다룰 거야).
- 데이터브릭스는 간단하게 유지했고, 그들이 실제 세계와 실제 사용 사례/문제를 해결하려고 했다는 것을 깨달았어. "쉽게 얻을 수 있는 열매(l﻿ow-hanging fruit)" 라는 용어를 들어봤어? 데이터 세계의 모든 사람들 — 엔지니어, 데이터 과학자, 분석가, 제품 관리자 및 리더들 — 매일 이러한 문제에 직면해. 그들의 모든 발표와 연설은 이 주제와 일치하며, Gen AI 시대의 기회와 혁신을 강조했어.
- 대부분의 연설자는 분명히 데이터브릭스 출신이었어. 그들은 유용한 통찰, 새로운 구현 및 산업 트렌드를 공유했어. 몇 명의 다른 기술 기관 출신인 추가적인 중요 인물들로부터 이러한 트렌드가 확인되었어. 가죽 자켓을 입은 록스타 CEO를 포함한 다른 기술 기관 출신 중요 인물들도 포함돼. 내 새로운 조언, 내 3살 아이에게까지 하는 것은 이제 "고통을 바라"야!

# 기술적 발표:

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

Databricks에서는 상당수의 릴리스가 있었는데, 그중 몇 가지를 발견해서 흥미롭다고 생각했습니다. 몇 개를 소개해 드리겠습니다. 뭔가 놓친 부분이 있을 수 있으니 양해 부탁드립니다.

- 완전히 서버리스: 휴, 정말 감사합니다. 엔지니어의 업무를 훨씬 쉽게 만들어 줍니다. 오랫동안 기다려왔고 유지보수 및 비용 절감 가능성에 흥분하고 있어요!
- AI BI: 이제 새로 발표된 GA 기능이 제공하는 것을 달성하기 위해 제3자 시각화 도구를 적게 사용할 것이라 확신합니다. 추가 보너스로 제공되는 Genie 기능을 활용하여 NLP를 사용해 제품용으로 준비된 즉석 대시보드를 생성할 수 있습니다!
- 노코드 파인 튜닝: 데이터의 이 측면에 대해 전문가는 아니지만, 노코드를 사용한 모델 파인 튜닝이 게임 체인저가 될 것이라는 동료 데이터 과학자로부터 흥미로운 확언을 받았습니다!
- LakeFlow ETL: 이제 다른 도구보다 Databricks를 선호하지 않았던 엔지니어들을 위한 노코드 솔루션입니다. 일괄 처리 또는 스트리밍 데이터의 가져오기를 위해 많은 코드를 작성해야 하는가요? 이제 그럴 필요가 없어졌습니다!
- LLMOps 및 Mosaic AI 에이전트 프레임워크/평가: MlFlow의 ML 모델 프레임워크가 게임 체인저였다고 생각한다면, 이 기능도 즐길 것입니다. MLOps와 유사한 LLMOps(ML 오퍼레이션)가 공개되었을 때 Databricks는 이 분야에서 각 플랫폼보다 앞서 있다는 것을 증명했습니다.
- 데이터 거버넌스, 모니터링 및 운영 탐지: 개인적으로 이것은 모든 ETL 도구에 내장되어야 한다고 생각하는 제가 특별히 좋아하는 부분입니다. Lakehouse로 자동화된 방식으로 이를 얼마나 구현했는지에 대해 너무나 흥미롭습니다!
- Spark 4.0 발표: 현재는 이에 대해 넘어갈 거예요. Spark 3.x에서 제공되는 최신 기능을 모두 사용하지는 않겠다고 자신합니다. 그래도 제공되는 기능을 따르는 것에는 여전히 흥미를 느끼고 있습니다!
- Unity Catalog OSS: "마지막이지만 최고의" 또는 여기에서 맥락을 두기 위해 "위에 소개된 기능을 즐기고 싶다면 필수"라고 말할 수 있습니다. Unity 표를 통해 모든 주요 SQL 엔진 및 데이터 형식에 액세스할 수 있습니다. 게임 체인저! 채택은 어려울 수 있지만 Databricks는 필요한 모든 지원을 제공하기 위해 열정적인 것으로 보입니다. 그리고, 'Spark'ing CTO가 라이브 영상에서 Git 리포를 공개한 것도 그만한 멋진 일이었습니다.

LakeFlow 엔지니어링 디렉터와 쿠키 광고에 관한 마케팅 캠페인 제품 관리자의 멋진 데모에 큰 찬사를 보냅니다(실시간 디버깅이 재미있었고 잘 작동해서 기뻤습니다!). 또한 ‘X 배 더 저렴하고/빠르다’와 같은 기능 수준의 KPI가 매우 참신했습니다.

# 특별한 언급 — Compound AI Systems Workshop:

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

위의 것들은 키노트 중에 고수준 발표/데모였습니다. 우리가 다 알다시피, "악마는 세부 사항에 있다"고 합니다. 그러한 세션 중에서 더 자세히 다루는 유용한 세션들이 몇 가지 있었습니다.

- 모든 세션 또는 최고의 세션을 참석하는 것은 인간적으로 불가능하지만, 많은/대부분은 가상으로도 이용 가능할 것입니다, 이미 제공되고 있지 않다면.
- 특별히, '워크샵'이라고 명시된 세션 중 어떤 것이라도 참석하시는 것을 강력히 권장합니다. 그리고 또한 7시간 동안 진행되는 세션 중 어떤 것이라도 참석해보세요. 이것이 개인적으로 저에게 가장 깨우침을 주는 경험이었으며 제게 Gen AI 분야의 놀라운 현재 연구자들의 마음에 들어가볼 수 있는 기회를 제공해주었습니다.
- 영감을 주는 20-30개의 연구 주제 포스터가 있었습니다. 초록문을 차분히 읽거나 작가들의 이야기를 듣거나 심지어 데이터 애호가들로부터의 질문들을 관찰하는 것은 새로운 경지로 나를 인도해주었습니다.
- 또한 교육, 로봇 공학 등 분야의 선도적인 연설자들이 참석하였으며 Anthropic, DeepMind, OpenAI, Microsoft, LangChain 등 최신 트렌드의 Gen AI 기관에서 온 패널리스트들과 함께 논의가 마무리되었습니다.

# 마지막으로:

가죽자켓을 입은 CEO가 정확하게 지적했습니다 - "무엇이든 시작해보세요. 이것은 빠르게 움직이는 기차입니다. 지수적인 추세를 기다려보거나 관찰하고 있기 원치 않으실 겁니다."

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

저번 주에 이 서밋 전에, 제가 따라가며 읽어본 상대적인 AI 구현을 강화하기 위해 Snowflake가 발표한 멋진 기능들에 대해 알아보았어요. 이 글을 큰 생각과 함께 마무리 짓는군요 — "다음 단계는 너야, Snowflake. 이미!"

PS: 서밋을 통해 제게 지지를 보내준 리더, 팀 그리고 조직에게 정말 큰 감사를 표합니다!
