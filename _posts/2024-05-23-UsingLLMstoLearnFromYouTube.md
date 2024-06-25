---
title: "유튜브에서 배우는 LLMs 사용하기"
description: ""
coverImage: "/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_0.png"
date: 2024-05-23 13:47
ogImage:
  url: /assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_0.png
tag: Tech
originalTitle: "Using LLMs to Learn From YouTube"
link: "https://medium.com/towards-data-science/using-llms-to-learn-from-youtube-4454934ff3e0"
---

![Using LLMs to Learn From YouTube](/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_0.png)

# 소개

팟캐스트나 시청하고 싶은 비디오를 만나본 적이 있나요? 하지만 길이 때문에 시간을 내기 어려워한 적이 있나요? 이러한 형태의 내용의 특정 부분을 다시 참조할 수 있는 쉬운 방법을 바란 적이 있나요?

저는 The Diary of a CEO와 같은 인기 팟캐스트의 YouTube 비디오들에 관해 많은 시간을 할애하기 어려운 문제에 직면해왔습니다. 사실 이러한 팟캐스트에서 다루는 많은 정보들은 빠른 구글 검색을 통해 손쉽게 찾을 수 있습니다. 그러나 저자가 열정적으로 어떤 것에 대한 견해를 표현하거나 성공한 기업가의 경험을 그들의 관점에서 듣는 것은 훨씬 더 통찰력과 명확함을 제공합니다.

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

해당 문제로 동기부여 받으면서 LLM(언어 모델)을 기반으로 한 애플리케이션 및 개발에 대해 배우고 싶어졌습니다. 그래서 YouTube 동영상의 내용에 관한 질문을 할 수 있는 챗봇을 구축하기로 결정했습니다. 이 프레임워크인 RAG(Retrieval Augmented Generation)를 사용했습니다. 이후에, 이 어플리케이션을 LangChain, Pinecone, Flask, React를 사용하여 개발하고 AWS에 배포하는 경험에 대해 이야기하겠습니다:

# 백엔드

LLM이 사용자 정의 질문에 답변을 생성하는 소스로 YouTube 동영상의 대본을 사용할 것입니다. 이를 용이하게 하기 위해, 백엔드는 이를 실시간으로 검색하고 적절히 저장하여 사용할 수 있는 방법과 답변 생성에 사용할 수 있게 하는 방법이 필요합니다. 또한 사용자가 나중에 참조할 수 있도록 채팅 기록을 저장하는 방법도 있으면 좋겠습니다. 이제 이러한 요구 사항을 모두 충족시키기 위해 백엔드를 개발하는 방법에 대해 살펴보겠습니다.

## 응답 생성

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

이 대화형 질문 응답 도구는 관련 컨텍스트와 채팅 기록을 모두 고려하여 질문에 대한 응답을 생성할 수 있어야 합니다. 이는 아래에 설명된대로 대화 기억을 갖춘 데이터 검색 확장 생성을 사용하여 달성할 수 있습니다:

![image](/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_1.png)

불분명한 부분을 명확히 하기 위해 다음과 같은 단계가 포함됩니다:

- 질문 요약: 현재 질문과 채팅 기록을 적절한 프롬프트를 사용하여 단독 질문으로 요약합니다. 이를 위해 LLM에게 요청합니다.
- 의미 검색: 다음으로, 이러한 축약된 질문과 가장 관련성 있는 YouTube 대본 청크를 검색해야 합니다. 대본 자체는 단어 및 구의 수치적 표현인 임베딩으로 저장되어 있습니다. 이 임베딩은 내용과 의미를 포착하는 임베딩 모델에 의해 학습되었습니다. 의미 검색 중에는 임베딩이 축약된 질문의 임베딩과 가장 유사한 각 대본 구성 요소를 검색합니다.
- 컨텍스트 인식 생성: 이러한 검색된 대본 청크는 다시 LLM에게 다른 프롬프트로 사용되어 축약된 질문에 대답하도록 요청됩니다. 축약된 질문을 사용하면 생성된 답변이 현재 질문 및 채팅 중 사용자가 이전에 물었던 질문과 관련이 있는지 보장됩니다.

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

## 데이터 파이프라인

