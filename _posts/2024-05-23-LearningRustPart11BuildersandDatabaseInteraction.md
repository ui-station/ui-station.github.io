---
title: "러스트 배우기 11부  빌더와 데이터베이스 상호작용"
description: ""
coverImage: "/assets/img/2024-05-23-LearningRustPart11BuildersandDatabaseInteraction_0.png"
date: 2024-05-23 14:11
ogImage:
  url: /assets/img/2024-05-23-LearningRustPart11BuildersandDatabaseInteraction_0.png
tag: Tech
originalTitle: "Learning Rust: Part 11 — Builders and Database Interaction"
link: "https://medium.com/gitconnected/learning-rust-part-11-builders-and-database-interaction-2c1f3207b6a2"
---

다음 시리즈로 넘어가보겠습니다; 이 부분에서는 데이터 구조에 빌더 패턴을 구현하는 방법을 살펴보겠습니다. 그런 다음 sqlx와 Postgres를 사용한 데이터베이스 상호작용으로 넘어가겠습니다.

![이미지](/assets/img/2024-05-23-LearningRustPart11BuildersandDatabaseInteraction_0.png)

# Rust 시리즈

부분 1 — 기본 개념

<div class="content-ad"></div>

Part 2 — 메모리

Part 3 — 흐름 제어와 함수

Part 4 — 옵션/결과 및 컬렉션

Part 5 — 트레이트, 제네릭 및 클로저

<div class="content-ad"></div>

제 6부 — 매크로, 반복자 및 파일 처리

제 7부 — 스레드 공유 상태 및 채널

제 8부 — Cargo, 크레이트, 모듈 및 라이브러리

제 9부 — 명령행 인수, 워크스페이스 및 테스팅

<div class="content-ad"></div>

제 10부 — 상자 포인터 및 웹 앱

제 11부 — 빌더와 데이터베이스 상호작용 (이 기사)

# 소개

이것은 러스트 학습 시리즈의 열한 번째 섹션입니다. 이번에는 러스트에서 빌더 패턴을 다룰 것입니다. 이 공통된 패턴은 구조체를 안전하고 투명하게 초기화하는 좋은 방법입니다. 다음으로, 우리는 포스트그레스와 SQLX 프레임워크를 사용하여 데이터베이스에서 CRUD 작업을 수행하는 방법을 살펴볼 것입니다.

<div class="content-ad"></div>

# 준비물

이 글의 데이터베이스 부분을 위한 유일한 준비물은 Rust와 Cargo가 설치되어 있고 시스템에 Docker가 설치되어 있는 것입니다. 만약 Docker를 가지고 있지 않지만 로컬 Postgres DB가 이미 설치되어 있거나 다른 서버의 DB에 액세스할 수 있다면 Docker Postgres 설정을 건너뛰고 DB에 연결하기 위한 연결 속성만 수정하면 됩니다.

# 빌더 패턴

빌더 패턴은 복잡한 객체의 구성을 해당 표현에서 분리하는 디자인 패턴입니다. 이 패턴을 사용하면 유효성 검사를 수행하고 기본값으로 대체하며 값을 부분적으로 할당한 후에 항목을 생성할 수 있습니다.

<div class="content-ad"></div>

Rust로 그 구현하는 방법을 살펴볼 거예요. 이것이 우리의 코드입니다.

```js
#[derive(Debug)]
struct ChargingSession {
    id: String,
    watts: u32,
    vin: String,
}

struct ChargingSessionBuilder {
    id: String,
    watts: Option<u32>,
    vin: Option<String>,
}

impl ChargingSessionBuilder {
    fn new(id: &str) -> ChargingSessionBuilder {
        ChargingSessionBuilder {
            id: id.to_string(),
            watts: None,
            vin: None,
        }
    }

    fn watts(mut self, watts: u32) -> ChargingSessionBuilder {
        self.watts = Some(watts);
        self
    }

    fn vin(mut self, vin: &str) -> ChargingSessionBuilder {
        self.vin = Some(vin.to_string());
        self
    }

    fn build(self) -> ChargingSession {
        ChargingSession {
            id: self.id,
            watts: self.watts.unwrap_or_else(|| 0),
            vin: self.vin.unwrap_or_else(|| "Unknown".to_string()),
        }
    }
}

fn main() {
    // 이것은 ChargingSession을 생성하는 표준적인 방법입니다.
    let cs_old_way = ChargingSession {
        id: String::from("11111"),
        watts: 420,
        vin: String::from("4Y1SL65848Z411439"),
    };
    println!("Regular way to create struct: {:?}", cs_old_way);

    // 빌더를 사용해서 생성하는 방법입니다.
    let cs = ChargingSessionBuilder::new("11111")
        .watts(420)
        .vin("4Y1SL65848Z411439")
        .build();
    println!("Builder pattern to create struct: {:?}", cs);

    // ID만 제공하여 생성하는 예시입니다.
    let cs_lean = ChargingSessionBuilder::new("11111")
    .build();
     println!("Builder pattern to create struct (default values): {:?}", cs_lean);
}
```

