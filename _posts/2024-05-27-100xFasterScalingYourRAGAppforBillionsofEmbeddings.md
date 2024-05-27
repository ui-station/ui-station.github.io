---
title: "100배 빠르게 - 수십억 개의 임베딩을 위한 RAG 앱 확장하기"
description: ""
coverImage: "/assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_0.png"
date: 2024-05-27 16:21
ogImage: 
  url: /assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_0.png
tag: Tech
originalTitle: "100x Faster — Scaling Your RAG App for Billions of Embeddings"
link: "https://medium.com/gitconnected/100x-faster-scaling-your-rag-app-for-billions-of-embeddings-ded34fccd16a"
---


가장 큰 문제 중 하나는 RAG 어플리케이션의 연산 검색 시간입니다. 1조 개의 임베딩 벡터 기록이 있는 벡터 데이터베이스가 있다고 상상해보세요. 사용자 쿼리를 1조 개의 벡터와 일치시키려고 하면 올바른 정보를 검색하는 데 1분 이상이 걸릴 것입니다.

시간을 단축하기 위해서는 사용자 쿼리 임베딩 벡터와 벡터 데이터베이스에 저장된 백만, 십억 또는 심지어 1조 개의 다른 임베딩 벡터 사이의 코사인 유사도를 계산하는 효율적인 방법을 찾아야 합니다.

MIT 라이선스 하에 Chunkdot은 밀집(dense) 및 희소(sparse) 행렬에 대한 멀티 스레드 행렬 곱셈을 제공하기 위해 특별히 설계되었습니다. Numba를 사용하여 계산을 가속화하며 항목 행렬 표현(임베딩)을 세분화하여 대규모 항목에 대한 K개의 가장 유사한 항목을 계산하는 데 적합합니다.

<div class="content-ad"></div>

Chunkdot GitHub 저장소

HuggingFace에는 Qdrant의 이 데이터셋과 같이 백만 개 이상의 엔트리의 임베딩 벡터를 제공하는 다양한 데이터셋이 많이 있습니다. Chunkdot 성능을 테스트하는 데 사용할 수 있습니다. 그러나 세부 성능 측정을 위해 우리는 NumPy 라이브러리를 사용하여 여러 차원의 임의의 임베딩 벡터를 생성할 것입니다.

Chunkdot의 접근 방식과 코사인 유사도의 의사 코드를 비교할 것이며, 크기와 차원을 증가시킴으로써 성능이 어떻게 영향을 받는지 관찰할 것입니다. 이 작업에는 일관성을 보장하기 위해 Kaggle (GPU 없음) 노트북을 사용할 것입니다.

본 블로그의 모든 코드는 제 GitHub 저장소에서 확인할 수 있습니다.

<div class="content-ad"></div>

# 목차

- 준비 단계 설정
- 의사 코드 알고리즘 코딩
- Chunkdot 알고리즘 코딩
- 계산 시간 함수 코딩
- 10k 벡터 임베딩에 대한 테스트
- 100k 벡터 임베딩에 대한 테스트
- 1 백만 벡터 임베딩에 대한 테스트
- 확장성 영향 시각화
- Chunkdot의 특징
- 다음에 할 일

# 준비 단계 설정

Chunkdot을 설치하려면 다른 라이브러리와 마찬가지인 유사한 설치 과정이 필요합니다.

<div class="content-ad"></div>

```md
# chunkdot 설치하기
pip install chunkdot
```

무엇이든 실행하기 전에 먼저 Kaggle 환경에서 사용 가능한 메모리를 확인해야 합니다.

```md
# 사용 가능한 메모리 확인하기
!free -h
```

<img src="/assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_1.png" />
```

<div class="content-ad"></div>

Chunkdot 에게 사용 가능한 메모리를 확인하는 것은 매우 중요합니다. 벡터 데이터베이스 크기가 증가할수록 계산 메모리도 증가합니다. 사용 가능한 메모리를 초과하지 않도록 하려면 하드웨어의 남은 메모리를 모니터링하는 것이 중요합니다. 제 경우에는 버퍼/캐시를 제외하고 25GB의 여유 공간이 있습니다.

필요한 라이브러리를 가져오겠습니다.

```js
# 행렬을 생성하기 위한 라이브러리
import numpy as np

# chunkdot에서 코사인 유사도 모듈을 가져옵니다.
from chunkdot import cosine_similarity_top_k

