---
title: "MySQL에서 CHAR vs VARCHAR 이해하기 포괄적인 안내"
description: ""
coverImage: "/assets/img/2024-05-20-UnderstandingCHARvsVARCHARinMySQLAComprehensiveGuide_0.png"
date: 2024-05-20 19:01
ogImage:
  url: /assets/img/2024-05-20-UnderstandingCHARvsVARCHARinMySQLAComprehensiveGuide_0.png
tag: Tech
originalTitle: "Understanding CHAR vs VARCHAR in MySQL: A Comprehensive Guide"
link: "https://medium.com/@ibrahimkarimeddin/understanding-char-vs-varchar-in-mysql-a-comprehensive-guide-7cb7e4723bb0"
---

![Understanding CHAR vs VARCHAR in MySQL: A Comprehensive Guide](/assets/img/2024-05-20-UnderstandingCHARvsVARCHARinMySQLAComprehensiveGuide_0.png)

데이터베이스를 디자인할 때, 마주치게 될 중요한 결정 중 하나는 열에 적합한 데이터 유형을 선택하는 것입니다. 텍스트 데이터의 경우, MySQL은 두 가지 주요 옵션인 CHAR과 VARCHAR을 제공합니다. 두 유형 모두 문자열을 저장하는 데 사용되지만, 저장 및 성능에 상당한 영향을 미칠 수 있는 독특한 특성을 갖고 있습니다. 이 글은 CHAR과 VARCHAR 간의 차이점을 살펴봅니다.

# 1- CHAR과 VARCHAR은 무엇인가요?

## CHAR 데이터 유형

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

CHAR은 "character"을 나타내며 고정 길이의 문자열을 저장하는 데 사용됩니다. CHAR 데이터 유형을 사용하여 열을 정의할 때 문자열의 고정된 길이를 지정합니다. 실제 문자열 길이에 관계없이 MySQL은 항상 지정된 전체 길이를 사용합니다.

```js
CREATE TABLE example (
    char_col CHAR(10)
);
```

## VARCHAR 데이터 유형

```js
CREATE TABLE example (
    varchar_col VARCHAR(10)
);
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

# 2- 저장소 차이점

## CHAR 저장

- 고정 길이: CHAR 열은 항상 지정된 길이를 사용합니다.
- 패딩: 더 짧은 문자열은 공백으로 채워집니다.
- 고정 길이 데이터에 효율적: 길이가 항상 일정한 데이터를 저장하는 데 이상적입니다 (예: 국가 코드, 고정 길이 코드).

## VARCHAR 저장

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

- 가변 길이: VARCHAR 열은 실제 문자열 길이에 필요한 만큼의 공간만 사용합니다.
- 길이 바이트: 문자열의 길이를 저장하기 위해 추가적으로 1 또는 2바이트를 사용합니다(255까지의 길이에는 1바이트, 65,535까지의 길이에는 2바이트).

## 3- 성능 고려사항

## CHAR 성능

- 빠른 액세스: CHAR 열은 고정 길이를 갖기 때문에 데이터에 빠르게 액세스할 수 있습니다.
- 간단한 패딩: 공백으로 패딩을 하면 몇 가지 작업을 간단하게 할 수 있지만, 후행 공백을 제거하기 위해 추가 처리가 필요할 수 있습니다.

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

## VARCHAR 성능

- 공간 효율성: VARCHAR 열은 가변 길이 데이터에 대해 더 공간 효율적이어서 I/O 작업이 감소하므로 성능이 향상될 수 있습니다.
- 단편화: 시간이 지남에 따라 VARCHAR 열의 업데이트는 단편화로 이어질 수 있어 성능이 저하될 수 있습니다.

가변 길이 데이터에 적합: 길이가 다양한 데이터(e.g., 이름, 주소)를 저장하는 데 적합합니다.

# 4- 일반적으로

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

'value' 값을 'mysql'로 저장하는 데는 VARCHAR(5) 또는 VARCHAR(200) 열에 저장하는 경우에 관계없이 동일한 공간이 사용됩니다. 그러나 더 짧은 VARCHAR 열을 선택하는 것이 주목할 만한 장점을 제공합니다. VARCHAR(200)과 같이 더 큰 VARCHAR 열의 경우, MySQL은 내부 값 저장을 위해 고정 크기의 메모리 청크를 할당하는 경향이 있어 메모리를 과다 소비할 위험이 있습니다. 이 메모리 팽창은 정렬이나 인메모리 임시 테이블 관련 작업 및 온 디스크 임시 테이블을 사용하는 파일 정렬 중에 특히 문제가 될 수 있습니다. 따라서 필요한 공간만 할당하는 것이 현명합니다.

또한 VARCHAR(5) 대신 CHAR(5)을 사용할 때, 인덱싱에 대한 잠재적 이점을 강조할 가치가 있습니다. CHAR 열의 고정 길이 특성은 가변 길이 VARCHAR 열보다 빠른 인덱스 조회를 용이하게 합니다. 이 장점은 인덱스 크기가 더 작고 예측 가능하여 전반적인 쿼리 성능을 향상시키는 시나리오에서 특히 두드러집니다.

다음 기사에서는 열거형에 대해 깊이 논의할 예정입니다.
