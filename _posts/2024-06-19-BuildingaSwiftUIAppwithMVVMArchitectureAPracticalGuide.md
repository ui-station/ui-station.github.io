---
title: "MVVM 아키텍처로 SwiftUI 앱 구축하기 실전 안내"
description: ""
coverImage: "/assets/img/2024-06-19-BuildingaSwiftUIAppwithMVVMArchitectureAPracticalGuide_0.png"
date: 2024-06-19 10:58
ogImage:
  url: /assets/img/2024-06-19-BuildingaSwiftUIAppwithMVVMArchitectureAPracticalGuide_0.png
tag: Tech
originalTitle: "Building a SwiftUI App with MVVM Architecture: A Practical Guide"
link: "https://medium.com/@bhavesh.sachala/building-a-swiftui-app-with-mvvm-architecture-a-practical-guide-4829c65f985d"
---

이 튜토리얼에서는 MVVM(Model-View-ViewModel) 아키텍처를 사용하여 간단한 SwiftUI 애플리케이션을 만드는 방법을 안내합니다. 데이터 처리 및 네트워킹을 위해 Combine 프레임워크를 활용할 것입니다. 이 예제에서는 REST API에서 데이터를 가져오고 게시하는 방법을 보여줍니다.

# 개요

다음과 같은 간단한 앱을 만들 것입니다:

- 서버에서 게시물 목록을 가져오기.
- SwiftUI 뷰에서 게시물을 표시하기.
- 새 게시물을 생성하기.

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

# 프로젝트 설정

먼저 Xcode에서 새로운 SwiftUI 프로젝트를 생성하세요. 그런 다음, 더 나은 구성을 위해 다음과 같은 폴더를 설정합니다:

- Models
- ViewModels
- Views
- Services

# Models

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

"Post" 모델을 시작해봅시다. 이 모델은 Identifiable과 Codable을 준수합니다.

```swift
// Models/Post.swift

import Foundation

struct Post: Identifiable, Codable {
    let id: Int
    let title: String
    let body: String
}
```

# 서비스

네트워크 요청을 처리하기 위한 서비스 레이어가 필요합니다. ApiService와 ApiEndpoints를 만들 것입니다.

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

// Services/ApiEndpoints.swift

import Foundation

class ApiEndpoints {
static let baseURL = "https://jsonplaceholder.typicode.com"

    static var posts: String { return "\(baseURL)/posts" }
    static func post(id: Int) -> String { return "\(baseURL)/posts/\(id)" }

}

// Services/ApiService.swift

import Foundation
import Combine

class ApiService {
static let shared = ApiService()
private init() {}

    private func makeRequest<T: Decodable>(url: String, method: String, body: Data? = nil) -> AnyPublisher<T, Error> {
        guard let url = URL(string: url) else {
            fatalError("Invalid URL")
        }

        var request = URLRequest(url: url)
        request.httpMethod = method
        if let body = body {
            request.httpBody = body
            request.setValue("application/json", forHTTPHeaderField: "Content-Type")
        }

        return URLSession.shared.dataTaskPublisher(for: request)
            .tryMap { output in
                guard let response = output.response as? HTTPURLResponse,
                      response.statusCode >= 200 && response.statusCode < 300 else {
                    throw URLError(.badServerResponse)
                }
                return output.data
            }
            .decode(type: T.self, decoder: JSONDecoder())
            .eraseToAnyPublisher()
    }

    func get<T: Decodable>(url: String) -> AnyPublisher<T, Error> {
        return makeRequest(url: url, method: "GET")
    }

    func post<T: Decodable, U: Encodable>(url: String, body: U) -> AnyPublisher<T, Error> {
        guard let bodyData = try? JSONEncoder().encode(body) else {
            fatalError("Invalid body data")
        }
        return makeRequest(url: url, method: "POST", body: bodyData)
    }

    func delete<T: Decodable>(url: String) -> AnyPublisher<T, Error> {
        return makeRequest(url: url, method: "DELETE")
    }

}

# ViewModels

이제, PostViewModel을 만들어서 게시물을 가져오고 만들어봅시다.

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
// ViewModels/PostViewModel.swift

import Foundation
import Combine

class PostViewModel: ObservableObject {
    @Published var posts: [Post] = []
    @Published var isLoading = false
    @Published var errorMessage: String? = nil

    private var cancellables = Set<AnyCancellable>()
    private let apiService = ApiService.shared

    func fetchPosts() {
        self.isLoading = true
        self.errorMessage = nil

        apiService.get(url: ApiEndpoints.posts)
            .receive(on: DispatchQueue.main)
            .sink(receiveCompletion: { [weak self] completion in
                self?.isLoading = false
                switch completion {
                case .failure(let error):
                    self?.errorMessage = error.localizedDescription
                case .finished:
                    break
                }
            }, receiveValue: { [weak self] posts in
                self?.posts = posts
            })
            .store(in: &cancellables)
    }

    func createPost() {
        let newPost = Post(id: 101, title: "New Post", body: "This is a new post.")

        apiService.post(url: ApiEndpoints.posts, body: newPost)
            .receive(on: DispatchQueue.main)
            .sink(receiveCompletion: { completion in
                switch completion {
                case .failure(let error):
                    print("Error: \(error.localizedDescription)")
                case .finished:
                    print("Post created successfully")
                }
            }, receiveValue: { (post: Post) in
                print("Created post: \(post)")
            })
            .store(in: &cancellables)
    }
}
```

# Views

마지막으로, SwiftUI 뷰를 생성하여 게시물을 표시하고 사용자 상호작용을 처리합니다.

```swift
// Views/PostListView.swift

import SwiftUI

struct PostListView: View {
    @StateObject private var viewModel = PostViewModel()

    var body: some View {
        NavigationView {
            VStack {
                if viewModel.isLoading {
                    ProgressView("로딩 중...")
                } else if let errorMessage = viewModel.errorMessage {
                    Text("오류: \(errorMessage)")
                } else {
                    List(viewModel.posts) { post in
                        VStack(alignment: .leading) {
                            Text(post.title)
                                .font(.headline)
                            Text(post.body)
                                .font(.subheadline)
                        }
                    }
                }
            }
            .navigationTitle("게시물")
            .onAppear {
                viewModel.fetchPosts()
            }
            .toolbar {
                Button(action: {
                    viewModel.createPost()
                }) {
                    Image(systemName: "plus")
                }
            }
        }
    }
}

struct PostListView_Previews: PreviewProvider {
    static var previews: some View {
        PostListView()
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

# 모두 함께 적용하기

모두가 동작하는 것을 보려면 PostListView를 App 구조체의 진입점으로 설정하세요.

```js
// demoApp.swift

import SwiftUI

@main
struct demoApp: App {
    var body: some Scene {
        WindowGroup {
            PostListView()
        }
    }
}
```

이 튜토리얼에서는 SwiftUI 애플리케이션에서 간단한 MVVM 아키텍처를 구현했습니다. 이 구조는 관심사를 분리하여 코드를 보다 깨끗하고 유지보수하기 쉽게 만들어주며, Combine과 같은 Swift의 강력한 기능을 활용하여 비동기 데이터 스트림 처리에도 도움이 됩니다.

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

이 안내를 따르면 SwiftUI 프로젝트에서 MVVM을 설정하고 사용하는 방법에 대해 잘 이해할 수 있을 것입니다. 코딩을 즐기세요!