# 계산 시간을 측정하기 위한 라이브러리
import timeit
```

# 코딩 의사 코드 알고리즘

<div class="content-ad"></div>

먼저 데이터베이스나 로컬에 저장된 수백만 벡터들과 사용자 쿼리 벡터 간의 코사인 유사도를 계산하는 의사 코드 알고리즘을 만들어 볼게요.

```js
def cosine_pseudocode(query_v, doc_v, num_indices):
    """
    Embedding 벡터와 쿼리 벡터 간의 가장 높은 코사인 유사도 값을 가진 인덱스를 반환합니다.
    
    매개변수:
        query_v (numpy.ndarray): 쿼리 벡터.
        doc_v (list of numpy.ndarray): Embedding 벡터의 목록.
        num_indices (int): 반환할 상위 인덱스 개수.
        
    반환값:
        list of int: 가장 높은 코사인 유사도 값을 가진 인덱스들.
    """
    cosine_similarities = []  # 코사인 유사도를 저장할 빈 리스트를 초기화합니다.

    query_norm = np.linalg.norm(query_v)  # 쿼리 벡터의 노름을 계산합니다.
    
    # 리스트에 있는 각 문서의 임베딩 벡터에 대해 반복합니다.
    for vec in doc_v:
        dot_product = np.dot(vec, query_v.T)  # 임베딩 벡터와 쿼리 벡터 간의 내적을 계산합니다.
        embedding_norm = np.linalg.norm(vec)  # 임베딩 벡터의 노름을 계산합니다.
        cosine_similarity = dot_product / (embedding_norm * query_norm)  # 코사인 유사도 계산
        cosine_similarities.append(cosine_similarity)  # 코사인 유사도를 리스트에 추가합니다.
    
    cosine_similarities = np.array(cosine_similarities)  # 리스트를 넘파이 배열로 변환합니다.
    
    # 배열을 내림차순으로 정렬합니다.
    sorted_array = sorted(range(len(cosine_similarities)), key=lambda i: cosine_similarities[i], reverse=True)

    # 상위 num_indices 값을 가져옵니다.
    top_indices = sorted_array[:num_indices]
    
    # 가장 높은 코사인 유사도 값을 가진 인덱스를 반환합니다.
    return top_indices
```

이 코사인 유사도 함수는 NumPy를 제외한 모든 라이브러리와 독립적으로 작동하며 세 가지 입력을 받습니다:

- query_v는 사용자 쿼리의 임베딩 벡터입니다.
- doc_v는 어딘가에 저장된 문서들의 임베딩 벡터들입니다.
- num_indices는 유사한 상위 k결과로부터 문서의 인덱스 번호입니다.

<div class="content-ad"></div>

# 코딩 Chunkdot 알고리즘

의사코드 알고리즘을 코딩했으니, 다음 단계는 Chunkdot 코사인 유사도 함수를 코딩하는 것입니다.

```js
def cosine_chunkdot(query_v, doc_v, num_indices, max_memory):
    """
    청크닷 라이브러리를 사용하여 코사인 유사도를 계산합니다.
    
    매개변수:
        query_v (numpy.ndarray): 쿼리 벡터.
        doc_v (numpy.ndarray): 임베딩 벡터 목록.
        num_indices (int): 검색할 상위 인덱스 수.
        max_memory (float): 사용할 최대 메모리.
        
    반환값:
        numpy.ndarray: 상위 k 인덱스. 
    """
    
    # 코사인 유사도 계산
    cosine_array = cosine_similarity_top_k(embeddings=query_v, embeddings_right=doc_v, 
                                         top_k=num_indices, max_memory=max_memory)  # 청크닷을 사용하여 코사인 유사도 계산

    # 상위 값의 인덱스 가져오기
    top_indices = cosine_array.nonzero()[1]
    
    # 상위 유사 결과 반환
    return top_indices
```

이 Chunkdot 함수는 네 개의 입력을 받습니다:

<div class="content-ad"></div>

- query_v은 사용자 쿼리의 임베딩 벡터를 나타냅니다.
- doc_v는 어딘가에 저장된 문서의 임베딩 벡터들을 나타냅니다.
- num_indices는 유사한 상위 k개 결과에 대한 문서의 인덱스 번호를 나타냅니다.
- max_memory는 계산에 사용할 수 있는 사용 가능한 메모리를 바이트 단위로 나타냅니다. 예를 들어, 1E9는 1GB를 의미하며, 10E9는 10GB를 의미합니다.

두 함수를 샘플 데이터셋에서 테스트하여 출력을 관찰해보겠습니다.

```js
doc_embeddings = np.random.randn(10, 100) # 10개의 문서 임베딩 (100차원)

