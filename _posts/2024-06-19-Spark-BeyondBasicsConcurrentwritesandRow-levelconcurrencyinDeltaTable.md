---
title: "스파크-비욘드 기본 델타 테이블에서 동시 쓰기와 행 수준 동시성"
description: ""
coverImage: "/assets/img/2024-06-19-Spark-BeyondBasicsConcurrentwritesandRow-levelconcurrencyinDeltaTable_0.png"
date: 2024-06-19 12:31
ogImage:
  url: /assets/img/2024-06-19-Spark-BeyondBasicsConcurrentwritesandRow-levelconcurrencyinDeltaTable_0.png
tag: Tech
originalTitle: "Spark-Beyond Basics: Concurrent writes and Row-level concurrency in Delta Table"
link: "https://medium.com/towardsdev/spark-beyond-basics-concurrent-writes-and-row-level-concurrency-in-delta-table-c49f8eab16d4"
---

![image](/assets/img/2024-06-19-Spark-BeyondBasicsConcurrentwritesandRow-levelconcurrencyinDeltaTable_0.png)

Databricks와 델타 테이블은 데이터 엔지니어의 삶을 쉽게 만들어줍니다. 😍🥰

하지만 이 엔지니어들은 어쩔 수 없이 그들에게 맞서려고 할 것입니다. 😒😒

델타 테이블이 제공하는 ACID 속성에 대해 이미 알고 있다면 좋겠지만(알지 못하신다고요? 읽어보세요), 이러한 속성은 델타 테이블에서 동시에 발생하는 쓰기 작업을 다루지 않습니다.

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

## 현재 상황

현재 델타 테이블에 병행(또는 동시) 쓰기 작업을 수행하면 "ConcurrentAppendException"이 발생합니다.

# 동시 작성이란

2명의 데이터 엔지니어인 Monica와 Ross Geller가 델타 테이블에 동시에 쓰기를 하려고 한다고 가정해보겠습니다. 🥴 (형제 맹견이야!)

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

이전에 (2024년 5월 16일 이전에 🙈), 데이터 엔지니어들은 어떻게든 동시 쓰기를 직렬 쓰기로 변환하기 위해 더러운 파이스파크 코드 💩를 작성해야 했습니다. 이러한 로직 중 하나를 아래에서 설명합니다.

로직: 델타 테이블에 대한 쓰기가 진행 중이면, 해당 쓰기가 끝날 때까지 기다린 후 현재 쓰기 프로세스를 시작합니다. 여기서 자세한 내용을 확인하세요.

따라서 델타 테이블에 동시에 쓰기할 수 있는 모든 노트북에는 위의 로직을 포함해야 합니다.

# Databricks에서 이 문제에 대해 무엇을 하였을까요?

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

그렇다구요! 🥳 Databricks Runtime 14.2 이상에서 데이터브릭은 "행 수준 동시성"(RLC)이라 불리는 새로운 기능을 소개했어요.

데이터브릭은 마치 요정 🧞‍♂️ 같죠. 무엇이든 물어보면 주어져요!

## 행 수준 동시성(RLC)을 위한 요구 사항

1. 델타 테이블의 "삭제 벡터"를 활성화해야 해요.

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
ALTER TABLE table_name SET TBLPROPERTIES ('delta.enableDeletionVectors' = true);
```

2. 델타 테이블은 파티션이 없어야 합니다.

# RLC를 사용한 동시 쓰기 작업 및 그 결과

## 삽입-삽입:

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

만약 Ross와 Monica가 동시에 삽입 작업을 수행한다면, RLC는 어떤 문제도 없이 처리할 수 있어요 😏 (충돌 없음)

## 삽입-갱신/삭제:

만약 Ross가 삽입을 수행하고 Monica가 갱신/삭제를 수행한다면, RLC는 조금 답답해집니다. 🥵

델타 테이블의 격리 수준이 "WriteSerializable"로 설정되어 있다면 (기본값), 동시 작성 작업은 어떤 문제도 발생하지 않고 수행됩니다.

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

그런데 고립 수준을 "Serializable"로 변경하면 (어떤 이유에서든 변경했을 것입니다 🤔) 충돌이 발생하고 오류가 발생할 것입니다.

## 업데이트/삭제

Ross와 Monica가 동일한 델타 테이블에서 업데이트 또는 삭제 작업을 수행하는 경우, 동일한 행에서 작업을 수행하면 오류가 발생합니다.

업데이트/삭제 작업이 다른 행에서 발생하는 경우 문제없이 작업이 수행됩니다.

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

# 요약:

- 델타 테이블에 대한 동시 행 수준 동시성(RLC)은 Databricks에서 도입되었으며 동시 쓰기 작업을 수행할 수 있게 해줍니다.
- RLC가 작동하기 위한 몇 가지 요구 사항이 있습니다.

자세한 내용은 [여기](https://link-to-more-info)에서 읽을 수 있습니다.

이 블로그를 좋아하셨다면 👏을 클릭하고 데이터 엔지니어들의 삶을 쉽게 만들기 위해 공유해주세요! 😉

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

읽어 주셔서 감사합니다! 😄
