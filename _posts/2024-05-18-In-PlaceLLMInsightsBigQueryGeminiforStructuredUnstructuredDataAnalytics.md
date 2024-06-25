---
title: "장소 LLM 통찰 구조화 및 비구조화 데이터 분석을 위한 BigQuery, Gemini"
description: ""
coverImage: "/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_0.png"
date: 2024-05-18 18:15
ogImage:
  url: /assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_0.png
tag: Tech
originalTitle: "In-Place LLM Insights: BigQuery , Gemini for Structured , Unstructured Data Analytics"
link: "https://medium.com/google-cloud/in-place-llm-insights-bigquery-gemini-for-structured-unstructured-data-analytics-fdfac0421626"
---

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

BigQuery 워크로드 내에서 Gemini 1.0 Pro (텍스트 전용) 및 Gemini 1.0 Pro Vision (멀티모달) 두 가지 LLM 모델을 통합하는 흥미로운 기술을 시연하겠습니다. 이를 통해 Low-code 생성적 인사이트 생성 경험을 제공할 수 있습니다. BigQuery에서 원격 모델 엔드포인트로 지원되는 모델인 Gemini 1.0 Pro와 같이, 데이터베이스 쿼리 내에서 모델을 호출하기 위해 ML.GENERATE_TEXT 구조를 직접 사용할 수 있습니다. 기본적으로 원격 모델로 사용할 수 없거나 생성적 AI 호출에 더 많은 사용자 정의가 필요한 경우 (또는 데이터베이스 내에서 원격으로 액세스하려는 API가 있는 경우), REMOTE FUNCTIONS 접근 방식을 사용할 수 있습니다. 두 시나리오를 모두 다루기 위해 블로그 글을 2개의 섹션으로 나눠서 설명하겠습니다:

## #1 원격 모델 호출:

- 이 섹션은 SELECT 쿼리에서 ML.GENERATE_TEXT를 사용하여 BigQuery 내에서 Gemini 1.0 Pro를 호출하는 방법을 안내합니다.
- 모델이 이미 BigQuery의 원격 모델로 사용 가능하고 기본 제공으로 사용하려는 경우에 이 접근 방법을 사용할 수 있습니다. 사용하려는 모델의 상태를 이 설명서에서 확인할 수 있습니다.
- 안내 사례:

인터넷 아카이브 책 데이터셋(공개적으로 BigQuery에서 사용 가능)에 대한 위치 요약기를 구축하며, BigQuery에서 Gemini 1.0 Pro의 원격 모델을 ML.GENERATE_TEXT 구조를 통해 호출하는 것입니다.

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

<img src="/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_1.png" />

## #2 원격 함수 구현:

- 이 섹션에서는 Gemini 1.0 Pro Vision을 구현한 클라우드 함수를 호출하는 방법에 대해 안내합니다. 이 클라우드 함수는 BigQuery에서 원격 함수로 노출됩니다.
- 사용하려는 모델이 원격 모델로 제공되지 않거나 사용 사례에서 더 많은 유연성 및 사용자 정의가 필요한 경우 이 접근 방식을 사용하십시오.
- 안내용 사용 사례:

기준 이미지와 테스트 이미지를 비교하는 이미지 유효성 검사기를 구축합니다. 이를 위해 외부 테이블에 테스트 이미지 스샷을 포함하는 데이터 세트를 만들고 Gemini 1.0 Pro Vision에 대해 확인하도록 요청합니다. 이를 위해 Gemini Pro Vision 호출을 구현한 Java 클라우드 함수를 만들고 이를 BigQuery에서 원격 함수로 호출합니다.

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

<img src="/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_2.png" />

# BigQuery

BigQuery은 서버리스, 멀티 클라우드 데이터 웨어하우스로, 바이트부터 페타바이트까지 최소한의 운영 오버헤드로 확장이 가능합니다. 이것은 ML 트레이닝 데이터를 저장하기에 좋은 선택지가 됩니다. 내장된 BigQuery Machine Learning (BQML)과 분석 기능을 통해 SQL 쿼리만 사용하여 노코드 예측을 생성할 수 있습니다. 게다가, 페더레이티드 쿼리로 외부 소스에서 데이터에 접근할 수 있어 복잡한 ETL 파이프라인이 필요하지 않습니다. BigQuery가 제공하는 모든 것에 대해 BigQuery 페이지에서 자세히 읽어볼 수 있습니다. 우리는 텍스트 요약 사례에 사용되는 원격 모델을 호출하기 위해 BigQuery ML의 ML.GENERATE_TEXT 구조를 사용할 것입니다.

