---
title: "Amazon Athena와 함께하는 Spring Boot 사용법"
description: ""
coverImage: "/assets/img/2024-06-23-SpringBootwithAmazonAthena_0.png"
date: 2024-06-23 20:39
ogImage: 
  url: /assets/img/2024-06-23-SpringBootwithAmazonAthena_0.png
tag: Tech
originalTitle: "Spring Boot with Amazon Athena"
link: "https://medium.com/gitconnected/spring-boot-with-amazon-athena-8eab6615f839"
---


Spring Boot과 데이터 레이크를 통합하는 방법을 알려드릴게요. 아마존 S3 데이터에 SQL 인터페이스를 제공합니다.

![Spring Boot with Amazon Athena](/assets/img/2024-06-23-SpringBootwithAmazonAthena_0.png)

# 소개

우리는 SpringBoot 애플리케이션을 Amazon Athena와 연결하는 방법을 살펴볼 거에요. Athena가 무엇이며 어떻게 작동하는지 먼저 배운 후, Athena를 쿼리하는 SpringBoot 3 애플리케이션을 생성할 거에요. 해당 애플리케이션은 노출된 Rest API를 통해 Athena에 쿼리합니다.

<div class="content-ad"></div>

# 아테나 소개

아마존 아테나는 다양한 소스와 형식에 저장된 데이터에 표준 SQL을 실행할 수 있는 쿼리 서비스입니다. 주로 S3와 함께 사용됩니다. 아테나는 Facebook에서 오픈 소스로 공개된 분산 SQL 엔진 Presto를 기반으로 합니다. 아테나는 서버리스이므로 설정하거나 관리할 인프라가 필요하지 않습니다.

아테나는 아마존 S3에 있는 데이터를 분석하는 데 사용됩니다. 아테나는 CSV, ORC, Apache Parquet, Apache Avro, JSON과 같은 데이터 형식을 포함한 다양한 유형의 구조화 및 비구조화 데이터 유형과 작동할 수 있습니다.

아마존의 Simple Storage Service인 S3는 아테나와 함께 사용하기 가장 권장되는 데이터 저장소입니다. S3는 AWS의 객체 저장 서비스로 어떤 파일 형식의 저렴한 저장을 제공합니다. S3는 버킷 내의 객체로 데이터를 저장합니다. 객체는 파일과 파일을 설명하는 메타데이터를 포함합니다. 버킷은 객체를 위한 컨테이너입니다.

<div class="content-ad"></div>

Amazon S3에 데이터를 저장하려면 먼저 버킷을 생성하고 버킷 이름과 AWS 지역을 지정해야 합니다. 그런 다음 해당 버킷에 데이터를 Amazon S3의 객체로 업로드합니다. 각 객체는 버킷 내에서의 객체를 식별하는 고유 키(또는 키 이름)를 가지고 있습니다.

# 데이터 다운로드

