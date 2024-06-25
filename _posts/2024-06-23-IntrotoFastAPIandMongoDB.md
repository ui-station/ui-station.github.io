---
title: "FastAPI와 MongoDB 소개 첫 걸음부터 시작하기"
description: ""
coverImage: "/assets/img/2024-06-23-IntrotoFastAPIandMongoDB_0.png"
date: 2024-06-23 22:46
ogImage:
  url: /assets/img/2024-06-23-IntrotoFastAPIandMongoDB_0.png
tag: Tech
originalTitle: "Intro to FastAPI and MongoDB"
link: "https://medium.com/@radesrdan/intro-to-fastapi-and-mongodb-cb2261333a08"
---

이 튜토리얼에서는 FastAPI에 의존하는 간단한 파이썬 애플리케이션을 구축하는 방법을 배울 수 있습니다. 이 애플리케이션은 MongoDB로 요청을 보내는 기능을 갖추고 있습니다.

프로그램이 모든 장치에서 실행될 수 있도록 하기 위해, 이 튜토리얼은 보편적인 접근 방식을 취하며 Docker Compose를 사용하여 코드를 실행합니다.

또한 보안과 확장성에 대한 내용에 대해서는 이 PoC에서는 중점을 두지 않았음을 알려드립니다. 해당 정보에 대해서는 FastAPI 보안 문서를 확인하는 것이 좋습니다.

## 사전 요구 사항

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

친구야, 기기에 다음 소프트웨어가 있는지 확인해주세요.

- Git
- Docker Desktop
- Python
- MongoDB Compass 또는 Mongosh

## PoC 실행 방법

저장소를 복제(clone)하기 전에, 모든 선행 요구 사항이 충족되었는지 확인하세요.

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
git clone https://github.com/raduul/FastMongo.git
```

Docker Desktop을 켜고, 데이터가 유지될 볼륨을 구성하세요.

```js
Docker Desktop > Settings > Resources > File Sharing > 원하는 위치 선택
```

애플리케이션을 실행한 후, 복제한 저장소로 이동해서 다음 명령어를 실행하세요:

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
docker compose -f deployment/docker-compose-fastapi.yaml up --build
```

로컬에서 실행 중인 MongoDB에 샘플 데이터를 보내려면 Swagger UI(localhost:8000/docs)에 액세스하십시오.

‘send_data’를 열어서 “Try it out”을 선택한 후 “Execute”를 선택하여 데이터를 데이터베이스로 보내세요.

<img src="/assets/img/2024-06-23-IntrotoFastAPIandMongoDB_0.png" />

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

이후에는 "실행" 버튼 아래에 요청 상태가 표시됩니다. 상태 코드 200은 데이터가 성공적으로 추가되었음을 나타냅니다!

![이미지](/assets/img/2024-06-23-IntrotoFastAPIandMongoDB_1.png)

데이터가 성공적으로 추가되면 MongoDB Compass로 이동하여 추가된 데이터가 있는 데이터베이스 및 컬렉션에 액세스하십시오.
연결 문자열은 다음과 같습니다:

```js
mongodb://root:example@localhost:27017/fast-api?authSource=admin
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

<img src="/assets/img/2024-06-23-IntrotoFastAPIandMongoDB_2.png" />

이제 데이터를 MongoDB에 성공적으로 보냈습니다. 코드를 실험하고 MongoDB 데이터베이스에 추가 경로와 컬렉션을 생성해보세요. 축하합니다 🥳🎊

## 코드 소개

이 애플리케이션을 실행하는 데 필요한 코드는 매우 간단합니다! 세 개의 그룹으로 구성되어 있습니다.

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

- FastAPI — 데이터를 전송하는 API Endpoint를 구축하는 데 사용됩니다.
- MongoDB — FastAPI에서 전송된 데이터를 저장하는 데 사용됩니다.
- Docker Compose — FastAPI 및 MongoDB의 컨테이너화된 이미지를 실행하는 데 사용됩니다.

## FastAPI

이 코드는 여러 개의 파이썬 파일로 구성되어 있지만, 가장 중요한 두 개는 다음과 같습니다:

- 먼저 MongoDB 데이터베이스에 연결이 설정됩니다: (database.py)

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
import logging
from motor.motor_asyncio import AsyncIOMotorClient
import os

logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

client: AsyncIOMotorClient = None

def startup_db_client():
    global client
    try:
        mongo_uri = os.getenv('MONGO_URL')
        client = AsyncIOMotorClient(mongo_uri)
        logging.info("Connected to MongoDB successfully.")
    except Exception as e:
        logging.error(f"Failed to connect to MongoDB: {e}")
    return client['fast-api']

def shutdown_db_client():
    client.close()
    logging.info("MongoDB connection closed.")

def get_database():
    return startup_db_client()
```

둘째로, 연결이 설정되면 데이터를 보낼 때 유추합니다 (main.py)

```js
from fastapi import FastAPI, Depends
from database import startup_db_client, shutdown_db_client, get_database

app = FastAPI()

app.add_event_handler("startup", startup_db_client)
app.add_event_handler("shutdown", shutdown_db_client)

@app.post("/send_data")
async def insert_sample_data(sample_data: dict, db=Depends(get_database)):
    try:
        collection = db['sample-users']
        result = await collection.insert_one(sample_data)
        return {"id of inserted record": str(result.inserted_id)}
    except Exception as e:
        return {"error": str(e)}
```

## MongoDB

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

MongoDB 구성은 PoC를 이해하기 쉽게 만들기 위해 간단하게 유지되었습니다. 따라서 구성은 순전히 기본 설정입니다. MongoDB의 두 가지 주요 파일은 "deployment/docker-compose-fastapi.yaml" 및 "deployment/init-mongo.js"입니다.

- docker-compose-fastapi.yaml — MongoDB를 위한 기본 배포 구성이 포함되어 있습니다.
- deployment/init-mongo.js — Docker Compose에 의해 MongoDB가 배포될 때 PoC를 위해 생성될 스키마에 대한 정보가 포함되어 있습니다.

## Docker Compose

Docker Compose은 이 PoC에서 사용되는 이미지 중 두 개를 실행하는 데 사용됩니다. 구성은 "deployment/docker-compose-fastapi.yaml"에서 찾을 수 있습니다.

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

- fastapi 서비스는 루트에 위치한 Dockerfile을 참조하며 FastAPI 앱을 빌드하고 빌드된 후에 실행하는 방법에 대한 구성을 포함합니다.
- mongo 서비스는 MongoDB 이미지를 참조합니다. "docker compose down" 후에도 데이터가 유지되도록 보장하기 위해 볼륨이 생성되어 데이터를 보관합니다. 사용 중인 특정 이미지는 OS/ARCH Amd64 및 Arm64와 호환됩니다.

LinkedIn에서 언제든지 연락 주세요.
