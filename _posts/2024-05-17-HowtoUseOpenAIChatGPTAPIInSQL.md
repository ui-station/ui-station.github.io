---
title: "오픈AI ChatGPT API를 SQL에서 사용하는 방법"
description: ""
coverImage: "/assets/img/2024-05-17-HowtoUseOpenAIChatGPTAPIInSQL_0.png"
date: 2024-05-17 19:12
ogImage: 
  url: /assets/img/2024-05-17-HowtoUseOpenAIChatGPTAPIInSQL_0.png
tag: Tech
originalTitle: "How to Use OpenAI ChatGPT API… In SQL"
link: "https://medium.com/towards-data-science/how-to-use-openai-chatgpt-api-in-sql-9b60d2526a9e"
---


<img src="/assets/img/2024-05-17-HowtoUseOpenAIChatGPTAPIInSQL_0.png" />

챗GPT 및 OpenAI API를 사용할 때 SQL이 먼저 떠오르지는 않겠지만, 데이터의 언어이기 때문에 SQL을 활용할 수 있다는 사실을 명심해야 합니다. SQL에서 HTTP 요청을 보낼 수 있다는 점은 다양한 가능성을 열어 줍니다.

오늘의 글에서는 PL/SQL을 사용하여 사용자 지정 Oracle SQL 함수를 작성하는 방법을 보여 드리겠습니다. 질문 문자열을 입력으로 받아서 형식화된 JSON을 반환합니다. Oracle의 dbms_cloud 패키지가 API 호출을 수행하는 데 주로 사용되며, 다른 데이터베이스 공급업체를 사용하는 경우 해당 작업을 수행할 수 있는 대체 패키지 및 함수 세트를 찾을 수 있을 것입니다.

따라서, 먼저 함께 따라 해야 할 전제 조건에 대해 설명하겠습니다.

<div class="content-ad"></div>

# ChatGPT in SQL — 필수 사항

소개에서 언급했듯이, 저는 오라클 클라우드에서 프로비저닝된 항상 무료 인스턴스에서 실행되는 Oracle SQL을 사용하고 있습니다. 만약 따라하기를 원한다면 무료 계정을 등록하고 데이터베이스 인스턴스를 프로비저닝하고 연결 지갑을 다운로드해야 합니다.

또한 필요한 것은 OpenAI API 키입니다. 링크된 기사를 통해 몇 분 안에 얻는 방법을 알 수 있습니다.

그게 거의 다에요! 이제 본격적으로 시작해 봅시다.

<div class="content-ad"></div>

# OpenAI API — 대화 완성 엔드포인트 테스트

SQL에서 구현할 대화 완성 예제는 매우 간단합니다. OpenAI의 공식 문서에서는 API에 요청하는 방법을 보여줍니다:

![image](/assets/img/2024-05-17-HowtoUseOpenAIChatGPTAPIInSQL_1.png)

SQL에서는 Python과 같이 OpenAI를 위한 서드파티 라이브러리가 없기 때문에 좀 더 수동적인 방법을 선택해야 합니다. 이론적으로는 — 위의 curl 명령을 실행하고 응답을 받는다면 SQL에서도 동일한 작업을 수행할 수 있습니다.

<div class="content-ad"></div>

이것을 시연하는 가장 쉬운 방법은 Postman을 통해 하는 것입니다. https://api.openai.com/v1/chat/completions에 새로운 POST 요청의 헤더와 JSON 본문을 채우고, 나와 비슷한 응답을 받을 수 있습니다:

![이미지](/assets/img/2024-05-17-HowtoUseOpenAIChatGPTAPIInSQL_2.png)

가장 인상적인 GPT 응답은 아닙니다만, 동작은 합니다. 이제 SQL로 가져와 봅시다.

# SQL에서 ChatGPT 사용하기 — 사용자 지정 PL/SQL 함수에서 OpenAI API 사용 방법

<div class="content-ad"></div>

PL/SQL을 사용하면 사용자 정의 함수를 정의할 수 있습니다. get_gpt_response() 함수는 문자열 질문을 받아 CLOB를 반환합니다. CLOB는 기본 VARCHAR2 타입으로는 너무 큰 문자열을 저장하는 데 사용되는 특별한 데이터 유형입니다.

이 함수 내에서 v_api_key 상수는 OpenAI API 키의 값을 보유하고 있습니다. 따라서 꼭 바꾸지 않도록 주의하세요.

이 함수는 Oracle의 dbms_cloud 패키지를 사용하여 OpenAI의 채팅 완료 엔드포인트로 HTTP 요청을 보냅니다. send_request() 프로시저는 다음 매개변수가 필요합니다:

- uri — 엔드포인트의 URL입니다.
- method — 요청에 사용되는 HTTP 메서드입니다. 여러분은 POST로 설정해야 합니다.
- headers — 요청 헤더를 지정하는 JSON 객체입니다. 이전에 본 바와 같이 Content-Type 및 Authorization을 명시해야 합니다.
- body — BLOB로 변환된 JSON 객체입니다. 보내는 데이터를 포함하며, 사용할 모델, 온도 매개변수(랜덤성) 및 GPT가 답변할 질문과 같은 정보가 포함됩니다.

