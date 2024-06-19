---
title: "iOS SwiftUI 프로젝트에서 MVVM으로 적용하는 Clean Architecture"
description: ""
coverImage: "/assets/img/2024-06-19-TheCleanArchitecturewithMVVMintheiOSSwiftUIProject_0.png"
date: 2024-06-19 14:01
ogImage: 
  url: /assets/img/2024-06-19-TheCleanArchitecturewithMVVMintheiOSSwiftUIProject_0.png
tag: Tech
originalTitle: "The Clean Architecture with MVVM in the iOS SwiftUI Project"
link: "https://medium.com/stackademic/the-clean-architecture-with-mvvm-in-the-ios-swiftui-project-05dd8fe9ec7a"
---


```markdown
![Clean Architecture](/assets/img/2024-06-19-TheCleanArchitecturewithMVVMintheiOSSwiftUIProject_0.png)

Clean architecture는 Robert C. Martin(명칭: Uncle Bob)이 소개한 소프트웨어 디자인 원칙입니다. Hexagonal Architecture, Onion Architecture, Screaming Architecture 등 소프트웨어 아키텍처에 대한 몇 가지 아이디어가 있었습니다. 이러한 아키텍처들의 주요 개념은 관심사의 분리입니다.

이 기사에서는 클린 아키텍처를 논의하고, 데모 프로젝트를 통해 iOS SwiftUI 앱에 Clean Architecture와 MVVM을 적용하는 방법을 살펴볼 것입니다.

# 클린 아키텍처
```

<div class="content-ad"></div>

```markdown
![그림](/assets/img/2024-06-19-TheCleanArchitecturewithMVVMintheiOSSwiftUIProject_1.png)

위 그래프에서 확인할 수 있듯이, 원들은 소프트웨어의 서로 다른 영역을 나타냅니다. 가장 바깥쪽 층은 소프트웨어의 가장 낮은 수준이며, 깊이 들어갈수록 소프트웨어의 수준이 높아집니다. 내부 층은 외부 층에 대해 아무것도 알지 못합니다. 보통, 층이 깊어질수록 해당 층은 변하기 적은 경향이 있습니다.

클린 아키텍처의 주요 규칙은 내부 층에서 외부 층으로의 의존성을 갖지 않는 것입니다. 의존성은 외부 층에서 내부 층으로 연결되어야 합니다. 바깥쪽에서 안쪽으로 향하는 화살표가 의존성 규칙입니다.

클린 아키텍처에는 다양한 층이 있으며, 그룹화한 후에 프레젠테이션, 도메인 및 데이터 층 세 개로 구분할 수 있습니다.
```  

<div class="content-ad"></div>

<img src="/assets/img/2024-06-19-TheCleanArchitecturewithMVVMintheiOSSwiftUIProject_2.png" />

## 표현 계층

이 계층은 View와 ViewModel로 구성되어 있습니다. View는 하나 이상의 사용 사례를 실행하는 ViewModel에 의해 조정됩니다. 표현 계층은 도메인 계층에만 의존합니다.

## 도메인 계층

<div class="content-ad"></div>

이것은 가장 안쪽 레이어입니다. 이 레이어에는 Entities, Use cases 및 Repository interfaces가 포함되어 있습니다. 이 레이어는 다른 레이어에 종속적이지 않습니다. 따라서 이 레이어는 재사용이 가능하고 테스트하기 쉽습니다. 또한 비즈니스 로직이 여기에 작성됩니다.

## 데이터 레이어

이 레이어에는 리포지토리 구현 및 데이터 소스가 포함되어 있습니다. 리포지토리는 데이터 소스로부터 데이터를 조정하는 역할을 합니다. 데이터 소스는 원격 또는 로컬(예: 코어 데이터)일 수 있습니다. 데이터 레이어는 도메인 레이어에 종속됩니다. 이 레이어에서는 네트워크 JSON 데이터를 도메인 모델로 매핑할 수 있습니다.

## 의존성 방향과 데이터 흐름

<div class="content-ad"></div>

