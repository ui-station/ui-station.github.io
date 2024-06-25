---
title: "Ruby on Rails에서 스파게티 코드 피하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_0.png"
date: 2024-06-23 20:44
ogImage:
  url: /assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_0.png
tag: Tech
originalTitle: "Avoiding Spaghetti Code in Ruby on Rails"
link: "https://medium.com/dev-genius/avoiding-spaghetti-code-in-ruby-on-rails-21838c95c32a"
---

![이미지](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_0.png)

깨끗하고 유지보수 가능한 코드를 작성하는 것은 개발자에게 중요한 기술입니다. 특히 Ruby on Rails와 같은 견고한 프레임워크를 사용할 때는 더욱 중요합니다. 스파게티 코드란 복잡하고 읽기 어렵고 모듈화되지 않은 코드를 가리키며, 이는 애플리케이션을 확장하거나 디버그하고 유지하기 어렵게 만들 수 있습니다. 이 기사는 Ruby on Rails 개발자들이 스파게티 코드 함정에 빠지지 않도록 도와주기 위한 실용적인 팁과 예제를 제공합니다.

## 레일스의 방식을 따르기

원칙: 더 깨끗하고 예측 가능한 코드베이스를 위해 레일스의 관례를 활용하세요.

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

예제: Rails는 모델, 뷰, 컨트롤러, 도우미 등과 관련된 코드를 위한 특정 장소를 제공합니다. 예를 들어, 새로운 리소스를 생성할 때:

![](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_1.png)

이 명령은 Rails의 관례를 준수하여 모델, 컨트롤러 및 뷰를 관행적인 위치에 생성하여 깨끗하고 조직화된 코드베이스를 유도합니다.

## RESTful 리소스 사용하기

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

원칙: 응용 프로그램을 리소스를 중심으로 구성하고 RESTful 경로를 준수하세요.

예: 가능한 경우에는 사용자 정의 경로가 아닌 RESTful 경로를 사용하여 config/routes.rb에서 다음을 수행하십시오:

![RESTful routes](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_2.png)

이 간단한 한 줄은 RESTful 원칙을 준수하는 일곱 가지 다른 경로를 생성하여 응용 프로그램 구조에서 일관성과 예측 가능성을 촉진합니다.

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

## DRY (Don’t Repeat Yourself)

원칙: 코드 중복을 피해서 응용 프로그램을 유지 관리하기 쉽게 만듭니다.

예: 공유 뷰 코드에 대해 부분 적용파일을 사용하십시오. 여러 뷰가 동일한 포스트 양식을 사용하는 경우 \_form.html.erb와 같은 부분 적용 파일을 만드세요:

<img src="/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_3.png" />

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

그럼 다음과 같이 Markdown 형식으로 해당 테이블을 추가하세요:

![Avoiding Spaghetti Code](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_4.png)

## 가벼운 컨트롤러, 뚱뚱한 모델

원칙: 컨트롤러는 경량이어야 하며, HTTP와 관련된 로직만 처리하고, 모델은 비즈니스 로직을 처리해야 합니다.

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

예시: 비즈니스 로직을 컨트롤러에 넣는 대신:

![AvoidingSpaghettiCodeinRubyonRails_5](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_5.png)

로직을 모델로 옮기세요:

![AvoidingSpaghettiCodeinRubyonRails_6](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_6.png)

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

## 서비스 객체 사용하기

주의 사항: 복잡한 비즈니스 로직을 다룰 때는 모델을 간단하고 유지보수하기 쉽도록 서비스 객체를 사용하세요.

예시: 게시물 생성이 여러 단계를 거치는 경우 서비스 객체를 만드세요:

![예시 이미지](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_7.png)

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

그럼, 귀하의 컨트롤러에서:

![AvoidingSpaghettiCodeinRubyonRails_8](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_8.png)

## ActiveRecord 관계 및 스코프

주요 원칙: 모델 관계에는 연관성을 사용하고 자주 사용하는 쿼리에는 스코프를 사용하십시오.

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

예시: 모델에서 연관 관계 정의하기:

![이미지](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_9.png)

그리고 일반적인 쿼리에 대해 스코프 사용하기:

![이미지](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_10.png)

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

## 테스트

원칙: 코드에 대한 테스트를 작성하여 예상대로 작동하는지 확인하세요.

예시: 모델 테스트를 작성하기 위해 RSpec을 사용하세요:

![image](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_11.png)

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

## 리팩터링

원칙: 코드의 구조와 가독성을 꾸준히 개선하세요.

예시: 복잡한 메서드를 작은 조각으로 나눠서 관리하기 쉬운 코드로 리팩터링하세요.

이전:

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

<img src="/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_12.png" />

After:

<img src="/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_13.png" />

## Follow Ruby and Rails Style Guides

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

원칙: 스타일 가이드를 준수하여 코드 일관성 유지하기

예시: RuboCop을 사용하여 스타일 지침을 자동으로 강제합니다. 간단한 .rubocop.yml 파일을 사용하여 커뮤니티 표준을 강요할 수 있습니다:

![image](/assets/img/2024-06-23-AvoidingSpaghettiCodeinRubyonRails_14.png)

## 코드 리뷰

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

**원칙:** 고품질을 유지하고 지식을 공유하기 위해 코드 리뷰에 참여하십시오.

**예시:** 코드 리뷰를 위해 GitHub 풀 리퀘스트를 사용하십시오. 팀원들로부터 리뷰를 요청하여 문제를 일찍 발견하고 일관된 코딩 관행을 유지할 수 있습니다.

이러한 원칙과 예시를 적용함으로써 Ruby on Rails 개발자들은 더 깨끗하고 유지보수하기 좋은 코드를 작성할 수 있으며, 꼬여버린 스파게티 코드를 피하고 응용 프로그램을 보다 즐겁고 확장하기 쉽게 만들 수 있습니다.