우리는 BigQuery를 구조적 및 반구조적 데이터를 분석하는 데 도움이 되는 완전 관리형 클라우드 데이터 웨어하우스로 알고 왔습니다. BigQuery는 비정형 데이터에서 모든 분석 및 ML을 수행할 수 있도록 확장되었습니다. 우리는 이미지 데이터를 저장하기 위해 객체 테이블을 사용할 것이며, Gemini Pro Vision 모델을 사용하여 이미지 유효성을 검증하는 원격 기능 사례에 필요한 데이터를 저장할 것입니다.

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

# 데모

이 블로그의 나머지 부분에서는 위에서 설명한 유즈 케이스 섹션에 자세히 기술된 실제 예제로 두 가지 유즈 케이스를 모두 시연하겠습니다. 유즈 케이스별 구현에 들어가기 전에 두 가지 유즈 케이스에 필요한 사전 설정 및 공통 단계를 완료해 봅시다.

# 설정

- Google Cloud Console에서 프로젝트 선택기 페이지에서 Google Cloud 프로젝트를 선택하거나 만듭니다.
- 클라우드 프로젝트에 청구가 활성화되어 있는지 확인하십시오. 프로젝트에 청구가 활성화되어 있는지 확인하는 방법을 알아보세요.
- Google Cloud에서 미리 로드된 bq를 실행하는 명령줄 환경인 Cloud Shell을 사용할 것입니다. Cloud 콘솔에서 오른쪽 상단의 'Cloud Shell 활성화'를 클릭하세요.
- 애플리케이션 구축 및 제공을 위한 지원을 위해서, Duet AI를 활성화해 봅시다. Duet AI Marketplace로 이동하여 API를 활성화하세요. 또는 Cloud Shell 터미널에서 다음 명령을 실행할 수도 있습니다.

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
gcloud services enable cloudaicompanion.googleapis.com –project PROJECT_ID
```

5. 이미 하지 않았다면, 이 구현을 위해 필요한 API를 활성화하세요.

BigQuery, BigQuery Connection, Vertex AI, Cloud Storage APIs

gcloud 명령어 대신 이 링크를 사용하여 콘솔을 통해 진행할 수도 있습니다.

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

# BigQuery Dataset과 외부 연결 생성하기

BigQuery 데이터셋은 애플리케이션의 모든 테이블과 객체를 포함하는 컨테이너입니다. BigQuery 연결은 Cloud Function과 상호작용하는 데 사용됩니다. 원격 함수를 생성하려면 BigQuery 연결을 만들어야 합니다. 데이터셋과 연결을 생성하는 방법을 알아보겠습니다.

- Google Cloud Console에서 BigQuery 페이지로 이동한 후 프로젝트 ID 옆에 있는 3개 수직 점 아이콘을 클릭하세요. 나타나는 옵션 중에서 “데이터 집합 만들기”를 선택하세요.
- “데이터 집합 만들기” 팝업에서 아래와 같이 데이터 집합 ID를 “gemini_bq_fn”로 입력하고 지역 값을 기본 값인 “US (다중 지역…)”으로 설정하세요.

![이미지](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_3.png)

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

3. BigLake Connection을 사용하면 외부 데이터 원본에 연결할 수 있으면서 세밀한 BigQuery 액세스 제어와 보안을 유지할 수 있습니다. 우리의 경우에는 Vertex AI Gemini Pro API를 사용합니다. 우리는 이 연결을 사용하여 Cloud Function을 통해 BigQuery의 모델에 액세스할 것입니다. 아래 단계를 따라 BigLake Connection을 만들어보세요:

a. BigQuery 페이지의 탐색기 창에서 ADD를 클릭하세요:

![이미지](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_4.png)

b. 소스 페이지에서 외부 데이터 원본에 대한 연결을 클릭하세요.

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

c. 팝업창에 아래 외부 데이터 원본 세부정보를 입력하고 CREATE CONNECTION을 클릭하세요:

![이미지](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_5.png)

d. 연결이 생성되면, 연결 구성 페이지로 이동하여 액세스 권한 부여를 위한 서비스 계정 ID를 복사하세요:

![이미지](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_6.png)

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

e. IAM 및 관리 페이지를 열고 액세스 부여를 클릭한 후 새 주체 탭에 서비스 계정 ID를 입력하고 아래에 표시된 역할을 선택한 다음 저장을 클릭하세요.

![그림](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_7.png)

# Use case #1 Remote Model Invocation

여기서는 Vertex AI Gemini Pro foundation 모델을 기반으로 BigQuery에 모델을 만들 것입니다. 이미 데이터 세트와 연결 설정이 완료되었습니다. 이제 3단계만으로 Gemini Pro 모델의 원격 모델 호출을 시연합니다. SQL 쿼리만 사용하여 LLM 애플리케이션이 가동됩니다!

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

## 테이블 및 모델 생성

인터넷 아카이브 도서 데이터셋을 예시로 들어서 BigQuery에서 공개로 사용할 수 있도록 소스로 가져왔다고 가정해봅시다.

## BigQuery 테이블 생성

위의 예제로부터 공개적으로 이용 가능한 BigQuery 데이터셋에서 약 50개의 레코드를 보유할 수 있는 테이블을 생성해봅시다:

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

BigQuery SQL 에디터 창에서 다음과 같이 DDL (데이터 정의 언어) 문을 실행해보세요:

```sql
create or replace table gemini_bq_fn.books as (
select *
from
bigquery-public-data.gdelt_internetarchivebooks.1905 limit 50);
```

이 쿼리는 이전에 생성한 데이터셋에 "books" 라는 새로운 테이블을 생성합니다.

## BigQuery 모델 생성

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

모델을 생성하려면 BigQuery SQL 편집기 창에서 다음 DDL을 실행하세요:

```js
CREATE MODEL `gemini_bq_fn.gemini_remote_model`
REMOTE WITH CONNECTION `us.gemini-bq-conn`
OPTIONS(ENDPOINT = 'gemini-pro');
```

모델이 생성되었음을 확인하고 방금 생성된 모델을 볼 수 있는 옵션이 제공됩니다.

## 새로운 생성 AI 애플리케이션을 테스트해보세요!

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

그것이에요! 이제 ML.GENERATE_TEXT 문을 사용하여 새로 생성한 생성 모델을 테스트해 보겠습니다.

```js
SELECT ml_generate_text_llm_result as Gemini_Response, prompt as Prompt
FROM ML.GENERATE_TEXT(MODEL `gemini_bq_fn.gemini_remote_model`,
  (select '텍스트 요약기와 표준화기를 당신은 개발했어요. 주소 정보를 포함한 다음 텍스트에서 표준화하고 하나의 표준화된, 통합된 주소를 출력해야 합니다. 빈 값으로 반환해서는 안 됩니다. 왜냐하면 이 필드의 텍스트에서 합리적인 데이터를 가져오는 방법을 알기 때문이에요: ' ||
substring(locations, 0, 200) as prompt
from `gemini_bq_fn.books`),
STRUCT(
  TRUE AS flatten_json_output));
