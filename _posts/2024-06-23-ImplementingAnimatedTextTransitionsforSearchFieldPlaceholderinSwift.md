---
title: "Swift에서 검색 필드 플레이스홀더에 애니메이션 텍스트 전환 구현하는 방법"
description: ""
coverImage: "/assets/img/2024-06-23-ImplementingAnimatedTextTransitionsforSearchFieldPlaceholderinSwift_0.png"
date: 2024-06-23 21:31
ogImage: 
  url: /assets/img/2024-06-23-ImplementingAnimatedTextTransitionsforSearchFieldPlaceholderinSwift_0.png
tag: Tech
originalTitle: "Implementing Animated Text Transitions for Search Field Placeholder in Swift"
link: "https://medium.com/@lakshmialekhya406/implementing-animated-text-transitions-for-search-field-placeholder-in-swift-ce55881cc07f"
---



![animated text transition](https://miro.medium.com/v2/resize:fit:1200/1*iAxDbK5Cno82BIy7Kf6p4A.gif)

이 프로젝트의 목표는 검색 필드의 자리 표시자를 위한 텍스트 애니메이션 전환을 가지는 것입니다. 자리 표시자의 일부가 전환과 함께 한 번에 업데이트되는 것입니다.

![search field placeholder](/assets/img/2024-06-23-ImplementingAnimatedTextTransitionsforSearchFieldPlaceholderinSwift_0.png)

시작하기 전에 검색 아이콘을 가진 텍스트 필드를 추가해 보겠습니다. 위에서 "magnifyingglass"라는 시스템 이미지를 사용하고 있습니다. 패딩을 추가하려면 특정 프레임의 뷰에 이미지를 추가하여 이를 텍스트 필드의 "왼쪽 뷰" 속성에 추가할 수 있습니다.


<div class="content-ad"></div>


// 검색 필드 설정
searchField = UITextField()
searchField.borderStyle = .roundedRect
searchField.translatesAutoresizingMaskIntoConstraints = false
searchField.textColor = UIColor.gray.withAlphaComponent(0.5)
searchField.tintColor = UIColor.gray.withAlphaComponent(0.5)
searchField.becomeFirstResponder()

// 검색 아이콘 이미지 뷰 생성
let searchIcon = UIImageView(image: UIImage(systemName: "magnifyingglass"))
searchIcon.tintColor = .gray
searchIcon.contentMode = .scaleAspectFit

// 이미지 뷰에 패딩 추가
let paddingView = UIView(frame: CGRect(x: 0, y: 0, width: 30, height: 30))
searchIcon.frame = CGRect(x: 5, y: 5, width: 20, height: 20)
paddingView.addSubview(searchIcon)

// 이미지 뷰를 텍스트 필드의 왼쪽 뷰로 설정
searchField.leftView = paddingView
searchField.leftViewMode = .always

// 검색 필드에 액션 추가
searchField.addTarget(
    self,
    action: #selector(textFieldDidChange),
    for: .editingChanged
)


이제 전환 문자열 역할을 하는 문자열을 포함하는 뷰를 추가해 봅시다. 여기서는 세 개의 레이블이 추가되어 하나는 정적 텍스트를 가져야하며, 다른 두 레이블은 전환과 함께 계속 업데이트됩니다.

이미지 참조: ![이미지](/assets/img/2024-06-23-ImplementingAnimatedTextTransitionsforSearchFieldPlaceholderinSwift_1.png)

'Label1'은 'Search for'라는 정적 레이블이고, 'Label1' 및 'Label2'는 현재 레이블 및 다음 레이블로 작동하여 현재 문자열과 현재 문자열 뒤에 나오는 문자열 즉, 현재 문자열 이후에 나오는 문자열을 저장합니다. 문자열을 업데이트하고 애니메이션하는 방법은 다음과 같습니다.


<div class="content-ad"></div>

```swift
// 문자열 목록
var strings = ["\'음식\'", "\'음식점\'", "\'식료품\'", "\'음료수\'", "\'빵\'", "\'피자\'", "\'비리야니\'", "\'버거\'", "\'바지\'", "\'국수\'", "\'수프\'", "\'샌드위치\'", "\'비스킷\'", "\'초콜릿\'"]

@objc func updateLabels() {
    if index < strings.count {
        // 단계 1: 문자열 할당
        nextLabel.text = strings[index]
        nextLabel.alpha = 0
        nextLabel.transform = CGAffineTransform(translationX: 0, y: searchView.frame.height / 2)
        
        // 단계 2: 전환 효과를 주기 위해 애니메이션 적용
        UIView.animate(withDuration: 1.0, delay: 0, options: .curveEaseOut, animations: {
            self.currentLabel.alpha = 0
            self.currentLabel.transform = CGAffineTransform(translationX: 0, y: -self.searchView.frame.height / 2)
            self.nextLabel.alpha = 1
            self.nextLabel.transform = .identity
        }, completion: { _ in
            // 단계 3: 라벨 로직
            // 라벨 교체
            self.currentLabel.text = self.nextLabel.text
            self.currentLabel.alpha = 1
            self.currentLabel.transform = .identity
            
            // 다음 라벨 초기화
            self.nextLabel.alpha = 0
            self.nextLabel.transform = CGAffineTransform(translationX: 0, y: self.searchView.frame.height / 2)
        })
        // 단계 4: 인덱스 증가
        index += 1
    } else {
        // 모든 문자열이 표시된 경우 타이머 무효화
        timer?.invalidate()
    }
}
```

- nextLabel의 텍스트를 현재 문자열로 설정합니다. 또한 알파를 0으로 설정하여(투명하게 함) 검색 뷰 아래로 이동하도록 번역 변환을 적용합니다.
- UIView.animate를 사용하여 애니메이션을 수행합니다. 애니메이션 중에:

- currentLabel이 페이드 아웃되고 위로 이동합니다.
- nextLabel이 페이드 인하여 원래 위치로 이동합니다.

3. 완료 블록에서 라벨의 텍스트를 교환하고 알파와 번역을 재설정합니다. 이렇게 하면 currentLabel이 다음 문자열을 위해 준비되고 nextLabel이 다음 전환을 위해 재설정됩니다.

<div class="content-ad"></div>

라벨이 어떻게 업데이트되는지 확인해 봅시다. 여기에서는 타이머를 사용합니다. 타이머는 설정된 간격이 지나면 타겟 객체로 지정된 메시지를 전송합니다.
라벨이 업데이트되도록 메서드를 초기화하고 타이머를 설정해야 합니다.

```js
func animateListOfLabels() {
    currentLabel.text = strings[index-1]
    nextLabel.alpha = 0
    timer?.invalidate()
    timer = Timer.scheduledTimer(
            timeInterval: 2,
            target: self,
            selector: #selector(updateLabels),
            userInfo: nil,
            repeats: true
    )
}
```

animateListOfLabels 함수는 라벨의 애니메이션 순서를 관리하는 데 중요합니다. 이 함수는 다음을 보장합니다:

- currentLabel이 올바른 텍스트로 설정됩니다.
- nextLabel이 숨겨지고 애니메이션을 위해 준비됩니다.
- 이전 타이머가 충돌을 피하기 위해 무효화됩니다.
- 라벨을 매 2초마다 업데이트하는 새로운 타이머가 설정되어 부드러운 전환 효과를 가능하게 합니다.

<div class="content-ad"></div>

이 설정은 각 문자열이 부드러운 전환과 함께 순차적으로 표시되는 레이블 목록을 애니메이션화하는 깔끔하고 제어된 방법을 제공합니다.

저희의 구현에서는 텍스트 필드가 업데이트될 때 타이머가 일시 중지되고, 텍스트 필드가 다시 비어 있을 때 다시 시작됩니다. 여기에 "stopTimer" 및 "resumeTimer" 메서드가 있습니다.

```js
// 실행 중인 타이머 무효화
func stopTimer() {
    timer?.invalidate()
}

// 타이머 재개
func resumeTimer() {
    currentLabel.text = strings[index-1]
    timer = Timer.scheduledTimer(
        timeInterval: 2,
        target: self,
        selector: #selector(updateLabels),
        userInfo: nil,
        repeats: true
    )
}

// 텍스트필드의 editingChanged 동작 실행
@objc func textFieldDidChange() {
    if let text = searchField.text, !text.isEmpty {
        searchView.isHidden = true
        stopTimer()
    } else {
        searchView.isHidden = false
        resumeTimer()
    }
}
```

- "resumeTimer" 함수는 일시 중단 후 레이블의 애니메이션 시퀀스를 다시 시작하는 데 필수적입니다. 이는 다음 사항을 보장합니다:

<div class="content-ad"></div>

- 현재 레이블 (currentLabel)은 이전에 표시된 문자열을 표시하도록 설정됩니다.
- 새 타이머가 생성되어 일정 간격으로 updateLabels 메서드를 호출하여 텍스트의 부드러운 전환을 계속합니다.

올바른 설정으로 타이머를 재개함으로써, 이 기능은 애니메이션된 텍스트 전환의 연속성과 부드러움을 유지하여 사용자 경험을 향상시킵니다. 애니메이션이 중단된 지점부터 이어서 재개함으로써, 사용자들이 애니메이션을 자연스럽게 이어가도록 도와줍니다.

2. stopTimer 함수는 타이머를 일시 중지하거나 완전히 중지해야 하는 상황에서 필수적입니다.

3. textFieldDidChange 함수는 앱 내 반응이 빠르고 사용자 친화적인 경험을 보장하는 데 중요한 역할을 합니다.
텍스트 필드 내용에 따라 검색 뷰를 동적으로 표시하거나 숨기며, 텍스트 필드 내용에 따라 타이머를 제어함으로써 부드럽고 직관적인 사용자 상호 작용을 가능하게 합니다.

<div class="content-ad"></div>

애니메이션 검색 필드와 텍스트 전환을 구현하는 주요 이점은 다음과 같아요

- 사용자 참여도 향상
- 가독성 향상
- 사용자 경험 개선
- 효율적인 정보 표시
- 섬세한 사용자 지원

여기서 코드의 완전한 버전을 찾을 수 있어요. 이 기능을 확인하려면 GitHub에서 클론해보세요.

감사합니다!!! 😃