저희 데이터는 전 세계 도시 인구에 대한 무료 버전 데이터 입니다. 다음 링크에서 액세스할 수 있습니다: [https://simplemaps.com/data/world-cities](https://simplemaps.com/data/world-cities). 원본 데이터 세트에는 몇 가지 조작이 필요했는데, 예를 들어 따옴표 제거와 같은 조작이 있었으며, 따라서 소스 코드에 포함된 클린한 버전이 제공되며 다음 링크를 통해 다운로드 받을 수 있습니다. 이 데이터에는 다음 스키마가 있습니다.

```js
도시, 도시_ascii, 위도, 경도, 국가, iso2, iso3, 관리 이름, 수도, 인구, id
```

<div class="content-ad"></div>

그리고 몇 줄의 샘플이 이렇게 보입니다.

```js
Tokyo,Tokyo,35.6839,139.7744,Japan,JP,JPN,Tōkyō,primary,39105000,1392685764
Jakarta,Jakarta,-6.2146,106.8451,Indonesia,ID,IDN,Jakarta,primary,35362000,1360771077
Delhi,Delhi,28.6667,77.2167,India,IN,IND,Delhi,admin,31870000,1356872604
Manila,Manila,14.6,120.9833,Philippines,PH,PHL,Manila,primary,23971000,1608618140
São Paulo,Sao Paulo,-23.5504,-46.6339,Brazil,BR,BRA,São Paulo,admin,22495000,1076532519
```

다운로드한 파일의 위치를 주의해주세요. 곧 접근할 것입니다.

# S3 준비

<div class="content-ad"></div>

다음으로는 데이터를 저장하는 데 사용할 S3 버킷을 생성해야 합니다. 이는 고유한 이름이어야 합니다. 제가 사용한 이름은 world-cities-athena 입니다. 상호적으로 따라오는 경우 다른 고유한 이름을 사용해야 합니다.

Amazon S3 - Buckets - Create bucket 페이지로 이동하여 다음을 입력하고, 버킷 이름으로 교체하세요.

이미지 src="/assets/img/2024-06-23-SpringBootwithAmazonAthena_1.png" />

나머지 설정은 기본값으로 선택하고, 하단의 "버킷 생성" 버튼을 눌러주세요.

<div class="content-ad"></div>

그럼 새 버킷을 선택하고 "폴더 생성" 버튼을 사용하여 input 및 queries라는 두 개의 폴더를 만들어주세요.

<img src="/assets/img/2024-06-23-SpringBootwithAmazonAthena_2.png" />

폴더를 만들면 위의 이미지와 같이 구성이 되어야 해요.

마지막으로, input 폴더를 클릭하고 "업로드"를 선택하여 이전 섹션에서 다운로드한 데이터 세트를 업로드해주세요.

<div class="content-ad"></div>


![Spring Boot with Amazon Athena](/assets/img/2024-06-23-SpringBootwithAmazonAthena_3.png)

이렇게 완료되면 위와 같이 나타날 것입니다.

마지막 단계는 쿼리 결과를 저장할 버킷을 생성하는 것입니다. 이 버킷은 우리가 Athena를 쿼리할 때 포함되며 각 쿼리의 결과가 해당 버킷에 저장됩니다. 그러면 쿼리를 다시 실행하지 않고 결과에 액세스할 수 있으며 비용이 발생하지 않습니다. 그러나 S3에 결과 파일의 저장 비용은 사용자가 책임져야 합니다. 버킷을 생성하려면 다시 Amazon S3 - `Buckets -` Create bucket 페이지로 이동하고 버킷 이름을 world-cities-results로 입력하십시오.

![Spring Boot with Amazon Athena](/assets/img/2024-06-23-SpringBootwithAmazonAthena_4.png)


<div class="content-ad"></div>

다시 모든 기본 값으로 유지하고 아래에 있는 버킷 생성 버튼을 선택하세요.

이제 S3를 구성하고, 다음 단계로 넘어가서 Athena 데이터베이스와 테이블을 설정할 준비가 되었습니다.

# Athena 구성

Athena 데이터베이스를 구성하기 위한 몇 가지 단계가 있습니다. 이 모든 작업은 Athena AWS 콘솔 내에서 수행됩니다.

<div class="content-ad"></div>


![SpringBootwithAmazonAthena_5](/assets/img/2024-06-23-SpringBootwithAmazonAthena_5.png)

Note that the interface changed somewhat recently and what you select is the line stating “Query your data with Trino SQL”, Trino being a fork of the SQL Query Engine.

Once you select Launch query editor then you will see this screen.

![SpringBootwithAmazonAthena_6](/assets/img/2024-06-23-SpringBootwithAmazonAthena_6.png)


<div class="content-ad"></div>

오른쪽에 있는 쿼리 창을 사용하여 Athena와 상호 작용할 수 있습니다. 이 방법을 사용하겠습니다. 객체를 수동으로 정의하는 마법사 형식의 테이블 생성 방법도 있습니다.

먼저 데이터베이스를 생성하겠습니다. 데이터베이스는 처음에 기본값으로 설정되어 있고 우리만의 특정 데이터베이스를 생성하려고 합니다.

쿼리 창에 다음을 입력하세요.

```js
create database worldcitiesdb
```

<div class="content-ad"></div>

다음과 같이 됩니다.


![Spring Boot with Amazon Athena](/assets/img/2024-06-23-SpringBootwithAmazonAthena_7.png)


그런 다음 **실행**을 누르세요.

그럼 왼쪽 데이터 창의 Database 드롭다운에서 새 데이터베이스를 선택할 수 있습니다.

<div class="content-ad"></div>

현재 시점에서는 새로운 데이터베이스에 테이블이 없는 상태일 것입니다.

테이블을 생성하려면 쿼리를 사용하여 데이터 파일을 가리키고 동시에 데이터로 테이블을 생성하고 채울 것입니다.

오른쪽 쿼리 창에 다음 SQL을 입력하세요.

```js
CREATE EXTERNAL TABLE IF NOT EXISTS `worldcitiesdb`.`worldcities` (
 `city` string,
 `city_ascii` string,
 `lat` double,
 `lng` double,
 `country` string,
 `iso2` string,
 `iso3` string,
 `admin_name` string,
 `capital` string,
 `population` int,
 `id` int
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe'
WITH SERDEPROPERTIES (
 'serialization.format' = ',',
 'field.delim' = ','
)
LOCATION 's3://world-cities-athena/input/'
TBLPROPERTIES ('skip.header.line.count' = '1')
```

<div class="content-ad"></div>

위의 create 문에서 특히 이 줄에 주목해주세요.

```js
LOCATION 's3://world-cities-athena/input/'
```

이 줄을 여러분의 버킷 이름으로 교체해야 하지만, 그 외에는 수정하지 말아야 합니다.

쿼리에서 world-cities-athena를 여러분의 버킷으로 교체하신 후 Run을 눌러주세요.

<div class="content-ad"></div>

한 두 번 실행한 후 녹색 성공 메시지가 표시됩니다. 그러면 새 테이블이 Athena에서 왼쪽 데이터 창에 표시됩니다.

![Spring Boot with Amazon Athena](/assets/img/2024-06-23-SpringBootwithAmazonAthena_8.png)

쿼리 창에 테스트 쿼리를 입력하고 실행하여 테스트할 수 있습니다.

```js
select * from worldcities limit 50
```

<div class="content-ad"></div>

다음은 데이터베이스 및 테이블이 있는지 확인하고 쿼리할 수 있다는 것을 확인합니다.

![image](/assets/img/2024-06-23-SpringBootwithAmazonAthena_9.png)

Athena 데이터베이스가 올바르게 설정되었습니다.

# 코드 워크스루

<div class="content-ad"></div>

이 기사의 코드는 제 Github에서 찾을 수 있습니다. 코드와 함께 다양한 SQL 문도 포함되어 있어요.

Athena가 설정되었다면, 다음 단계는 REST API를 통해 노출할 SpringBoot 애플리케이션을 구현하는 것입니다. S3 기반 Athena 데이터베이스를 삭제하거나 업데이트할 수 없지만, 쿼리를 사용하여 테이블에 추가하거나 쿼리를 실행할 수 있어요.

우리 경우 이전 섹션에서 만든 테이블을 쿼리할 수 있는 두 개의 REST 엔드포인트를 생성할 거에요. 첫 번째 엔드포인트는 국가별로 가져오고, 두 번째 엔드포인트는 국가 코드가 필요합니다. 둘 다 인구순으로 레코드를 표시하며, 가장 큰 값부터 (내림차순) 나타날 거에요.

시작하기 전에 아래에 표시된 Spring Initializr에서 기본 종속성 집합을 가진 SpringBoot 애플리케이션을 생성할 거에요.

<div class="content-ad"></div>

<img src="/assets/img/2024-06-23-SpringBootwithAmazonAthena_10.png" />

여기서는 아테나와 작업하기 위해 필요한 라이브러리를 추가해야 합니다. 이를 위해 Java AWS SDK를 사용할 것입니다. 아래 종속성을 추가하여 이를 활성화하세요.

```js
<dependency>
   <groupId>software.amazon.awssdk</groupId>
   <artifactId>auth</artifactId>
   <version>2.22.9</version>
</dependency>
<dependency>
   <groupId>software.amazon.awssdk</groupId>
   <artifactId>athena</artifactId>
   <version>2.22.9</version>
</dependency>
<dependency>
   <groupId>com.amazonaws</groupId>
   <artifactId>aws-java-sdk</artifactId>
   <version>1.12.636</version>
</dependency>
```

위의 종속성들은 구현에 필요합니다.

<div class="content-ad"></div>

여기서 전체 pom.xml을 확인할 수 있습니다.

## 응용 프로그램 구성

응용 프로그램 구성에는 athena에 액세스하기 위해 필요한 모든 정보가 포함됩니다.

```java
@Data
@Component
@ConfigurationProperties(prefix = "athena")
public class AppConfiguration {
    private String region;
    private String workGroup;
    private String catalog;
    private String database;
    private String resultsBucket;
    private int clientExecutionTimeout;
    private int limit;
    private int retrySleep;
}
```

<div class="content-ad"></div>

앱리케이션.yml의 해당 섹션은 아래와 같이 표시됩니다.


athena:
  region: us-east-1
  workgroup: primary
  catalog: AwsDataCatalog
  database: worldcitiesdb
  limit: 25
  client-execution-timeout: 100000
  retry-sleep: 1000          # 1초
  results-bucket: s3://world-cities-results


## Athena 레이어

다음으로 Athena 클라이언트를 생성하고 쿼리를 실행하는 접근 논리를 구현해야 합니다.

<div class="content-ad"></div>

먼저 Athena 클라이언트를 생성하는 것이 중요합니다. 아래 제공된 코드대로 작업이 진행됩니다.

```js
@Component
public class AthenaClientFactory {

    public AthenaClient createAthenaClient() {
        return AthenaClient.builder()
            .credentialsProvider(DefaultCredentialsProvider.create())
            //.credentialsProvider(EnvironmentVariableCredentialsProvider.create()) // use if creds are
                                                                                    // env vars
            .build();
    }
}
```

이 코드는 ~/.aws/credentials 파일에서 액세스 및 시크릿을 로딩하는 과정입니다. 하지만 환경 변수를 사용하고 싶다면 EnvironmentVariableCredentialsProvider를 사용할 수도 있습니다.

이 작업은 software.amazon.awssdk.services.athena.AthenaClient 클래스의 인스턴스를 생성합니다.

<div class="content-ad"></div>

다음으로 필요한 구성 요소는 쿼리 시작 및 완료입니다. 이것은 두 단계 프로세스입니다.

- 쿼리를 실행하면 시스템에 의해 쿼리 ID가 생성됩니다.
- 위의 쿼리 ID는 쿼리 완료 상태를 확인하기 위해 성공 또는 실패 여부를 묻는 루프에서 사용됩니다.

아래에 코드가 표시되어 있습니다. 코드에 대한 설명은 코드 뒷부분에 이어집니다.

```js
@Slf4j
@Component
public class AthenaQuery {

    private final AppConfiguration appConfiguration;

    @Autowired
    public AthenaQuery(AppConfiguration appConfiguration) {
        this.appConfiguration = appConfiguration;
    }

    public String createAthenaQuery(AthenaClient athenaClient, String query) {

        try {
            QueryExecutionContext queryExecutionContext = QueryExecutionContext.builder()
                .database(appConfiguration.getDatabase())
                .build();

            ResultConfiguration resultConfiguration = ResultConfiguration.builder()
                .outputLocation(appConfiguration.getResultsBucket())
                .build();

            StartQueryExecutionRequest startQueryExecutionRequest = StartQueryExecutionRequest.builder()
                .queryString(query)
                .queryExecutionContext(queryExecutionContext)
                .resultConfiguration(resultConfiguration)
                .build();

            StartQueryExecutionResponse startQueryExecutionResponse = athenaClient.startQueryExecution(startQueryExecutionRequest);
            return startQueryExecutionResponse.queryExecutionId();
        } catch (AthenaException e) {
            log.error("Error during query execution", e);
        }
        return "";
    }

    public void completeAthenaQuery(AthenaClient athenaClient, String queryExecutionId) {

        GetQueryExecutionRequest getQueryExecutionRequest = GetQueryExecutionRequest.builder()
            .queryExecutionId(queryExecutionId).build();

        GetQueryExecutionResponse getQueryExecutionResponse;
        boolean isStillRunning = true;
        while (isStillRunning) {
            getQueryExecutionResponse = athenaClient.getQueryExecution(getQueryExecutionRequest);
            String queryState = getQueryExecutionResponse.queryExecution().status().state().toString();
            if (queryState.equals(QueryExecutionState.FAILED.toString())) {
                throw new RuntimeException("The Amazon Athena query failed to run with error message: " + getQueryExecutionResponse
                    .queryExecution().status().stateChangeReason());
            } else if (queryState.equals(QueryExecutionState.CANCELLED.toString())) {
                throw new RuntimeException("The Amazon Athena query was cancelled.");
            } else if (queryState.equals(QueryExecutionState.SUCCEEDED.toString())) {
                isStillRunning = false;
            } else {
                try {
                    Thread.sleep(appConfiguration.getRetrySleep());
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
            log.debug("The current status is: " + queryState);
        }
    }
}
```

<div class="content-ad"></div>

쿼리는 createAthenaQuery 메서드로 시작됩니다. 이는 데이터베이스와 결과 버킷을 가진 쿼리 컨텍스트를 생성한 다음 쿼리를 Athena에 제출하여 수행됩니다. 성공적으로 실행된 경우 UUID 형식의 쿼리 실행 ID가 반환되며, 그렇지 않으면 빈 문자열이 반환됩니다.

completeAthenaQuery 메서드에서는 쿼리가 성공적으로 완료되거나 쿼리가 실패하거나 취소(시간 초과) 될 때까지 루프를 사용합니다. 이는 결과가 준비될 때까지 실행 경로를 차단하기 위한 것입니다.

## DTO 레이어

이는 쿼리에서 가져온 필드와 일치하는 간단한 레코드입니다.

<div class="content-ad"></div>

```kotlin
data class TopTenCities(val name: String, val population: Int)
```

자바 레코드의 사용으로 이것은 간단한 한 줄짜리 문장이 됩니다. 레코드 클래스를 사용하면 getter 및 동등성 비교 기능이 있는 변경 불가능한 POJO를 얻을 수 있습니다.

## 서비스

구현 내에서 서비스는 모든 것을 연결합니다. 이는 이전에 살펴본 Athena 클래스를 사용하여 Athena에서 데이터를 검색하고, 그런 다음 위의 POJO TopTenCities로 매핑합니다.

<div class="content-ad"></div>

```java
@Service
@AllArgsConstructor
@Slf4j
public class AthenaService {

    private static final String QUERY_ALL = "SELECT city, population FROM worldcities " +
        "ORDER BY population DESC LIMIT {limit}";

    private static final String QUERY = "SELECT city, population FROM worldcities where iso2 = '{country}' " +
        "ORDER BY population DESC LIMIT {limit}";

    private final AthenaClientFactory athenaClientFactory;

    private final AthenaQuery athenaQuery;

    public List<TopTenCities> getTop10All(Integer limit) {
        return run("", limit, QUERY_ALL);
    }

    public List<TopTenCities> getTop10(String country, Integer limit) {
        return run(country, limit, QUERY);
    }

    public List<TopTenCities> run(String country, Integer limit, String query) {

        AthenaClient athenaClient = athenaClientFactory.createAthenaClient();

        String finalQuery = query.replace("{country}", country)
            .replace("{limit}", String.valueOf(limit));
        String queryExecutionId = athenaQuery.createAthenaQuery(athenaClient,
            finalQuery);

        if (!queryExecutionId.isEmpty()) {
            log.info("Query submitted with query id {} and current mills {}", queryExecutionId, System.currentTimeMillis());

            athenaQuery.completeAthenaQuery(athenaClient, queryExecutionId);

            log.info("Query finished at {}", System.currentTimeMillis());

            return processResultRows(athenaClient, queryExecutionId);
        } else {
            log.error("Issue while running the query " + finalQuery);
            return new ArrayList<>(); // return empty array
        }
    }

    private List<TopTenCities> processResultRows(AthenaClient athenaClient, String queryExecutionId) {

        List<TopTenCities> result = new ArrayList<>();
        GetQueryResultsRequest getQueryResultsRequest = GetQueryResultsRequest.builder()
            .queryExecutionId(queryExecutionId).build();

        GetQueryResultsIterable getQueryResultsResults = athenaClient.getQueryResultsPaginator(getQueryResultsRequest);

        for (GetQueryResultsResponse Resultresponse : getQueryResultsResults) {
            List<ColumnInfo> columnInfoList = Resultresponse.resultSet().resultSetMetadata().columnInfo();

            int resultSize = Resultresponse.resultSet().rows().size();
            log.info("Result size: " + resultSize);

            List<Row> results = Resultresponse.resultSet().rows();
            result = processRow(results, columnInfoList);
        }
        return result;
    }

    private List<TopTenCities> processRow(List<Row> rowList, List<ColumnInfo> columnInfoList) {

        List<String> columns = new ArrayList<>();
        List<TopTenCities> result = new ArrayList<>();

        for (ColumnInfo columnInfo : columnInfoList) {
            columns.add(columnInfo.name());
        }

        int rowCtr = 0;
        for (Row row : rowList) {
            int index = 0;

            // simple mapping logic for the POJO
            String name = "";
            String population = "";

            for (Datum datum : row.data()) {
                log.debug(columns.get(index) + ": " + datum.varCharValue());
                // skip row header row
                if (rowCtr > 0) {
                    if (index == 0) {
                        name = datum.varCharValue();
                    } else {
                        population = datum.varCharValue();
                    }
                }
                index++;
            }
            rowCtr++;
            if (!name.isEmpty()) {
                var topTenCities = new TopTenCities(name, Integer.valueOf(population));
                result.add(topTenCities);
            }
            log.debug("===================================");
        }
        return result;
    }
}
```

이제 각 부분을 확인하면서 구현 내용을 이해하겠습니다. run 메서드는 주요 기능을 담당합니다. 여기서 SQL 쿼리 생성이 이루어지고, Athena 처리 레이어에서 이전에 살펴본 createAthenaQuery 및 completeAthenaQuery 메서드가 호출됩니다. 전자는 쿼리를 초기화하고 제출하며, 후자는 처리를 계속하기 전에 완료될 때까지 대기합니다.

AthenaService의 processResultRows 메서드에서 GetQueryResultsRequest 클래스를 사용하여 쿼리 결과를 검색합니다. 이는 전체 결과를 반환하고, processRows가 각 행을 하나씩 처리하고 Java 개체로 매핑하는 데 사용됩니다.

## Rest API

<div class="content-ad"></div>

마지막으로 살펴볼 부분은 API 레벨입니다. 여기에서 컨트롤러를 구현합니다.

```js
@RestController
@RequestMapping(value = "/cities", produces = APPLICATION_JSON_VALUE)
@AllArgsConstructor
public class ApiController {

    private final AthenaService athenaService;

    @GetMapping(value = {"", "/"})
    public ResponseEntity<List<TopTenCities>> getTop10All(@RequestParam(defaultValue = "10") Integer limit) {

        List<TopTenCities> result = athenaService.getTop10All(limit);
        return ResponseEntity.accepted().body(result);
    }

    @GetMapping(value = {"/{country}"})
    public ResponseEntity<List<TopTenCities>> getTop10(@PathVariable("country") String country,
                                                       @RequestParam(defaultValue = "10") Integer limit) {

        List<TopTenCities> result = athenaService.getTop10(country, limit);
        return ResponseEntity.accepted().body(result);
    }
}
```

여기에 두 개의 엔드포인트가 선언되어 있습니다. 하나는 (GET) /cities 이고 다른 하나는 (GET) /cities/'country' 입니다. 두 엔드포인트 모두 선택적으로 쿼리 파라미터를 받아들이며, 반환할 값의 수를 설정할 수 있습니다. 기본값은 10입니다.

# 테스팅

<div class="content-ad"></div>

위 명령어로 애플리케이션을 시작하여 테스트해보세요.

```js
java -jar target/athena-sample-0.0.1-SNAPSHOT.jar
```

이렇게 하면 애플리케이션이 실행됩니다. 브라우저 URL에 다음을 입력하여 실행 여부를 확인할 수 있습니다.

```js
http://localhost:8080/api/actuator
```

<div class="content-ad"></div>

해당 URL을 브라우저에 입력하면 모든 국가의 인구 순으로 상위 열 개의 도시가 반환됩니다.


http://localhost:8080/api/cities


다음과 같이 표시됩니다.

![이미지](/assets/img/2024-06-23-SpringBootwithAmazonAthena_11.png)

<div class="content-ad"></div>

다음 코드도 시도해 볼 수 있어요.

```js
http://localhost:8080/api/cities/US?limit=15
```

이렇게 하면 미국 내 인구 순으로 상위 15개의 도시 정보를 받아볼 수 있어요. DE, BR, CA와 같은 다른 국가 코드로도 시도해 보세요.

우리는 SpringBoot 애플리케이션을 통해 Athena를 쿼리할 수 있음을 보여줬고, 이를 REST API를 통해 모두 연결했어요.

<div class="content-ad"></div>

# 참고 자료

- Amazon Athena를 활용한 서버리스 분석 — Packt Publishing — Anthony Virtuoso, Mert Turkay Hocanin, Aaron Wishnick

# 요약

오늘은 SpringBoot 애플리케이션에서 Athena로 인터페이스를 만드는 방법을 살펴보았습니다. 우리는 Athena에 대한 배경 정보부터 시작했습니다. 그 후에 CSV 파일을 Athena로 로드하고 사용 가능하게 만드는 방법을 살펴보았습니다. 다음으로 SpringBoot 애플리케이션과 Athena를 연결하고 쿼리하는 방법을 살펴보았습니다. 이러한 클래스들은 여러분의 애플리케이션에 기초를 제공할 수 있습니다. 마지막으로 우리는 테스트를 해보았습니다. 함께 이 여정을 함께해줘서 감사합니다.

<div class="content-ad"></div>

이 글의 코드는 제 Github에서 찾아볼 수 있어요.

즐거운 여정되세요!