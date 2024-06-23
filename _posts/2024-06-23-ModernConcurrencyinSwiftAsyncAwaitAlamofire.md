---
title: "Swift의 모던 동시성 프로그래밍 Async Await와 Alamofire 활용 방법"
description: ""
coverImage: "/assets/img/2024-06-23-ModernConcurrencyinSwiftAsyncAwaitAlamofire_0.png"
date: 2024-06-23 21:26
ogImage: 
  url: /assets/img/2024-06-23-ModernConcurrencyinSwiftAsyncAwaitAlamofire_0.png
tag: Tech
originalTitle: "Modern Concurrency in Swift: Async Await + Alamofire"
link: "https://medium.com/nicholausadi/modern-concurrency-in-swift-async-await-alamofire-2846379cc621"
---



![이미지](/assets/img/2024-06-23-ModernConcurrencyinSwiftAsyncAwaitAlamofire_0.png)

안녕하세요! 모두 다시 만나서 반가워요! 이번 달에는 async/await에 대해 배운 내용을 Alamofire 네트워크 라이브러리에 적용해볼 거에요. 이 시리즈의 첫 번째 부분을 아직 읽지 못했다면, 여기서 잠시 멈추시고 읽어보시는 걸 강력히 추천드려요. 이렇게 하면 속도를 낼 수 있어요.

# Alamofire란 무엇인가요?

Alamofire는 iOS 개발 커뮤니티에서 널리 사용되는 네트워크 라이브러리로, 네트워크 요청을 처리하는 우아한 솔루션을 제공해요. 아마도 이미 Alamofire와 iOS 개발에서의 사용법에 익숙하실 거예요.

<div class="content-ad"></div>

# Alamofire을 사용한 Async-Await 구현

첫 번째 부분에서 배운 개념들을 Alamofire로 통합하고, 작업 실행 및 오류 처리에 초점을 맞춥니다. 또한, 액터가 어떻게 병렬성 관리를 향상시킬 수 있는지 살펴볼 것입니다.

## 네트워크 매니저 생성

다음은 Alamofire를 사용하여 API 요청을 초기화하는 기본 코드 스니펫입니다:

<div class="content-ad"></div>

```js
// Alamofire를 사용하여 API 요청을 수행하는 코드 조각입니다.
class NetworkManager {
    func request<T: Decodable>(
        method: HTTPMethod,
        url: String,
        headers: [String: String],
        params: Parameters?,
        of type: T.Type,
        completion: @escaping (Result<T, Error>) -> Void
    ) {
        // 인코딩 설정
        var encoding: ParameterEncoding = JSONEncoding.default
        switch method {
        case .post:
            encoding = JSONEncoding.default
        case .get:
            encoding = URLEncoding.default
        default:
            encoding = JSONEncoding.default
        }

        AF.request(
            url,
            method: method,
            parameters: params,
            encoding: encoding,
            headers: HTTPHeaders(headers)
        ).responseDecodable(of: type) { response in
            switch response.result {
            case let .success(data):
                completion(.success(data))

            case let .failure(error):
                completion(.failure(error))
            }
        }
    }
}
```

이제 이 함수에 async-await을 적용해 보겠습니다:

```js
// Async-await을 사용하여 API 요청을 수행하는 코드 조각입니다.
class NetworkManager {
    func request<T: Decodable>(
        method: HTTPMethod,
        url: String,
        headers: [String: String],
        params: Parameters?,
        of type: T.Type
    ) async throws -> T {
        // 인코딩 설정
        var encoding: ParameterEncoding = JSONEncoding.default
        switch method {
        case .post:
            encoding = JSONEncoding.default
        case .get:
            encoding = URLEncoding.default
        default:
            encoding = JSONEncoding.default
        }

        // 반드시 continuation을 한 번만 재개해야 합니다.
        return try await withCheckedThrowingContinuation { continuation in
            AF.request(
                url,
                method: method,
                parameters: params,
                encoding: encoding,
                headers: HTTPHeaders(headers)
            ).responseDecodable(of: type) { response in
                switch response.result {
                case let .success(data):
                    continuation.resume(returning: data)

                case let .failure(error):
                    continuation.resume(throwing: error)
                }
            }
        }
    }
}
```

하지만 여기서 멈추지 않고, 더 나아가서 actor를 도입하여 접근 방식을 더욱 세련되게 만들 수 있습니다.


<div class="content-ad"></div>

## 배우자들 소개

배우들은 여러 스레드 간에 안전하게 상태를 공유하기 위해 사용할 수 있는 동시적인 객체 유형입니다. 배우들은 코드를 연속적으로 실행한다는 것이 보장되어 있어서, 동시적인 응용 프로그램에서 공유 상태를 관리하는 데 이상적입니다.

