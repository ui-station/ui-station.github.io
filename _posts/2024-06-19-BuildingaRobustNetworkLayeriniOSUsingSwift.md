---
title: "iOS에서 Swift를 사용하여 견고한 네트워크 계층 구축하기"
description: ""
coverImage: "/assets/img/2024-06-19-BuildingaRobustNetworkLayeriniOSUsingSwift_0.png"
date: 2024-06-19 10:56
ogImage: 
  url: /assets/img/2024-06-19-BuildingaRobustNetworkLayeriniOSUsingSwift_0.png
tag: Tech
originalTitle: "Building a Robust Network Layer in iOS Using Swift"
link: "https://medium.com/@rohitsainier/building-a-robust-network-layer-in-ios-using-swift-660870e976a9"
---



![이미지](/assets/img/2024-06-19-BuildingaRobustNetworkLayeriniOSUsingSwift_0.png)

현대 iOS 개발에서 API 호출 및 데이터 검색을 효율적이고 안전하게 처리하기 위해 잘 구조화된 네트워크 레이어를 갖는 것이 중요합니다. 이 기사에서는 제공된 코드를 기반으로 Swift로 견고한 네트워크 레이어를 만드는 방법을 안내합니다.

# 개요

우리의 네트워크 레이어는 여러 중요한 구성 요소로 구성됩니다:


<div class="content-ad"></div>

- NetworkError: 다양한 종류의 네트워크 오류를 처리하기 위한 포괄적 인템.
- NetworkRequest: 네트워크 요청에 필요한 속성과 메소드를 정의하는 프로토콜.
- NetworkManager: 네트워크 요청 수행, 응답 해석 및 파일 다운로드 처리를 담당하는 싱글톤 클래스입니다.

또한 이러한 구성 요소를 예시 API 호출 및 파일 다운로드와 함께 사용하는 방법을 보여줄 것입니다.

# 단계 1: 네트워크 오류 정의하기

다양한 네트워크 관련 오류를 깨끗하고 조직된 방식으로 처리하는 데 도움이 되는 NetworkError 열거형을 정의하는 것부터 시작해보겠습니다.

<div class="content-ad"></div>

```js
import Foundation

enum NetworkError: Error {
    case badURL
    case requestFailed(Error)
    case invalidResponse
    case dataNotFound
    case decodingFailed(Error)
    case encodingFailed(Error)
    case notFound
    case internalServerError
    case unknownError(statusCode: Int)
}

struct DecodingError: Error {
    let message: String
}
```

설명:

NetworkError Enum: 이 Enum은 발생할 수 있는 가능한 네트워크 관련 오류를 나열하여 오류 처리를 더 쉽게 할 수 있게 합니다.

- badURL: 유효하지 않은 URL을 나타냄.
- requestFailed: 네트워크 요청 실패를 나타내며 원래 오류를 저장함.
- invalidResponse: 받은 응답이 유효하지 않음을 나타냄.
- dataNotFound: 응답에서 기대하는 데이터를 찾을 수 없음을 나타냄.
- decodingFailed: 응답 데이터를 기대하는 타입으로 디코딩하는 데 실패함을 나타냄.
- encodingFailed: 요청 매개변수 인코딩 실패를 나타냄.
- notFound: 404 오류를 나타냄.
- internalServerError: 500 오류를 나타냄.
- unknownError: 연결된 상태 코드와 함께 알 수 없는 오류를 나타냄.

<div class="content-ad"></div>

표 태그를 Markdown 형식으로 변경하세요.

<div class="content-ad"></div>

네트워크 요청 프로토콜: 모든 네트워크 요청이 따라야 하는 구조를 정의합니다.

- url: 엔드포인트 URL입니다.
- method: HTTP 메서드(GET, POST 등)입니다.
- headers: 요청에 필요한 헤더입니다.
- parameters: 인코딩 가능 프로토콜을 준수하는 요청 매개변수입니다.