이걸 한 번에 이해하기에 많지만, 단계적으로 진행해 봅시다.

먼저, 충전 세션 정보를 저장하는 구조체를 정의했습니다. 이 시리즈의 이전 예제에서 하나의 필드인 세션용 차량 ID 번호인 vin을 추가했어요.

<div class="content-ad"></div>

그러면 데이터 구조체와 동일한 필드를 가진 빌더를 위한 구조체를 정의합니다.

빌더는 impl 블록에서 구현되며, id 및 각 추가 필드를 설정하는 함수를 정의하는 새 함수가 있습니다. 그러나 몇 가지 중요한 사항이 있습니다.

- 각 함수는 ChargingSessionBuilder 유형을 반환합니다. 기본적으로 self입니다.
- 추가 속성 필드에는 mut self를 첫 번째 매개변수로 사용하는 메서드가 있습니다. 이는 이러한 함수 호출의 체이닝을 허용하는 데 중요합니다. 또한 여기에 유효성 검사 논리를 코딩할 수 있습니다.
- build 함수가 모두 통합되는 곳입니다. 존재하는 값들을 할당하고 나면 기본값을 결정하고 대상 구조체를 생성할 수 있습니다.

이를 통해 빌더 패턴의 우아함과 Rust 내에서의 구현 방법을 살펴보았습니다. 이것이 앱이나 라이브러리에 코드를 구현하는 훌륭한 방법임을 알 수 있기를 바랍니다.

<div class="content-ad"></div>

다음 섹션인 데이터베이스로 넘어가 봅시다.

# 데이터베이스 — sqlx

이 섹션에서는 Rust 프로그램에서 sqlx를 사용하여 데이터베이스 작업을 살펴볼 것입니다. 먼저 일부 설정이 필요합니다.

## 설정

<div class="content-ad"></div>

새 프로젝트를 시작해봅시다. db_app이라고 이름 짓고, cargo new db_app으로 생성할 수 있어요. 기본 디렉토리에 몇 개의 파일을 생성할 거에요. 첫 번째 파일은 docker-compose.yml이라고 하며 다음 내용이 있어야 합니다. Postgres와 Pgadmin이 노출되는 임의의 포트를 선택했으며, 컴퓨터에 설치된 다른 앱들과 충돌하지 않도록 했어요.

```js
version: '3'
services:
  postgres:
    image: postgres:latest
    container_name: postgres
    ports:
      - '6500:5432'
    volumes:
      - postgresDB:/data/postgres
    env_file:
      - ./.env
  pgAdmin:
    image: dpage/pgadmin4
    container_name: pgAdmin
    env_file:
      - ./.env
    ports:
      - "5050:80"
volumes:
  postgresDB:
```

.env이라는 파일이 하나 더 필요하며, 다음 내용이 있어야 해요. 이 파일은 docker-compose 파일과 나중에 Rust 애플리케이션에서 모두 사용할 거에요.

```js
POSTGRES_HOST=127.0.0.1
POSTGRES_PORT=6500
POSTGRES_USER=admin
POSTGRES_PASSWORD=password123
POSTGRES_DB=charging_session

DATABASE_URL=postgresql://admin:password123@localhost:6500/charging_session?currentSchema=public

PGADMIN_DEFAULT_EMAIL=admin@admin.com
PGADMIN_DEFAULT_PASSWORD=password123
```

<div class="content-ad"></div>

위의 내용과 함께 데이터베이스 사용자, 비밀번호, 그리고 Pgadmin에 대한 연결 정보를 제공해 드렸습니다.

이 두 가지 항목을 만들고 위의 내용을 사용하여 Docker Compose를 사용하여 로컬 DB 인스턴스를 시작할 수 있습니다. 처음에는 항상 전경에서 시작하는 것을 좋아합니다. 이미지 다운로드 및 기타 작업을 하기 때문에 여러분의 컴퓨터 및 네트워크 속도에 따라 시간이 소요될 수 있습니다. docker-compose.yml 및 .env 파일이 있는 디렉토리와 동일한 위치에서 다음을 실행하십시오.

