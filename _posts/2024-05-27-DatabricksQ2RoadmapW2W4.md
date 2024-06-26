---
title: "데이터브릭스 Q2 로드맵 W2W4"
description: ""
coverImage: "/assets/img/2024-05-27-DatabricksQ2RoadmapW2W4_0.png"
date: 2024-05-27 17:15
ogImage:
  url: /assets/img/2024-05-27-DatabricksQ2RoadmapW2W4_0.png
tag: Tech
originalTitle: "Databricks Q2 Roadmap: W2W4"
link: "https://medium.com/@matt_weingarten/databricks-q2-roadmap-w2w4-d6d186580153"
---

![이미지](/assets/img/2024-05-27-DatabricksQ2RoadmapW2W4_0.png)

# 소개

저는 어떤 이유 때문인지 원래 초대를 놓쳐서 한 주를 뒤처져 이 글을 작성했습니다만, 최신 Databricks 분기 로드맵 웨비나에서 발표된 주요 소식들을 강조하고 싶었습니다. 고객 아카데미 계정을 가지신 분들은 재생 영상을 거기서도 볼 수 있습니다.

# 유니티, 유니티, 유니티

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

<img src="/assets/img/2024-05-27-DatabricksQ2RoadmapW2W4_1.png" />

작년 분기 웨비나에서 언급했었지만, Databricks는 플랫폼 내 데이터 거버넌스의 미래로 Unity Catalog에 올인했습니다. 새 작업 영역은 기본적으로 Unity로 활성화되며, 오래된 작업 영역이 더 나은 지원을 위해 이전해야 할 때가 올 것입니다.

몇 달 동안 Unity 이주 작업을 진행해 온 사람으로서 이야기하자면, 그것은 유용하며 상기한 그래픽에 나열된 많은 이점을 제공합니다. 어떤 부분은 때로는 도전적일 수도 있지만 (아마도 과장되었다고 할 수 있을 정도로), 시간이 지남에 따라 그 과정이 더 쉬워질 것이라고 확신합니다.

Unity의 새로운 기능 측면에서 외부 파티션 메타데이터에 대한 더 나은 지원이 곧 추가될 예정이며, 현재보다 Parquet 데이터 액세스 속도를 향상시킬 것입니다. 게다가 기존 계보를 통합하고 싶은 사람들을 위해 BYOL(본인의 계보 가져오기)이라는 개념도 곧 나올 예정입니다.

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

## 대시보드/Databricks SQL

지난 몇 달 동안 Databricks 내의 대시보드는 확실히 새롭게 변화했습니다. 곧 다른 사람들과 대시보드를 공유할 수 있는 기능이 추가될 예정이며, 이를 통해 새로운 사용자를 워크스페이스에 등록하는 데 여러 채널을 통해 지나야 하는 사용자들에게 큰 도움이 될 것입니다. 또한 대시보드는 웹페이지/앱에 플러그인으로 추가될 예정이므로, 더 많은 호환성을 원하는 사용자들을 위한 것입니다.

반면에 Databricks SQL은 몇 가지 좋은 향상이 예정되어 있습니다. SQL 작성의 개념을 도입하여 쿼리와 협업 편집을 위한 Git 통합을 지원할 것입니다. SQL 스크립트는 트랜잭션 수준의 처리 및 루프와 같은 구조 지원을 제공할 것입니다.

저도 이게 아니었군요, 그러나 변형 데이터 유형이 드디어 Databricks에 추가될 예정입니다. JSON 필드 작업을 하는 사람으로써, 이것이 Snowflake에서 큰 도움이 되었고, 여기에도 도입되어 기쁩니다.

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

# 워크플로우/노트북

저는 주로 워크플로우에 Databricks를 사용하기 때문에, 이 섹션이 웨비나에서 나타날 때 놓치지 않아요. Delta Table 업데이트 후 워크플로우를 트리거하고 싶었던 적이 있다면, 테이블 트리거의 개념을 사용하면 이를 쉽게 할 수 있어요. 이제 워크플로우에서 센서의 개념이 더 많이 사용되어 추가 작업 관리 도구에 절대적으로 의존할 필요가 없게 되었다는 것은 좋은 일이에요.

개발자 경험은 DAB를 위한 새로운 VS Code 통합으로 개선될 것이에요. Databricks Connect가 Databricks와 작업을 이끄는 모든 코드 간에 더 원활한 지원을 제공하는 긍정적인 발전이었기 때문에, 자산 번들을 보다 쉽게 개발하고 테스트할 수 있는 능력은 제게 자연스러운 진전 같아요.

# 결론

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

이번에 나오는 새로운 릴리스에 대해 기대되는 것이 많네요. 개발 노력을 계획하기 위해 항상 유익한 세션을 마련해 주셔서 Databricks에게 항상 감사드립니다.
