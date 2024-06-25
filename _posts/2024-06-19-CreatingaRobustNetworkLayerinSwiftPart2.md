---
title: "Swift로 강력한 네트워크 레이어 만들기 파트 2"
description: ""
coverImage: "/assets/img/2024-06-19-CreatingaRobustNetworkLayerinSwiftPart2_0.png"
date: 2024-06-19 10:54
ogImage:
  url: /assets/img/2024-06-19-CreatingaRobustNetworkLayerinSwiftPart2_0.png
tag: Tech
originalTitle: "Creating a Robust Network Layer in Swift: Part 2"
link: "https://medium.com/@rohitsainier/creating-a-robust-network-layer-in-swift-part-2-9839ad871cf9"
---

<img src="/assets/img/2024-06-19-CreatingaRobustNetworkLayerinSwiftPart2_0.png" />

현대 iOS 개발에서는 API 호출 및 데이터 검색을 효율적이고 안전하게 처리하는 데 잘 구조화된 네트워크 레이어가 필수적입니다. 본 후속 기사에서는 SOLID 원칙을 준수하고 클래스 기반에서 구조체 기반으로 전환하여 네트워크 레이어를 개선하고 리팩터링한 내용을 안내해 드릴 것입니다. 이를 통해 코드베이스를 더 잘 유지보수할 수 있고 확장 가능하도록 보장할 것입니다.

# 개요

저희가 업그레이드한 네트워크 레이어에는 다음 구성요소가 포함되어 있습니다:

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

- NetworkError: 다양한 네트워크 오류를 처리하기 위한 포괄적인 열거형입니다.
- NetworkRequest: 네트워크 요청을 위한 필수 속성 및 메서드를 정의하는 프로토콜입니다.
- HTTPResponseHandler: HTTP 응답을 처리하고 디코딩하는 프로토콜입니다.
- NetworkEngine: 네트워크 요청 수행, 로깅 및 응답 처리를 담당하는 구조체입니다.

이러한 구성 요소를 예제 API 호출과 함께 사용하는 방법을 보여드리겠습니다.

# 단계 1: 네트워크 오류 정의

먼저 NetworkError 열거형을 정의하여 다양한 네트워크 관련 오류를 깔끔하고 조직적으로 처리합니다.

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
import Foundation

enum NetworkError: Error {
    case badURL
    case requestFailed(Error)
    case invalidResponse
    case dataNotFound
    case decodingFailed(Error)
    case encodingFailed(Error)
    case notFound
    case timeout
    case internalServerError
    case unknownError(statusCode: Int)
}

struct DecodingError: Error {
    let message: String
}
```

해설:

- NetworkError: 이 열거형은 네트워크 작업 중 발생할 수 있는 다양한 유형의 오류를 캡슐화합니다. 각 case는 특정 오류 시나리오를 나타냅니다:
  - badURL: URL이 잘못되었음을 나타냅니다.
  - requestFailed: 요청을 만드는 데 실패한 것을 나타내며 내장된 오류를 캡슐화합니다.
  - invalidResponse: 응답이 유효하지 않음을 나타냅니다.
  - dataNotFound: 응답에서 데이터를 찾을 수 없음을 신호합니다.
  - decodingFailed: 응답 데이터를 해독하는 데 실패한 것을 나타내며 해독 오류를 캡슐화합니다.
  - encodingFailed: 요청 매개변수를 인코딩하는 데 실패한 것을 나타내며 인코딩 오류를 캡슐화합니다.
  - notFound: 요청한 리소스를 찾을 수 없음을 나타냅니다 (HTTP 404).
  - timeout: 타임아웃 오류를 나타냅니다.
  - internalServerError: 서버 오류임을 나타냅니다 (HTTP 500).
  - unknownError: 다른 HTTP 상태 코드 오류를 나타내며 상태 코드를 캡슐화합니다.

DecodingError: 해독 중 추가 오류 정보를 제공하기 위한 사용자 정의 오류 구조체입니다.

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

# 단계 2: NetworkRequest 프로토콜 생성하기

NetworkRequest 프로토콜은 URL, HTTP 메소드, 헤더, 매개변수 및 타임아웃 간 필수적인 속성을 정의합니다.

```js
import Foundation