```js
docker-compose up
```

시작된 모든 것을 확인한 후에는 언제든지 -d 스위치를 사용하여 데몬 모드로 시작할 수 있습니다. 완료되면 두 컨테이너가 시작되었는지 확인해 봅시다. 다음과 같이 명령을 실행합니다.

<div class="content-ad"></div>

```js
docker ps
```

비슷한 결과가 표시됩니다.

<img src="/assets/img/2024-05-23-LearningRustPart11BuildersandDatabaseInteraction_1.png" />

만약 두 개의 컨테이너가 표시되지 않는다면, docker-compose를 실행한 터미널에서 에러를 확인해보세요.

<div class="content-ad"></div>

한 번 이들이 실행되면 DB에 연결해 봅시다. Pgadmin을 포트 5050에서 실행하도록 구성했으니 브라우저에서 http://localhost:5050을 입력하면 pgadmin의 로그인 화면이 표시됩니다. .env 파일에 구성된 자격 증명(관리자@관리자.com/비밀번호123)을 사용하여 pgadmin에 로그인할 수 있습니다. 그런데 아직 데이터베이스에 연결되지 않았습니다. 이를 위해 다음을 실행해야 합니다.

```js
docker inspect postgres
```

출력을 확인하여 "NetworkSettings" 섹션으로 이동하고 "IPAddress" 속성의 값을 복사합니다. 이 값은 DB에 연결하는 데 사용할 호스트(IP)입니다. 제 컴퓨터에서 이 값은 172.23.0.1 이었습니다.

로그인한 후 "새 서버 추가" 버튼을 클릭하고, "호스트 이름/주소"로 이전에 복사한 IP 주소를 포함한 필수 자격 증명을 제공하고 "저장" 버튼을 클릭하세요.

<div class="content-ad"></div>

서버에 로그인하면 데이터베이스 charging_session을 볼 수 있습니다.

![이미지](/assets/img/2024-05-23-LearningRustPart11BuildersandDatabaseInteraction_2.png)

잘 했어요. 데이터베이스를 사용할 준비가 되었고 Pgadmin에서 관리할 수 있습니다.

이제 Rust 앱의 종속성을 구성해 봅시다. 처음에는 모두 필요하지 않지만 결국 필요하게 될 것이므로 지금 추가해 두는 것이 좋습니다.

<div class="content-ad"></div>

Cargo.toml 파일의 종속성 부분을 다음과 같이 수정하세요.

```toml
[dependencies]
chrono = { version = "0.4.31", features = ["serde"] }
dotenv = "0.15.0"
env_logger = "0.10.1"
log = "0.4.20"
serde = { version = "1.0.193", features = ["derive"] }
serde_json = "1.0.108"
sqlx = { version = "0.7.3", features = ["runtime-tokio-native-tls", "postgres", "uuid", "chrono"] }
tokio = { version = "1.35.0", features = ["macros", "rt-multi-thread"]}
uuid = { version = "1.6.1", features = ["serde", "v4"] }
```

다양한 종속성에 대해 활성화된 기능을 검토하는 데도 시간을 할애하는 것이 좋습니다.

다음으로, 필요한 테이블을 만들기 위해 sqlx-cli 마이그레이션 기능을 사용할 것입니다.

<div class="content-ad"></div>

우선 명령 줄에서 다음을 실행하여 CLI를 설치해야 합니다.

```js
cargo install sqlx-cli --no-default-features --features rustls,postgres
```

그런 다음 마이그레이션 파일을 초기화해야 합니다. 다음과 같이 실행합니다.

```js
sqlx migrate add initial-tables
```

<div class="content-ad"></div>

이 명령어는 우리가 마이그레이션 스크립트를 작성하기 위해 새 파일 migrations/`timestamp`\_initial-tables.sql을 생성합니다.

![이미지](/assets/img/2024-05-23-LearningRustPart11BuildersandDatabaseInteraction_3.png)

이 파일을 열고 아래 SQL 문을 추가하여 테이블을 생성하세요.


create table locations (
  id bigserial primary key,
  name varchar(255) unique not null
);

create table sessions (
  id bigserial primary key,
  location_id bigint not null,
  watts bigint not null,
  vin varchar(255) not null,
  constraint fk_location foreign key (location_id) references locations(id) on delete cascade
);


<div class="content-ad"></div>