```

다음 결과가 표시되어야 합니다:

<img src="/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_8.png" />

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

우와! 이렇게 쉽게 BigQuery ML에서 데이터베이스의 원격 모델을 사용할 수 있어요.

이제 다른 Vertex AI 모델을 사용해 빅쿼리 원격 함수를 시도해봅시다. 예를 들어, 빅쿼리에서 원격으로 모델을 사용하는 방법을 더 맞춤화하고 유연하게 사용하고 싶다고 가정해봅시다. 현재 지원되는 모델은 이 문서에서 참조할 수 있어요.

# 사용 사례 #2 원격 함수 구현

여기서는 Gemini 1.0 Pro Vision foundation 모델을 구현하는 Java Cloud Function을 기반으로 빅쿼리에서 함수를 생성할 거에요. 먼저 Gemini 1.0 Pro Vision 모델을 사용해 이미지를 비교하기 위해 Java Cloud Function을 생성하고 배포하고, 그 다음에는 빅쿼리에서 배포된 Cloud Function을 호출하는 원격 함수를 생성할 거에요. 기억해 주세요, 빅쿼리에서의 원격 함수 실행에 대해 동일한 절차를 따를 수 있어요.

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

# Java 클라우드 함수 만들기

Gen 2 클라우드 함수를 Java로 생성하여 외부 테이블에 저장된 베이스라인 이미지와 테스트 이미지를 비교하는 기능을 구축할 것입니다. 이 작업은 BigQuery의 테스트 이미지 스크린샷이 포함된 데이터셋을 사용하며 Gemini Pro Vision 모델 (Java SDK)을 이용하여 REST 엔드포인트에 배포됩니다.

# Java 클라우드 함수

- Cloud Shell 터미널을 열고 루트 디렉토리나 기본 작업 공간 경로로 이동합니다.
- 상태 표시줄의 왼쪽 하단에 있는 Cloud Code 로그인 아이콘을 클릭하고 Cloud Functions을 생성할 Google Cloud 프로젝트를 선택합니다.
- 다시 아이콘을 클릭하고 이번에는 새 응용 프로그램을 만드는 옵션을 선택합니다.
- "새 응용 프로그램 생성" 팝업에서 Cloud Functions 응용 프로그램을 선택합니다:

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

![image1](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_9.png)

5. Select the "Java: Hello World" option from the next pop-up:

![image2](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_10.png)

6. Provide a name for the project in the project path. In this case, it is "Gemini-BQ-Function".

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

7. 새로운 Cloud Shell Editor 보기에서 프로젝트 구조가 열린 것을 확인해야합니다:

![이미지](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_11.png)

8. 이제 pom.xml 파일의 `dependencies`...`/dependencies` 태그 안에 필요한 종속성을 추가해주세요.

```xml
<dependency>
      <groupId>com.google.cloud</groupId>
      <artifactId>google-cloud-vertexai</artifactId>
      <version>0.1.0</version>
   </dependency>

     <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.10</version>
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

