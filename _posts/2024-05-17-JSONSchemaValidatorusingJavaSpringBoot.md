---
title: "자바 SpringBoot를 사용한 JSON 스키마 유효성 검사기"
description: ""
coverImage: "/assets/img/2024-05-17-JSONSchemaValidatorusingJavaSpringBoot_0.png"
date: 2024-05-17 17:43
ogImage:
  url: /assets/img/2024-05-17-JSONSchemaValidatorusingJavaSpringBoot_0.png
tag: Tech
originalTitle: "JSON Schema Validator using Java SpringBoot."
link: "https://medium.com/@mohommad.belal/json-schema-validator-using-java-springboot-667ed42480d5"
---

## Json 스키마란 무엇인가요?

JSON Schema은 선언적 언어입니다. 이는 우리 서비스에 특정한 json 구조를 정의하고 유효성을 검사합니다. 주어진 json 데이터에 대한 표준 구조로 여러 시스템에서 사용할 수 있습니다. 자세한 내용은 여기를 참조하세요: what-is-jsonschema?

## Json 스키마를 사용하는 이유는 무엇인가요?

대부분의 경우, 서비스에서 들어오는 json을 유효성 검사하는 것이 필요합니다. 간단한 json은 속성에 제약 조건을 적용하여 POJO 또는 모델에 매핑할 때 유효성을 검사할 수 있습니다. 그러나 때로는 json이 복잡하여 이러한 제약 조건을 사용하여 모든 필드를 유효성을 검사할 수 없는 경우가 있습니다. JsonSchema를 사용하면 표준화된 구조를 사용하여 복잡한 json을 유효성을 검사할 수 있습니다.

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

## 내용:

SpringBoot와 Json Schema를 사용하는 단계별 가이드입니다. 이를 위해 networknt 라이브러리를 사용할 것입니다. 단계를 거친 후에는 테스트를 위해 수신된 json에 대한 몇 가지 시나리오가 있습니다.

참고: 이 문서는 SpringBoot와 JsonSchema의 사용을 위한 것입니다. 이는 JsonSchema의 일부 기능 및 사용법에 대해 가르치기 위한 것입니다.

## 1. SpringBoot 웹 애플리케이션을 생성하세요: https://start.spring.io/

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

![image](/assets/img/2024-05-17-JSONSchemaValidatorusingJavaSpringBoot_0.png)

알림: spring-boot 및 java의 모든 버전 및 프로젝트 유형을 선택할 수 있습니다.

## 2. pom.xml 또는 build.gradle에 종속성 추가.

```js
<--
pom.xml
https://mvnrepository.com/artifact/com.networknt/json-schema-validator
-->
<dependency>
  <groupId>com.networknt</groupId>
  <artifactId>json-schema-validator</artifactId>
  <version>1.4.0</version>
</dependency>
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

```js
// build.gradle
// https://mvnrepository.com/artifact/com.networknt/json-schema-validator
implementation 'com.networknt:json-schema-validator:1.4.0'
```

## 3. 리소스 유효성 검사 JSON 파일을 생성합니다.

```js
{
 "$schema": "http://json-schema.org/draft-07/schema#",
    "title": "Order Event",
    "description": "예제용 주문 이벤트 스키마",
    "required": ["order_id", "total_price", "products" ],
    "properties": {
       "order_id": {
          "type": "string"
        },
        "event": {
          "enum": ["PLACED", "DELIVERED", "RETURNED"],
          "type": "string"
        },
        "total_price": {
         "type": "number",
             "minimum": 0
     },
        "products": {
      "type": "array",
      "items": {
        "additionalProperties": true,
        "required": ["product_id", "price"],
        "minItems": 1,
        "properties": {
          "product_id": {
            "type": "string"
          },
          "price": {
            "type": "number",
            "minimum": 0
          },
          "quantity": {
            "type": "integer"
          }
        }
      }
    }
   }
}
```

## 4. JsonSchema 빈을 생성합니다.

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

호출자 메서드에서는 JsonSchema 객체를 직접 생성할 수 있지만, 빈을 생성하고 사용하는 것을 권장합니다.

```java
@Configuration
public class AppConfiguration {
    private static final String SCHEMA_VALIDATION_FILE = "validation.json";

    @Bean
    public JsonSchema jsonSchema() {
        return JsonSchemaFactory
                .getInstance( SpecVersion.VersionFlag.V7 )
                .getSchema( getClass().getResourceAsStream( SCHEMA_VALIDATION_FILE ) );
    }
}
```

## 5. JsonSchema 사용법

이제 JsonSchema 객체를 사용해보겠습니다. JsonNode를 매개변수로 사용하는 메서드가 있는 Service 클래스를 만들겠습니다.

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

```java
@Slf4j
@Service
public class JsonSchemaValidationService{

  @Autowired
  private JsonSchema jsonSchema;

  public String validateJson(JsonNode jsonNode){

    Set<ValidationMessage> errors = jsonSchema.validate(jsonNode);
    //if errors have a single miss match, there would be a value in the errors set.
    if(errors.isEmpty()){
      //event is valid.
      log.info("event is valid");
    }else{
        //event is in_valid.
      log.info("event is invalid");
     }
      return errors.toString();
  }
}
```

## 6. Create a Rest Controller.

```java
import com.fasterxml.jackson.databind.JsonNode;
@RestController
public class JsonSchemaController {
    @Autowired
    private JsonSchemaValidationService service;

    @PostMapping("/validate")
    public String validateEvent( @RequestBody JsonNode jsonNode ){
       return service.validateJson(jsonNode);
    }
}
```

## 7. Start the SpringBoot Application and start sending requests.

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

이제 모든 것이 준비되었으니, 즐겨 사용하는 클라이언트를 사용하여 코드를 테스트할 수 있습니다. 저는 PostMan을 사용하고 있어요. 아래에서 유효한 이벤트로 시작해보겠습니다.

```js
# 유효한 데이터
curl --location 'localhost:8080/validate' \
--header 'Content-Type: application/json' \
--data '{
  "order_id":"order134",
   "event": "PLACED",
   "products": [
     {
       "product_id": "product_1",
        "price":20.5,
       "quantity":2
     }
   ],
   "total_price": 41
}'
```

응답:

```js
[];
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

```js
# order id 없는 페이로드
curl --location 'localhost:8080/validate' \
--header 'Content-Type: application/json' \
--data '{
   "event": "PLACED",
   "products": [
     {
       "product_id": "product_1",
        "price":20.5,
       "quantity":2
     }
   ],
   "total_price": 41
}'
```

응답 :

```js
[$.order_id: 필수 항목이지만 누락되었습니다]
```

```js
# order id 없는 페이로드
curl --location 'localhost:8080/validate' \
--header 'Content-Type: application/json' \
--data '{
"order_id":"order134",
   "event": "PLACED",
   "total_price": 41
}'
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

```js
[$.products: is missing but it is required]
```

```js
# order id가 없는 payload
curl --location 'localhost:8080/validate' \
--header 'Content-Type: application/json' \
--data '{
 "order_id" : "order_123",
   "event": "PLACED",
   "products": [

   ],
   "total_price": 41
}'
```

응답 :

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

```js
[$.products: 배열에는 최소 1개의 항목이 있어야 합니다.]
```

<img src="/assets/img/2024-05-17-JSONSchemaValidatorusingJavaSpringBoot_1.png" />