enum HTTPMethod: String {
    case get = "GET"
    case post = "POST"
    case put = "PUT"
    case delete = "DELETE"
}

enum HTTPHeader: String {
    case contentType = "Content-Type"
    case authorization = "Authorization"
}

enum ContentType: String {
    case json = "application/json"
    case xml = "application/xml"
    case formUrlEncoded = "application/x-www-form-urlencoded"
}

protocol NetworkRequest {
    var url: URL? { get }
    var method: HTTPMethod { get }
    var headers: [HTTPHeader: String]? { get }
    var parameters: Encodable? { get }
    var timeoutInterval: TimeInterval { get }
}

extension NetworkRequest {
    var timeoutInterval: TimeInterval {
        return 30
    }

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

설명:

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

- HTTPMethod: 우리가 사용할 HTTP 메소드(GET, POST, PUT, DELETE)를 나타내는 enum입니다.
- HTTPHeader: 일반적인 HTTP 헤더를 위한 enum입니다.
- ContentType: HTTP 헤더에서 사용되는 일반적인 콘텐츠 유형을 위한 enum입니다.
- NetworkRequest: 네트워크 요청에 필요한 속성을 정의하는 프로토콜입니다.
  - url: 요청을 위한 URL입니다.
  - method: 요청을 위한 HTTP 메소드입니다.
  - headers: 요청에 대한 선택적 헤더입니다.
  - parameters: 인코딩 가능한 형식을 준수하는 요청에 대한 선택적 매개변수입니다.
  - timeoutInterval: 요청의 타임아웃 간격입니다.
- 프로토콜 확장은 timeoutInterval의 기본 구현과 속성에서 URLRequest를 만들기 위한 urlRequest() 메소드를 제공합니다.
  - 만약 메소드가 GET이면, 매개변수는 URL에 쿼리 아이템으로 추가됩니다.
  - 다른 메소드의 경우, 매개변수는 JSON으로 인코딩되어 요청 바디에 추가됩니다.
  - 헤더는 요청에 추가됩니다.

# 단계 3: HTTPResponseHandler 구현하기

HTTPResponseHandler 프로토콜은 HTTP 응답을 처리하고 디코딩하기 위한 메소드를 정의합니다.

```js
import Foundation

public protocol HTTPResponseHandler {
    func handleStatusCode(response: URLResponse?) throws
    func decode<T: Decodable>(data: Data, to type: T.Type) throws -> T
    func extractETag(from response: URLResponse?) -> String?
}

extension HTTPResponseHandler {
    public func decode<T: Decodable>(data: Data, to type: T.Type) throws -> T {
        do {
            let decodedObject = try JSONDecoder().decode(T.self, from: data)
            return decodedObject
        } catch let decodingError {
            throw NetworkError.decodingFailed(decodingError)
        }
    }

    public func handleStatusCode(response: URLResponse?) throws {
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

    public func extractETag(from response: URLResponse?) -> String? {
        guard let httpResponse = response as? HTTPURLResponse else {
            return nil
        }
        return httpResponse.allHeaderFields["ETag"] as? String
    }
}

public struct DefaultHTTPResponseHandler: HTTPResponseHandler {
    public init() {}
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

설명:

- HTTPResponseHandler: HTTP 응답을 처리하고 해독하는 메서드를 정의하는 프로토콜입니다.
  - handleStatusCode(response:): HTTP 상태 코드를 확인하고 적절한 오류를 throw합니다.
  - decode(data:to:): Decodable을 준수하는 특정 타입으로 데이터를 디코딩합니다.
  - extractETag(from:): 응답 헤더에서 ETag을 추출합니다.

이 확장(extension)은 다음을 제공합니다:

- decode(data:to:)은 데이터를 디코딩하기 위해 JSONDecoder를 사용하고 실패할 경우 NetworkError.decodingFailed를 throw합니다.
- handleStatusCode(response:)은 HTTP 상태 코드를 확인하고 공통 오류(404, 500)에 대해 특정 NetworkError 케이스를 throw하며, 다른 상태 코드에 대해서는 알 수 없는 오류를 throw합니다.
- extractETag(from:)은 응답 헤더에서 ETag을 가져옵니다.
- DefaultHTTPResponseHandler: HTTPResponseHandler의 구체적인 구현체입니다.

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

# 단계 4: NetworkEngine 구현

NetworkEngine 구조체는 네트워크 요청을 수행하고 응답을 처리하는 역할을 담당합니다.

```js
import Foundation

struct NetworkRequestContext<T: Decodable> {
    let request: NetworkRequest
    let type: T.Type
    let completion: (Result<T, NetworkError>) -> Void
    let requestInvokeTime: Date
}

protocol NetworkEngineAdapter {
    func invokeEngine<T: Decodable>(_ request: NetworkRequest, decodeTo type: T.Type, completion: @escaping (Result<T, NetworkError>) -> Void)
}

public struct NetworkEngine {
    private let urlSession: URLSession
    private let logger: Logger
    private let responseHandler: HTTPResponseHandler