user_query = np.random.rand(1, 100) # 1개의 사용자 쿼리 (100차원)

top_indices = 1 # 검색할 상위 인덱스 수

max_memory = 5E9 # 사용할 최대 메모리 (5GB)

# 의사코드를 사용하여 가장 높은 코사인 유사도 값을 갖는 인덱스를 검색
print("의사코드를 사용한 상위 인덱스:", cosine_pseudocode(user_query, doc_embeddings, top_indices))

# chunkdot을 사용하여 가장 높은 코사인 유사도 값을 갖는 인덱스를 검색
print("chunkdot을 사용한 상위 인덱스:", cosine_chunkdot(user_query, doc_embeddings, top_indices, max_memory))
```

```js
### 출력 ###
의사코드를 사용한 상위 인덱스: [4]
chunkdot을 사용한 상위 인덱스: [4]
### 출력 ###
```

<div class="content-ad"></div>

문서 임베딩에 대한 유사한 항목의 인덱스를 최고의 코사인 유사도를 기반으로 한 항목만 반환한다는 것을 의미하는 top_indices 매개변수를 1로 설정했습니다. 메모리 사용량을 5E9로 설정했는데, 이는 5GB와 동일합니다. 두 함수 모두 동일한 인덱스 4를 반환하여 두 함수를 정확하게 코딩했음을 나타냅니다.

# 코딩 계산 시간 함수

또한 이러한 두 함수에 의해 소요된 계산 시간을 측정할 수 있는 타이밍 함수를 만들어야 합니다.

```js
# 소요 시간 계산
def calculate_execution_time(query_v, doc_v, num_indices, max_memory, times):
    
    # 의사코드 함수 실행에 걸리는 시간 계산
    pseudocode_time = round(timeit.timeit(lambda: cosine_pseudocode(query_v, doc_v, num_indices), number=times), 5)

    # chunkdot 함수 실행에 걸리는 시간 계산
    chunkdot_time = round(timeit.timeit(lambda: cosine_chunkdot(query_v, doc_v, num_indices, max_memory), number=times), 5)

    # 소요 시간 출력
    print("의사코드 함수에 걸리는 시간:", pseudocode_time, "초")
    print("chunkdot 함수에 걸리는 시간:", chunkdot_time, "초")
```

<div class="content-ad"></div>

우리는 이미 이 함수로 전달되는 매개변수를 검토했습니다. 여기서 새로운 매개변수는 times입니다. 이 매개변수는 코드를 몇 번 실행할지를 함수에 알려줍니다. 더 큰 규모에서 Chunkdot 성능을 테스트해 봅시다.

# 10,000 벡터 임베딩 테스트

우리는 합리적인 수의 문서 임베딩인 10000개로 시작할 것입니다. 이는 소규모 도메인별 RAG 애플리케이션과 비교 가능합니다. 각 임베딩 벡터의 차원을 1536으로 설정했습니다. 이는 OpenAI 임베딩 모델 텍스트 임베딩-3-small과 동등합니다.

각 접근 방식의 계산 시간을 계산하기 위해 각각 100번 실행하여 효율성을 테스트해 봅시다.

<div class="content-ad"></div>

```python
doc_embeddings = np.random.randn(10000, 1536)  # 10K 문서 임베딩 (1536 차원)

user_query = np.random.rand(1, 1536)  # 사용자 쿼리 (1536 차원)

top_indices = 1  # 검색할 상위 인덱스 수

max_memory = 5E9  # 최대 메모리를 5GB로 설정

# 함수 실행에 소요된 시간을 계산합니다.
calculate_execution_time(user_query, doc_embeddings, top_indices, max_memory, 100)
```

10,000개의 문서 임베딩, 1536 차원을 가진 두 알고리즘을 100번 실행한 결과는 다음과 같습니다:

![그래프](/assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_2.png)

Chunkdot은 유사도 비교 코드와 비교해 더 많은 시간이 소요됩니다. 이는 Chunkdot이 먼저 청크를 생성하고 각 청크에서 계산을 수행한 후 병합하기 때문입니다. 따라서 이 소규모 예제에 대해서는 적합한 해결책이 아닐 수 있습니다. 그러나 나중에 더 큰 예제를 다룰 때 Chunkdot의 이점을 확인하게 될 것입니다.
```