9. "HelloWorld.java" 클래스의 이름을 더 의미 있는 이름인 "GeminiBigQueryFunction.java"로 변경하세요. 클래스 이름을 이에 맞게 변경해야 합니다.

10. 아래 코드를 복사하고 파일 "GeminiBigQueryFunction.Java"의 플레이스홀더 코드를 대체하세요. Github 레포지토리에서 소스를 참조해주세요.

```js
package cloudcode.helloworld;
import java.io.BufferedWriter;
import com.google.cloud.functions.HttpFunction;
import com.google.cloud.functions.HttpRequest;
import com.google.cloud.functions.HttpResponse;
import com.google.cloud.vertexai.VertexAI;
import com.google.cloud.vertexai.api.Blob;
import com.google.cloud.vertexai.api.Content;
import com.google.cloud.vertexai.generativeai.preview.ContentMaker;
import com.google.cloud.vertexai.api.GenerateContentResponse;
import com.google.cloud.vertexai.api.GenerationConfig;
import com.google.cloud.vertexai.api.Part;
import com.google.cloud.vertexai.generativeai.preview.PartMaker;
import com.google.cloud.vertexai.generativeai.preview.GenerativeModel;
import com.google.cloud.vertexai.generativeai.preview.ResponseStream;
import com.google.cloud.vertexai.generativeai.preview.ResponseHandler;
import com.google.protobuf.ByteString;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Base64;
import java.util.List;
import java.util.Arrays;
import java.util.Map;
import java.util.LinkedHashMap;
import com.google.gson.Gson;
import com.google.gson.JsonObject;
import com.google.gson.JsonArray;
import java.util.stream.Collectors;
import java.lang.reflect.Type;
import com.google.gson.reflect.TypeToken;
import java.io.ByteArrayOutputStream;
import java.io.InputStream;
import java.net.HttpURLConnection;
import java.net.URL;


public class GeminiBigQueryFunction implements HttpFunction {
    private static final Gson gson = new Gson();


  public void service(final HttpRequest request, final HttpResponse response) throws Exception {
    final BufferedWriter writer = response.getWriter();
   // 요청 본문을 JSON 객체로 가져옵니다.
    JsonObject requestJson = new Gson().fromJson(request.getReader(), JsonObject.class);
    JsonArray calls_array = requestJson.getAsJsonArray("calls");
    JsonArray calls = (JsonArray) calls_array.get(0);
    String baseline_url = calls.get(0).toString().replace("\"", "");
    String test_url = calls.get(1).toString().replace("\"", "");
    String prompt_string = calls.get(2).toString().replace("\"", "");
    String raw_result = validate(baseline_url, test_url, prompt_string);
    raw_result = raw_result.replace("\n","");
    String trimmed = raw_result.trim();
    List<String> result_list = Arrays.asList(trimmed);
    Map<String, List<String>> stringMap = new LinkedHashMap<>();
    stringMap.put("replies", result_list);
    // 직렬화
    String return_value = gson.toJson(stringMap);
    writer.write(return_value);
  }


public String validate(String baseline_url, String test_url, String prompt_string) throws IOException{
  String res = "";
    try (VertexAI vertexAi = new VertexAI("YOUR_PROJECT", "us-central1"); ) {
      GenerationConfig generationConfig =
          GenerationConfig.newBuilder()
              .setMaxOutputTokens(2048)
              .setTemperature(0.4F)
              .setTopK(32)
              .setTopP(1)
              .build();
    GenerativeModel model = new GenerativeModel("gemini-pro-vision", generationConfig, vertexAi);
    String context = prompt_string;
    Content content = ContentMaker.fromMultiModalData(
     context,
     PartMaker.fromMimeTypeAndData("image/png", readImageFile(baseline_url)),
     PartMaker.fromMimeTypeAndData("image/png", readImageFile(test_url))
    );
    GenerateContentResponse response = model.generateContent(content);
     res = ResponseHandler.getText(response);
  }catch(Exception e){
    System.out.println(e);
  }
  return res;
}


  // 지정된 URL의 이미지 데이터를 읽어옵니다.
  public static byte[] readImageFile(String url) throws IOException {
    URL urlObj = new URL(url);
    HttpURLConnection connection = (HttpURLConnection) urlObj.openConnection();
    connection.setRequestMethod("GET");
    int responseCode = connection.getResponseCode();
    if (responseCode == HttpURLConnection.HTTP_OK) {
      InputStream inputStream = connection.getInputStream();
      ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
      byte[] buffer = new byte[1024];
      int bytesRead;
      while ((bytesRead = inputStream.read(buffer)) != -1) {
        outputStream.write(buffer, 0, bytesRead);
      }
      return outputStream.toByteArray();
    } else {
      throw new RuntimeException("Error fetching file: " + responseCode);
    }
  }
}
```