위 예제에서 사용할 두 개의 테이블 정의입니다. 이 시리즈의 충전 세션에 대한 표준 예제를 확장하여, 충전 장치의 위치를 저장할 locations 테이블을 추가했습니다.

이제 다음을 실행하여 테이블을 생성하세요.

```js
sqlx migrate run
```

그러면 즉시 20231212235431/migrate initial-tables (타임스탬프 부분은 달라질 수 있음)과 같은 메시지가 표시됩니다. 이제 Pgadmin에 가서 테이블을 확인할 수 있습니다.

<div class="content-ad"></div>

<img src="/assets/img/2024-05-23-LearningRustPart11BuildersandDatabaseInteraction_4.png" />

위에서 보듯이 두 개의 테이블이 생성되었고, 문서 목적을 위해 Pgadmin 내에 ERD 다이어그램도 생성했습니다. 이제 초기 설정과 프로젝트 구성을 완료했습니다.

# 데이터베이스 함수

이 섹션에서는 데이터베이스 상호 작용의 다양한 유형과 Rust 구현을 살펴볼 것입니다. 이는 데이터베이스에 연결하는 것으로 시작됩니다.

<div class="content-ad"></div>

DB에 연결하기

데이터베이스에 연결하는 방법은 PgPoolOptions connect 함수를 통해 연결을 생성하는 것입니다. 우리의 .env 파일에서 데이터베이스 연결 문자열을 읽고 로깅을 구성하며 성공 또는 오류를 기록하는 코드 전체는 아래와 같습니다.

```js
use sqlx::{postgres::PgPoolOptions, Pool, Postgres};
use dotenv::dotenv;
use log::{info, error};

#[tokio::main]
async fn main() {
    if std::env::var_os("RUST_LOG").is_none() {
        std::env::set_var("RUST_LOG", "info");
    }
    dotenv().ok();
    env_logger::init();

    let database_url = std::env::var("DATABASE_URL").expect("DATABASE_URL must be set");
    let pool = match PgPoolOptions::new()
        .max_connections(10)
        .connect(&database_url)
        .await
    {
        Ok(pool) => {
            info!("✅ 데이터베이스에 연결되었습니다!");
            pool
        }
        Err(err) => {
            error!("🔥 데이터베이스 연결에 실패했습니다: {:?}", err);
            std::process::exit(1);
        }
    };
}
```

시작 부분의 #[tokio::main] 매크로를 주목해주세요. 이는 async fn main()을 동기 fn main()로 변환하여 런타임 인스턴스를 초기화하고 async main 함수를 실행하게 합니다.

<div class="content-ad"></div>

위 코드를 실행하면 출력 로깅에서 다음 메시지가 표시됩니다.

[2023-12-13T00:16:42Z INFO db_app] ✅ 데이터베이스에 연결되었습니다!

우리는 데이터베이스에 연결할 수 있습니다. .env 파일에서 구성한 모든 연결 정보를 기억해 주세요. 이 정보는 docker-compose, sqlx-cli, 그리고 우리의 어플리케이션에서 공유되었습니다.

## Inserts

<div class="content-ad"></div>

이제 연결 풀이 준비되었으니, 우리가 살펴볼 첫 번째 데이터베이스 로직은 삽입입니다. sqlx를 사용하여 삽입하는 방법은 다음과 같습니다.

```rust
let insert_result = sqlx::query_as!(
    Locations,
    "INSERT INTO locations (id,name) VALUES (1, 'Location A') RETURNING *"
)
.fetch_one(&pool)
.await;

match insert_result {
    Ok(location) => {
        info!("✓Inserted: {:?}", location);
    }
    Err(e) => {
        error!("Error Insert: {}", e.to_string())
    }
}
```

이 코드는 하나의 레코드를 삽입할 것입니다. 물론 매개변수를 사용할 수도 있으며, 이는 업데이트를 살펴볼 때 살펴볼 것입니다.

## 질의하기

<div class="content-ad"></div>

다음으로, 데이터베이스를 조회하는 방법을 살펴보겠습니다.

```js
    let query_result = sqlx::query_as!(Locations, "SELECT * FROM Locations")
        .fetch_all(&pool)
        .await;
    if query_result.is_err() {
        let message = "모든 위치를 가져오는 동안 문제가 발생했습니다.";
        error!("{}{}", message, query_result.err().unwrap());
    } else {
        info!("😎 위치에 대한 쿼리 결과 {:?}", query_result.unwrap());
    }
```

