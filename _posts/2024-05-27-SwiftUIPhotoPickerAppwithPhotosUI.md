---
title: "SwiftUI Photo Picker 앱을 PhotosUI로 만들기"
description: ""
coverImage: "/assets/img/2024-05-27-SwiftUIPhotoPickerAppwithPhotosUI_0.png"
date: 2024-05-27 16:26
ogImage: 
  url: /assets/img/2024-05-27-SwiftUIPhotoPickerAppwithPhotosUI_0.png
tag: Tech
originalTitle: "SwiftUI Photo Picker App with PhotosUI"
link: "https://medium.com/@rizal_hilman/swiftui-photo-picker-app-with-photosui-09cf032ac434"
---


```markdown
![이미지](/assets/img/2024-05-27-SwiftUIPhotoPickerAppwithPhotosUI_0.png)

SwiftUI와 PhotosUI는 iOS 앱에서 풍부하고 인터랙티브한 사용자 인터페이스를 만드는 강력한 도구를 제공합니다. 이 튜토리얼에서는 사용자가 사진 라이브러리에서 사진을 선택하고 표시할 수 있는 앱을 만들겠습니다.

# 프로젝트 설정

- Xcode를 열고 새 SwiftUI 프로젝트를 생성합니다.
- 프로젝트의 이름을 지정합니다 (예: "PhotoPickerApp").
- 사용자 인터페이스로 SwiftUI를 선택하고 프로그래밍 언어로 Swift를 선택해야 합니다.
```

<div class="content-ad"></div>

# 코드 설명

## 단계 1: 필요한 프레임워크 가져오기

```js
import SwiftUI
import PhotosUI
```

- SwiftUI: 사용자 인터페이스를 구축하기 위한 프레임워크입니다.
- PhotosUI: 사진 및 비디오 선택 및 관리를 위한 프레임워크입니다.

<div class="content-ad"></div>

## 단계 2: 상태 변수 정의

```js
struct ContentView: View {
   @State private var selectedPhotos: [PhotosPickerItem] = []
   @State private var images: [UIImage] = []
   @State private var errorMessage: String?

   // ... 나머지 코드
}
```

- selectedPhotos: 선택된 사진 항목을 저장합니다.
- images: 불러온 이미지를 저장합니다.
- errorMessage: 발생한 오류 메시지를 저장합니다.

## 단계 3: 메인 뷰 생성

<div class="content-ad"></div>

```swift
var body: some View {
    VStack {
        Form {
            photoPickerSection
            imagesSection
        }
        if let errorMessage = errorMessage {
            Text(errorMessage)
                .foregroundColor(.red)
                .padding()
        }
    }
}
```

- VStack: 폼과 에러 메시지를 포함합니다.
- Form: 사진 선택기와 이미지를 위한 섹션을 포함합니다.

## 단계 4: 사진 선택기 섹션 정의

```swift
private var photoPickerSection: some View {
    Section {
        PhotosPicker(selection: $selectedPhotos, maxSelectionCount: 3, matching: .images) {
            Label("사진 선택", systemImage: "photo")
        }
        .onChange(of: selectedPhotos) { _ in
            loadSelectedPhotos()
        }
    }
}
```

<div class="content-ad"></div>

- PhotosPicker: 사진을 선택하는 UI 구성 요소입니다.
- selection: `selectedPhotos`에 바인딩됩니다.
- maxSelectionCount: 선택을 3개의 사진으로 제한합니다.
- matching: 이미지로 제한된 선택을 합니다.
- .onChange: `selectedPhotos`이 변경될 때 `loadSelectedPhotos`를 호출합니다.

## 단계 5: 이미지 섹션 정의하기

```js
private var imagesSection: some View {
    Section {
        ForEach(images, id: \.self) { image in
            Image(uiImage: image)
                .resizable()
                .scaledToFit()
                .frame(maxWidth: .infinity)
                .clipShape(RoundedRectangle(cornerRadius: 10.0))
                .padding(.vertical, 10)
        }
    }
}
```

- ForEach: `images` 배열을 반복합니다.
- Image(uiImage:): 각 이미지를 표시합니다.
- .resizable(), .scaledToFit(), .frame(maxWidth: .infinity), .clipShape(), .padding(): 이미지를 스타일링합니다.

<div class="content-ad"></div>

## 단계 6: 선택한 사진 불러오기

```js
private func loadSelectedPhotos() {
    images.removeAll()
    errorMessage = nil
    
    Task {
        await withTaskGroup(of: (UIImage?, Error?).self) { taskGroup in
            for photoItem in selectedPhotos {
                taskGroup.addTask {
                    do {
                        if let imageData = try await photoItem.loadTransferable(type: Data.self),
                           let image = UIImage(data: imageData) {
                            return (image, nil)
                        }
                        return (nil, nil)
                    } catch {
                        return (nil, error)
                    }
                }
            }
            
            for await result in taskGroup {
                if let error = result.1 {
                    errorMessage = "한 개 이상의 이미지를 불러오지 못했습니다."
                    break
                } else if let image = result.0 {
                    images.append(image)
                }
            }
        }
    }
}
```

- images.removeAll(): 기존 이미지를 지웁니다.
- errorMessage = nil: 오류 메시지를 초기화합니다.
- Task: 비동기 작업을 시작합니다.
- withTaskGroup: 사진을 로드하는 동시 작업을 관리합니다.
- photoItem.loadTransferable(type: Data.self): 사진 데이터를 로드합니다.
- UIImage(data: imageData): 데이터를 `UIImage`로 변환합니다.
- errorMessage: 오류 발생 시 업데이트됩니다.
- images.append(image): 로드된 이미지를 배열에 추가합니다.

## 단계 7: 뷰 미리보기

<div class="content-ad"></div>

```js
# Preview {
    ContentView()
}
```

- # Preview: Xcode 캔버스에서 `ContentView`를 미리 볼 수 있습니다.

# 프로젝트 실행

이제 프로젝트를 시뮬레이터나 실제 장치에 실행하세요.

<div class="content-ad"></div>

```markdown
![Image](https://miro.medium.com/v2/resize:fit:720/1*AtvjZ8d79WvKb85R-jBVVg.gif)

# 요약

이 튜토리얼은 사용자가 사진을 선택하고 표시할 수 있는 SwiftUI 앱을 만드는 방법을 안내했습니다. 상태 관리, 비동기 작업 및 이미지 처리를 다루었습니다. 이 앱은 사진 편집, 저장 또는 공유와 같은 추가 기능으로 확장할 수 있습니다. SwiftUI와 PhotosUI를 활용하여 앱의 기능과 사용자 경험을 향상시켜 보세요.

즐거운 코딩되세요!
```

<div class="content-ad"></div>

# 완성된 코드

이 저장소를 복제하거나 다운로드하여 완전한 코드를 확인하십시오.