11. 이제 Cloud Shell 터미널로 이동하여 아래 명령을 실행하여 클라우드 함수를 빌드하고 배포하세요:

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
gcloud functions deploy gemini-bq-fn --runtime java17 --trigger-http --entry-point cloudcode.helloworld.GeminiBigQueryFunction --allow-unauthenticated
```

여기에 결과는 아래와 같은 형식으로 REST URL이 생성됩니다:

https://us-central1-YOUR_PROJECT_ID.cloudfunctions.net/gemini-bq-fn

12. 터미널에서 다음 명령을 실행하여 이 클라우드 함수를 테스트해보세요:

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
gcloud functions call gemini-bq-fn --region=us-central1 --gen2 --data '{"calls":[["https://storage.googleapis.com/img_public_test/image_validator/baseline/1.JPG", "https://storage.googleapis.com/img_public_test/image_validator/test/2.JPG", "PROMPT_ABOUT_THE_IMAGES_TO_GEMINI"]]}'
```

임의의 샘플 프롬프트에 대한 응답:

<img src="/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_12.png" />

제네릭 Cloud Function을 사용하여 Gemini Pro Vision 모델 구현이 준비되었습니다. 이제 이 엔드포인트를 직접 BigQuery 원격 함수 내에서 BigQuery 데이터에 사용하겠습니다.

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