이미 설명된 프로세스의 구현 단계에 들어가기 전에, YouTube 비디오 트랜스크립트 자체에 집중해 보겠습니다. 언급했듯이, RAG 프로세스의 시맨틱 검색 단계에서 효율적으로 검색하고 검색될 수 있도록 이들을 임베딩으로 저장해야 합니다. 이제 이를 위한 소스, 검색 방법 및 저장 방법을 살펴보겠습니다.

- 소스: YouTube는 비디오 ID 및 자동 생성된 트랜스크립트와 같은 메타데이터에 액세스할 수 있도록 Data API를 제공합니다. 첫 번째로, 다양한 돈 전문가와 기업가가 개인 재무, 투자, 성공적인 비즈니스 구축에 대해 논의하는 The Diary of a CEO 팟캐스트 플레이리스트를 선택했습니다.
- 검색: YouTube 비디오의 비디오 ID와 같은 메타데이터를 검색하는 데 책임 있는 한 클래스와, youtube-transcript-API Python 패키지를 사용하여 비디오 트랜스크립트를 검색하는 데 책임 있는 다른 클래스를 활용합니다. 이 트랜스크립트는 그들의 원시 형태로 JSON 파일로 저장됩니다.
- 저장: 그런 다음, 트랜스크립트를 임베딩으로 변환하고 벡터 데이터베이스에 저장해야 합니다. 그러나 이 단계의 사전 조건은 각 질문에 가장 관련성 높은 텍스트 세그먼트를 얻는 동시에 LLM 프롬프트의 길이를 최소화하기 위해 이를 청크로 분할해야 한다는 것입니다. 이 요구 사항을 충족시키기 위해 본 요구 사항을 만족시키기 위해 본 사용자 정의 S3JsonFileLoader 클래스를 정의하고(상자 밖의 문제로 인해 일부 문제가 발생함), 텍스트 분할 객체를 사용하여 트랜스크립트를 로드할 때 분할합니다. 그런 다음, 오픈AI의 gpt-3.5-turbo 모델이 예상하는 임베딩으로 트랜스크립트 청크를 저장하기 위해 LangChain의 인터페이스를 이용하여 선택한 Vectorstore인 Pinecone Vectorstore에 연결합니다:

```js
import os

import pinecone
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.vectorstores import Pinecone
from langchain.chat_models import ChatOpenAI
from langchain.embeddings.openai import OpenAIEmbeddings

from chatytt.embeddings.s3_json_document_loader import S3JsonFileLoader

# 트랜스크립트를 청크로 나누기 위한 스플리터 정의
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=pre_processing_conf["recursive_character_splitting"]["chunk_size"],
    chunk_overlap=pre_processing_conf["recursive_character_splitting"][
        "chunk_overlap"
    ],
)

# 트랜스크립트 로드 및 분할
loader = S3JsonFileLoader(
    bucket="s3_bucket_name",
    key="transcript_file_name",
    text_splitter=text_splitter,
)
transcript_chunks = loader.load(split_doc=True)

# 관련 Pinecone 벡터스토어에 연결
pinecone.init(
    api_key=os.environ.get("PINECONE_API_KEY"),
    environment="gcp-starter"
)
pinecone_index = pinecone.Index(os.environ.get("INDEX_NAME"), pool_threads=4)

# Pinecone에 트랜스크립트 청크를 임베딩으로 저장
vector_store = Pinecone(
    index=pinecone_index,
    embedding=OpenAIEmbeddings(),
    text_key="text",
)
vector_store.add_documents(documents=transcript_chunks)
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

우리는 주기적으로 실행되도록 구성된 워크플로를 이용하여 이러한 단계를 자동화하기 위해 몇 가지 AWS 서비스를 활용할 수도 있습니다. 저는 위에서 언급한 세 가지 단계마다 별도의 AWS Lamba 함수(필요한 리소스를 런타임에 필요에 따라 프로비저닝 및 활용하는 서버리스 컴퓨팅 형태)를 구현하고, 이러한 함수의 실행 순서를 AWS Step Functions(서버리스 오케스트레이션 도구)를 사용하여 정의합니다. 그런 다음, 이 워크플로는 매주 한 번 실행되도록 설정한 Amazon EventBridge 일정에 의해 실행됩니다:

![이미지](/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_2.png)

## RAG 구현

이제 우리가 선택한 재생 목록의 대본이 주기적으로 검색되고 임베딩으로 변환되어 저장되고 있는데, 이제 응용 프로그램의 핵심 백엔드 기능 구현으로 이동할 수 있습니다. 즉, 사용자 정의 질문에 대한 답변을 생성하는 프로세스를 구현해야 합니다. 다행히 LangChain은 이 작업을 완벽하게 수행하는 ConversationalRetrievalChain을 기본 제공합니다! 필요한 것은 쿼리, 채팅 기록, 대본 청크를 검색하는 데 사용할 수 있는 벡터 저장소 개체 및 선택한 LLM을 이 체인에 전달하는 것뿐입니다:

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
import pinecone
from langchain.vectorstores import Pinecone
from langchain.chat_models import ChatOpenAI
from langchain.embeddings.openai import OpenAIEmbeddings

# 세맨틱 검색을 수행할 벡터 저장소를 정의합니다. 이를 위해 Pinecone 벡터 데이터베이스에 대해 세맨틱 검색을 수행합니다.
pinecone.init(
    api_key=os.environ.get("PINECONE_API_KEY"), environment="gcp-starter"
)
pinecone_index = pinecone.Index(os.environ.get("INDEX_NAME"), pool_threads=4)
vector_store = Pinecone(
    index=pinecone_index,
    embedding=OpenAIEmbeddings(),
    text_key="text",
)

# RAG에서 대화 기억을 갖고 있는 단계를 수행할 검색 연쇄를 정의합니다.
chain = ConversationalRetrievalChain.from_llm(
    llm=ChatOpenAI(), retriever=vector_store.as_retriever()
)

# 현재 질문과 채팅 이력을 전달하여 체인을 호출합니다.
response = chain({"question": query, "chat_history": chat_history})["answer"]
```

