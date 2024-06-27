---
title: "Swift를 사용한 iOS 클린 아키텍처 구현 방법"
description: ""
coverImage: "/assets/img/2024-06-27-CleanArchitectureiniOSwithSwift_0.png"
date: 2024-06-27 19:21
ogImage: 
  url: /assets/img/2024-06-27-CleanArchitectureiniOSwithSwift_0.png
tag: Tech
originalTitle: "Clean Architecture in iOS with Swift"
link: "https://medium.com/@kalidoss.shanmugam/clean-architecture-in-ios-with-swift-0ec1369c3545"
---


# 클린 아키텍처 소개

클린 아키텍처는 로버트 C. 마틴 (아저씨 밥)이 개발한 설계 패턴으로, 소프트웨어 애플리케이션의 테스트 가능성, 유지 관리성, 확장성을 향상시키고 각 요소 간 명확한 역할 분리를 제공하는 것을 목표로 합니다. 코드를 서로 다른 층으로 구성하여 서로의 의존성을 줄이고 비즈니스 로직이 사용자 인터페이스나 데이터 액세스와 같은 외부 요소에 독립적으로 유지되도록 합니다.

# 클린 아키텍처의 층

1. 엔티티: 외부 변경에 독립적인 핵심 비즈니스 객체와 규칙을 표현합니다.
2. 유스 케이스 (상호작용자): 애플리케이션의 비즈니스 로직을 포함하며, 엔티티로부터 데이터 흐름을 조율합니다.
3. 인터페이스 어댑터: 유스 케이스에서 사용자 인터페이스나 다른 시스템이 이해할 수 있는 형식으로 데이터를 변환합니다.
4. 프레임워크 및 드라이버: 외부 라이브러리, UI 구성 요소, 데이터베이스 및 기타 시스템이 포함됩니다.

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

# 예제 사용 사례

간단한 예제를 살펴보겠습니다. 노트 앱이 있습니다. 새로운 노트를 추가하는 사용 사례에 중점을 둘 것입니다.

# 엔티티

```js
struct Note {
    let id: UUID
    let title: String
    let content: String
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

# 사용 사례

```js
protocol AddNoteUseCase {
    func execute(note: Note)
}

class AddNoteInteractor: AddNoteUseCase {
    private let noteRepository: NoteRepository

    init(noteRepository: NoteRepository) {
        self.noteRepository = noteRepository
    }

    func execute(note: Note) {
        noteRepository.add(note: note)
    }
}
```

# 인터페이스 어댑터

```js
protocol NoteRepository {
    func add(note: Note)
}

class NoteRepositoryImpl: NoteRepository {
    private var notes = [Note]()
    
    func add(note: Note) {
        notes.append(note)
    }
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

# Frameworks and Drivers

```js
class NotesViewController: UIViewController {
    private let addNoteUseCase: AddNoteUseCase
    
    init(addNoteUseCase: AddNoteUseCase) {
        self.addNoteUseCase = addNoteUseCase
        super.init(nibName: nil, bundle: nil)
    }
    
    required init?(coder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    func onAddNoteButtonTapped() {
        let note = Note(
            id: UUID(),
            title: "Sample Note",
            content: "This is a sample note."
        )
        addNoteUseCase.execute(note: note)
    }
}
```

# Advantages of Clean Architecture

1. Separation of Concerns: 각 계층은 명확한 역할을 하므로 코드의 복잡성이 줄어듭니다.
2. Testability: 비즈니스 로직은 사용자 인터페이스나 데이터 계층과 독립적으로 테스트할 수 있습니다.
3. Maintainability: 한 계층의 변경이 다른 계층에 미치는 영향이 최소화되어 코드베이스를 유지보수하기 쉽게 만듭니다.
4. Scalability: 아키텍처는 애플리케이션의 성장과 복잡성을 잘 지원합니다.
5. Flexibility: 비즈니스 로직에 영향을 미치지 않고 구현을 쉽게 교체할 수 있습니다(예: 데이터베이스나 UI 프레임워크 변경).

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

# 클린 아키텍처의 단점

1. 초기 복잡성: 아키텍처를 설정하는 데 추가적인 노력과 이해가 필요해 소규모 프로젝트에는 압도적일 수 있습니다.
2. 오버헤드: 간단한 애플리케이션의 경우, 추가된 레이어가 불필요한 오버헤드를 발생시킬 수 있습니다.
3. 학습 곡선: 개발자는 클린 아키텍처의 원칙과 실천 방법을 이해해야 하며, 이는 학습 곡선이 필요할 수 있습니다.

# 클린 아키텍처 구현을 위한 최상의 실천 방법

1. 천천히 시작하기: 특히 기존 코드베이스를 리팩토링할 경우 클린 아키텍처 원칙을 점진적으로 도입하세요.
2. SOLID 원칙 준수: 디자인이 클린 아키텍처를 보완하는 SOLID 원칙을 준수하도록 확인하세요.
3. 의존성 주입 사용: 의존성을 주입하여 레이어 간 결합을 줄입니다.
4. UI 레이어를 얇게 유지: UI는 데이터 표시 및 사용자 상호작용만 처리하도록 하고 최소한의 로직을 포함시키세요.
5. 테스트 작성: 클린 아키텍처의 테스트 용이성을 활용하여 유증분 및 엔티티에 대한 단위 테스트를 작성하세요.
6. 문서화: 새로운 개발자가 아키텍처를 이해하는 데 도움이 될 명확한 문서를 유지하세요.

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

# 결론

Clean Architecture은 iOS 애플리케이션에서 코드를 체계적으로 구성하는 견고한 프레임워크를 제공하여 관심의 분리, 테스트 가능성 및 유지 관리성을 촉진합니다. 일부 복잡성을 도입하지만 장기적인 이점은 확장 가능하고 유지 보수 가능한 애플리케이션을 구축하기 위한 가치 있는 접근법입니다. 최고의 실천 방법을 따르고 트레이드오프를 이해하여 개발자들은 Clean Architecture를 효과적으로 활용하여 Swift로 고품질 iOS 앱을 만들 수 있습니다.