HTTPMethod Enum: 요청에 사용되는 다양한 HTTP 메서드를 나타냅니다.

<div class="content-ad"></div>

HTTPHeader Enum: 일반적인 HTTP 헤더 필드를 나타냅니다.

ContentType Enum: HTTP 헤더의 일반적인 콘텐츠 유형을 나타냅니다.

# 단계 3: URLRequest 생성을 위해 NetworkRequest 확장

NetworkRequest 프로토콜을 확장하여 URLRequest 객체를 생성하는 메서드를 포함시킵니다. 이 확장은 HTTP 헤더 및 매개변수 설정을 처리합니다.

<div class="content-ad"></div>

```swift
extension NetworkRequest {
    func urlRequest() throws -> URLRequest {
        guard let url = url else {
            throw NetworkError.badURL
        }
        
        var request = URLRequest(url: url)
        request.httpMethod = method.rawValue
        
        if let headers = headers {
            for (key, value) in headers {
                request.setValue(value, forHTTPHeaderField: key.rawValue)
            }
        }
        
        if let parameters = parameters {
            if method == .get {
                var urlComponents = URLComponents(url: url, resolvingAgainstBaseURL: false)
                let parameterData = try JSONEncoder().encode(parameters)
                let parameterDictionary = try JSONSerialization.jsonObject(with: parameterData, options: []) as? [String: Any]
                urlComponents?.queryItems = parameterDictionary?.map { URLQueryItem(name: $0.key, value: "\($0.value)") }
                request.url = urlComponents?.url
            } else {
                do {
                    let jsonData = try JSONEncoder().encode(parameters)
                    request.httpBody = jsonData
                } catch {
                    throw NetworkError.encodingFailed(error)
                }
            }
        }
        
        return request
    }
}
```

해설:

URLRequest 생성 메소드: NetworkRequest를 URLRequest 객체로 변환합니다.

- 유효한 URL을 확인하고, 그렇지 않으면 badURL 오류를 발생시킵니다.
- HTTP 메소드 설정합니다.
- 제공된 헤더를 설정합니다.
- 매개변수를 인코딩하고 설정합니다:
	- GET 요청의 경우, 매개변수를 쿼리 항목으로 추가합니다.
	- 다른 메소드의 경우, 매개변수를 JSON으로 인코딩하고 요청 본문으로 설정합니다.
- 인코딩에 실패하면 encodingFailed 오류를 발생시킵니다.

<div class="content-ad"></div>

# 단계 4: NetworkManager 구현

NetworkManager는 네트워크 요청을 수행하고 응답을 처리하는 싱글톤 클래스입니다. 이 클래스는 역 호환성을 위해 async/await 및 completion handlers를 모두 지원합니다.

# Async/Await 구현

```js
import Foundation
import UIKit

class NetworkManager {
    static let shared = NetworkManager()
    private let urlSession = URLSession.shared
    
    private init() {}
    
    func perform<T: Decodable>(_ request: NetworkRequest, decodeTo type: T.Type) async throws -> T {
        if #available(iOS 15.0, *) {
            let urlRequest = try request.urlRequest()
            let (data, response) = try await urlSession.data(for: urlRequest)
            try processResponse(response: response)
            return try decodeData(data: data, type: T.self)
        } else {
            return try await withCheckedThrowingContinuation { continuation in
                perform(request, decodeTo: type) { result in
                    switch result {
                    case .success(let data):
                        continuation.resume(returning: data)
                    case .failure(let error):
                        continuation.resume(throwing: error)
                    }
                }
            }
        }
    }
    
    private func decodeData<T: Decodable>(data: Data, type: T.Type) throws -> T {
        do {
            let decodedObject = try JSONDecoder().decode(T.self, from: data)
            return decodedObject
        } catch let decodingError {
            throw NetworkError.decodingFailed(decodingError)
        }
    }
    
    private func processResponse(response: URLResponse?) throws {
        guard let httpResponse = response as? HTTPURLResponse else {
            throw NetworkError.invalidResponse
        }
        
        switch httpResponse.statusCode {
        case 200...299:
            return
        case 404:
            throw NetworkError.notFound
        case 500:
            throw NetworkError.internalServerError
        default:
            throw NetworkError.unknownError(statusCode: httpResponse.statusCode)
        }
    }
    
    func downloadFile(from url: URL) async throws -> URL {
        if #available(iOS 15.0, *) {
            let (localURL, response) = try await urlSession.download(from: url)
            try processResponse(response: response)
            return localURL
        } else {
            return try await withCheckedThrowingContinuation { continuation in
                downloadFile(from: url) { result in
                    switch result {
                    case .success(let localURL):
                        continuation.resume(returning: localURL)
                    case .failure(let error):
                        continuation.resume(throwing: error)
                    }
                }
            }
        }
    }
}
```