    public init(urlSession: URLSession = .shared,
                logger: Logger = DefaultLogger(),
                responseHandler: HTTPResponseHandler = DefaultHTTPResponseHandler()) {
        self.urlSession = urlSession
        self.logger = logger
        self.responseHandler = responseHandler
    }
}

extension NetworkEngine: NetworkEngineAdapter {
    func invokeEngine<T>(_ request: NetworkRequest, decodeTo type: T.Type, completion: @escaping (Result<T, NetworkError>) -> Void) where T: Decodable {
        let requestInvokeTime = Date()
        let context = NetworkRequestContext(request: request, type: type, completion: completion, requestInvokeTime: requestInvokeTime)
        fetch(context)
    }

    private func fetch<T>(_ context: NetworkRequestContext<T>) where T: Decodable {
        do {
            var urlRequest = try context.request.urlRequest()
            urlRequest.timeoutInterval = context.request.timeoutInterval

            urlSession.dataTask(with: urlRequest) { data, response, error in
                let requestFinishTime = Date()
                let duration = requestFinishTime.timeIntervalSince(context.requestInvokeTime)

                logger.logMetrics(startTime: context.requestInvokeTime, endTime: requestFinishTime, duration: duration, request: urlRequest)

                if let error = error {
                    context.completion(.failure(.requestFailed(error)))
                    return
                }

                guard let data = data else {
                    context.completion(.failure(.dataNotFound))
                    return
                }

                do {
                    try responseHandler.handleStatusCode(response: response)
                    let decodedObject = try responseHandler.decode(data: data, to: context.type)
                    context.completion(.success(decodedObject))
                } catch let error as NetworkError {
                    context.completion(.failure(error))
                } catch {
                    context.completion(.failure(.unknownError(statusCode: (response as? HTTPURLResponse)?.statusCode ?? -1)))
                }
            }.resume()
        } catch {
            context.completion(.failure(.requestFailed(error)))
        }
    }
}
```

설명:

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

- NetworkRequestContext: 네트워크 요청의 컨텍스트를 캡슐화하는 구조체로, 요청 자체, 디코딩할 유형, 완료 핸들러, 그리고 요청이 호출된 시간을 포함합니다.
- NetworkEngineAdapter: 네트워크 엔진을 요청과 함께 호출하고, 디코딩할 유형을 지정하며 완료 핸들러를 제공하는 메소드를 정의하는 프로토콜입니다.
- NetworkEngine: 네트워크 작업을 수행하는 주요 구조체입니다:
  - urlSession: 요청을 만드는 데 사용되는 URLSession 인스턴스입니다.
  - logger: 메트릭 및 오류를 기록하기 위한 로거입니다.
  - responseHandler: 응답 처리를 위한 HTTPResponseHandler의 인스턴스입니다.
  - init: NetworkEngine을 urlSession, logger 및 responseHandler에 대한 옵션 매개변수로 초기화합니다.
- invokeEngine 메소드는 NetworkRequestContext를 생성하고 fetch 메소드를 호출합니다.

fetch 메소드:

- NetworkRequest로부터 URLRequest를 만들려고 시도합니다.
- 타임아웃 간격을 설정합니다.
- urlSession.dataTask를 사용하여 네트워크 요청을 수행합니다.
- 요청에 대한 메트릭을 기록합니다.
- 오류, 누락된 데이터 및 상태 코드를 처리합니다.
- 데이터를 디코딩하고 결과와 함께 완료 핸들러를 호출합니다.

# 단계 5: 네트워크 레이어 사용하기

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

이 예시는 iOS에서 NetworkEngine을 사용하는 방법을 보여줍니다.

## 네트워크 요청 정의하기

먼저, NetworkRequest 프로토콜을 준수하는 PostService enum을 정의합니다. 이 enum은 네트워크 요청의 세부 사항을 캡슐화합니다.

```swift
import Foundation

enum PostService {
    case fetchPosts
}

extension PostService: NetworkRequest {
    var url: URL? {
        switch self {
        case .fetchPosts:
            return URL(string: "https://2e84f9d6-0dcb-4b93-9238-8b272604b4c1.mock.pstmn.io/v1/posts")
        }
    }

