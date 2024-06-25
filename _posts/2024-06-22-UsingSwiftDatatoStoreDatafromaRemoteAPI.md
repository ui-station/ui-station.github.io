---
title: "SwiftData로 원격 API에서 데이터 저장하는 방법"
description: ""
coverImage: "/assets/img/2024-06-22-UsingSwiftDatatoStoreDatafromaRemoteAPI_0.png"
date: 2024-06-22 23:10
ogImage:
  url: /assets/img/2024-06-22-UsingSwiftDatatoStoreDatafromaRemoteAPI_0.png
tag: Tech
originalTitle: "Using SwiftData to Store Data from a Remote API"
link: "https://medium.com/@jpmtech/using-swiftdata-to-store-data-from-a-remote-api-9f283834aa50"
---

앱 사용자에게 오프라인 기능을 제공하고 앱 운영 비용을 줄이는 데 중요한 로컬 데이터 저장은 매우 중요합니다.

[![Image](/assets/img/2024-06-22-UsingSwiftDatatoStoreDatafromaRemoteAPI_0.png)](//)

시작하기 전에 몇 초 동안 제 게시물을 팔로우하고 👏 클랩(clap)하여 더 많은 사람들이 이 유용한 내용을 배울 수 있도록 도와주세요.

# API 호출로부터 SwiftData를 사용하여 로컬로 데이터 저장하기

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

코드로 넘어가기로 하겠습니다. SwiftData에 익숙하지 않으신 경우, 새로운 내용을 익히시려면 이 기사를 확인해보세요.

우리는 구조체의 이름 뒤에 약어 DTO(Data Transfer Object)를 추가하기로 결정했습니다. DTO는 한 시스템에서 다른 시스템으로 데이터를 전송하는 데 사용됩니다. 우리의 예제에서는 API 응답의 데이터를 JSON에서 SwiftData entity로 전송하려고 합니다. DTO는 JSON 응답을 일시적으로 보유하여 해당 데이터를 파싱하고 Swift 객체로 변환할 수 있게 합니다. 앞으로 몇 개의 코드 블록에서는 해당 Swift 객체를 SwiftData entity로 변환할 것입니다.

```swift
// PhotoDTO.swift
import Foundation

struct PhotoDTO: Identifiable, Codable {
    let albumId: Int
    let id: Int
    let title: String
    let url: String
    let thumbnailUrl: String
}
```

SwiftData로 시작하는 기사를 읽으신 경우, 이 내용은 익숙하게 느껴질 것입니다. 클래스에 새로운 코드 조각을 추가했는데, 그것은 편의 이니셜라이저입니다. 이를 통해 DTO를 직접 전달하여 SwiftData entity를 생성할 수 있습니다.

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

```swift
//  PhotoObject.swift
import Foundation
import SwiftData

@Model
class PhotoObject {
    var albumId: Int
    @Attribute(.unique) var id: Int
    var title: String
    var url: String
    var thumbnailUrl: String

    init(albumId: Int, id: Int, title: String, url: String, thumbnailUrl: String) {
        self.albumId = albumId
        self.id = id
        self.title = title
        self.url = url
        self.thumbnailUrl = thumbnailUrl
    }

    convenience init(item: PhotoDTO) {
        self.init(
            albumId: item.albumId,
            id: item.id,
            title: item.title,
            url: item.url,
            thumbnailUrl: item.thumbnailUrl
        )
    }
}
```

웹 서비스도 만약 SwiftUI에서 API 호출을 만들고 파싱한 적이 있다면 익숙할 것입니다. 웹 서비스를 살펴보면, 그 기사에서 살짝 수정하여 새로운 기능을 보유한 웹 서비스를 가지고 있다는 것을 알 수 있습니다. updateDataInDatabase를 이용해 API 호출에서 데이터를 파싱할 때마다 데이터를 데이터베이스에 저장할 수 있도록 새 기능을 추가했습니다. updateDataInDatabase 함수는 PhotoDTO를 SwiftData 엔티티로 변환하고 각 레코드를 데이터베이스에 저장하는 메서드입니다. 또한, ID로 찾을 수 없는 경우 새 레코드를 생성할지 또는 기존 레코드를 업데이트할지 결정하기 위해 insert 메서드를 사용하고 있음을 알 수 있습니다.

```swift
//  WebService.swift
import Foundation
import SwiftData

enum NetworkError: Error {
    case badUrl
    case invalidRequest
    case badResponse
    case badStatus
    case failedToDecodeResponse
}

class WebService {
    @MainActor
    func updateDataInDatabase(modelContext: ModelContext) async {
        do {
            let itemData: [PhotoDTO] = try await fetchData(fromUrl: "https://jsonplaceholder.typicode.com/albums/1/photos")
            for eachItem in itemData {
                let itemToStore = PhotoObject(item: eachItem)
                modelContext.insert(itemToStore)
            }
        } catch {
            print("Error fetching data")
            print(error.localizedDescription)
        }
    }

    private func fetchData<T: Codable>(fromUrl: String) async throws -> [T] {
        guard let downloadedData: [T] = await WebService().downloadData(fromURL: fromUrl) else { return [] }

        return downloadedData
    }

    private func downloadData<T: Codable>(fromURL: String) async -> T? {
        do {
            guard let url = URL(string: fromURL) else { throw NetworkError.badUrl }
            let (data, response) = try await URLSession.shared.data(from: url)
            guard let response = response as? HTTPURLResponse else { throw NetworkError.badResponse }
            guard response.statusCode >= 200 && response.statusCode < 300 else { throw NetworkError.badStatus }
            guard let decodedResponse = try? JSONDecoder().decode(T.self, from: data) else { throw NetworkError.failedToDecodeResponse }

            return decodedResponse
        } catch NetworkError.badUrl {
            print("There was an error creating the URL")
        } catch NetworkError.badResponse {
            print("Did not get a valid response")
        } catch NetworkError.badStatus {
            print("Did not get a 2xx status code from the response")
        } catch NetworkError.failedToDecodeResponse {
            print("Failed to decode response into the given type")
        } catch {
            print("An error occured downloading the data")
        }

        return nil
    }
}
```

뷰 레이어에서는 데이터베이스에서 PhotoObjects를 읽어와 목록으로 표시합니다. 목록 내에서는 AsyncImage 구성 요소를 사용해 API에서 이미지를 표시하고 해당 항목의 ID를 이미지 옆에 표시합니다. 목록의 하단에는 몇 가지 다른 변형을 추가했습니다. overlay 변형은 목록이 비어 있을 때 진행 스피너를 표시합니다. task 변형은 목록이 비어 있으면 API에서 데이터를 자동으로 가져오도록 합니다. 또한, refreshable 변형을 사용해 목록 구성 요소에 pull to refresh를 추가했습니다.

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

"task" 및 "refreshable" 수정자를 사용하면 사용자가 업데이트된 데이터를 확인하려고 할 때만 API가 호출되도록 보장합니다(API를 실행하는 비용을 줄임). 이는 앱을 런칭할 때마다 API에서 데이터를 가져오는 대신 두 번째로 데이터가 데이터베이스에서 가져와지게 함을 의미합니다.

또한 미리보기가 일반적인 경우보다 코드가 더 많이 보일 수 있습니다. SwiftData에서 데이터를 미리보는 것은 일반적인 뷰에서 데이터를 표시하는 것보다 조금 더 복잡하기 때문입니다. 미리보기에서는 데이터가 임시로 저장되는 새 컨테이너를 만들고(미리보기 장치에 데이터를 영구적으로 저장하지 않기 위함), 그런 다음 객체를 만들어 그 컨테이너에 저장합니다. 미리보기 데이터를 저장하는 별도의 파일을 생성하는 다른 자습서도 볼 수 있지만 이는 작동하며 전혀 문제가 없습니다. 저는 미리보기에 대한 코드를 미리보기 자체에 가까이 유지하고 싶어하여 별도의 파일을 생성하지 않았습니다.

```js
//  ContentView.swift
import SwiftData
import SwiftUI

struct ContentView: View {
    @Environment(\.modelContext) var modelContext
    @Query(sort: \PhotoObject.id) var photos: [PhotoObject]

    var body: some View {
        List(photos) { item in
            HStack {
                Text(item.id, format: .number)
                Spacer()

                AsyncImage(url: URL(string: item.url)!) { imagePhase in
                    switch imagePhase {
                    case .empty:
                        Image(systemName: "ellipsis")
                    case .success(let returnedImage):
                        returnedImage
                            .resizable()
                            .scaledToFit()
                    case .failure:
                        Image(systemName: "xmark.circle")
                            .font(.headline)
                            .foregroundColor(.red)
                    @unknown default:
                        Image(systemName: "ellipsis")
                    }
                }
            }
        }
        .overlay {
            if photos.isEmpty {
                ProgressView()
            }
        }
        .task {
            if photos.isEmpty {
                await WebService().updateDataInDatabase(modelContext: modelContext)
            }
        }
        .refreshable {
            await WebService().updateDataInDatabase(modelContext: modelContext)
        }
    }
}

#Preview {
    do {
        let config = ModelConfiguration(isStoredInMemoryOnly: true)
        let container = try ModelContainer(for: PhotoObject.self, configurations: config)
        let sampleObject = PhotoObject(
            albumId: 1,
            id: 1,
            title: "accusamus beatae ad facilis cum similique qui sunt",
            url: "https://via.placeholder.com/600/92c952",
            thumbnailUrl: "https://via.placeholder.com/150/92c952"
        )
        container.mainContext.insert(sampleObject)

        return ContentView().modelContainer(container)
    } catch {
        fatalError("Failed to create model container")
    }
}
```

이 글이 유익하다고 느끼신다면, 제를 팔로우하는 것, 이 글에 👏 반응을 보내는 것, 또는 공유하여 다른 사람이 더 쉽게 찾을 수 있도록 돕는 것도 고려해주세요."

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

해당 주제에 대해 궁금한 점이 있거나 동일한 작업을 수행하는 다른 방법을 알고 계신다면, 이 게시물에 답글을 달거나 친구에게 공유하여 의견을 얻을 수 있습니다. Native 모바일 개발에 대해 더 배우고 싶다면, 여기에서 작성한 다른 기사들을 확인해보세요: [https://medium.com/@jpmtech](https://medium.com/@jpmtech). Native 모바일 개발로 제작된 앱들을 보고 싶다면, 여기에서 제가 만든 앱들을 확인해보세요: [https://jpmtech.io/apps](https://jpmtech.io/apps). 제 작품을 확인해주셔서 감사합니다!