## 채팅 이력 저장

백엔드는 이제 질문에 대한 답변을 생성할 수 있지만 사용자가 이전 채팅 내용을 참조할 수 있도록 채팅 이력을 저장하고 검색할 수도 있으면 좋을 것입니다. 이는 동일한 항목에 대한 다른 사용자의 액세스 패턴을 알려진 대로 처리하는 NoSQL 데이터베이스인 DynamoDB를 사용하기로 결정했습니다. 이 데이터베이스는 이러한 형식의 비구조적 데이터를 처리하는 빠른 속도와 비용 효율성으로 알려져 있습니다. 추가로 boto3 SDK는 데이터베이스와 상호 작용을 단순화하여 저장 및 검색에 몇 가지 함수만 필요합니다:

```js
import os
import time
from typing import List, Any

import boto3

table = boto3.resource("dynamodb").Table(os.environ.get("CHAT_HISTORY_TABLE_NAME"))

def fetch_chat_history(user_id: str) -> str:
    response = table.get_item(Key={"UserId": user_id})
    return response["Item"]


def update_chat_history(user_id: str, chat_history: List[dict[str, Any]]):
    chat_history_update_data = {
        "UpdatedTimestamp": {"Value": int(time.time()), "Action": "PUT"},
        "ChatHistory": {"Value": chat_history, "Action": "PUT"},
    }
    table.update_item(
        Key={"UserId": user_id}, AttributeUpdates=chat_history_update_data
    )


def is_new_user(user_id: str) -> bool:
    response = table.get_item(Key={"UserId": user_id})
    return response.get("Item") is None


def create_chat_history(user_id: str, chat_history: List[dict[str, Any]]):
    item = {
        "UserId": user_id,
        "CreatedTimestamp": int(time.time()),
        "UpdatedTimestamp": None,
        "ChatHistory": chat_history,
    }
    table.put_item(Item=item)
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

## API를 통해 로직 노출하기

이제 모든 핵심 기능을 다루었지만 사용자가 상호작용하는 앱의 클라이언트 측은 이러한 프로세스를 트리거하고 활용하는 방법이 필요합니다. 이를 용이하게하기 위해 Flask API 내의 각 로직 조각(응답 생성, 채팅 기록 저장, 채팅 기록 검색)은 개별 엔드포인트를 통해 노출되며, 프론트 엔드에서 호출될 것입니다:

```js
from dotenv import load_dotenv
from flask import Flask, request, jsonify
from flask_cors import CORS

from chatytt.chains.standard import ConversationalQAChain
from chatytt.vector_store.pinecone_db import PineconeDB
from server.utils.chat import parse_chat_history
from server.utils.dynamodb import (
    is_new_user,
    fetch_chat_history,
    create_chat_history,
    update_chat_history,
)