<div class="content-ad"></div>

설명:

NetworkManager Singleton: 네트워크 요청을 수행하기 위한 단일 인스턴스를 제공합니다.

- shared: 싱글톤 인스턴스입니다.
- urlSession: 네트워크 작업을 위한 공유 URLSession 인스턴스입니다.
- init(): 여러 인스턴스 생성을 방지하기 위한 비공개 이니셜라이저입니다.

perform 메서드:

<div class="content-ad"></div>

- iOS 15.0 이상인 경우:
  - NetworkRequest에서 URLRequest를 생성합니다.
  - 요청을 비동기적으로 처리합니다.
  - 응답을 처리합니다.
  - 응답 데이터를 지정된 유형으로 디코딩합니다.
- 이전 iOS 버전의 경우:
  - 완료 핸들러를 사용하여 요청을 처리하는 데 continuation을 사용합니다.

decodeData 메서드: 데이터를 지정된 유형으로 디코딩하고 디코딩에 실패하면 decodingFailed 오류를 throw합니다.

processResponse 메서드: HTTP 응답을 유효성 검사하고 상태 코드에 따라 적절한 오류를 throw합니다.

downloadFile 메서드: 지정된 URL에서 파일을 다운로드하며 역호환성을 위해 async/await 및 완료 핸들러를 모두 지원합니다.

<div class="content-ad"></div>

# 완료 핸들러 구현

오래된 iOS 버전을 위해 완료 핸들러를 사용하여 네트워크 요청을 구현합니다.

```swift
extension NetworkManager {
    private func perform<T: Decodable>(_ request: NetworkRequest, decodeTo type: T.Type, completion: @escaping (Result<T, NetworkError>) -> Void) {
        do {
            let urlRequest = try request.urlRequest()
            urlSession.dataTask(with: urlRequest) { data, response, error in
                if let error = error {
                    completion(.failure(.requestFailed(error)))
                    return
                }
                
                guard let data = data else {
                    completion(.failure(.dataNotFound))
                    return
                }
                
                do {
                    try self.processResponse(response: response)
                    let decodedObject = try self.decodeData(data: data, type: T.self)
                    completion(.success(decodedObject))
                } catch {
                    completion(.failure(error as? NetworkError ?? .invalidResponse))
                }
            }.resume()
        } catch {
            completion(.failure(error as? NetworkError ?? .invalidResponse))
        }
    }
    
    private func downloadFile(from url: URL, completion: @escaping (Result<URL, NetworkError>) -> Void) {
        urlSession.downloadTask(with: url) { localURL, response, error in
            if let error = error {
                completion(.failure(.requestFailed(error)))
                return
            }
            
            guard let localURL = localURL else {
                completion(.failure(.dataNotFound))
                return
            }
            
            do {
                try self.processResponse(response: response)
                completion(.success(localURL))
            } catch {
                completion(.failure(error as? NetworkError ?? .invalidResponse))
            }
        }.resume()
    }
}
```

설명:

<div class="content-ad"></div>

완료 핸들러를 사용하여 perform 메소드:

- URLRequest를 생성하고 URLSession.dataTask를 사용하여 요청을 수행합니다.
- 오류를 처리하고 응답 유효성을 확인합니다.
- 응답 데이터를 디코딩하고 결과로 완료 핸들러를 호출합니다.

완료 핸들러를 사용하여 downloadFile 메소드:

- URLSession.downloadTask를 사용하여 파일을 다운로드합니다.
- 오류를 처리하고 응답 유효성을 확인합니다.
- 다운로드된 파일의 로컬 URL로 완료 핸들러를 호출합니다.

<div class="content-ad"></div>

# 단계 5: 이미지 다운로드 및 캐싱

이미지 다운로드를 처리하고 선택적으로 캐싱하는 NetworkManager를 확장합니다.

```js
extension NetworkManager {
    func downloadImage(from url: URL, cacheEnabled: Bool = true) async -> Result<UIImage, NetworkError> {
        do {
            if cacheEnabled, let cachedImage = try getCachedImage(for: url) {
                return .success(cachedImage)
            }
            
            let localURL = try await NetworkManager.shared.downloadFile(from: url)
            let imageData = try Data(contentsOf: localURL)
            if let image = UIImage(data: imageData) {
                if cacheEnabled {
                    cacheImage(imageData, for: url)
                }
                return .success(image)
            } else {
                return .failure(.decodingFailed(DecodingError(message: "Failed to decode image data")))
            }
        } catch {
            return .failure(error as? NetworkError ?? .invalidResponse)
        }
    }
    
    private func cacheImage(_ imageData: Data, for url: URL) {
        let cachedResponse = CachedURLResponse(response: HTTPURLResponse(url: url, statusCode: 200, httpVersion: nil, headerFields: nil)!, data: imageData)
        URLCache.shared.storeCachedResponse(cachedResponse, for: URLRequest(url: url))
        checkAndClearCache()
    }
    
    private func checkAndClearCache() {
        let cacheSize = URLCache.shared.currentDiskUsage
        let cacheLimit: Int = 100 * 1024 * 1024 // 100 MB
        if cacheSize > cacheLimit {
            URLCache.shared.removeAllCachedResponses()
        }
    }
    
    private func getCachedImage(for url: URL) throws -> UIImage? {
        if let cachedResponse = URLCache.shared.cachedResponse(for: URLRequest(url: url)),
           let image = UIImage(data: cachedResponse.data) {
            return image
        }
        return nil
    }
}
```

설명:

<div class="content-ad"></div>

다음은 친절한 톤으로 번역한 내용입니다.


downloadImage 메서드: URL에서 이미지를 다운로드하며 선택적으로 캐싱합니다.

- 캐싱이 활성화된 경우 먼저 캐시를 확인합니다.
- async/await 또는 이전 iOS 버전을 위한 완료 핸들러를 사용하여 다운로드를 수행합니다.
- 응답을 처리하고 이미지 데이터를 디코딩합니다.
- 캐싱이 활성화된 경우 이미지를 캐시에 저장합니다.

cacheImage 메서드: 이미지를 캐시에 저장합니다.

loadImageFromCache 메서드: 캐시에서 이미지를 로드합니다.


<div class="content-ad"></div>

# 예시 사용법

# API 호출 요청

```js
struct ExampleAPIRequest: NetworkRequest {
    var url: URL? {
        return URL(string: "https://api.example.com/data")
    }
    var method: HTTPMethod {
        return .get
    }
    var headers: [HTTPHeader: String]? {
        return [.contentType: ContentType.json.rawValue]
    }
    var parameters: Encodable? {
        return ExampleParameters(param1: "value1", param2: "value2")
    }
}

struct ExampleParameters: Encodable {
    let param1: String
    let param2: String
}

struct ExampleData: Decodable {
    let id: Int
    let name: String
}

func fetchExampleData() async {
    let request = ExampleAPIRequest()
    
    if #available(iOS 15.0, *) {
        do {
            let data: ExampleData = try await NetworkManager.shared.perform(request, decodeTo: ExampleData.self)
            print("데이터 가져오기 성공: \(data)")
        } catch {
            print("데이터 가져오기 실패: \(error)")
        }
    } else {
        NetworkManager.shared.perform(request, decodeTo: ExampleData.self) { result in
            switch result {
            case .success(let data):
                print("데이터 가져오기 성공: \(data)")
            case .failure(let error):
                print("데이터 가져오기 실패: \(error)")
            }
        }
    }
}
```