이것은 성공적으로 작동했다면 unwrap을 통해 결과에 접근할 수 있는 결과를 반환합니다. 쿼리 내의 이슈가 발생했다면 해당 에러에 접근할 수도 있습니다.

## 업데이트

<div class="content-ad"></div>

다음 작업은 데이터베이스를 업데이트하는 것입니다. 이는 이전에 삽입한 것과 비슷할 것입니다.

```js
    let update_result = sqlx::query_as!(
        Sessions,
        "UPDATE sessions SET watts = 415 WHERE id = $1 RETURNING *",
        1i64,
    )
    .fetch_one(&pool)
    .await;

    match update_result {
        Ok(session) => {
            info!("✓Update: {:?}", session);
        }
        Err(e) => {
            error!("Error Update: {}", e.to_string())
        }
    }
```

이 예제에서 주목할 점은 $1이라는 매개변수 자리 표시자를 사용하는 준비된 문(statement)를 사용하고 있다는 것입니다. 그런 다음 SQL 문자열 뒤에 매개변수를 전달합니다.

## Deletes

<div class="content-ad"></div>

마지막으로, 데이터베이스에서 레코드를 삭제하는 CRUD 작업을 완료합니다. 우리의 목적은 데이터베이스를 정리하는 데 사용할 것이며, 이렇게 하면 언제든지 응용 프로그램을 실행할 수 있습니다.

```js
let rows_deleted = sqlx::query!("DELETE from sessions")
    .execute(&pool)
    .await
    .unwrap()
    .rows_affected();

info!("✕ 세션 테이블에서 {}개의 행 삭제됨", rows_deleted);
```

여기서는 연산에서처럼 sqlx::query 대신 sqlx::query_as를 사용합니다. 또한 언랩 이후 .rows_affected를 추가하여 삭제된 행 수를 얻습니다.

## 트랜잭션

<div class="content-ad"></div>

```rust
   let tx = pool.begin().await.expect("트랜잭션을 시작할 수 없습니다");

   // 데이터베이스 작업 수행(데이터 삽입 또는 변경)

   tx.commit().await.expect("트랜잭션을 커밋할 수 없습니다");
```

커밋을 호출하지 않으면 트랜잭션이 범위를 벗어나면 자동으로 롤백됩니다.

## 완전한 응용 프로그램

<div class="content-ad"></div>

위의 코드는 우리가 이전에 논의한 모든 다양한 기능이 하나의 애플리케이션에 모두 포함된 완전한 애플리케이션입니다.