인터페이스(UI)는 ViewModel(프리젠터)에서 메서드를 호출합니다. ViewModel은 Use Case를 실행하고, Use Case는 데이터를 가져오기 위해 Repository를 호출합니다. Repository는 네트워크나 영구 저장 DB에서 데이터를 가져옵니다. 데이터는 다시 UI로 흐르고, 여기서 데이터를 보여줍니다.

이 흐름은 다음과 같습니다:

인터페이스(UI) - ViewModel(프리젠터) - Use Case(도메인) - Repository(데이터)

이 흐름은 Clean Architecture의 종속성 규칙을 위배합니다. 종속성 규칙에 따르면, 가장 내부에 있는 도메인 레이어는 가장 바깥에 있는 데이터 레이어에 종속되어서는 안 됩니다.

<div class="content-ad"></div>

도메인 계층(Use Case)이 사용 사례(도메인 계층)부터 직접적으로 의존하는 대신, 사용 사례는 저장소 인터페이스/프로토콜에 의존하게 됩니다. 이 저장소 프로토콜은 도메인 계층 안에 존재할 것입니다. 이렇게 함으로써 우리는 흐름의 방향을 뒤집을 수 있습니다. 이 뒤집힌 방향이 어떻게 이뤄지는지 자세히 이해해 봅시다:

![이미지](/assets/img/2024-06-19-TheCleanArchitecturewithMVVMintheiOSSwiftUIProject_3.png)

의존성 역전은 데이터 계층과 도메인 계층 간에 발생합니다. 도메인 계층 내부에 저장소 인터페이스가 있기 때문에 데이터 계층은 저장소 인터페이스를 위해 도메인 계층에 의존하게 됩니다. 도메인 계층에서 인터페이스가 변경된다면 데이터 계층도 그에 따라 변경될 것입니다. 여기서는 의존성 방향이 역전되어 있습니다.

인터페이스는 더 높은 수준(도메인에서)에서 결정되고 해당 수준의 클래스(사용 사례)가 이에 의존하므로 추상화에 의존하게 됩니다. 게다가 하위 수준 클래스(저장소) 구현은 더 높은 수준에서 정의된 인터페이스에 의존하므로 세부 사항은 이제 추상화에 의존하게 됩니다. 이것이 바로 의존성 역전의 원칙입니다. 기억하세요, 소프트웨어의 내부 원이 더 높은 수준의 소프트웨어 수준이므로 도메인 계층은 데이터 계층보다 높은 수준에 있습니다.

<div class="content-ad"></div>

그래서, 흐름이 다음과 같이 됩니다:

프레젠테이션 레이어 — `도메인 레이어` — 데이터 레이어

이론은 여기까지! 이제 각 레이어와 MVVM을 예제 프로젝트 및 샘플 코드를 통해 이해하는 시간입니다.

# 예제 프로젝트 — ProductClean

<div class="content-ad"></div>

이것은 공개 API에서 제품 목록을 가져와 목록으로 표시하는 간단한 iOS 애플리케이션입니다. 목록에서 각 항목을 클릭하면 제품 세부 정보 페이지로 이동합니다. 이 프로젝트는 MVVM과 Clean 아키텍처를 사용하여 구축되었습니다. SwiftUI를 사용하여 UI를 구현했습니다.

Clean 아키텍처는 구성 요소 간의 역할 분리를 위해 사용되었고 MVVM과 Clean 아키텍처를 함께 사용하여 UI와 프리젠터 사이의 역할을 분리했습니다.

앱의 HLD (고수준 설계) 및 폴더 구조를 살펴보겠습니다:

HLD를 통해 이 앱에는 제품이라는 단일 모듈이 있음을 알 수 있습니다. 필요한 경우 여러 모듈을 가질 수 있습니다. 예를 들어, 이 앱에 로그인 및 결제 기능을 추가하려면 로그인 및 결제 모듈을 추가할 수 있습니다.

<div class="content-ad"></div>

저희는 세 개의 레이어를 가지고 있다는 것을 알고 있어요. 각 레이어를 자세히 살펴볼게요:

## 프레젠테이션 레이어

프레젠테이션 레이어 = ViewModel(프레젠터) + View(UI)

프레젠테이션 레이어에는 ProductListView와 ProductListViewModel이 포함되어 있어요.

<div class="content-ad"></div>

ProductListView

ProductListView는 비즈니스 로직(Use cases)에 대한 접근이 없고 데이터 조작과 관련된 코드도 포함하고 있지 않아요. 오직 ViewModel에만 접근할 수 있어요. 따라서 뷰는 재사용성이 높아지고 교체가 가능해져요.

뷰는 ViewModel에 제품 데이터를 가져오도록 요청하고 ViewModel에서 제품 배열을 관찰해요. 제품 배열을 받으면 해당 데이터를 목록에서 표시해요.

```js
import SwiftUI

struct ProductListView<ViewModel>: View where ViewModel: ProductListViewModelProtocol {

    @ObservedObject private var viewModel: ViewModel
    init(viewModel: ViewModel) {
        self.viewModel = viewModel
    }

    var body: some View {
        NavigationStack {
            if viewModel.shouldShowLoader() {
                ProgressView()
                    .progressViewStyle(.circular)
            } else {
                ProductListLayout(items: viewModel.products)
                    .overlay {
                        if viewModel.isError {
                            ErrorView(errorTitle: AppConstant.errorTitle, errorDescription: viewModel.error) {
                                Task {
                                    await fetchProducts()
                                }
                            }
                        }
                    }
                    .navigationTitle(viewModel.title)
            }
        }
        .task {
            await fetchProducts()
        }
    }

    private func fetchProducts() async {
        await viewModel.fetchProducts()
    }
}
```

<div class="content-ad"></div>

참고: @ObservedObject 프로퍼티 래퍼는 뷰 내에 observable 유형인 ViewModel을 저장하는 데 사용됩니다.

ProductListViewModel

ProductListViewModel은 어떤 UI 프레임워크도 import하지 않습니다. 따라서 재사용 가능해집니다. 이는 ProductListViewModelProtocol을 준수하며 ViewModel을 모의(mock)할 수 있고 테스트할 수 있도록 합니다.

```js
import Foundation

protocol ProductListViewModelProtocol: ObservableObject {
    var products: [ProductListItemViewModel] { get set }
    var isError: Bool { get }
    var error: String { get }
    var isEmpty: Bool { get }
    var title: String { get }
    func shouldShowLoader() -> Bool
    func fetchProducts() async
}

final class ProductListViewModel: ProductListViewModelProtocol {
    
    @Published var products: [ProductListItemViewModel] = []
    @Published var isError: Bool = false
    @Published var error: String = ""
    var isEmpty: Bool { return products.isEmpty }
    var title: String = AppConstant.productListTitle
    private let productListUseCase: ProductListUseCase!
    
    init(useCase: ProductListUseCase) {
        self.productListUseCase = useCase
    }
    
    /// This method fetches products and catches error if any
    @MainActor func fetchProducts() async {
        do {
            let productList = try await productListUseCase.fetchProductList()
            self.products = self.transformFetchedProducts(products: productList)
            self.isError = false
        } catch {
            self.isError = true
            if let networkError = error as? NetworkError {
                self.error = networkError.description
            } else {
                self.error = error.localizedDescription
            }
        }
    }
    
    /// This method maps Product to ProductListItemViewModel
    /// - Parameter products: array of Product
    /// - Returns: array of ProductListItemViewModel
    private func transformFetchedProducts(products: [ProductDomainListDTO]) -> [ProductListItemViewModel] {
        products.map { ProductListItemViewModel(id: $0.productId,
                                           title: $0.title,
                                           description: $0.description,
                                                price: $0.price.getAmountWithCurrency(),
                                                category: $0.category.capitalized,
                                           image: $0.thumbnail) }
    }
    
    /// This method checks if the loader should be shown or not
    /// - Returns: True if there the product array is empty and error is not there
    func shouldShowLoader() -> Bool {
        return (isEmpty && !isError)
    }
}
```

<div class="content-ad"></div>

ProductListViewModel은 제품 배열, 오류 및 기타 정보를 저장하기 위해 ObservableObject 프로토콜을 준수합니다. ProductListViewModel 내부의 속성에는 뷰에서 액세스되기 때문에 뷰가 업데이트되는 @Published 속성 래퍼가 추가되었습니다. 따라서 @Published 속성 (제품, isError 등) 중 하나가 변경되면 뷰가 업데이트됩니다.

fetchProducts 함수에 @MainActor 속성이 사용되면 해당 작업이 주 스레드에서 실행됨을 보장합니다. fetchProducts 함수 내부에서 @Published 속성이 업데이트되는 것을 볼 수 있습니다. 이러한 속성이 뷰에서 사용되고 이러한 속성이 변경될 때 뷰가 업데이트된다는 것을 알고 있습니다. 뷰(UI)가 주 스레드에서 업데이트되기를 원합니다.

## MVVM

MVVM(Mode-View-ViewModel)은 ViewModel이 뷰와 모델 사이에서 중재자 역할을 하는 디자인 패턴입니다.

<div class="content-ad"></div>

![image](/assets/img/2024-06-19-TheCleanArchitecturewithMVVMintheiOSSwiftUIProject_4.png) 

뷰모델은 UI 프레임워크를 가져오지 않기 때문에 재사용 가능하고 테스트할 수 있습니다. 하나의 뷰모델에 대해 서로 다른 뷰 구현을 사용할 수 있습니다. 예를 들어, 같은 뷰모델에 대해 UIKit 또는 SwiftUI 뷰를 사용할 수 있습니다.

위에서 논의한 대로 데이터 바인딩에는 ObservableObject 프로토콜을 사용했지만 다른 데이터 바인딩 방법도 사용할 수 있습니다. 예를 들어 델리게이트, 클로저, 알림, 구독자/발행자 등이 있습니다.

저희 프로젝트에서 MVVM은 UI와 프리젠터 간의 관심을 분리하는 데 사용되었습니다.

<div class="content-ad"></div>

## 도메인 레이어

도메인 레이어 = 유스 케이스 + 엔티티 + 리포지토리 인터페이스

도메인 레이어는 ProductListUseCase, ProductDomainListDTO(엔티티) + ProductListRepository 프로토콜로 이루어져 있어요.

특정 모듈의 요구 사항에 따라 여러 개의 유스 케이스가 있을 수 있고, 하나의 유스 케이스가 다른 유스 케이스에 의존할 수 있어요.

<div class="content-ad"></div>

이 계층은 Use Case에 쓰인 응용 프로그램 비즈니스 규칙 때문에 가장 중요한 계층입니다. 우리는 Use Case를 보기만 해도 응용 프로그램이 무엇을 하는지 이해할 수 있습니다. 이것을 Screaming Architecture라고 합니다.

ProductListUseCase

ProductListUseCase는 fetchProductList 함수를 포함하는 ProductListUseCase 프로토콜을 준수합니다. 이는 저장소에 접근하고 저장소로부터 제품 목록 데이터를 불러옵니다.

```js
import Foundation

protocol ProductListUseCase {
    func fetchProductList() async throws -> [ProductDomainListDTO]
}

final class DefaultProductListUseCase: ProductListUseCase {
    
    private let repository: ProductListRepository
    
    init(repository: ProductListRepository) {
        self.repository = repository
    }
    
    func fetchProductList() async throws -> [ProductDomainListDTO] {
        try await repository.fetchProductList()
    }
}
```

<div class="content-ad"></div>

ProductListRepositoryProtocol

도메인 레이어에는 ProductListRepositoryProtocol이 포함되어 있고 ProductListUseCase는 직접 repository 대신 ProductListRepositoryProtocol에 종속되어 있습니다. 따라서 의존성 방향이 역전되었습니다. 위의 의존성 방향 및 데이터 흐름 섹션에서 이미 자세히 논의한 바 있습니다.

```js
protocol ProductListRepository {
    func fetchProductList() async throws -> [ProductDomainListDTO]
}
```

참고: ProductListRepositoryProtocol은 의존성 역전에 사용됩니다.

<div class="content-ad"></div>

ProductDomainListDTO

ProductDomainListDTO는 도메인 데이터 전송 객체(DTO)로, 중간 객체로 작동하고 개체를 프레젠테이션 레이어에서 사용할 모델로 변환하는 역할을 담당합니다.

```js
import Foundation

struct ProductDomainListDTO {
    let productId: Int
    let title: String
    let description: String
    let price: Double
    var category: String
    let thumbnail: String
}
```

## 데이터 레이어

<div class="content-ad"></div>

데이터 레이어 = 리포지토리 구현 + 서비스 / 데이터 저장소

데이터 레이어는 DefaultProductListRepository(리포지토리 구현)와 ProductListService로 구성되어 있습니다.

DefaultProductListRepository

DefaultProductListRepository는 ProductListService에 액세스하고 서비스로부터 제품 데이터를 가져와 도메인으로 데이터를 반환하는 fetchProductList 함수를 갖고 있습니다.

<div class="content-ad"></div>

```swift
import Foundation

final class DefaultProductListRepository: ProductListRepository {
    
    private let service: ProductListService
    
    init(service: ProductListService) {
        self.service = service
    }
    
    func fetchProductList() async throws -> [ProductDomainListDTO] {
        try await service.fetchProductListFromNetwork().products.map { $0.toDomain() }
    }
}
```

참고: 앱이 오프라인 지원을 하는 경우 ProductListRepository에 영속 데이터 저장소 (예: CoreData)가 주입됩니다. 먼저 리포지토리는 캐시된 데이터가 있는지 영구 저장소에서 확인하고, 캐시된 데이터를 도메인 모델로 매핑하여 데이터가 있으면 도메인에 반환합니다. 그런 다음 서비스에게 네트워크에서 데이터를 가져 오도록 요청합니다. 데이터를 가져온 후 리포지토리는 새 데이터로 영구 저장소를 업데이트하고, 도메인 모델로 매핑하여 도메인 계층에 데이터를 반환합니다.

ProductListService

ProductListService는 네트워크에서 제품 데이터를 가져 오는 fetchProductListFromNetwork 함수 내에서 사용되는 apiDataService에 액세스 권한이 있습니다.
```

<div class="content-ad"></div>

```js
import Foundation

protocol ProductListService {
    func fetchProductListFromNetwork() async throws -> ProductPageDataListDTO
}

final class DefaultProductListService: ProductListService {
    
    private let apiDataService: DataTransferService
    
    init(apiDataService: DataTransferService) {
        self.apiDataService = apiDataService
    }
    
    func fetchProductListFromNetwork() async throws -> ProductPageDataListDTO {
        let productListNetworkRequest = DefaultNetworkRequest(path: APIEndpoint.products,method: .get)
        return try await apiDataService.request(request: productListNetworkRequest)
    }
}
```

## 네트워크

여기서 URLSession을 사용하여 실제 API 호출이 발생합니다. 먼저 EndPoint와 HTTPMethod를 제공하여 첫 번째 네트워크 요청을 생성합니다. 그런 다음 URL 요청이 생성됩니다. URL 요청을 사용하여 데이터를 가져옵니다. 에러가 발생하면 에러를 처리합니다. 또한 Decodable을 사용하여 데이터를 매핑합니다.

```js
import Foundation

protocol DataTransferService {
    func request<T: Decodable>(request: NetworkRequest) async throws -> T
}

final class DefaultDataTransferService: DataTransferService {
    
    private let networkManager: NetworkManager
    init(networkManager: NetworkManager) {
        self.networkManager = networkManager
    }
    
    /// 네트워크 매니저에서 데이터를 가져와 decode 메서드를 사용하여 데이터를 디코딩하는 메서드입니다.
    /// - Parameter request: 네트워크 요청
    /// - Returns: Decodable 유형의 객체
    func request<T>(request: NetworkRequest) async throws -> T where T : Decodable {
        let data = try await networkManager.fetch(request: request)
        return try decode(data: data)
    }
    
    /// JSONDecoder를 사용하여 데이터를 디코딩하는 메서드입니다.
    /// - Parameter data: 데이터
    /// - Returns: Decodable 유형의 객체
    func decode<T>(data: Data) throws -> T where T : Decodable {
        do {
            let decodedData = try JSONDecoder().decode(T.self, from: data)
            return decodedData
        } catch {
            throw NetworkError.unableToDecode
        }
    }
}

import Foundation

protocol NetworkSessionManager {
    func request(with config: NetworkConfigurable, request: NetworkRequest) async throws -> (Data?, URLResponse?)
}
protocol NetworkManager {
    func fetch(request: NetworkRequest) async throws -> Data
}

final class DefaultNetworkManager: NetworkManager {
    
    private let config: NetworkConfigurable
    private let sessionManager: NetworkSessionManager
    
    init(config: NetworkConfigurable,
        sessionManager: NetworkSessionManager = DefaultNetworkSessionManager()) {
        self.config = config
        self.sessionManager = sessionManager
    }
    
    /// 세션 매니저에서 데이터를 가져오고 데이터 및 응답을 유효성 검사하는 메서드입니다.
    /// - Parameter request: 네트워크 요청
    /// - Returns: 데이터
    func fetch(request: NetworkRequest) async throws -> Data {
        let (data, response) = try await sessionManager.request(with: config, request: request)
        guard let response = response as? HTTPURLResponse else { throw NetworkError.noResponse }
        if response.statusCode != 200 { throw NetworkError.failed }
        guard let data = data else { throw NetworkError.noData }
        return data
    }
}
```

<div class="content-ad"></div>

노트: 네트워크 관련된 더 많은 코드는 예시 프로젝트의 네트워킹 폴더에 있습니다.

# 의존성 주입 (DI)

의존성 주입은 객체가 자체적으로 만들지 않고 필요로 하는 다른 객체들을 받는 프로그래밍 기법입니다.

우리의 예시 프로젝트에서는 AppDIContainer라는 클래스를 만들었는데, 이 클래스에는 apiDataTransferService가 포함되어 있습니다. 이 apiDataTransferService 객체는 Product 모듈을 생성하는 데 필요합니다.

<div class="content-ad"></div>

```swift
import Foundation

final class AppDIContainer {
    
    lazy private var apiDataTransferService: DataTransferService = {
        let config = ApiDataNetworkConfig(baseURL: AppConfiguration.baseURL)
        let sessionManager = DefaultNetworkSessionManager(session: SharedURLSession.shared)
        let networkManager = DefaultNetworkManager(config: config, sessionManager: sessionManager)
        return DefaultDataTransferService(networkManager: networkManager)
    }()

    lazy var productListView: ProductListView = {
        let productsModule = ProductsModule(apiDataTransferService: apiDataTransferService)
        return productsModule.generateProductListView()
    }()
}
```

참고: 우리는 의존성 주입으로 apiDataTransferService를 Product Module에 전달하고 있습니다.

# 결론

클린 아키텍처는 매우 확장 가능하고 테스트 가능하며 유지보수 가능한 소프트웨어를 구축하는 데 도움을 줍니다. 각 계층이 특정 역할을 가지고 다른 부분과 격리되도록 응용 프로그램을 여러 계층으로 분리하여 관련성을 제공합니다. 이는 재사용성의 가능성을 높이고 비즈니스 요구 사항 및 경쟁 시장의 변화에 대응할 수 있도록 보장합니다.```

<div class="content-ad"></div>

읽어 주셔서 감사합니다. 연락 기다리겠습니다!

좋은 코딩하세요 :)

![image](https://miro.medium.com/v2/resize:fit:200/1*bdto5WbUfHzG7AuZXY3aGA.gif)

링크드인에서 연결해요.