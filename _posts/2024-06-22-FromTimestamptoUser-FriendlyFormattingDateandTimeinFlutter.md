---
title: "Flutter에서 시간표시를 사용자 친화적으로 포맷하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-FromTimestamptoUser-FriendlyFormattingDateandTimeinFlutter_0.png"
date: 2024-06-22 22:45
ogImage:
  url: /assets/img/2024-06-22-FromTimestamptoUser-FriendlyFormattingDateandTimeinFlutter_0.png
tag: Tech
originalTitle: "From Timestamp to User-Friendly: Formatting Date and Time in Flutter"
link: "https://medium.com/@AryanBeast/format-date-and-time-in-flutter-1a8edfab1054"
---

Flutter 앱을 개발 중이라면 날짜를 어떤 방식으로든 표시해야 할 가능성이 높습니다. 특정 순서로 날짜를 표시하려면 올바른 장소에 오신 것을 환영합니다. 이 기사에서는 Flutter에서 어떻게 날짜와 시간을 어떤 형식으로든 포맷할 수 있는지 배워보겠습니다.

우선 기본부터 시작해 봅시다. 현재 날짜와 시간을 가져오는 방법을 알아보겠습니다.

```js
DateTime currentDateTime = DateTime.now();
debugPrint(currentDateTime.toString());
```

위 코드는 이와 같은 결과를 제공할 것입니다.

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

![image](/assets/img/2024-06-22-FromTimestamptoUser-FriendlyFormattingDateandTimeinFlutter_0.png)

안 예쁘게 보이네요, 그래서 여기 계신 거죠. 이것을 더 읽기 쉬운 방식으로 서식을 지정하려고 합니다. currentDateTime에는 오늘의 날짜와 현재 시간이 포함됩니다. 이를 원하는 대로 문자열 형식으로 원격 또는 로컬 데이터베이스에 저장하거나 지금 표시하려면 직접 조작할 수 있습니다.

참고: - 다른 곳(데이터베이스)에서 날짜를 가져오는 경우 문자열 형식으로 저장되었을 가능성이 높으므로 이를 DateTime 형식으로 변환하는 방법입니다.

```js
DateTime current = DateTime.parse(variableName);
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

중요한 부분이 왔어요! 포맷을 변경하는 방법을 설명하겠어요. 두 가지 방법을 설명할 거에요. 첫 번째 방법은 간단하고 쉽게 사용할 수 있어요. 두 번째 방법은 사용자 정의 형식이지만 쉬워요 :-).

![image](/assets/img/2024-06-22-FromTimestamptoUser-FriendlyFormattingDateandTimeinFlutter_1.png)

만약 이 중 하나를 원한다면 아주 잘 알겠어요. 그렇지 않다면 더 다양한 경우에 대한 하나의 해결책이 더 있어요. 이 해결책은 이 글의 두 번째 부분에서 확인할 수 있어요.

- 표준 형식 사용
  먼저, public.yaml 파일에 intl 라이브러리를 추가해야 합니다.

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

```yaml
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.2
  intl: ^0.17.0
```

이 라이브러리의 최신 버전을 intl 라이브러리에서 확인할 수 있습니다.

```js
DateTime now = DateTime.now();
// String 형식의 변수를 dateTime으로 사용하는 경우 DateTime dateTime = DateTime.parse(variableName);
String formattedDate = DateFormat.yMMMEd().format(now);
print(formattedDate);
```

Tue, Jan 25, 2022

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

Cheatsheet:-

![Screenshot](/assets/img/2024-06-22-FromTimestamptoUser-FriendlyFormattingDateandTimeinFlutter_2.png)

2. If you want a custom pattern

Sometimes we need to format a date in a specific format that may not be present in the above cheatsheet. What should we do? Give up!! Never! Is that why you started your journey, to give up after coming so close to completing your task?

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

여기 답변을 찾을 수 있는 기사입니다. 그래서 계속해서 진행하세요!!

```js
DateTime now = DateTime.now();
formattedDate = DateFormat('EEEE, MMM d, yyyy').format(now);
print(formattedDate);
```

```js
Tuesday, Jan 25, 2022
```

![이미지](/assets/img/2024-06-22-FromTimestamptoUser-FriendlyFormattingDateandTimeinFlutter_3.png)

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

더 잘 이해하실 수 있도록 몇 가지 포인트를 설명해 드릴게요.

EEEE -`은 요일의 전체 이름을 나타냅니다 (예: 월요일, 화요일..)

E -`은 요일의 약어를 나타냅니다 (예: 월, 화...)

MM -`는 월의 숫자를 의미합니다

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

MMM - 월 이름 약어

MMMM - 전체 월 이름

yy - 년도 뒷 두 자리

yyyy - 년도

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

| 일련번호 | 형식 | 설명        |
| -------- | ---- | ----------- |
| dd       | 날짜 | 일          |
| HH       | 시간 | 24시간 형식 |
| hh       | 시간 | 12시간 형식 |
| a        | 구분 | 오전/오후   |

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

어떤 특수 문자든 사용하셔도 됩니다.

Aryan Bisht님의 더 많은 소식

# 코딩 및 개발 여정 업그레이드

우리 커뮤니티의 일원이 되어 주셔서 감사합니다! 떠나시기 전에:

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

- 👏 도움이 되셨다면 이 이야기에 박수를 치시고 작가를 팔로우해주세요 👉
- 🔔 팔로우하세요: LinkedIn