async/await를 사용하여 배우들을 사용하려면, 간단히 배우를 async로 선언하고, 배우의 async 메서드를 호출하기 위해 await를 사용하면 됩니다.

```js
// 배우 사용을 보여주는 코드 스니펫
배우 NetworkManager {
    func request<T: Decodable>(
        method: HTTPMethod,
        url: String,
        headers: [String: String],
        params: Parameters?,
        of type: T.Type
    ) async throws -> T {
        // 인코딩 설정
        var encoding: ParameterEncoding = JSONEncoding.default
        switch method {
        case .post:
            encoding = JSONEncoding.default
        case .get:
            encoding = URLEncoding.default
        default:
            encoding = JSONEncoding.default
        }

        // 계속을 정확히 한 번만 재개해야 합니다.
        return try await withCheckedThrowingContinuation { continuation in
            AF.request(
                url,
                method: method,
                parameters: params,
                encoding: encoding,
                headers: HTTPHeaders(headers)
            ).responseDecodable(of: type) { response in
                switch response.result {
                case let .success(data):
                    continuation.resume(returning: data)

                case let .failure(error):
                    continuation.resume(throwing: error)
                }
            }
        }
    }
}
```

<div class="content-ad"></div>

네트워크 매니저가 이제 async 액터로 변경되었으므로 안전하고 동시성 있는 방식으로 상호 작용하는 방법을 소개합니다:

```js
// 코드의 어딘가.
let network = NetworkManager()

...

func getPeople() async -> Result<ApiPeopleResponse, Error> {
    do {
        // API 요청 보내기
        let response = try await network.request(method: .get, url: "https://swapi.dev/api/people", headers: [:], params: [:], of: ApiPeopleResponse.self)
        return .success(response)
    } catch {
        return .failure(error)
    }
}

...

// 코드 어딘가에서 비동기 함수 getPeople 호출합니다.
let result = await getPeople()
switch result {
case .success(let response):
    // 여기에서 데이터 응답 처리
    break

case .failure(let error):
    // 실패한 응답 처리.
    print("불러오기 실패: \(error.localizedDescription)")
    break
}
```

아래는 async/await와 함께 작업(tasks)을 활용하는 방법을 간단히 살펴봅니다:

```js
// async/await와 Task를 함께 사용하는 예제
func getPeople(completion: @escaping (Result<ApiPeopleResponse, Error>) -> Void) {
    Task {
        do {
            // API 요청 보내기
            let response = try await network.request(method: .get, url: "https://swapi.dev/api/people", headers: [:], params: [:], of: ApiPeopleResponse.self)
            await MainActor.run {
                completion(.success(response))
            }
        } catch {
            await MainActor.run {
                completion(.failure(error))
            }
        }
    }
}

...

// 코드 어딘가에서 완료 함수인 getPeople 호출합니다.
getPeople { result in
    switch result {
    case .success(let response):
        // 여기에서 데이터 응답 처리
        break
    case .failure(let error):
        // 실패한 응답 처리.
        print("불러오기 실패: \(error.localizedDescription)")
        break
    }
}
```

<div class="content-ad"></div>

# 결론

Swift에서의 Async/await는 깔끔하고 유지보수 가능한 비동기 코드 작성을 위한 가능성을 열어줍니다. 이 글을 통해 우리는 이러한 기능에 대해 더 깊게 파고들어 보았고, 에러 처리, 취소, 그리고 중요한 동시성과 같은 고급 기능에 초점을 맞췄습니다. 이러한 개념을 Alamofire와 통합하면 iOS 애플리케이션의 성능과 신뢰성을 향상시킬 수 있습니다. 항상 강조하듯이, 연습이 중요하니 여러분들의 프로젝트에서 이러한 개념을 실험해보는 것을 적극 권장합니다.

Actor와 task의 사용은 공유 리소스 관리와 효율적인 동시 작업 실행에 대한 우리의 이해를 더욱 확고하게 만들어줍니다. 앞으로 나아가면서 이 경험이 우리에게 계속 더 나은 iOS 개발의 최신 동향을 탐색하고 통합하는 데 영감을 줄 것입니다. 이를 통해 우리의 기술과 애플리케이션이 최신 기술과 함께 선도적인 위치를 유지할 수 있도록 하세요.

![이미지](/assets/img/2024-06-23-ModernConcurrencyinSwiftAsyncAwaitAlamofire_1.png)

<div class="content-ad"></div>

iOS 엔지니어링에 대한 더 많은 통찰을 기대해주세요! 코딩을 즐기며 행복하게 일하세요! 🌟👩‍💻👨‍💻📱