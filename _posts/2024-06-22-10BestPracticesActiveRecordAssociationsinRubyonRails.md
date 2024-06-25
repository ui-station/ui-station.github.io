---
title: "루비 온 레일즈Ruby on Rails에서 Active Record Associations를 위한 최고 모범 사례 10가지"
description: ""
coverImage: "/assets/img/2024-06-22-10BestPracticesActiveRecordAssociationsinRubyonRails_0.png"
date: 2024-06-22 22:35
ogImage:
  url: /assets/img/2024-06-22-10BestPracticesActiveRecordAssociationsinRubyonRails_0.png
tag: Tech
originalTitle: "10 Best Practices Active Record Associations in Ruby on Rails"
link: "https://medium.com/@patrickkarsh/10-best-practices-active-record-associations-in-ruby-on-rails-e914e8df45db"
---

![Active Record associations](/assets/img/2024-06-22-10BestPracticesActiveRecordAssociationsinRubyonRails_0.png)

루비 온 레일즈( Ruby on Rails)의 Active Record associations은 응용 프로그램의 다른 모델 간 관계를 표현하는 강력한 방법입니다. 직관적인 API를 통해 관련 데이터를 처리하기 쉽게 만들어줍니다. Active Record associations을 효과적으로 활용하는 몇 가지 모베스트 프랙티스는 다음과 같습니다:

## 적절한 Association 유형 선택

Rails는 여러 가지 Association 유형을 지원합니다: belongs_to, has_many, has_one, has_many :through, has_one :through, 그리고 has_and_belongs_to_many. 모델 간 관계를 정확하게 반영하는 것을 선택하십시오. 예를 들어, 일대다 관계의 경우 has_many를 사용하고 해당 관계의 반대쪽에는 belongs_to를 사용합니다.

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

<img src="/assets/img/2024-06-22-10BestPracticesActiveRecordAssociationsinRubyonRails_1.png" />

##dependent: 올바르게 사용하기

소유자가 삭제될 때 관련된 객체에 대해 어떻게 처리해야 하는지 지정하세요. :destroy, :delete_all, :nullify 또는 :restrict_with_error와 같은 옵션을 사용하여 고아 레코드를 관리하고 데이터베이스 무결성을 유지할 수 있습니다.

<img src="/assets/img/2024-06-22-10BestPracticesActiveRecordAssociationsinRubyonRails_2.png" />

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

## 연관성을 활용하여 스코프 활용하기

공통 질의 패턴을 재사용하기 위해 연관성에 대한 스코프를 정의할 수 있습니다.

아래와 같이 작성할 수 있습니다:

![img](/assets/img/2024-06-22-10BestPracticesActiveRecordAssociationsinRubyonRails_3.png)

이렇게 하면 사용자의 게시물 중 게시된 것만 반환하는 것으로 자동으로 필터링됩니다.

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

## has_many :through를 has_and_belongs_to_many보다 선호하세요

has_many :through는 조인 모델을 일반적인 ActiveRecord 모델로 사용할 수 있어 has_and_belongs_to_many보다 유연성이 높습니다. 조인 테이블에 추가 속성을 필요로 할 때 유용합니다.

## 다형 관계는 되도록 삼가세요

다형 관계는 강력하지만 스키마를 더 복잡하고 이해하기 어렵게 만들 수 있습니다. 실제로 필요할 때에만 사용하세요. 예를 들어, 모델이 단일 관계에서 두 개 이상의 다른 모델에 속할 수 있는 경우에 사용하세요.

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

## 데이터베이스에서 외래 키 인덱싱

데이터베이스에 외래 키 열을 생성하는 관계(예: belongs_to)에 대해 해당 열이 인덱싱되어 있는지 확인하세요. 이렇게 하면 해당 관계를 활용하는 쿼리의 성능을 크게 향상시킬 수 있습니다.

## N+1 쿼리 피하기

N+1 쿼리 문제를 주의하세요. 이 문제는 하나의 쿼리로 기본 객체를 검색한 다음 각 객체의 관계를 가져오기 위해 추가 쿼리가 실행되는 상황을 의미합니다. 관련 관계를 미리로드하고 데이터베이스 쿼리 수를 줄이기 위해 .includes 메서드를 사용하세요.

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

## 콜백에 주의하세요

모델에서 콜백 (before_save, after_create 등)을 사용할 때 특히 관련성을 주의해야 합니다. 적절하게 관리되지 않으면 예상치 못한 동작이나 성능 문제를 일으킬 수 있습니다.

## inverse_of 옵션 사용 고려

:inverse_of 옵션을 사용하여 역 관련성을 수동으로 지정할 수 있습니다. 이를 통해 객체 로딩을 최적화하고 단일 요청 범위 내에서 관련성 일관성을 보장할 수 있습니다.

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

## 적절하게 연관된 객체 유효성 검사

저장하기 전에 연관된 객체가 특정 기준을 충족하는지 확인하기 위해 유효성을 사용하세요. Rails는 이를 위해 validates_associated를 제공하지만, 연관된 객체의 유효성만을 확인하며 연관 자체의 존재 여부는 확인하지 않음을 기억해주세요.

이러한 모범 사례를 따르면 Active Record 관계를 최대한 활용하여 유지보수성, 효율성 및 견고성이 높은 애플리케이션을 구축할 수 있습니다.