```js
use dotenv::dotenv;
use log::{error, info};
use sqlx::{postgres::PgPoolOptions, Pool, Postgres};

#[derive(Debug)]
struct Locations {
    id: i64,
    name: String,
}

#[derive(Debug)]
struct Sessions {
    id: i64,
    location_id: i64,
    watts: i64,
    vin: String,
}
async fn insert_into_locations(pool: Pool<Postgres>) {
    let tx = pool.begin().await.expect("Unable to begin transaction");

    let insert_result = sqlx::query_as!(
        Locations,
        "INSERT INTO locations (id,name) VALUES (1, 'Location A') RETURNING *"
    )
    .fetch_one(&pool)
    .await;

    match insert_result {
        Ok(location) => {
            info!("✓Inserted: {:?}", location);
        }
        Err(e) => {
            error!("Error Insert: {}", e.to_string())
        }
    }
    let insert_result = sqlx::query_as!(
        Locations,
        "INSERT INTO locations (id,name) VALUES (2, 'Location B') RETURNING *"
    )
    .fetch_one(&pool)
    .await;

    match insert_result {
        Ok(location) => {
            info!("✓Inserted: {:?}", location);
        }
        Err(e) => {
            error!("Error Insert: {}", e.to_string())
        }
    }

    tx.commit().await.expect("Unable to commit the transaction");
}

async fn insert_into_sessions(pool: Pool<Postgres>) {
    let tx = pool.begin().await.expect("Unable to begin transaction");

    let insert_result = sqlx::query_as!(
        Sessions,
        "INSERT INTO sessions (id,location_id, watts, vin) VALUES (1, 1, 420, '2FMZA52286BA02033') RETURNING *"
    )
    .fetch_one(&pool)
    .await;

    match insert_result {
        Ok(session) => {
            info!("✓Inserted: {:?}", session);
        }
        Err(e) => {
            error!("Error Insert: {}", e.to_string())
        }
    }
    let insert_result = sqlx::query_as!(
        Sessions,
        "INSERT INTO sessions (id,location_id, watts, vin) VALUES (2, 2, 393, '1GMYA52286BA04055') RETURNING *"
)
    .fetch_one(&pool)
    .await;

    match insert_result {
        Ok(session) => {
            info!("✓Inserted: {:?}", session);
        }
        Err(e) => {
            error!("Error Insert: {}", e.to_string())
        }
    }

    tx.commit().await.expect("Unable to commit the transaction");
}

async fn update_sessions(pool: Pool<Postgres>) {
    let tx = pool.begin().await.expect("Unable to begin transaction");

    let update_result = sqlx::query_as!(
        Sessions,
        "UPDATE sessions SET watts = 415 WHERE id = $1 RETURNING *",
        1i64,
    )
    .fetch_one(&pool)
    .await;

    match update_result {
        Ok(session) => {
            info!("✓Update: {:?}", session);
        }
        Err(e) => {
            error!("Error Update: {}", e.to_string())
        }
    }

    tx.commit().await.expect("Unable to commit the transaction");
}

async fn clean_db(pool: Pool<Postgres>) {
    let rows_deleted = sqlx::query!("DELETE from sessions")
        .execute(&pool)
        .await
        .unwrap()
        .rows_affected();

    info!("✕Deleted {} rows from sessions table", rows_deleted);

    let rows_deleted = sqlx::query!("DELETE from locations")
        .execute(&pool)
        .await
        .unwrap()
        .rows_affected();
    info!("✕Deleted {} rows from locations table", rows_deleted);
}

async fn query_locations(pool: Pool<Postgres>) {
    let query_result = sqlx::query_as!(Locations, "SELECT * FROM Locations")
        .fetch_all(&pool)
        .await;
    if query_result.is_err() {
        let message = "Something bad happened while fetching all locations";
        error!("{}", message);
    } else {
        info!("😎Query Result For Locations {:?}", query_result);
    }
}

async fn query_sessions(pool: Pool<Postgres>) {
    let query_result = sqlx::query_as!(Sessions, "SELECT * FROM Sessions")
        .fetch_all(&pool)
        .await;
    if query_result.is_err() {
        let message = "Something bad happened while fetching all sessions";
        error!("{}", message);
    } else {
        info!("😎Query Result for Sessions {:?}", query_result);
    }
}

#[tokio::main]
async fn main() {
    if std::env::var_os("RUST_LOG").is_none() {
        std::env::set_var("RUST_LOG", "info");
    }
    dotenv().ok();
    env_logger::init();

    let database_url = std::env::var("DATABASE_URL").expect("DATABASE_URL must be set");
    let pool = match PgPoolOptions::new()
        .max_connections(10)
        .connect(&database_url)
        .await
    {
        Ok(pool) => {
            info!("✅Connection to the database is successful!");
            pool
        }
        Err(err) => {
            error!("🔥 Failed to connect to the database: {:?}", err);
            std::process::exit(1);
        }
    };
    clean_db(pool.clone()).await;

    insert_into_locations(pool.clone()).await;
    query_locations(pool.clone()).await;

    insert_into_sessions(pool.clone()).await;
    query_sessions(pool.clone()).await;

    update_sessions(pool.clone()).await;
    query_sessions(pool.clone()).await;
}
```

위 애플리케이션을 실행한 결과는 다음과 같습니다.

<img src="/assets/img/2024-05-23-LearningRustPart11BuildersandDatabaseInteraction_5.png" />

<div class="content-ad"></div>

위와 같이 데이터베이스 상호작용 토론을 마쳤습니다. sqlx를 사용하여 데이터베이스 작업을 수행하는 방법에 대해 좋은 개요를 제공했을 겁니다.

# 요약

저희 러스트 학습 시리즈의 이 부분을 즐기셨기를 바랍니다. 시리즈 이번 섹션에서는 먼저 러스트에서 객체 생성에 대한 매우 유용한 패턴인 빌더 패턴을 살펴보았습니다. 이는 다른 언어에서 익숙할 수 있지만, 러스트에서 어떻게 구현하는지 살펴보았습니다.

다음으로, 우리는 Rust를 사용하여 데이터베이스인 특히 Postgres와 상호작용하는 방법을 검토했습니다. 우리는 마이그레이션을 실행하고 데이터베이스에 연결하는 방법을 보았으며, 그 후 DB에 대해 여러 가지 CRUD 작업을 수행하는 방법을 살펴보았습니다.

<div class="content-ad"></div>

러스트 학습 여정에 함께해줘서 고마워요.

좋은 여행 되세요!
