---
title: "소프트웨어 품질 보증  데이터베이스와의 시스템 통합 검증 방법"
description: ""
coverImage: "/assets/img/2024-05-18-SoftwarequalityassuranceHowtoverifysystemintegrationwithdatabases_0.png"
date: 2024-05-18 15:10
ogImage: 
  url: /assets/img/2024-05-18-SoftwarequalityassuranceHowtoverifysystemintegrationwithdatabases_0.png
tag: Tech
originalTitle: "Software quality assurance — How to verify system integration with databases"
link: "https://medium.com/itnext/software-quality-assurance-how-to-verify-system-integration-with-databases-acd1667fd7fa"
---



![이미지](/assets/img/2024-05-18-SoftwarequalityassuranceHowtoverifysystemintegrationwithdatabases_0.png)

소프트웨어 개발의 성공에는 테스트가 중요합니다. 소프트웨어 엔지니어들은 대부분의 시간을 자동화된 테스트 작성에 할애합니다. 비교적으로, 비즈니스 로직 개발에 소요되는 시간은 그리 많지 않습니다.

테스트는 소프트웨어 전달에서 중요한 역할을 하므로, 테스팅 지식은 소프트웨어 엔지니어로서의 채용 면접에서 표준 질문이 되었습니다. 가끔 지원자 중 일부는 데이터베이스 통합의 품질을 어떻게 보증할지에 대해 대답하지 못하는 경우가 있습니다.

데이터베이스는 대부분의 시스템에서 중요한 구성 요소입니다. 데이터베이스와 통신하기 위한 일반적인 설계 패턴은 액세스 요청을 SQL 쿼리로 변환하기 위한 데이터 액세스 계층을 갖는 것입니다.


<div class="content-ad"></div>

이 다이어그램은 API 엔드포인트, 비즈니스 로직 및 데이터 액세스를 별도의 레이어로 분리하는 전통적인 디자인 패턴을 보여줍니다. 비즈니스 로직은 데이터 액세스 레이어에 의존하여 데이터베이스에서 읽기/쓰기 작업을 수행합니다.

![이미지](/assets/img/2024-05-18-SoftwarequalityassuranceHowtoverifysystemintegrationwithdatabases_1.png)

테스트할 때, 데이터 액세스 레이어의 SQL 쿼리가 올바른지 어떻게 확인할까요?

최근 취업 면접에서 소프트웨어 엔지니어와 이야기를 나눴습니다. 몇 년의 경험이 있더라도 자동화된 테스트가 표준적인 실천 방법이 되었음에도 불구하고 데이터 액세스 레이어에 대한 통합 테스트를 어떻게 구현하는지 알지 못하는 사람들도 있음을 깨달았습니다.

<div class="content-ad"></div>

우선, 통합 테스트는 단위 테스트와 유사합니다. 테스트 범위는 예를 들어 데이터 액세스 레이어와 같은 단일 컴포넌트에 집중됩니다. 그러나 차이점은 단위 테스트에서 하는 것처럼 가짜 데이터베이스를 만들 수 없다는 것입니다. 대신, 독립 실행형 데이터베이스 엔진을 시작하여 데이터베이스와 상호 작용을 확인해야 합니다.

![이미지](/assets/img/2024-05-18-SoftwarequalityassuranceHowtoverifysystemintegrationwithdatabases_2.png)

이 글에서 데이터 액세스 레이어에 대한 자동화된 테스트를 구현하는 2가지 다른 방법을 공유하겠습니다. 작업 방식을 더 잘 이해하도록 샘플 코드는 스프링 프레임워크를 사용하지 않고 모두 일반 자바 코드로 작성되어 있습니다.

# 샘플 데이터 스키마

<div class="content-ad"></div>

직원 레코드의 간단한 데이터베이스 스키마를 기반으로 한 통합 테스트의 구현을 보여드리겠습니다. 직원 레코드에는 다음과 같은 데이터 필드가 포함되어 있습니다: 고유 식별자, 이름, 급여 및 부서.

```js
CREATE TABLE EMPLOYEE (
   id int,
   name varchar(255),
   department varchar(255),
   salary decimal(14,2),
   primary key (id)
);
```

다음과 같은 함수를 제공하는 데이터 액세스 레이어가 있습니다:

- 직원 레코드 삽입
- ID에 따른 직원 레코드 검색
- 부서에 따른 직원 레코드 검색
- 부서별 평균 급여 검색