# 빅쿼리 오브젝트 테이블 및 원격 함수 만들기

이 데모 애플리케이션에서는 클라우드 스토리지 버킷을 생성해 보겠습니다:

- 클라우드 스토리지 콘솔로 이동하여 생성 버튼을 클릭하여 버킷을 만듭니다.
- 버킷에 이름을 제공하고 "demo-bq-gemini-public"과 같은 이름을 지정한 다음 "이 버킷에서의 공개 액세스 방지 강화" 옵션의 선택 해제(공개로 유지)를 기억하세요. 이 데모에서는 이 버킷을 공개 액세스로 설정하고 있지만, 권장하는 방법은 공개 액세스를 방지하고 필요에 따라 특정 서비스 계정에 권한을 부여하는 것입니다.
- 방금 만든 클라우드 스토리지 버킷의 PERMISSION 탭에서 권한 설정을 보고 변경할 수 있습니다. 원칙을 추가하려면 VIEW BY PRINCIPALS 탭 아래의 GRANT ACCESS를 클릭하고 (특정 계정을 위한) 서비스 계정 ID를 입력하거나 "allUsers" (공개 액세스에 대한)를 입력한 후 역할을 "Storage Object Viewer"로 설정하고 저장을 클릭합니다.
- 이제 버킷이 생성되었으므로 OBJECTS 탭으로 이동하여 이미지를 업로드하고 UPLOAD FILES를 클릭하여 업로드하세요.
- 비교하기 위해 기준 및 테스트 이미지를 업로드하세요.

이 데모를 위해 3개의 객체를 생성하고 기준이고 test1 및 test2를 공개로 사용할 수 있도록 만들었습니다.

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

# BigQuery 객체 테이블 생성

BigQuery에서 외부 객체 테이블을 만들어 생성한 연결 및 데이터셋을 사용하여 버킷의 비구조화된 데이터에 액세스할 수 있습니다. BigQuery 쿼리 에디터 창에서 다음과 같은 DDL(데이터 정의 언어) 문을 실행하세요:

```js
CREATE OR REPLACE EXTERNAL TABLE `gemini_bq_fn.image_validation`
WITH CONNECTION `us.gemini-bq-conn`
OPTIONS(object_metadata="SIMPLE", uris=["gs://demo-bq-gemini-public/*.JPG"]);
```

이 쿼리는 이전에 만든 데이터셋에 "image_validation"이라는 새 객체 테이블을 생성해야 합니다.

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

# BigQuery 원격 함수 생성

BigQuery에서 Java Cloud Function을 호출하는 원격 함수를 만들어 봅시다. Gemini Pro Vision 모델을 구현한 Java Cloud Function을 호출할 것입니다. 이 함수는 동일한 데이터셋에 만들 것입니다. BigQuery 콘솔의 SQL 편집 창에서 다음 DDL을 실행해 주세요:

```js
CREATE OR REPLACE FUNCTION `gemini_bq_fn.FN_IMAGE_VALIDATE` (baseline STRING, test STRING, prompt STRING) RETURNS STRING
  REMOTE WITH CONNECTION `us.gemini-bq-conn`
  OPTIONS (
    endpoint = 'https://us-central1-********.cloudfunctions.net/gemini-bq-fn',
    max_batching_rows = 1
  );
```