    var method: HTTPMethod {
        switch self {
        case .fetchPosts:
            return .get
        }
    }

    var headers: [HTTPHeader : String]? {
        return [.contentType: ContentType.json.rawValue]
    }

    var parameters: (any Encodable)? {
        switch self {
        case .fetchPosts:
            return nil
        }
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

여기서 PostService는 URL, HTTP 메서드, 헤더 및 요청에 대한 매개변수를 정의하는 단일 case fetchPosts를 갖습니다. 필요에 따라 더 추가할 수 있어요.

## Repository 생성

다음으로, NetworkEngine를 사용하여 요청을 실행할 repository 클래스를 만듭니다. 이 repository는 게시물을 가져오는 작업을 처리할 거에요:

[연결](/assets/img/2024-06-19-CreatingaRobustNetworkLayerinSwiftPart2_1.png)

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
protocol PostsListRepositoryProtocol {
  func fetchPostsList<T: Decodable>(with request: NetworkRequest, responseType: T.Type, completion: @escaping (Result<T, NetworkError>) -> Void)
}

final class DefaultPostsListRepository: PostsListRepositoryProtocol {
    private let engine: NetworkEngine

    init(engine: NetworkEngine = NetworkEngine()) {
        self.engine = engine
    }

    func fetchPostsList<T>(with request: NetworkRequest, responseType: T.Type, completion: @escaping (Result<T, NetworkError>) -> Void) where T : Decodable {
        engine.invokeEngine(request, decodeTo: responseType, completion: completion)
    }
}
```

이 저장소에서 fetchPostsList 메서드는 NetworkRequest와 응답을 디코딩할 responseType을 사용합니다. NetworkEngine을 사용하여 요청을 실행하고 결과를 전달하기 위해 완료 핸들러를 호출합니다. 이 저장소를 직접 ViewModel이나 보기 UseCase에 사용할 수 있습니다.

# 결론

이 코드베이스의 네트워크 레이어 디자인은 모듈화, 확장 가능성, 견고한 오류 처리, 테스트 용이성 및 성능 모니터링을 강조하여 확장 가능한 애플리케이션에 적합합니다. 이러한 특성들은 유지 보수성과 신뢰성을 저해하지 않고 앱이 기능과 복잡성을 향상시킬 수 있음을 보장합니다.

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

더 많은 통찰과 업데이트를 원하시면 LinkedIn에서 저를 팔로우해주세요.