설명:

<div class="content-ad"></div>

`ExampleAPIRequest Struct`: 네트워크 요청을 나타내며 `NetworkRequest` 프로토콜을 준수하는 구조체입니다.

- URL, HTTP 메서드, 헤더 및 매개변수를 지정합니다.

`ExampleParameters Struct`: `Encodable`을 준수하는 요청 매개변수를 나타내는 구조체입니다.

`ExampleData Struct`: `Decodable`을 준수하는 응답 데이터를 나타내는 구조체입니다.

<div class="content-ad"></div>

`table` 태그를 Markdown 형식으로 변경해주세요.


fetchExampleData 함수: async/await를 활용하여 요청을 수행하고 응답을 처리하는 방법을 보여줍니다.

# 이미지 다운로드

```js
import SwiftUI

struct HomeView: View {
    @State private var image: UIImage? = nil
    
    var body: some View {
        VStack {
            if let image = image {
                Image(uiImage: image)
                    .resizable()
                    .aspectRatio(contentMode: .fit)
                    .frame(width: 200, height: 200)
            } else {
                ProgressView()
            }
        }
        .onAppear {
            let imageURL = URL(string: "https://picsum.photos/200/200")!
            Task {
                let result = await NetworkManager.shared.downloadImage(from: imageURL, cacheEnabled: false)
                switch result {
                case .success(let success):
                    self.image = success
                case .failure(_):
                    self.image = nil
                }
            }
        }
    }
}

struct HomeView_Previews: PreviewProvider {
    static var previews: some View {
        HomeView()
    }
}
```

설명:


<div class="content-ad"></div>

HomeView Struct는 URL에서 다운로드된 이미지를 표시하는 SwiftUI 뷰입니다.

- 이미지 상태를 관리하기 위해 @State를 사용합니다.
- 이미지를 로드하는 동안 ProgressView를 표시합니다.
- appear될 때 async/await를 사용하여 이미지를 다운로드합니다.
- 다운로드 결과를 처리합니다.

# 파일 다운로드

```js
import Foundation

func downloadExampleFile() async {
    let fileURL = URL(string: "https://example.com/file.zip")!
    
    if #available(iOS 15.0, *) {
        do {
            let localURL = try await NetworkManager.shared.downloadFile(from: fileURL)
            print("다운로드한 파일 경로: \(localURL)")
        } catch {
            print("파일 다운로드 실패: \(error)")
        }
    } else {
        NetworkManager.shared.downloadFile(from: fileURL) { result in
            switch result {
            case .success(let localURL):
                print("다운로드한 파일 경로: \(localURL)")
            case .failure(let error):
                print("파일 다운로드 실패: \(error)")
            }
        }
    }
}
```

<div class="content-ad"></div>

설명:

downloadExampleFile 함수: async/await를 사용하여 파일을 다운로드하는 방법을 보여줍니다.

- 이전 iOS 버전을 위한 async/await 및 완료 핸들러를 모두 지원합니다.

# 결론

<div class="content-ad"></div>

이러한 네트워크 계층이 구현되면 iOS 애플리케이션이 API 요청을 처리하고 응답을 처리하며 파일 다운로드를 관리하는 데 더 잘 준비될 것입니다. 이 구조화된 접근 방식은 코드베이스를 더 깔끔하게 만들 뿐만 아니라 유지 보수가 용이하고 확장성이 뛰어난 장점을 제공합니다.