load_dotenv()
app = Flask(__name__)

# 서버와 클라이언트가 각각 호스팅되므로 Cross Origin Resource Sharing을 활성화
CORS(app)

pinecone_db = PineconeDB(index_name="youtube-transcripts", embedding_source="open-ai")
chain = ConversationalQAChain(vector_store=pinecone_db.vector_store)

@app.route("/get-query-response/", methods=["POST"])
def get_query_response():
    data = request.get_json()
    query = data["query"]

    raw_chat_history = data["chatHistory"]
    chat_history = parse_chat_history(raw_chat_history)
    response = chain.get_response(query=query, chat_history=chat_history)

    return jsonify({"response": response})


@app.route("/get-chat-history/", methods=["GET"])
def get_chat_history():
    user_id = request.args.get("userId")

    if is_new_user(user_id):
        response = {"chatHistory": []}
        return jsonify({"response": response})

    response = {"chatHistory": fetch_chat_history(user_id=user_id)["ChatHistory"]}

    return jsonify({"response": response})


@app.route("/save-chat-history/", methods=["PUT"])
def save_chat_history():
    data = request.get_json()
    user_id = data["userId"]

    if is_new_user(user_id):
        create_chat_history(user_id=user_id, chat_history=data["chatHistory"])
    else:
        update_chat_history(user_id=user_id, chat_history=data["chatHistory"])

    return jsonify({"response": "chat saved"})


if __name__ == "__main__":
    app.run(debug=True, port=8080)
```

마지막으로, 세 개의 엔드포인트를 하나의 함수로 래핑하여 AWS Lambda를 사용하고, API Gateway 리소스에 의해 트리거되는 방식으로 요청을 올바른 엔드포인트로 보내도록 하는 방법을 설명합니다. 이제 이 설정의 흐름은 다음과 같습니다:

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

아래는 이 전체에 사용되는 각 섹션에 대한 전용 기능 컴포넌트를 활용하여 채팅 봇 애플리케이션에서 기대할 수 있는 모든 일반 요구 사항을 다룹니다:

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

- 사용자 입력을 보내는 채팅 버튼이 있는 컨테이너입니다.
- 사용자 입력 및 답변이 표시되는 채팅 피드가 있습니다.
- 채팅 기록, 새로운 채팅 버튼 및 채팅 저장 버튼이 있는 사이드바가 있습니다.

이 컴포넌트들 간의 상호작용과 데이터 흐름은 아래와 같이 설명됩니다:

![Components Interaction](/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_4.png)

세 가지 엔드포인트로의 API 호출 및 클라이언트 측에서 관련 변수의 상태 변경은 각각의 기능 컴포넌트에서 정의됩니다:

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

- 질문마다 생성된 답변을 가져오는 논리:

```js
import React from "react";
import {chatItem} from "./LiveChatFeed";

interface Props {
    setCurrentChat: React.SetStateAction<any>
    userInput: string
    currentChat: Array<chatItem>
    setUserInput: React.SetStateAction<any>
}

function getCurrentChat({setCurrentChat, userInput, currentChat, setUserInput}: Props){
    // 현재 채팅은 라이브 채팅 피드에 표시됩니다. LLM 응답을 기다리기 전에
    // 사용자 질문을 제공하기 위해 API에 대답을 요청하기 전에 복사하여
    // 별도 변수에 전달하고 현재 채팅에 추가합니다.
    const userInputText = userInput
    setUserInput("")
    setCurrentChat([
        ...currentChat,
        {
            "text": userInputText,
            isBot: false
        }
    ])

    // 포스트 요청을 위한 API 페이로드 생성
    const options = {
        method: 'POST',
        headers: {
            "Content-Type": 'application/json',
            'Accept': 'application/json'
        },
        body: JSON.stringify({
            query: userInputText,
            chatHistory: currentChat
        })
    }

    // 엔드포인트에 핑을 보내 응답을 기다린 후 현재 채팅에 추가하여
    // 라이브 채팅 피드에 표시
    fetch(`${import.meta.env.VITE_ENDPOINT}get-query-response/`, options).then(
        (response) => response.json()
    ).then(
        (data) => {
            setCurrentChat([
                ...currentChat,
                {
                    "text": userInputText,
                    "isBot": false
                },
                {
                    "text": data.response,
                    "isBot": true
                }
            ])
        }
    )
}