<div class="content-ad"></div>

# 100k 벡터 임베딩 테스트

10천 개의 가상 코드 방식으로는 우리가 이긴다고 하지만, 이제는 문서 임베딩 벡터를 중형 규모의 RAG 응용 프로그램과 유사한 100K 벡터까지 늘려보겠습니다.

각 방법에 대한 계산 시간을 계산해 봅시다. 이번에는 벡터의 수가 매우 많기 때문에 계산을 여러 번 수행할 필요가 없으므로 times 매개변수를 1로 설정하여 코드를 한 번 실행합니다.

```js
doc_embeddings = np.random.randn(100000, 1536) # 100K 문서 임베딩 (1536 차원)

user_query = np.random.rand(1,1536) # 사용자 쿼리 (1536 차원)

top_indices = 1 # 반환할 상위 인덱스 수

max_memory = 5E9 # 최대 메모리를 5GB로 설정

times = 1 # 함수를 실행할 횟수

# 함수 실행 시간 계산
calculate_execution_time(user_query, doc_embeddings, top_indices, max_memory, times)
```

<div class="content-ad"></div>

100,000개의 문서 임베딩, 차원이 1536인 경우, 두 알고리즘을 한 번씩 실행하여 비교한 결과는 다음과 같습니다:

![Comparison Chart](/assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_3.png)

Chunkdot은 의사코드에 비해 거의 절반의 시간이 소요됩니다. 이제 Chunkdot의 유망한 영향을 확인하고 있습니다.

1백만 벡터 임베딩을 위한 테스트 중입니다.

<div class="content-ad"></div>

백만 개의 임베딩을 다루는 작업 중에, 첫 번째로 확인해야 할 사항은 문서 임베딩 벡터가 얼마나 많은 메모리를 차지하는지입니다.

```js
# 1 백만 개의 문서 임베딩 (1536 차원)
doc_embeddings = np.random.randn(1000000, 1536)

# 사용자 쿼리 (1536 차원)
user_query = np.random.rand(1, 1536)

# doc_embeddings와 user_query 임베딩의 메모리 크기 확인
print(doc_embeddings.nbytes / (1024 * 1024 * 1024),
      user_query.nbytes / (1024 * 1024))
```

![이미지](/assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_4.png)

저희 문서 임베딩은 대략 12GB를 차지합니다. 남은 공간을 확인해 보세요.

<div class="content-ad"></div>

```markdown
![image](/assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_5.png)

17GB의 사용 가능한 메모리가 있습니다. 어떠한 메모리 오류도 피하기 위해 max_memory 매개변수의 안전한 값을 12GB로 설정할 것입니다. 결과를 확인해 봅시다.

```js
# 100만 개의 문서 임베딩 (1536 차원)
doc_embeddings = np.random.randn(1000000, 1536)

# 사용자 쿼리 (1536 차원)
user_query = np.random.rand(1, 1536)

top_indices = 1 # 가져올 상위 인덱스 수

max_memory = 12E9 # 최대 메모리 설정 --- 12GB ---

times = 1 # 함수를 실행할 횟수

# 함수 실행 시간 계산하기
calculate_execution_time(user_query, doc_embeddings, top_indices, max_memory, times)
```

![image](/assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_6.png)
```

<div class="content-ad"></div>

ChunkDot은 실제로 계산을 효과적으로 줄입니다. 진지한 RAG 앱을 만들려면 적어도 백만 개의 쿼리부터 시작하는 것이 좋습니다. 높은 차원의 임베딩 모델로 작업을하는 경우 4000까지 증가합니다. 이 접근 방식은 훨씬 더 효율적이어질 것입니다.

# 확장성 영향 시각화

문서 임베딩 벡터 수를 증가시키는 영향을 시각화해 보겠습니다. 시작은 10,000부터 매우 큰 수까지입니다.

![이미지](/assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_7.png)

<div class="content-ad"></div>

저는 세 가지 방법을 그래프로 나타내었고, 문서 임베딩의 수를 늘릴 때 Chunkdot이 모든 방법 중에서 가장 우수하다는 것을 확인했습니다. 이제 임베딩 벡터의 차원이 계산 시간에 어떤 영향을 미치는지 살펴보겠습니다.

![Embedding Vectors Computation Time](/assets/img/2024-05-27-100xFasterScalingYourRAGAppforBillionsofEmbeddings_8.png)