이렇게 하면 BigQuery에 원격 함수가 생성됩니다. 위의 DDL에는 3개의 매개변수가 있습니다. 처음 두 매개변수는 이전 단계에서 생성된 객체 테이블에 저장된 이미지의 URL입니다. 마지막 매개변수는 모델(Gemini Pro Vision)에 대한 프롬프트입니다. 이 시그니처를 파싱하는 Java Cloud Functions 코드를 참조하시기 바랍니다:

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
Gson().fromJson(request.getReader(), JsonObject.class);
JsonArray calls_array = requestJson.getAsJsonArray("calls");
JsonArray calls = (JsonArray) calls_array.get(0);
String baseline_url = calls.get(0).toString().replace("\"", "");
String test_url = calls.get(1).toString().replace("\"", "");
String prompt_string = calls.get(2).toString();
```

# BigQuery에서 Gemini 호출하기!

이제 원격 함수가 생성되었으니, 테스트 이미지를 프롬프트와 대조하여 이미지 유효성을 확인하는 원격 함수를 테스트하기 위해 SELECT 쿼리에서 사용해봅시다:

테스트 이미지가 참조와 어떤지 확인하기 위한 쿼리:

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
select gemini_bq_fn.FN_IMAGE_VALIDATE(
'https://storage.googleapis.com/demo-bq-gemini-public/Baseline.JPG',
REPLACE(uri, 'gs://', 'https://storage.googleapis.com/') ,
'전문 이미지 유효성 검사자이며 JSON 결과로 응답할 수 있는 이미지 유효성 검사자입니다. 여기에서 2개의 이미지를 찾을 수 있습니다. 첫 번째 이미지는 기준 이미지이고 두 번째 이미지는 테스트 이미지입니다. 두 번째 이미지가 첫 번째 이미지와 텍스트 측면에서 유사한지 확인하세요. "YES" 또는 "NO"인 SIMILARITY, 백분율인 SIMILARITY_SCORE, 문자열인 DIFFERENCE_COMMENT 3가지 속성이 포함된 JSON 형식으로만 응답하세요.' ) as IMAGE_VALIDATION_RESULT
from `gemini_bq_fn.image_validation`
where uri like '%TEST1%';
```

위 쿼리를 TEST1.JPG 및 TEST2.JPG와 함께 시도해보세요. 아래와 유사한 결과를 보게 될 것입니다:

<img src="/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_13.png" />

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

기준 이미지:

![이미지1](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_14.png)

테스트 이미지:

![이미지2](/assets/img/2024-05-18-In-PlaceLLMInsightsBigQueryGeminiforStructuredUnstructuredDataAnalytics_15.png)

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

위에서 확인할 수 있듯이, 두 이미지 모두 Duet AI 클라우드 콘솔 뷰를 가지고 있지만, 두 이미지의 텍스트는 모델에 의해 생성된 JSON 형식에 따라 다릅니다.

# 혜택 및 사용 사례

- 데이터에 GenAI를 적용하세요: 데이터 이동, 중복 및 추가 복잡성이 더 이상 필요하지 않습니다. 동일한 BigQuery 환경 내에서 데이터를 분석하고 인사이트를 생성할 수 있습니다.
- 향상된 분석: Gemini의 자연어 설명은 데이터에 새로운 이해의 층을 더해주며, SQL 쿼리만을 사용하여 이를 달성할 수 있습니다.
- 확장성: 이 솔루션은 대규모 데이터셋과 복잡한 분석을 쉽고 Low-Code 방식으로 처리할 수 있습니다.

실제 사례: 금융(시장 트렌드 분석), 소매(고객 감정), 의료(의료 보고서 요약) 등 분석 및 비즈니스 팀이 비교적 적은 노력, 자원 및 익숙한 언어 및 도구를 선택하여 이를 구현할 수 있는 시나리오를 고려해보세요.

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

# 결론

축하합니다. Gemini 모델이 BigQuery에 통합되어 데이터 분석을 넘어 데이터 이야기꾼이 되셨습니다. 데이터셋 안에 숨겨진 이야기를 찾아내고 통찰력을 이해하는 방법을 변화시킬 수 있습니다. 지금 실험을 시작하세요! 이 기술을 여러분의 데이터셋에 적용하여 데이터 안에 깔려있는 이야기들을 발견해보세요. BigQuery가 객체 테이블(External Tables)에서 비구조적인 데이터를 지원하므로, 이미지 데이터에 대한 생성적 인사이트를 만들기 위해 Gemini Pro Vision을 사용해보세요. 더 깊은 안내를 위해서 Vertex AI, BigQuery Remote Functions 및 Cloud Functions 문서를 참고하세요. 이 프로젝트의 Github 저장소는 여기에 있습니다. 이 학습으로 어떤 것을 구축하시는지 저에게 알려주세요!