<div class="content-ad"></div>

send_request() 함수의 결과는 v_response 변수에 저장되며, 그것은 사용자에게 텍스트로 반환됩니다:

```js
create or replace function get_gpt_response(
    p_question varchar2
) return clob
is
    v_api_key constant varchar2(100) := '<your-api-key>';
    v_response dbms_cloud_types.resp;
begin
    v_response := dbms_cloud.send_request(
        uri     => 'https://api.openai.com/v1/chat/completions',
        method  => dbms_cloud.method_post,
        -- Headers must be of type JSON_OBJECT
        headers => json_object(
            'Content-Type'  value 'application/json',
            'Authorization' value 'Bearer ' || v_api_key
        ),
        -- Request body must be a JSON structure converted to BLOB
        body    => utl_raw.cast_to_raw(
            json_object(
                'model'    value 'gpt-3.5-turbo',
                'messages' value json_array(
                    json_object(
                        'role'    value 'user',
                        'content' value p_question
                    )
                ),
                'temperature' value 0.5
            )
        )
    );
    return dbms_cloud.get_response_text(v_response);
end get_gpt_response;
/
```

위 스니펫을 실행하면 해당 함수를 사용할 수 있게 됩니다.

예상대로 작동하는지 확인해 봅시다:

<div class="content-ad"></div>

```js
select 
    json_query(get_gpt_response('What is the capital of United States?'), '$' returning clob pretty) as response 
from dual;
```

여기에 얻은 응답이 있어요:

<img src="/assets/img/2024-05-17-HowtoUseOpenAIChatGPTAPIInSQL_3.png" />

매우 좋은 결과 나왔네요! 하지만 하나 문제가 있는데요 — 반환된 응답이 JSON 형식으로 표시되는데, 이는 관계형 데이터베이스를 다룰 때 기본적으로 원하는 형식은 아니죠.

<div class="content-ad"></div>

다행히도, Oracle은 탁월한 JSON 지원을 제공하기 때문에 관련 필드를 추출하고 응답을 일반 데이터베이스 테이블 형식으로 포맷할 수 있습니다:

```js
with response as (
    select get_gpt_response('What is the capital of United States?') as data
    from dual
)
select
    jt.*
from response r,
    json_table(
        r.data,
        '$'
        columns(
            id                varchar2(50)   path '$.id',
            answer            varchar2(1000) path '$.choices[0].message.content',
            prompt_tokens     number         path '$.usage.prompt_tokens',
            completion_tokens number         path '$.usage.completion_tokens',
            total_tokens      number         path '$.usage.total_tokens'
        )
    ) jt;
```

아래는 출력 결과입니다:

![출력 이미지](/assets/img/2024-05-17-HowtoUseOpenAIChatGPTAPIInSQL_4.png)

<div class="content-ad"></div>

여기서부터는 세상이 당신의 조개껍질처럼 열립니다. 결과를 그대로 사용하거나 표로 저장할 수도 있습니다. 토큰 사용 정보는 자주 이 함수를 실행할 계획이 있다면 사용된 리소스를 잘 보여주는 지표가 될 것입니다.

# 개선할 수 있는 점 (하고야 할 것)

오늘 구현한 솔루션은 작동하지만 상당히 기본적이며 몇 가지 조정이 필요합니다:

- 예외 처리 — 현재 전혀 구현되지 않았습니다. dbms_cloud 오류뿐만 아니라 일반적인 다른 오류도 잡을 수 있도록 해야 합니다.
- 하드코딩된 엔드포인트 — 현재 상태에서 함수는 채팅 완성 엔드포인트로만 요청을 보냅니다. 엔드포인트를 동적으로 만드는 것이 더 좋을 것입니다.
- 응답이 저장되지 않음 — 사용자에게 반환하기 전에 응답을 데이터베이스 테이블에 저장하는 것이 좋을 것입니다.

<div class="content-ad"></div>

위의 모든 부분은 기본적인 SQL 기술이 있다면 쉽게 해결할 수 있기 때문에 여러분에게 맡기겠습니다.

# SQL에서 OpenAI API 요약

많은 사람들이 SQL이 OpenAI API에 HTTP 호출을 위한 유효한 옵션으로 여겨지지 않는다고 생각하지만, 실제로 많은 신입사원들은 SQL이 기본적인 데이터 조작 이상의 기능을 수행할 수 있다는 것을 알지 못합니다. SQL은 데이터의 언어이며, 이에 따라 Python이 할 수 있는 거의 모든 작업을 데이터 이동 없이 수행할 수 있습니다.

오늘의 예시는 Oracle SQL과 PL/SQL로 한정되어 있지만, 여러분이 나의 해결책을 SQL Server, MySQL 및 Postgres에 구현할 방법을 찾을 수 있을 것입니다.

<div class="content-ad"></div>

SQL 사용 사례 중에서 머신 러닝과 공간 데이터 분석과 같은 더 흥미로운 내용이 추가로 올라올 예정이니 기대해 주세요.

읽어 주셔서 감사합니다.