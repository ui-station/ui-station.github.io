---
title: "Swft Data로 SwiftUI 어플리케이션 개발하기"
description: ""
coverImage: "/assets/img/2024-05-27-BuildingSwiftUIApplicationswithSwiftData_0.png"
date: 2024-05-27 18:02
ogImage:
  url: /assets/img/2024-05-27-BuildingSwiftUIApplicationswithSwiftData_0.png
tag: Tech
originalTitle: "Building SwiftUI Applications with Swift Data"
link: "https://medium.com/@raj.anand09/building-swiftui-applications-with-swift-data-7e698c4e5462"
---

![Image](/assets/img/2024-05-27-BuildingSwiftUIApplicationswithSwiftData_0.png)

# Swift UI는 Swift Data를 시작하는 가장 쉬운 방법입니다

- Swift UI와의 완벽한 통합
- 간편한 구성
- 데이터 자동 가져오기 및 뷰 업데이트

# Swift Data 소개

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

표를 마크다운 형식으로 변경해 주세요.

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

```kotlin
@Model
class Category {

  @Attribute(.unique) var name: String
  var imageName: String

  init(name: String,
       imageName: String = "rupeesign.circle",
       type: CategoryType) {
    self.name = name
    self.imageName = imageName
  }

}
```

## Using the model macro

Modifies all stored properties

@Model

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

- 강력한 새로운 스위프트 매크로
- 코드로 스키마 정의하기
- 모델 유형에 스위프트 데이터 기능 추가하기

속성을 어떻게 추론할지 제어합니다.

@Attributes

- 속성에서 추론된 속성
- 기본 값 유형 지원
- 구조체, 열거형, Codable 및 값 유형의 컬렉션과 같은 복잡한 값 유형의 지원

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

@관계

- 관계는 참조 유형에서 유추됩니다.
- 다른 모델 유형
- 모델 유형의 컬렉션

@Transient로 속성 제외

더 많은 정보: Swift Data로 스키마 모델링하기 (WWDC 2023)

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

# 데이터 작업하기

## 모델 컨테이너

- 영속성 백엔드
- 구성에 맞게 사용자 지정
- 스키마 마이그레이션 옵션 제공

```js
.modelContainer(for: Category.self) { result in
    // TODO: - 결과 처리
}
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

## 모델 컨텍스트

- 업데이트 추적
- 모델 가져오기
- 변경 내용 저장
- 변경 내용 취소

![이미지](/assets/img/2024-05-27-BuildingSwiftUIApplicationswithSwiftData_1.png)

더 많은 정보: SwiftData에 대한 깊은 탐구 (WWDC 2023)

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

# SwiftUI와 통합

SwiftData는 SwiftUI를 염두에 두고 만들어졌으며 함께 사용하는 것이 더 쉽습니다.

뷰 수정자

- 씬 및 뷰 수정자 활용
- .modelContainer로 데이터 구성
- SwiftUI 환경 전체에 전파됨

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

@Query Property Wrapper

```swift
@Query var categories: [Category]
var body: some View {
 List(categories) { category in
  NavigationLink(category.name, destination: CategoryView(category))
 }
}
```

SwiftData와 SwiftUI가 함께 작동하여 기본 데이터가 변경될 때 뷰에 실시간 업데이트를 제공하며 결과를 수동으로 새로 고침할 필요가 없습니다.

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

변경 사항 관찰

@Published를 사용할 필요가 없습니다. SwiftUI는 관찰 대상 속성을 자동으로 새로 고칩니다.

추가 정보: SwiftData(WWDC 2023)를 활용하여 앱을 개발해보세요.

# 자동 지속성

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

SwiftData는 사용자 모델로 사용자 지정 스키마를 빌드하고 효율적으로 필드를 기반 저장소에 매핑합니다. SwiftData가 관리하는 객체는 필요할 때 데이터베이스에서 가져오고 추가 작업없이 적절한 시점에 자동으로 저장됩니다. 또한 ModelContext API를 사용하여 완전한 제어를 할 수도 있습니다.

# CloudKit 동기화

데이터는 DocumentGroup을 사용하여 파일로 저장하고 iCloud Drive를 통해 동기화하거나 CloudKit을 사용하여 장치간 데이터 동기화를 할 수 있습니다.

# Core Data와 호환 가능

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

SwiftData는 Core Data의 검증된 저장 아키텍처를 사용하므로 동일한 기본 저장소를 사용하여 동일한 앱에서 둘 다 사용할 수 있습니다. 준비가 되면 Xcode가 Core Data 모델을 SwiftData와 함께 사용할 클래스로 변환할 수 있습니다.

# 참고:
