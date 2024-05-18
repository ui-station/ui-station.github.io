---
title: "응용 프로그램 아키텍처 데이터 레이어"
description: ""
coverImage: "/assets/img/2024-05-18-AppArchitectureDatalayer_0.png"
date: 2024-05-18 17:09
ogImage: 
  url: /assets/img/2024-05-18-AppArchitectureDatalayer_0.png
tag: Tech
originalTitle: "App Architecture: Data layer"
link: "https://medium.com/proandroiddev/app-architecture-data-layer-8d681e8f8a6d"
---


```markdown
![AppArchitectureDatalayer_0](/assets/img/2024-05-18-AppArchitectureDatalayer_0.png)

이전 글에서는 도메인 레이어를 안정적이고 플랫폼 독립적인 레이어로 다루었습니다. 오늘은 데이터 레이어의 목적을 다루겠습니다. 함께 알아봅시다.

![AppArchitectureDatalayer_1](/assets/img/2024-05-18-AppArchitectureDatalayer_1.png)

데이터 레이어는 앱의 나머지 부분에 데이터를 노출하는 역할을 합니다. 다양한 데이터 소스를 관리하고 그들 사이의 충돌을 처리합니다.
```

<div class="content-ad"></div>

# 저장소

데이터 레이어는 Repository로 이루어져 있습니다. 이전 챕터에서 언급했듯이 도메인 레이어에 대해:

데이터 레이어는 도메인 레이어의 저장소를 구현한 것입니다. Repository 클래스는 앱에서 처리하는 각기 다른 유형의 데이터를 나타내어야 합니다. 예를 들어:

## 네이밍 규칙

<div class="content-ad"></div>

레포지토리 클래스는 담당하는 데이터를 따라 이름이 지어집니다.

데이터 유형 + DataRepository와 같은 형식을 따릅니다.

예를 들어 TicketRepository 인터페이스가 있으면 충돌을 피하기 위해 구현체는 TicketDataRepository로 지정됩니다.

## 데이터 소스

<div class="content-ad"></div>

레포지토리는 다양한 데이터 소스를 관리합니다 (예: 로컬, 메모리, 네트워크). 이 작업을 담당하는 구성 요소는 DataSource입니다. 이는 데이터베이스, 네트워크, ShearedPreference, WorkManager 및 파일과 같은 데이터 소스와 레포지토리 사이의 추상화입니다.

- 데이터 소스는 한 번에 하나의 데이터 소스와만 작업해야 합니다.
- 데이터 소스는 데이터 레이어를 위해 비공개이어야 하며 레포지토리를 통해서만 접근해야 합니다.

## 네이밍 규칙

데이터 소스 클래스는 그들이 책임지는 데이터와 사용하는 소스를 기반으로 명명됩니다.

<div class="content-ad"></div>

데이터 유형 + 소스 유형 + DataSource.

데이터 유형으로는 Remote 또는 Local을 사용하여 구현이 변경될 수 있으므로 더 일반적으로 설정하세요 (예: FaresLocalDataSource 또는 FaresRemoteDataSource). 소스가 중요한 경우를 위해 소스 유형을 사용하여 더 구체적으로 설명하세요 (예: FaresNetworkDataSource 또는 FaresFileDataSource).

구현 세부 정보를 기반으로한 이름을 피하세요. 예를 들어, UserSQLiteDataSource와 같이 구현 세부 정보에 기반한 이름을 사용하지 마세요. 해당 데이터 소스를 사용하는 리포지토리는 데이터가 어떻게 저장되는지 알 필요가 없습니다. 이 규칙을 따르면 데이터 소스의 구현을 변경(예: SQLite에서 DataStore로 마이그레이션)하더라도 해당 소스를 호출하는 계층에 영향을 주지 않습니다.

UserApi 인터페이스는 네트워크 API 클라이언트의 구현을 숨깁니다. Retrofit 또는 GraphQL이 인터페이스를 지원하는데 차이가 없습니다. 인터페이스에 의존함으로써 앱에서 API 구현을 교체할 수 있습니다. 또한 이러한 방식은 유연성을 제공하며 의존성을 쉽게 교체할 수 있도록 합니다. 예를 들어, 테스트에서 가짜 데이터 소스 구현을 주입할 수 있습니다.

<div class="content-ad"></div>

데이터 원본은 오류 발생 시 예외 처리를 하는 좋은 위치입니다. 애플리케이션에 데이터 원본과 관련된 예외를 노출시키지 말고 대신 애플리케이션이 처리할 수 있는 예외로 매핑해야 합니다. 

이전에 언급했던 대로, 리포지토리는 데이터 소스와 동시성을 관리하는 데 더 중점을 둔 것입니다.

위의 예시에서 보듯이, 우리는 로컬 캐시를 관리하고 로컬 및 원격 데이터 소스의 우선순위를 설정하는 전략을 구현했습니다. 리포지토리는 상태를 가질 수 있으며 예를 들어 Mutex를 사용하여 다른 스레드에서 변경 가능한 변수에 대한 읽기 및 쓰기 액세스를 관리할 수 있습니다. 따라서 리포지토리의 수명주기에 대해 고려해야 합니다.

## 수명주기

<div class="content-ad"></div>

가능하다면 리포지토리를 무상태로 만들 것을 권장합니다. 동시에 싱글톤으로 유지하여(instance-duplication of data sources와 같은 버그를 피하기 위해 DI를 통해 관리) 안정성을 높일 수 있습니다. DataSource도 마찬가지로 싱글톤으로 만들고 캐시는 리포지토리에서 관리하는 것을 권장합니다.

## 동시성

리포지토리는 어떤 CoroutineDispatcher에서 작업을 실행할지 결정해야 하는 곳입니다. 서로 다른 유형의 작업을 서로 다른 디스패처(또는 쓰레드 풀)에서 실행하는 것이 좋은 실천입니다. IO 작업을 수행하는 경우 Dispatcher.IO를 사용하는 것이 좋습니다. 테스트하기 쉽도록 클래스 생성자를 통해 디스패처를 전달해야 합니다.

# 모델

<div class="content-ad"></div>

데이터 레이어에는 도메인 레이어에서 모델을 반영하는 데이터 모델이 있지만 표현이 다를 수 있습니다. 별도의 데이터 모델을 사용하면 전송 프로토콜에 맞게 사용자 정의할 수 있는 유연성을 제공합니다. 예를 들어, 서버와 클라이언트 간의 통신에 JSON 형식을 사용하고 일부 필드는 JSON 프로토콜에 더 적합한 다른 유형을 가질 수 있습니다. 도메인 모델을 DTO로 매핑하는 로직 및 그 반대는 Repository 클래스에 배치되어야 합니다.

- 데이터 레이어는 도메인 모델만 노출하고 입력으로 사용해야 합니다.
- 데이터 모델은 Parcelable 및 Serializable와 같은 플랫폼별 직렬화 방식을 구현할 수 있습니다.
- 사전에 모델 클래스가 정의된 경우, 한 팀의 구성원이 기능의 다른 레이어에 개별적으로 작업할 수 있습니다.
- 이 레이어에서 노출된 데이터는 변경할 수 없어야 합니다.

## 명명 규칙

모델 클래스는 책임을 지는 데이터 유형의 이름으로 지어집니다.

<div class="content-ad"></div>

데이터 유형 + DTO.

예를 들어: RyderDTO, FareDTO.

# 패키지 규칙

```js
data/
├─ local/
│ ├─ dto/
│ │ ├─ FareDTO
│ │ ├─ RyderDTO
│ ├─ FaresLocalDataSource
│ ├─ FaresRemoteDataSource
├─ network/
│ ├─ api/
│ │ ├─ UserNetworkApi // UserApi 인터페이스의 Retrofit 구현
│ ├─ dto/
│ │ ├─ UserDTO
│ ├─ UserRemoteDataSource // UserApi 인터페이스 포함
├─ repository/
│ ├─ FaresDataRepository
│ ├─ RydersDataRepository
│ ├─ TicketsDataRepository
```

<div class="content-ad"></div>

# 마무리

데이터 레이어는 도메인 레이어에 정의된 리포지토리 구현을 중심으로 구축되었습니다. 이는 데이터의 다양한 소스를 관리하고 사용하는 방법에 대한 전략을 관리하는 역할을 합니다.

다음 앱 아키텍처 주제에 대해서는 계속해서 확인해보세요. 그동안 레이어 간 데이터 매핑 방법에 대해 읽어볼 수 있습니다.