벡터의 차원을 늘리면서 10만 개의 문서를 사용했고, 문서 수를 늘릴 때 관찰한 현상과 동일한 결과를 얻었습니다.

# Chunkdot의 특징

<div class="content-ad"></div>

Chunkdot에는 진행 표시 막대를 표시할 수 있는 기능이 있어서 얼마만큼의 계산이 남았는지 추적할 수 있습니다.

```js
doc_embeddings = np.random.randn(100000, 1536) # 100K document embeddings (1536 dim)

user_query = np.random.rand(1,1536) # user query (1536 dim)

top_indices = 100 # 검색할 상위 인덱스 수

max_memory = 5E9 # 최대 메모리를 5GB로 설정

# 진행 표시 막대와 함께
output_array = cosine_similarity_top_k(user_query, doc_embeddings, 
                        top_k=top_indices, 
                        show_progress=True)
```

![진행 표시 막대](https://miro.medium.com/v2/resize:fit:1400/1*3A2KQ9fDvAA-VfQNKjphJw.gif)

Chunkdot의 출력은 희소 행렬이며, 다음을 사용하여 배열로 변환할 수 있습니다:

<div class="content-ad"></div>

```markdown
# 출력 변환
output_array.toarray()
```

Chunkdot은 문서 임베딩에 대해서만 사용할 수 있습니다. 이는 각 문서 임베딩 요소에 대해 가장 유사한 상위 k개 요소를 반환할 것입니다.

```markdown
# 총 5개의 문서 임베딩
embeddings = np.random.randn(5, 256)

# 각각의 상위 2개 가장 유사한 항목 인덱스 반환
cosine_similarity_top_k(embeddings, top_k=2).toarray()
```

```markdown
### 출력 ###
array([[1.        , 0.        , 0.        , 0.        , 0.09924064],
       [0.        , 1.        , 0.        , 0.09935381, 0.        ],
       [0.02358785, 0.        , 1.        , 0.        , 0.        ],
       [0.        , 0.09935381, 0.        , 1.        , 0.        ],
       [0.09924064, 0.        , 0.        , 0.        , 1.        ]]
### 출력 ###
```

<div class="content-ad"></div>

비슷한 방법으로 top_k 매개변수에 음수 값을 제공하여 가장 다른 항목을 반환할 수도 있습니다.

```js
# 총 5개의 문서 임베딩
embeddings = np.random.randn(5, 256)

# 각 항목의 가장 다른 상위 2개 항목 인덱스 반환
# Top_K = -2
cosine_similarity_top_k(embeddings, top_k=-2).toarray()
```

```js
### 출력 ###
array([[ 0.        ,  0.        , -0.04357524,  0.        , -0.05118288],
       [ 0.        ,  0.        ,  0.        ,  0.01619543, -0.01836534],
       [-0.04357524,  0.        ,  0.        , -0.02466613,  0.        ],
       [ 0.        ,  0.01619543, -0.02466613,  0.        ,  0.        ],
       [-0.05118288, -0.01836534,  0.        ,  0.        ,  0.        ]])
### 출력 ###
```

당신의 상황이 아닐 수도 있지만, 1만 차원까지의 희소한 임베딩을 처리하는 경우 density 매개변수를 사용하여 계산을 더 효율적으로 줄일 수 있습니다.

<div class="content-ad"></div>

```js
# 희소 임베딩 생성을 위해
from scipy import sparse

# 각각 10,000 차원을 가진 100,000개의 문서로 이루어진 희소 매트릭스 생성
# 밀도를 0.005로 정의
임베딩 = sparse.rand(100000, 10000, density=0.005)

# 시스템의 모든 메모리 사용
코사인 유사도 상위 k(embeddings, top_k=50)
```

# 다음 단계

Chunkdot 알고리즘이 어떻게 작동하는지 알고 싶다면, 저자의 멋진 블로그를 확인해보세요. Chunkdot의 가장 큰 장점 중 하나는 CPU 코어에서 작동한다는 것입니다. 앞으로, GPU 지원을 통합할 계획이 있으며, 이는 연산에 소요되는 시간을 크게 줄일 것입니다. 로컬 환경에 충분한 RAM이 없는 경우에는 Kaggle이나 GitHub Codespaces 같은 플랫폼을 활용할 수 있습니다. 이러한 클라우드 CPU 코어와 RAM은 GPU 비용에 비해 매우 저렴합니다. Chunkdot가 어떻게 작동하는지를 잘 설명한 공식 GitHub 저장소와 블로그도 꼭 확인해보세요.```