export default getCurrentChat
```

2. 사용자가 채팅 저장 버튼을 클릭할 때 채팅 기록을 저장하는 방법:

```js
import React, {useState} from "react";
import {chatItem} from "./LiveChatFeed";
import saveIcon from "../assets/saveicon.png"
import tickIcon from "../assets/tickicon.png"

interface Props {
    userId: String
    previousChats: Array<Array<chatItem>>
}

function SaveChatHistoryButton({userId, previousChats}: Props){
    // 현재 채팅이 저장되었는지 여부를 결정하는 상태 정의합니다.
    const [isChatSaved, setIsChatSaved] = useState(false)

    // 채팅 기록을 저장하는 PUT 요청 페이로드 생성
    const saveChatHistory = () => {
        const options = {
            method: 'PUT',
            headers: {
                "Content-Type": 'application/json',
                'Accept': 'application/json'
            },
            body: JSON.stringify({
                "userId": userId,
                "chatHistory": previousChats
            })
        }

        // 채팅 기록이 성공적으로 저장되면 API에 핑 보내고
        // isChatSaved 상태를 true로 설정합니다
        fetch(`${import.meta.env.VITE_ENDPOINT}save-chat-history/`, options).then(
            (response) => response.json()
        ).then(
            (data) => {
                setIsChatSaved(true)
            }
        )
    }

    // isChatSaved 상태 값에 따라 저장된 채팅 버튼에 동적으로 텍스트 표시
    return (
        <button
            className="save-chat-history-button"
            onClick={() => {saveChatHistory()}
        > <img className={isChatSaved?"tick-icon-img":"save-icon-img"} src={isChatSaved?tickIcon:saveIcon}/>
        {isChatSaved?"Chats Saved":"Save Chat History"}
        </button>
    )
}

export default SaveChatHistoryButton
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

3. 앱이 처음으로 로드될 때 채팅 기록을 검색합니다:

```js
import React from "react";
import {chatItem} from "./LiveChatFeed";

interface Props {
    userId: String
    previousChats: Array<Array<chatItem>>
    setPreviousChats: React.SetStateAction<any>
}

function getUserChatHistory({userId, previousChats, setPreviousChats}: Props){
    // GET 요청을 위한 페이로드 생성
    const options = {
            method: 'GET',
            headers: {
                "Content-Type": 'application/json',
                'Accept': 'application/json'
            }
        }

        // GET 요청이므로 사용자 ID를 쿼리 매개변수로 전달합니다.
        // API에서 반환된 채팅 기록으로 이전 채팅 상태를 설정합니다.
        fetch(`${import.meta.env.VITE_ENDPOINT}get-chat-history/?userId=${userId}`, options).then(
            (response) => response.json()
        ).then(
            (data) => {
            if (data.response.chatHistory.length > 0) {
                setPreviousChats(
                        [
                            ...previousChats,
                            ...data.response.chatHistory
                        ]
                    )
                }
            }
        )
}

export default getUserChatHistory
```

UI 자체에 대해, ChatGPT의 인터페이스와 매우 유사한 것을 선택했습니다. 중앙의 채팅 피드 구성 요소와 채팅 기록과 같은 지원 콘텐츠를 담은 사이드바가 있습니다. 사용자를 위한 편안함 기능으로는 가장 최근에 생성된 채팅 항목으로 자동 스크롤링 및 로그인시 이전 채팅이 로드되는 등이 있습니다. 최종 UI 모습은 아래와 같습니다:

이제 완전히 기능하는 UI가 준비되었으니 온라인 사용을 위해 호스팅해야 합니다. 저는 AWS Amplify를 사용하여 이를 수행하기로 선택했습니다. Amplify는 리소스 프로비저닝 및 웹 애플리케이션 호스팅을 처리하는 완전 관리형 웹 호스팅 서비스 중 하나입니다. 앱의 사용자 인증은 Amazon Cognito에 의해 관리되며 사용자 회원가입, 로그인, 자격 증명 저장 및 관리를 제공합니다.

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

<img src="/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_5.png" />

# ChatGPT 응답과 비교

앱을 구축하는 과정을 논의했으니, 몇 가지 질문에 대한 생성된 응답을 심도 있게 살펴보고, 이를 ChatGPT\*에 제시된 동일한 질문과 비교해 보겠습니다.

저희 애플리케이션에서 사용되는 LLM에 제시된 기본 프롬프트에는 시맨틱 검색 단계에서 검색된 추가 컨텍스트(관련 트랜스크립트 청킹 형태)가 포함될 것이기 때문에 이러한 비교는 본질적으로 "부당"한 것이라는 점에 유의하십시오. 그러나, RAG를 사용하여 생성된 프롬프트와 같은 기본 LLM에 의해 생성된 응답과 어떤 차이가 있는지 정성적으로 평가할 수 있게 해줄 것입니다.

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

\*All ChatGPT responses are from gpt-3.5, since this was the model used in the application.

## 예시 1:

Steven Bartlett가 금융 작가이자 투자자인 Morgan Housel과의 대화를 나누는 이 비디오의 내용에 대해 알고 싶습니다. 비디오 제목을 보면 집 구입에 반대하는 것으로 보이지만, 시간이 없어서 전체 내용을 확인할 수 없다고 가정해 봅시다. 여기 저는 이에 대해 애플리케이션과의 대화 스니펫을 보여드립니다:

<img src="/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_6.png" />

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

표 태그를 Markdown 형식으로 변경해주세요.

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

## 예시 2

아래는 수익화 및 인수 전문가인 Alex Hormozi와의 이 토론에 대한 채팅에서 일부 스니펫이 있습니다. 비디오 제목으로부터, 그는 비즈니스를 성공적으로 확장하는 데에 대해 잘 알고 있다는 것 같아, 그에 대해 더 많은 세부 정보를 요청했습니다:

![이미지](/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_8.png)

이것은 합리적인 대답으로 보이지만, 동일한 질문 라인에서 더 많은 정보를 추출할 수 있는지 확인해 보겠습니다.

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

![LLM 이미지1](/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_9.png)

![LLM 이미지2](/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_10.png)

YouTube 트랜스크립트에서 LLM이 추출할 수 있는 세부 수준에 주목해보세요. 위의 내용은 비디오의 17:00-35:00 타임스탬프 주변 15~20분 부분에서 찾을 수 있습니다.

ChatGPT에 같은 질문을 한 결과, 기업가에 관한 일반적인 답변만 제공되었지만 비디오 트랜스크립트 내용을 통해 사용할 수 있는 세부 정보가 부족합니다.

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

![사용 예시 이미지](/assets/img/2024-05-23-UsingLLMstoLearnFromYouTube_11.png)

# 배포

마지막으로 논의할 주제는 AWS에서 각 구성 요소를 배포하는 과정입니다. 데이터 파이프라인, 백엔드 및 프론트엔드는 각각 자체 CloudFormation 스택(여러 AWS 리소스의 모음) 내에 포함되어 있습니다. 이렇게 각각을 격리된 상태에서 배포할 수 있도록 해줌으로써, 개발 중에 전체 앱이 불필요하게 다시 배포되지 않도록 합니다. 저는 AWS SAM (서버리스 애플리케이션 모델)을 사용하여 각 구성 요소에 대한 인프라를 코드로 배포하는데 활용하고 있습니다. SAM 템플릿 사양 및 CLI를 활용합니다:

- SAM 템플릿 사양 - AWS CloudFormation을 확장하는 용도로 사용되는 간결한 구문으로, AWS 리소스의 모음을 정의하고 구성하는 데 사용됩니다. 리소스 간 상호 작용 방식 및 필요한 권한을 지정합니다.
- SAM CLI - SAM 템플릿에 정의된 리소스를 빌드하고 배포하는 데 사용되는 명령줄 도구입니다. 애플리케이션 코드 및 종속성의 패키징, SAM 템플릿을 CloudFormation 구문으로 변환하고 CloudFormation에서 개별 스택으로 템플릿을 배포하는 작업을 처리합니다.

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

위 글 전체 템플릿(자원 정의)을 모두 포함하는 대신, 우리가 이야기한 각 서비스에 대해 특정 관심 영역을 강조하겠습니다.

AWS 리소스에 민감한 환경 변수 전달하기:

유튜브 데이터 API, OpenAI API, Pinecone API와 같은 외부 구성 요소는 애플리케이션 전체에서 많이 의존합니다. 이러한 값을 CloudFormation 템플릿에 하드코딩하고 '매개변수'로 전달하는 것이 가능하지만, 더 안전한 방법은 AWS SecretsManager에서 각각의 비밀을 만들고 다음과 같이 해당 템플릿에서 이 비밀을 참조하는 것입니다:

```js
Parameters: YoutubeDataAPIKey: Type: String;
Default: "{resolve:secretsmanager:youtube-data-api-key:SecretString:youtube-data-api-key}";
PineconeAPIKey: Type: String;
Default: "{resolve:secretsmanager:pinecone-api-key:SecretString:pinecone-api-key}";
OpenaiAPIKey: Type: String;
Default: "{resolve:secretsmanager:openai-api-key:SecretString:openai-api-key}";
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

람다 함수 정의:

이 서버리스 코드 단위들은 데이터 파이프라인의 중심을 형성하며 웹 애플리케이션의 백엔드로의 진입점 역할을 합니다. SAM을 사용하여 이들을 배포하는 것은 호출될 때 함수가 실행해야 할 코드의 경로를 정의하는 것과 필요한 권한 및 환경 변수를 함께 지정하는 것만으로 간단합니다. 다음은 데이터 파이프라인에서 사용되는 함수 중 하나의 예시입니다:

```js
FetchLatestVideoIDsFunction:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: ../code_uri/.
      Handler: chatytt.youtube_data.lambda_handlers.fetch_latest_video_ids.lambda_handler
      Policies:
        - AmazonS3FullAccess
      Environment:
        Variables:
          PLAYLIST_NAME:
            Ref: PlaylistName
          YOUTUBE_DATA_API_KEY:
            Ref: YoutubeDataAPIKey
```

Amazon States 언어로 데이터 파이프라인의 정의를 검색하는 것:

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

개별 람다 함수에 Step Functions을 오케스트레이터로 사용하려면, Amazon States Language에서 각 함수가 실행되어야 하는 순서 및 최대 재시도 횟수와 같은 구성을 정의해야 합니다. 이 작업을 간단하게 수행하는 방법은 Step Functions 콘솔의 Workflow Studio를 사용하여 워크플로우를 다이어그램으로 만든 다음, 해당 워크플로우의 자동 생성된 ASL 정의를 적절히 수정할 수 있는 시작점으로 활용하는 것입니다. 그러면 이를 CloudFormation 템플릿에 링크하여 해당 위치에 정의하는 대신에 사용할 수 있습니다:

```js
EmbeddingRetrieverStateMachine:
  Type: AWS::Serverless::StateMachine
  Properties:
    DefinitionUri: statemachine/embedding_retriever.asl.json
    DefinitionSubstitutions:
      FetchLatestVideoIDsFunctionArn: !GetAtt FetchLatestVideoIDsFunction.Arn
      FetchLatestVideoTranscriptsArn: !GetAtt FetchLatestVideoTranscripts.Arn
      FetchLatestTranscriptEmbeddingsArn: !GetAtt FetchLatestTranscriptEmbeddings.Arn
    Events:
      WeeklySchedule:
        Type: Schedule
        Properties:
          Description: Schedule to run the workflow once per week on a Monday.
          Enabled: true
          Schedule: cron(0 3 ? * 1 *)
    Policies:
    - LambdaInvokePolicy:
        FunctionName: !Ref FetchLatestVideoIDsFunction
    - LambdaInvokePolicy:
        FunctionName: !Ref FetchLatestVideoTranscripts
    - LambdaInvokePolicy:
        FunctionName: !Ref FetchLatestTranscriptEmbeddings
```

이 포스트에서 논의한 데이터 파이프라인을 위해 사용된 ASL 정의는 여기에서 확인할 수 있습니다.

API 리소스 정의:

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

웹 애플리케이션용 API가 프론트엔드와 별도로 호스팅될 예정이므로 API 리소스를 정의할 때 CORS (Cross-Origin Resource Sharing) 지원을 활성화해야 합니다:

```js
ChatYTTApi: Type: AWS::Serverless::Api;
Properties: StageName: Prod;
Cors: AllowMethods: "'*'";
AllowHeaders: "'*'";
AllowOrigin: "'*'";
```

위 설정을 통해 두 리소스가 서로 자유롭게 통신할 수 있게 됩니다. 람다 함수를 통해 액세스할 수 있는 여러 엔드포인트는 다음과 같이 정의할 수 있습니다:

```js
ChatResponseFunction:
    Type: AWS::Serverless::Function
    Properties:
      Runtime: python3.9
      Timeout: 120
      CodeUri: ../code_uri/.
      Handler: server.lambda_handler.lambda_handler
      Policies:
        - AmazonDynamoDBFullAccess
      MemorySize: 512
      Architectures:
        - x86_64
      Environment:
        Variables:
          PINECONE_API_KEY:
            Ref: PineconeAPIKey
          OPENAI_API_KEY:
            Ref: OpenaiAPIKey
      Events:
        GetQueryResponse:
          Type: Api
          Properties:
            RestApiId: !Ref ChatYTTApi
            Path: /get-query-response/
            Method: post
        GetChatHistory:
          Type: Api
          Properties:
            RestApiId: !Ref ChatYTTApi
            Path: /get-chat-history/
            Method: get
        UpdateChatHistory:
          Type: Api
          Properties:
            RestApiId: !Ref ChatYTTApi
            Path: /save-chat-history/
            Method: put
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

리액트 앱 리소스를 정의하는 방법:

AWS Amplify를 사용하면 관련 Github 저장소에 대한 참조와 적절한 액세스 토큰을 사용하여 애플리케이션을 빌드하고 배포할 수 있습니다:

```js
AmplifyApp:
    Type: AWS::Amplify::App
    Properties:
      Name: amplify-chatytt-client
      Repository: <https://github.com/suresha97/ChatYTT>
      AccessToken: '{resolve:secretsmanager:github-token:SecretString:github-token}'
      IAMServiceRole: !GetAtt AmplifyRole.Arn
      EnvironmentVariables:
        - Name: ENDPOINT
          Value: !ImportValue 'chatytt-api-ChatYTTAPIURL'
```

한 번 저장소 자체에 액세스할 수 있으면, Amplify는 앱을 구축하고 배포하는 방법에 대한 지침이 포함된 구성 파일을 찾습니다:

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

```yaml
version: 1
frontend:
  phases:
    preBuild:
      commands:
        - cd client
        - npm ci
    build:
      commands:
        - echo "VITE_ENDPOINT=$ENDPOINT" >> .env
        - npm run build
  artifacts:
    baseDirectory: ./client/dist
    files:
      - "**/*"
  cache:
    paths:
      - node_modules/**/*
```

덤으로, 계속적인 배포 프로세스를 자동화하기 위해 모니터링할 브랜치 리소스를 정의하여 추가 커밋이 발생할 때 앱을 자동으로 재배포할 수도 있습니다:

```yaml
AmplifyBranch:
  Type: AWS::Amplify::Branch
  Properties:
    BranchName: main
    AppId: !GetAtt AmplifyApp.AppId
    EnableAutoBuild: true
```

이렇게 최종 배포가 완료되면, AWS Amplify 콘솔에서 제공된 링크를 통해 누구에게나 접근 가능합니다. 이렇게 접근한 앱의 데모 기록은 다음에서 확인할 수 있습니다:

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

높은 수준에서 다음 단계를 다뤘습니다:

- 콘텐츠 수집 및 저장을 위한 데이터 파이프라인 구축.
- 대화 기억을 활용한 검색 증강 생성을 수행하는 백엔드 서버 구성.
- 생성된 답변 및 채팅 기록을 제공하는 사용자 인터페이스 설계.
- 이러한 구성 요소를 연결하고 배포하여 가치를 제공하고 시간을 절약하는 솔루션을 생성하는 방법.

이러한 응용프로그램이 학습 및 개발 목적의 YouTube 비디오 소비를 최적화하고 어떤 방식으로든 간소화할 수 있는 방법을 알아보았습니다. 그러나 이러한 방법은 동일하게 직장에서 내부 사용이나 고객을 위한 솔루션을 확장하는 데 쉽게 적용할 수 있습니다. 이것이 LLMs의 인기와 특히 RAG 기술이 많은 조직에서 큰 주목을 받는 이유입니다.

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

마크다운 형식으로 표 태그를 변경해보세요.

# 감사의 글

Diary of a CEO 팀에게 이 프로젝트와 이 글 작성 중에 이 플레이리스트의 비디오 대본을 사용할 수 있는 허락을 받아 감사의 말씀을 전합니다.

모든 이미지는 별도 명시가 없는 한 저자가 찍은 것입니다.
