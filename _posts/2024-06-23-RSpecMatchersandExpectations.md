---
title: "RSpec 매처와 기대 설정 방법"
description: ""
coverImage: "/assets/img/2024-06-23-RSpecMatchersandExpectations_0.png"
date: 2024-06-23 20:48
ogImage:
  url: /assets/img/2024-06-23-RSpecMatchersandExpectations_0.png
tag: Tech
originalTitle: "RSpec Matchers and Expectations"
link: "https://medium.com/@patrykrogedu/rspec-matchers-and-expectations-99e0752b5639"
---

![이미지](/assets/img/2024-06-23-RSpecMatchersandExpectations_0.png)

버그가 없는 코드를 작성하는 것은 끝이 없는 전투입니다. 가장 경험 많은 개발자라도 모든 예외 상황과 잠재적인 문제를 잡는 데 어려움을 겪습니다. 그러나 올바른 도구와 기술을 활용하면 소프트웨어의 신뢰성을 크게 향상시킬 수 있습니다.

# RSpec Expectations 탐색

RSpec의 expectations는 Ruby 웹 어플리케이션을 테스트하는 강력한 도구입니다. 전통적인 어설션과 달리 expectations는 조합성, 자동 부정, 가독성 향상 및 더 유용한 에러 메시지를 제공합니다.

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

# 기대하는 요소

기대는 세 가지 주요 부분으로 구성됩니다:

- Subject: 일반적으로 루비 클래스의 인스턴스인 테스트 대상 객체입니다.
- Matcher: 예상되는 동작을 지정하고 통과/실패 논리를 제공하는 객체입니다.
- 사용자 정의 실패 메시지(선택 사항): 기대가 실패할 때 추가적인 문맥을 제공하기 위한 사용자 정의 메시지입니다.

여기 예시가 있습니다:

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

# 주제

subject = Course.new(name: "루비 입문")

# 매처 및 사용자 정의 실패 메시지를 사용한 예상

expect(subject.name).to eq("루비 입문"), "과정 이름이 일치해야 함"

# 매처 구성

다양한 방법으로 매처를 조합하여 복잡한 동작을 정밀하게 지정할 수 있습니다:

- 다른 매처에 매처 전달하기:

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
expect(subject.lessons).to start_with(an_object_having_attributes(title: "Ruby Basics"))
```

2. Embedding matchers in Arrays and Hashes:

```js
expected_student = {
  name: "John Doe",
  enrolled_courses: an_object_having_attributes(name: a_string_starting_with("Introduction"))
}
expect(subject.students).to include(expected_student)
```

3. Combining matchers with logical operators:

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
expect(subject.grading_system).to be_present & match(/letter|numeric/)
```

## 전통적인 단언문보다 RSpec의 장점

전통적인 단언문과는 달리, RSpec의 기대치는 다음과 같은 장점을 제공합니다:

- 개선된 가독성: 구문은 자연어에 더 가깝기 때문에 테스트를 읽고 이해하기 쉽습니다.
- 결합성: 매처를 결합하여 더 복잡한 단언문을 만들 수 있으며 명확성을 잃지 않습니다.
- 더 나은 오류 메시지: 테스트가 실패할 때 RSpec은 디버깅을 더 쉽게 만드는 자세한 메시지를 제공합니다.
- 자동 부정: .not_to 또는 .to_not를 사용하면 부정이 더 직관적이며 테스트의 표현력을 향상시킵니다.

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

# 생성된 예제 설명

Matchers는 자체적으로 설명을 제공하여 RSpec이 예제에 대한 가독성있는 설명을 생성할 수 있게 합니다. 이는 중복을 줄이고 테스트를 미래를 대비하여 더 견고하게 만드는 데 도움이 될 수 있습니다.

```js
RSpec.describe Course, "#enrolled_students" do
  subject { Course.new(name: "Introduction to Ruby") }

  it { is_expected.to have_attributes(enrolled_students: be_empty) }
  it { should_not have_attributes(enrolled_students: include(an_object_having_attributes(name: "John Doe"))) }
end
```

출력은 다음과 같이 표시됩니다:

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

Course#enrolled_students
should have attributes enrolled_students: []
should not have attributes enrolled_students: [#<name: "John Doe">]

While generated descriptions are convenient, use them judiciously. They can be misleading if your setup code changes, and one-liner specs can sometimes
be harder to read and maintain.

# Exploring RSpec’s Matchers for Your App

RSpec offers a wide range of built-in matchers to help you write expressive and robust tests.

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

# 기본 일치자

이러한 일치자는 문자열, 숫자 및 부울과 같은 기본 데이터 유형을 처리합니다. 예를 들어, 학생의 이름이 특정 값과 일치하는지 테스트하려면:

```js
student = Student.new(name: "John Doe")
expect(student.name).to eq("John Doe")
```

# 컬렉션 일치자

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

RSpec은 배열이나 해시와 같은 컬렉션과 함께 작업하는 여러 매처를 제공합니다. 특정 요소가 컬렉션에 포함되어 있는지 확인하려면 include를 사용할 수 있습니다:

```js
enrolled_courses = [
  { name: "Ruby Basics", credits: 3 },
  { name: "Web Development", credits: 5 }
]

expect(enrolled_courses).to include(
  an_object_having_attributes(name: "Ruby Basics")
)
```

match 매처는 깊게 중첩된 데이터 구조에 유용하며, 배열 요소나 해시 값에 대해 어떤 수준에서든 매처를 대체할 수 있습니다.

# 블록 매처

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

가끔은 코드 블록의 동작을 테스트해야 할 때가 있습니다. 예를 들어, 예외가 발생하는지 확인하거나 메소드가 블록에 제어를 양보하는지 확인하는 것입니다. 이런 시나리오에는 RSpec의 블록 매처들이 유용합니다.

```js
expect { course.enroll(nil) }.to raise_error(ArgumentError)

expect { |block| course.lessons.each(&block) }.to yield_successive_args(
  ["Lesson 1", 1],
  ["Lesson 2", 2]
)
```

# 조합된 매처들

RSpec의 장점 중 하나는 매처들을 조합할 수 있다는 것입니다. 이를 통해 복잡한 동작을 정확하게 지정할 수 있습니다. 논리 연산자를 사용하여 매처를 결합하거나, 하나의 매처를 다른 매처로 전달하거나, 매처를 데이터 구조 안에 포함시킬 수 있습니다.

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
expect(course.lessons).to all(
  have_attributes(duration: a_value_between(30, 90))
    .and(start_with("Lesson"))
)
```

# 간단한 설명

RSpec expectations은 조합성, 가독성 및 자세한 오류 메시지를 제공합니다. 복잡한 조건을 정확하게 지정하기 위해 matcher composition을 사용하세요.

기본 데이터 유형의 경우 등가성 및 진실성 matcher를 사용하세요. include, match 및 contain_exactly로 컬렉션을 확인하세요. 예외와 블록 matcher를 사용하여 코드 동작을 테스트하세요.

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

일치자를 중첩하여 배치하거나 데이터 구조에 삽입하여 논리 연산자를 사용하여 작성하세요. 엄격한 동등성보다는 유연한 일치자를 선택하세요. 가독성을 높이기 위해 별칭 및 사용자 정의 메시지를 사용하세요.