<div class="content-ad"></div>

GitHub 레포지토리의 전체 소스 코드는 다음 링크를 참조해 주세요 (https://github.com/gavinklfong/data-access-layer-test-demo).

# 내부 메모리 데이터베이스

이 아이디어는 컴포넌트가 테스트하는 데 연결할 수 있는 일회용 데이터베이스를 시작하는 것입니다. 가벼운 내부 메모리 데이터베이스 엔진을 실행하는 것은 통합 테스트를 신속하게 실행하는 빠른 방법입니다. 이는 데이터베이스가 디스크의 읽기/쓰기 없이 메모리에서만 작동하기 때문에 빠릅니다. 테스트 실행이 끝나면 데이터베이스를 파기하고 버릴 수 있습니다.

H2 데이터베이스는 인기 있는 내부 메모리 데이터베이스입니다. H2 데이터베이스를 활성화하려면 Maven의 pom.xml에 이 의존성을 추가하세요.

<div class="content-ad"></div>


<의존성>
   <그룹 아이디>com.h2database</그룹 아이디>
   <아티팩트 아이디>h2</아티팩트 아이디>
   <범위>런타임</범위>
</의존성>


H2 데이터베이스에서 테스트를 실행하는 것은 정말 편리합니다. 데이터베이스 시작업이 필요 없습니다. JDBC 연결 문자열 jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;MODEL=MYSQL을 사용하여 데이터베이스 연결을 생성하면 H2 데이터베이스가 자동으로 시작됩니다.

다음 2가지 매개변수로 인메모리 데이터베이스가 생성된 것을 알 수 있습니다:

- DB_CLOSE_DELAY=-1 — JVM이 종료될 때까지 데이터베이스를 유지하려면 이 매개변수가 필요합니다.
- MODEL=MYSQL — 실제 환경에서 사용되는 MYSQL과 SQL 구문을 호환되게 만듭니다.

<div class="content-ad"></div>

테스트 시나리오에서 데이터베이스는 일반적으로 모두 공유되며, 데이터베이스 연결은 정적 멤버로 설정되어 있습니다. 처음에는 직원 테이블을 생성하는 schema.sql을 실행합니다.

이 예에서는 경량 라이브러리 JDBI를 사용하여 데이터베이스 연결 및 쿼리 실행을 합니다.

```js
class EmployeeDaoInMemoryH2Test {

   // 인메모리 데이터베이스에 연결
   private static final Jdbi jdbi = Jdbi
           .create("jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;MODE=MYSQL");

   private EmployeeDao employeeDao;

   @BeforeAll
   static void setupAll() {
       // 테이블 설정
       jdbi.withHandle(handle ->
               handle.createScript(ClasspathSqlLocator
                        .removingComments().getResource("schema.sql"))
                        .execute()
       );
   }

   @BeforeEach
   void setup() {
       employeeDao = new EmployeeDao(jdbi);

       // 클린업 및 테스트 데이터 다시 삽입
       jdbi.withHandle(handle ->
               handle.createScript(ClasspathSqlLocator
                        .removingComments().getResource("employee.sql"))
                        .execute()
       );
   }


// ... 테스트 시나리오 ...

}
```

각 테스트 시나리오 시작 전에 data.sql이 실행되어 직원 테이블의 모든 레코드가 정리되고 테스트 레코드가 다시 삽입됩니다.

<div class="content-ad"></div>

```js
DELETE FROM EMPLOYEE;

INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (1, 'Rueben Hardy', 'FINANCE', 1291.8);
INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (2, 'Frank Dunlap', 'FINANCE', 10025.3);
INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (3, 'Anna Melendez', 'FINANCE', 8773.13);
INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (4, 'Joan Riggs', 'OPERATION', 6597.89);
INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (5, 'April Davidson', 'OPERATION', 1563.19);
INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (6, 'Molly Woodward', 'MARKETING', 10442.24);
INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (7, 'Roisin Noble', 'MARKETING', 7288.92);
INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (8, 'Abdullahi Morse', 'MARKETING', 1635.55);
INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (9, 'Gina Shepard', 'SALES', 7703);
INSERT INTO EMPLOYEE(id, name, department, salary) 
  VALUES (10, 'Cecil Burch', 'SALES', 3422.67);
```

스키마와 테스트 데이터가 준비되면, 테스트 시나리오 구현은 직관적일 것입니다. 결과를 확인하려면 직관적인 SQL을 사용하는 것이 좋습니다.

# 데이터 설정 및 테스트 결과 확인 방법

데이터 액세스 계층이 테스트 중이므로, 데이터 액세스 계층의 시스템 동작이 완전히 테스트되기 전에는 보장되지 않습니다. 따라서, 데이터 설정 및 확인은 데이터 액세스 계층의 메서드를 호출하는 대신 직접적인 SQL을 사용하여 수행해야 합니다.```

<div class="content-ad"></div>

그러므로 데이터베이스 테스트 데이터 설정은 데이터 액세스 계층의 insertEmployee()를 사용하는 대신 SQL 스크립트 파일로 수행됩니다.

또한, 데이터 액세스 계층의 insertEmployee()를 확인하는 것이 더 좋은 방법은 일반 SQL을 사용하는 것입니다. 아래 예시는 getEmployeeById()를 호출하여 삽입된 직원 레코드를 확인하는 것은 좋은 방법이 아닙니다. 이는 getEmployeeById()가 정상 작동하는지 알 수 없기 때문입니다. 레코드 검색 함수에 잠재적인 결함이 있는 경우 정확하지 않은 테스트 결과를 생성할 수 있습니다.

```js
@Test
void testInsertEmployee() {
   Employee newEmployee = Employee.builder()
           .id(99)
           .name("Gina Bond")
           .department("MARKETING")
           .salary(new BigDecimal("3425.5"))
           .build();

   // 새 직원 입력 테스트
   assertThat(employeeDao.insertEmployee(newEmployee)).isEqualTo(1);
   
   // 이것은 좋은 방법이 아닙니다
   // getEmployeeById()를 사용하여 삽입된 레코드 확인
   assertThat(employeeDao.getEmployeeById(99))
          .isNotEmpty()
          .contains(newEmployee);
}
```

대안으로, 새로 삽입된 레코드를 검색하기 위한 일반 SQL을 사용하는 것이 더 안전한 방법입니다.

<div class="content-ad"></div>


@Test
void testInsertEmployee() {
   Employee newEmployee = Employee.builder()
           .id(99)
           .name("Gina Bond")
           .department("MARKETING")
           .salary(new BigDecimal("3425.5"))
           .build();

   assertThat(employeeDao.insertEmployee(newEmployee)).isEqualTo(1);

   // verify test result using plain SQL
   Optional<Employee> insertedRecord = jdbi.withHandle(handle ->
           handle.createQuery("SELECT id, name, department, salary " +
                           "FROM EMPLOYEE " +
                           "WHERE id = :id")
                   .bind("id", 99)
                   .mapToBean(Employee.class)
                   .findOne());

   assertThat(insertedRecord).isNotEmpty()
           .contains(newEmployee);
}


# 도커 컨테이너 내의 실제 데이터베이스

H2 데이터베이스가 지원하는 MySQL 호환 모델에도 불구하고, 시스템 동작은 아직 실제 MySQL 데이터베이스와 완전히 동일하지는 않습니다. MySQL 데이터베이스와 진지하게 테스트를 하려면 도커 컨테이너 내에서 MySQL 데이터베이스를 이용하는 것이 합리적인 선택입니다.

이 개념은 인메모리 데이터베이스 사용과 유사합니다. 데이터베이스를 메모리에서 실행하는 대신, 도커 컨테이너에서 데이터베이스를 생성합니다. 그런 다음 테스트가 완료되면 컨테이너를 정지하고 제거합니다.
  

<div class="content-ad"></div>

도커 컨테이너에서 데이터베이스를 실행함으로써 목표 데이터베이스에 대한 데이터 액세스 레이어를 테스트할 수 있는 이점을 누릴 수 있어요. 이는 시스템 동작이 실제 환경의 데이터베이스에 가까워짐을 의미합니다.

Testcontainers는 도커 컨테이너를 테스트하기 위한 간편한 방법을 제공하는 멋진 라이브러리에요. MySQL 컨테이너를 실행하기 위해 Maven pom.xml에 이 종속성을 추가하세요.

```js
<dependency>
   <groupId>org.testcontainers</groupId>
   <artifactId>mysql</artifactId>
   <scope>test</scope>
</dependency>
```

각 테스트 시나리오마다 MySQL 데이터베이스를 시작하는 것은 가능하지만, 새로운 도커 컨테이너를 각 시나리오 전에 시작해야 하므로 권장하지 않아요. 도커 컨테이너는 시작하는 데 훨씬 더 오랜 시간이 걸리기 때문에 컴퓨팅 성능에 따라 약 4~5초가 소요될 수 있어요. 그리고 각 시나리오 전에 새 도커 컨테이너를 시작해야 한다면 10가지 테스트 시나리오를 실행하는 데 약 50초가 소요될 수 있어요.

<div class="content-ad"></div>

테스트 요구사항이 특별하지 않다면, 일반적인 관행은 MySQL 도커 컨테이너를 시작하고 모든 테스트 시나리오가 동일한 데이터베이스를 공유하도록하는 것입니다.

따라서 MySQLContainer는 정적 멤버로 정의됩니다. 초기 스크립트인 schema.sql을 지정하여 컨테이너가 실행된 직후 employee 테이블이 생성됩니다.

컨테이너 라이프사이클을 관리하기 위해 JUnit 어노테이션 @BeforeAll을 사용하여 컨테이너를 시작합니다. 모든 테스트 시나리오가 완료된 후 @AfterAll을 사용하여 컨테이너를 중지하고 각 테스트 시나리오 전에 데이터를 재설정합니다.

```js
class EmployeeDaoMySQLTestContainersTest {

  private static final MySQLContainer<?> MYSQL_CONTAINER =
           new MySQLContainer<>(DockerImageName.parse("mysql:latest"))
                   .withInitScript("schema.sql");

  private Jdbi jdbi;

  private EmployeeDao employeeDao;

  @BeforeAll
  static void beforeAll() {
     MYSQL_CONTAINER.start();
  }
  
  @AfterAll
  static void afterAll() {
     MYSQL_CONTAINER.stop();
  }
  
  @BeforeEach
  void setup() {
     jdbi = Jdbi.create(MYSQL_CONTAINER.getJdbcUrl(),
             MYSQL_CONTAINER.getUsername(), MYSQL_CONTAINER.getPassword());
  
     employeeDao = new EmployeeDao(jdbi);
  
     jdbi.withHandle(handle ->
         handle.createScript(ClasspathSqlLocator
                 .removingComments().getResource("employee.sql"))
                 .execute()
  );
}

// 테스트 시나리오 ...
```

<div class="content-ad"></div>

컨테이너의 라이프사이클 관리를 간소화하기 위해 testcontainers 라이브러리는 @Testcontainers와 @Container 어노테이션을 제공합니다.

JUnit 5에서 testcontainers를 지원하기 위해 다음 의존성을 추가해주세요.

```js
<dependency>
   <groupId>org.testcontainers</groupId>
   <artifactId>junit-jupiter</artifactId>
   <scope>test</scope>
</dependency>
```

테스트 클래스에 @Testcontainers를 넣고 MySQLContainer에 @Container를 추가하면, @BeforeAll 및 @AfterAll을 사용하여 컨테이너를 시작 및 중지할 필요가 없어집니다. 컨테이너는 자동으로 시작되며, 모든 테스트 시나리오가 완료된 후에 중지됩니다.

<div class="content-ad"></div>

```java
@Testcontainers
class EmployeeDaoMySQLTestContainersAnnotationTest {

   @Container
   private static final MySQLContainer<?> MYSQL_CONTAINER =
           new MySQLContainer<>(DockerImageName.parse("mysql:latest"))
                   .withInitScript("schema.sql");

     @BeforeEach
     Private setup() {

      }

   // … test scenarios …
}
```

# 마지막으로

통합 테스트와 단위 테스트는 같은 개념을 공유하지만, 두 유형 모두 일부 의존성에 대한 모의/스터브화된 특정 수준의 구성요소에 중점을 둡니다.

그러나 데이터베이스를 사용한 통합 테스트는 약간 다릅니다. 올바른 테스트를 수행하기 위해 실제 데이터베이스 엔진이 필요합니다. 이 글은 테스트를 원활하게 실행하기 위해 스키마 설정이 포함된 일회용 데이터베이스를 생성하는 방법에 대한 간략한 개요와 기술을 제공합니다.


<div class="content-ad"></div>

다음 GitHub 저장소(https://github.com/gavinklfong/data-access-layer-test-demo)를 참조하시면 전체 소스 코드를 확인하실 수 있습니다.