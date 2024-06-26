---
title: "자체 데이터에 대한 질문 응답을 위해 Langchain 사용하기"
description: ""
coverImage: "/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_0.png"
date: 2024-05-18 20:32
ogImage:
  url: /assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_0.png
tag: Tech
originalTitle: "Using langchain for Question Answering on Own Data"
link: "https://medium.com/@onkarmishra/using-langchain-for-question-answering-on-own-data-3af0a82789ed"
---

## langchain을 사용하여 자신의 데이터로 대화하는 단계별 안내서

대형 언어 모델은 학습된 주제에 관한 질문에 대답할 수 있습니다. 그러나 그들은 우리의 개인 데이터나 회사의 소유 문서 또는 LLM 학습 이후에 작성된 기사에 관한 질문에 대답할 수 없습니다. 우리 자신의 문서와 대화하고 이러한 문서에서 질문에 대답하는 데 LLM을 사용할 수 있다면 정말 멋질 것입니다. 본 문서는 LangChain: Chat with Your Data라는 강의에서 Andrew Ng 교수와 LangChain 창립자 Harrison Chase로부터 대부분의 내용을 가져왔습니다. 이 글은 langchain에 관한 세 번째 글입니다. 첫 번째 글은 langchain이 LLM 응용 프로그램 개발에 어떻게 사용될 수 있는지에 대해 논의하고 있습니다. 두 번째 글은 LLM 응용 프로그램 개발에 체인과 에이전트를 사용하는 방법에 대해 논의하고 있습니다.

LangChain은 LLM 응용 프로그램을 구축하기 위한 오픈 소스 개발자 프레임워크입니다. 본 글에서는 LangChain의 특정 사용 사례인 LangChain을 사용하여 자신의 데이터와 대화하는 방법에 중점을 둘 것입니다. 이 글에서는 주로 다음 주제를 다룰 것입니다:

- 문서 로딩
- 문서 분할
- 벡터 저장소 및 임베딩
- 검색
- 질문 응답

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

# 문서 로딩

검색 증강 생성 (RAG) 프레임워크에서 LLM은 실행 중 일환으로 외부 데이터셋에서 문맥적 문서를 검색합니다. 이는 우리가 특정 문서 (예: PDF, 비디오 등)에 대해 질문하고 싶을 때 유용합니다. 데이터와 대화할 수 있는 애플리케이션을 만들고 싶다면 먼저 데이터를 작업할 수 있는 형식으로 로드해야 합니다.

![이미지](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_0.png)

이를 위해 LangChain의 문서 로더를 사용합니다. 문서 로더는 다양한 형식과 소스에서 데이터에 액세스하고 변환하는 구체적인 사항을 다룹니다. 구조화된 데이터 소스나 구조화되지 않은 데이터 소스에서 로드할 수 있습니다. 예를 들어 웹사이트, 데이터베이스, YouTube, arxiv, Twitter, Hacker News 또는 Figma, Notion과 같은 소유 데이터 소스 또는 Airbyte, Stripe, Airtable과 같은 소스에서 데이터에 액세스하고 로드해야 할 수 있습니다. 이러한 문서는 pdf, html, json, word, PowerPoint과 같은 다양한 데이터 유형이거나 표 형식일 수 있습니다. 문서 로더는 이러한 데이터 소스로부터 데이터를 가져와 내용과 관련 메타데이터로 구성된 표준 문서 객체로 로드합니다. 또한 langchain에는 아래에서 확인할 수 있는 80가지가 넘는 다양한 문서 로더가 있습니다.

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

![Screenshot](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_1.png)

## PyPDF DataLoader

이제 PyPDF 로더를 사용하여 pdf를 로드할 것입니다. Andrew Ng의 유명한 CS229 강의에서 MachineLearning-Lecture01.pdf를 로드할 것입니다.

```js
from langchain.document_loaders import PyPDFLoader
loader = PyPDFLoader("docs/cs229_lectures/MachineLearning-Lecture01.pdf")

#Load the document by calling loader.load()
pages = loader.load()

print(len(pages))
print(pages[0].page_content[0:500])

print(pages[0].metadata)
# {'source': 'docs/cs229_lectures/MachineLearning-Lecture01.pdf', 'page': 0}
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

이것은 문서 목록을 불러옵니다. 이 경우에는 PDF에 22개의 서로 다른 페이지가 있습니다. 각 페이지는 문서이며 문서에는 페이지 콘텐츠와 메타데이터가 포함되어 있습니다. 페이지 콘텐츠는 페이지의 내용이며 메타데이터 요소는 각 문서와 관련된 메타데이터가 포함되어 있습니다.

## YouTube DataLoader

LangChain은 YouTube에서 비디오를 불러오는 YoutubeAudioLoader를 제공합니다. 이 loader를 사용하여 비디오나 강의에서 질문을 하거나 할 수 있습니다.

```js
from langchain.document_loaders.generic import GenericLoader
from langchain.document_loaders.parsers import OpenAIWhisperParser
from langchain.document_loaders.blob_loaders.youtube_audio import YoutubeAudioLoader

url="https://www.youtube.com/watch?v=jGwO_UgTS7I"
save_dir="docs/youtube/"
loader = GenericLoader(
    YoutubeAudioLoader([url],save_dir),
    OpenAIWhisperParser()
)
docs = loader.load()

print(docs[0].page_content[0:500])
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

YouTubeAudioLoader는 YouTube 링크에서 오디오 파일을 로드하고 OpenAIWhisperParser를 사용합니다. OpenAIWhisperParser는 OpenAI의 음성 대 텍스트 Whisper 모델을 사용하여 YouTube 오디오를 작업할 수 있는 텍스트 형식으로 변환합니다. YouTube URL과 오디오 파일을 저장할 디렉토리를 지정해야 합니다.

## WebBaseLoader

WebBaseLoader는 인터넷에서 URL을 로드하는 데 사용됩니다.

```js
from langchain.document_loaders import WebBaseLoader

# 깃허브 페이지에서 마크다운 파일 사용
loader = WebBaseLoader("https://github.com/basecamp/handbook/blob/master/37signals-is-you.md")

docs = loader.load()
print(docs[0].page_content[:500])
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

위에 나온 출력물에는 많은 공백이 있으므로 해당 출력물에 후처리를 해야 합니다.

## NotionDirectoryLoader

NotionDirectoryLoader을 사용하여 Notion에서 데이터를 로드합니다. Notion은 개인 및 회사 데이터를 저장하기 위한 인기 있는 툴입니다. Notion Space에서 페이지를 복제하고 페이지를 마크다운/CSV 파일로 내보낼 수 있습니다.

```js
from langchain.document_loaders import NotionDirectoryLoader
loader = NotionDirectoryLoader("docs/Notion_DB")
docs = loader.load()

print(docs[0].page_content[0:200])
print(docs[0].metadata)
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

지금까지 우리는 다양한 소스에서 데이터를로드하고 표준화 된 문서 인터페이스로 가져 오는 방법에 대해 다뤘습니다. 그러나 이러한 문서가 크다면, 작은 청크로 나누어야 할 수도 있습니다. 이것은 검색 확장 생성의 경우, 우리가 우리에게 가장 관련이있는 콘텐츠 조각들을 검색해야하기 때문에 중요합니다.

# 문서 분할

문서 분할은 문서를 작은 청크로 나누는 것이 필요합니다. 문서 분할은 데이터를 표준화 된 문서 형식으로로드 한 후에 일어나지만 벡터 저장소로 이동하기 전에 발생합니다.

![image](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_2.png)

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

문서를 작은 조각으로 나누는 것은 중요하며 각 조각 사이의 의미 있는 관계를 유지해야 하는 부분이 까다롭습니다. 예를 들어, Toyota Camry에 대한 2개의 조각이 있다면:

이 경우에 간단히 나누었더니 한 조각에 문장의 일부가, 다른 조각에는 다른 부분이 포함되어 있습니다. 따라서 Camry의 사양에 관한 질문에 대답할 수 없게 될 수 있습니다. 따라서 의미론적으로 관련된 조각으로 나누는 것이 중요합니다.

이제 아래와 같이 RecursiveCharacterTextSplitter와 CharacterTextSplitter를 초기화합니다:

```js
from langchain.text_splitter import RecursiveCharacterTextSplitter, CharacterTextSplitter

chunk_size =26
chunk_overlap = 4

r_splitter = RecursiveCharacterTextSplitter(
    chunk_size=chunk_size,
    chunk_overlap=chunk_overlap
)
c_splitter = CharacterTextSplitter(
    chunk_size=chunk_size,
    chunk_overlap=chunk_overlap
)
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

입력 텍스트는 지정된 청크 크기 및 정의된 청크 중첩에 따라 분할됩니다. 청크 크기는 청크의 크기를 측정하는 길이 함수입니다. 이는 주로 문자 또는 토큰입니다.

![이미지](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_3.png)

청크 중첩은 두 청크 사이에 작은 중첩이 있도록 사용되며, 이는 2개의 청크 사이에 어떤 일관성 개념을 가질 수 있도록 합니다. 페이지에 나와 있는 것처럼 Lang Chain에는 다양한 유형의 스플리터가 있습니다:

![이미지](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_4.png)

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

Lang Chain의 Text Splitters에는 문서 생성 및 문서 분할이라는 2가지 메소드가 있습니다. 둘 다 내부에서 동일한 논리를 가지고 있지만 하나는 텍스트 목록을, 다른 하나는 문서 목록을 입력으로 받습니다. 이러한 텍스트 분할기들은 청크를 나누는 방식(문자 또는 토큰으로)이나 청크의 길이를 측정하는 방식과 같은 다양한 차원에서 다를 수 있습니다. 때로는 문장의 끝을 결정하기 위해 다른 작은 모델을 사용하고 해당 정보를 사용하여 청크를 분할할 수도 있습니다. 메타데이터는 텍스트/문서를 청크로 분할할 때 중요합니다. 모든 청크를 통일된 메타데이터를 유지하면서 새로운 메타데이터 조각을 추가해야 할 수도 있습니다. 때로는 청크의 분할이 문서 유형에 특정할 수 있습니다. 코드에서 분할할 때 나타날 수 있습니다. Python, Ruby, C와 같은 서로 다른 언어에 대해 서로 다른 분리자를 사용하는 언어 텍스트 분할기를 사용합니다.

이제 LangChain의 몇 가지 텍스트 분할기 예제를 살펴보겠습니다.

```js
# Recursive text Splitter
text1 = 'abcdefghijklmnopqrstuvwxyz'
r_splitter.split_text(text1)
# Output - ['abcdefghijklmnopqrstuvwxyz']

# Character Text Splitter
text2 = 'abcdefghijklmnopqrstuvwxyzabcdefg'
r_splitter.split_text(text2)
# Output - ['abcdefghijklmnopqrstuvwxyz', 'wxyzabcdefg']

# Recursive text Splitter
text3 = "a b c d e f g h i j k l m n o p q r s t u v w x y z"
r_splitter.split_text(text3)
# output - ['a b c d e f g h i j k l m', 'l m n o p q r s t u v w x', 'w x y z']

# Character Text Splitter
c_splitter.split_text(text3)
# output - ['a b c d e f g h i j k l m n o p q r s t u v w x y z']

# Character Text Splitter with separator defined
c_splitter = CharacterTextSplitter(
    chunk_size=chunk_size,
    chunk_overlap=chunk_overlap,
    separator = ' '
)
c_splitter.split_text(text3)
# Output - ['a b c d e f g h i j k l m', 'l m n o p q r s t u v w x', 'w x y z']
```

## RecursiveSplitting

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

이제 실제 예시 몇 가지를 시도해 보겠습니다. 재귀 텍스트 분할기와 문자 텍스트 분할기가 어떻게 다르게 작동하는지 살펴볼 것입니다.

```js
some_text = """When writing documents, writers will use document structure to group content. \
This can convey to the reader, which idea's are related. For example, closely related ideas \
are in sentances. Similar ideas are in paragraphs. Paragraphs form a document. \n\n  \
Paragraphs are often delimited with a carriage return or two carriage returns. \
Carriage returns are the "backslash n" you see embedded in this string. \
Sentences have a period at the end, but also, have a space.\
and words are separated by space."""

len(some_text) -> 496

c_splitter = CharacterTextSplitter(
    chunk_size=450,
    chunk_overlap=0,
    separator = ' '
)
r_splitter = RecursiveCharacterTextSplitter(
    chunk_size=450,
    chunk_overlap=0,
    separators=["\n\n", "\n", " ", ""]
)
```

여기서 CharacterTextSplitter는 공백을 구분자로 사용하며, RecursiveCharacterTextSplitter의 경우 구분자 목록을 전달합니다.

첫 번째 경우에, 다음 출력이 나옵니다:

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

```json
[
  "문서를 작성할 때 작성자는 문서 구조를 사용하여 콘텐츠를 그룹화합니다. 이는 독자에게 관련된 아이디어를 전달할 수 있습니다. 예를 들어, 밀접하게 관련된 아이디어는 문장에 있습니다. 비슷한 아이디어들은 단락에 있습니다. 단락은 문서를 형성합니다. \n\n 단락은 종종 개행 문자 또는 두 개의 개행 문자로 구분됩니다. 개행 문자는 이 문자열에 포함된 \"백슬래시 n\"입니다. 문장은 끝에 마침표가 있지만 또한 띄어쓰기를 하고 단어들은 띄어쓰기로 구분됩니다."
]
```

두 번째 경우에 대한 출력은 다음과 같습니다:

```json
[
  "문서를 작성할 때 작성자는 문서 구조를 사용하여 콘텐츠를 그룹화합니다. 이는 독자에게 관련된 아이디어를 전달할 수 있습니다. 예를 들어, 밀접하게 관련된 아이디어는 문장에 있습니다. 비슷한 아이디어들은 단락에 있습니다. 단락은 문서를 형성합니다.",
  "단락은 종종 개행 문자 또는 두 개의 개행 문자로 구분됩니다. 개행 문자는 이 문자열에 포함된 \"백슬래시 n\"입니다. 문장은 끝에 마침표가 있지만 띄어쓰기를 하고 단어들은 띄어쓰기로 구분됩니다."
]
```

RecursiveCharacterTextSplitter의 경우, 구분자 목록으로는 두 번의 개행, 한 개의 개행, 공백, 빈 문자열이 있습니다. 따라서 이는 텍스트를 두 번의 개행으로 분할하고, 그 다음으로 한 개의 개행 뒤에 공백이 따라오는 경우와 마지막으로 문자별로 분할합니다. RecursiveTextSplitter는 이중 개행을 기준으로 텍스트를 분할하므로 텍스트가 두 개의 단락으로 분할됩니다. 첫 번째 단락이 450자보다 짧은 것을 확인할 수 있으며, 이는 두 번의 개행을 기준으로 분할하는 것이 더 나은 분할인 것으로 생각됩니다. 문자 텍스트 분할은 띄어쓰기에 따라 분할되므로, 문장 중간에 이상한 구분이 발생하는 것을 확인할 수 있습니다.

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

자 이제 PDF 파일을 사용한 TextSplitter의 실제 예제를 하나 더 실행해 보겠습니다.

```js
from langchain.document_loaders import PyPDFLoader
loader = PyPDFLoader("docs/cs229_lectures/MachineLearning-Lecture01.pdf")
pages = loader.load()

from langchain.text_splitter import CharacterTextSplitter
text_splitter = CharacterTextSplitter(
    separator="\n",
    chunk_size=1000,
    chunk_overlap=150,
    length_function=len
)

docs = text_splitter.split_documents(pages)

len(docs) -> 77
len(pages) -> 22
```

여기서는 Python의 기본 길이 함수를 전달한 것입니다.

## 토큰 분할

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

지금까지 문자 기반으로 텍스트를 분할해 왔습니다. 이제 토큰 수를 기준으로도 분할할 수 있습니다. 이는 LLMs에서 종종 토큰으로 지정된 컨텍스트 창을 가지고 있기 때문에 유용할 수 있습니다. 토큰은 일반적으로 ~4개의 문자로 이루어져 있습니다.

```js
from langchain.text_splitter import TokenTextSplitter

text_splitter = TokenTextSplitter(chunk_size=1, chunk_overlap=0)

text1 = "foo bar bazzyfoo"
text_splitter.split_text(text1)
# ['foo', ' bar', ' b', 'az', 'zy', 'foo']
```

```js
text_splitter = TokenTextSplitter(chunk_size=10, chunk_overlap=0)
docs = text_splitter.split_documents(pages)

docs[0]
# Document(page_content='MachineLearning-Lecture01  \n', metadata={'source': 'docs/cs229_lectures/MachineLearning-Lecture01.pdf', 'page': 0})

pages[0].metadata
# {'source': 'docs/cs229_lectures/MachineLearning-Lecture01.pdf', 'page': 0}
```

## 컨텍스트에 따라 분할하기

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

체킹의 목적은 공통 컨텍스트를 가진 텍스트를 함께 묶는 것입니다. 텍스트 분할은 종종 문장이나 다른 구분자를 사용하여 관련된 텍스트를 함께 유지하지만 많은 문서(예: Markdown)는 구조(헤더)가 있으므로 분할에 명시적으로 사용할 수 있습니다.

이를 위해 헤더 텍스트 스플리터를 사용하여 헤더 메타데이터를 유지하는 목적으로 마크다운 파일을 분할할 수 있습니다. 헤더나 하위 헤더를 기반으로 마크다운 파일을 나누고 해당 헤더를 메타데이터 필드에 내용으로 추가하여 해당 분할에서 파생된 모든 청크로 전달됩니다.

Markdown 형식의 표를 다음과 같이 변경해보겠습니다:

```js
from langchain.document_loaders import NotionDirectoryLoader
from langchain.text_splitter import MarkdownHeaderTextSplitter
```

제목이 있는 문서와 그 후 부제목 (장 1)과 여러 문장이 있는 섹션이 있습니다. 그런 다음 다른 섹션에 부제목 (장 2)과 그곳에 몇 개의 문장이 있습니다.

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
markdown_document = """# Title\n\n \
## Chapter 1\n\n \
안녕하세요, 제임스입니다\n\n \
안녕하세요, 조입니다\n\n \
### 섹션\n\n \
안녕하세요, 랜스입니다\n\n \
## Chapter 2\n\n \
안녕하세요, 말리입니다"""


headers_to_split_on = [
    ("#", "Header 1"),
    ("##", "Header 2"),
    ("###", "Header 3"),
]
```

이제 MarkdownHeaderTextSplitter를 정의합니다.

```js
markdown_splitter = MarkdownHeaderTextSplitter(
  (headers_to_split_on = headers_to_split_on)
);
md_header_splits = markdown_splitter.split_text(markdown_document);
```

마지막으로, 텍스트 분할 결과를 얻습니다:

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
md_header_splits[0]
# 문서(page_content='안녕하세요, 제 이름은 짐입니다  \n안녕하세요, 제 이름은 조입니다', metadata={'Header 1': '제목', 'Header 2': '장 1'})

md_header_splits[1]
# 문서(page_content='안녕하세요, 제 이름은 랜스입니다', metadata={'Header 1': '제목', 'Header 2': '장 1', 'Header 3': '섹션'})
```

의미론적으로 관련있는 청크를 적절한 메타데이터와 함께 얻을 수 있었습니다. 이제 이 데이터 청크를 벡터 저장소로 이동할 것입니다.

# 벡터 저장소와 임베딩

우리는 문서를 작은 청크로 나누었고, 이제 이러한 청크를 색인에 넣어두어 이 문서에 대한 질문에 답변할 때 쉽게 검색할 수 있도록 합니다. 이를 위해 임베딩과 벡터 저장소를 사용합니다.

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

![이미지](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_5.png)

텍스트 분할 이후에는 벡터 저장소와 임베딩이 필요합니다. 문서를 쉽게 접근할 수 있는 형식으로 저장해야 합니다. 임베딩은 텍스트를 가져와 텍스트의 수치적 표현을 만듭니다. 즉, 의미론적으로 유사한 내용을 가진 텍스트는 임베딩 공간에서 유사한 벡터를 가집니다. 따라서 임베딩(벡터)을 비교하고 유사한 텍스트를 찾을 수 있습니다.

전체 파이프라인은 문서로 시작합니다. 이러한 문서를 작은 덩어리로 분할하고 이러한 분할 또는 문서의 임베딩을 만듭니다. 마지막으로, 모든 이러한 임베딩을 벡터 저장소에 저장합니다.

![이미지](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_6.png)

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

벡터 저장소는 나중에 비슷한 벡터를 쉽게 찾을 수 있는 데이터베이스입니다. 질문과 관련 있는 문서를 찾을 때 유용합니다.

![이미지](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_7.png)

따라서 질문에 대한 답변을 얻고 싶을 때, 우리는 질문의 임베딩을 만들고 이를 벡터 저장소 내 모든 다른 벡터들과 비교하여 가장 유사한 n개를 선택합니다. 마지막으로, n개의 가장 유사한 청크를 질문과 함께 LLM에 전달하고 답변을 얻습니다.

이제 우리는 어떻게 문서 세트를 벡터 저장소에 로드하는지 알아봅시다.

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
from langchain.document_loaders import PyPDFLoader

# PDF 파일 로드하기
loaders = [
    # 일부러 문서를 중복하여 넣어 지저분한 데이터를 만듭니다.
    PyPDFLoader("docs/cs229_lectures/MachineLearning-Lecture01.pdf"),
    PyPDFLoader("docs/cs229_lectures/MachineLearning-Lecture01.pdf"),
    PyPDFLoader("docs/cs229_lectures/MachineLearning-Lecture02.pdf"),
    PyPDFLoader("docs/cs229_lectures/MachineLearning-Lecture03.pdf")
]
docs = []
for loader in loaders:
    docs.extend(loader.load())
```

문서가 로드된 후 RecursiveCharacterTextSplitter를 사용하여 청크를 생성합니다.

```js
# 텍스트 스플리터 정의
from langchain.text_splitter import RecursiveCharacterTextSplitter
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1500,
    chunk_overlap=150
)

# 텍스트 스플리터를 사용해 문서를 나눕니다.
splits = text_splitter.split_documents(docs)
```

이제 PDF의 모든 청크에 대한 임베딩을 생성한 다음 벡터 저장소에 저장할 것입니다. 우리는 OpenAI를 사용하여 이러한 임베딩을 만들 것입니다. 우리는 Chroma를 우리의 경우에 벡터 저장소로 사용할 것입니다. Chroma는 가벼우며 메모리에 저장되어 쉽게 시작할 수 있습니다.

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
from langchain.vectorstores import Chroma
from langchain.embeddings.openai import OpenAIEmbeddings

embedding = OpenAIEmbeddings()
```

저희는 이 벡터 저장소를 지속적인 디렉토리에 저장하여 나중에 사용할 수 있도록 합니다.

```js
persist_directory = 'docs/chroma/'

# 벡터 저장소 생성
vectordb = Chroma.from_documents(
    documents=splits,
    embedding=embedding,
    persist_directory=persist_directory
)

print(vectordb._collection.count())
```

저희는 이전에 생성된 splits, embedding (OpenAI 임베딩 모델), 그리고 지속 디렉토리를 전달하여 벡터 저장소를 생성합니다.

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

## 유사성 검색

유사성 검색 방법을 사용하여 k를 전달하고 반환할 문서의 수를 지정하는 질문을 하겠습니다.

```js
question = "도움을 요청할 수 있는 이메일이 있나요"

docs = vectordb.similarity_search(question,k=3)

# 문서의 길이 확인
len(docs)

# 첫 번째 문서의 내용 확인
docs[0].page_content

# 나중에 사용하기 위해 데이터베이스 유지
vectordb.persist()
```

## 유사성 검색: 극단적인 경우

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

기본적인 유사성 검색은 대부분의 결과를 올바르게 가져옵니다. 그러나 유사성 검색이 실패하는 특이한 경우도 있습니다. 이제 다른 쿼리를 수행하여 중복 결과를 확인할 것입니다.

```js
question = "matlab에 대해 어떻게 말했나요?"

# k = 5로 유사성 검색
docs = vectordb.similarity_search(question,k=5)

# 처음 두 결과 확인
print(docs[0])
print(docs[1])
```

여기서 처음 두 결과는 중복 PDF(duplicate MachineLearning-lecture01.pdf)를 로드했기 때문에 동일합니다. 따라서 중복 청크를 받아서 언어 모델로 넘겼습니다. 코드를 실행하면 의미론적 검색이 모든 비슷한 문서를 가져오지만 다양성은 보장하지 않는다는 결론을 내릴 수 있습니다. 다음 섹션에서 어떻게 관련성이 있으면서도 다른 청크를 동시에 가져오는 방법을 다루겠습니다.

다른 쿼리로 유사성 검색에서 다른 실패 사례가 있을 수 있습니다.

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
question = "세 번째 강의에서 회귀에 대해 무슨 이야기를 했나요?"

docs = vectordb.similarity_search(question,k=5)


# 유사성 검색 결과의 메타데이터를 출력합니다
for doc in docs:
    print(doc.metadata)

print(docs[4].page_content)
```

우리는 검색 결과의 메타데이터, 즉 이 결과가 어떤 강의에서 나왔는지 확인했습니다. 우리는 결과가 세 번째 강의, 두 번째 강의 및 첫 번째 강의에서 나온 것을 볼 수 있습니다. 이 실패의 이유는 우리가 제 3 강의에서만 문서를 원하는 사실이 구조화된 정보 조각이지만, 우리는 임베딩을 기반으로 의미적 조회만을 수행하고 있으며 임베딩은 아마도 '회귀'라는 단어에 더 초점을 맞추고 세 번째 강의에 대한 정보를 캡처하지 못합니다. 따라서 우리는 회귀와 관련이 있는 모든 결과를 받고 있습니다. 이를 확인하기 위해 다섯 번째 문서를 출력하여 실제로 회귀라는 단어가 언급되었는지 확인할 수 있습니다.

# 검색

검색은 검색 보강 생성(RAG) 플로우의 핵심입니다. 문서에서 질문에 대답을 시도할 때 가장 큰 어려움 중 하나는 검색입니다. 질문에 대한 답변이 실패하는 대부분의 경우, 그 원인은 검색에서 실수하는 것입니다. 또한 LangChain에서 자체 쿼리 및 맥락 압축과 같은 고급 검색 메커니즘에 대해 논의할 것입니다. 검색은 쿼리가 입력되고 가장 관련성이 높은 분할을 검색하고자 할 때 쿼리 시간에 중요합니다.

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

![img](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_8.png)

의미 검색이 많은 사용 사례에 대해 꽤 잘 작동했음을 확인했습니다. 그러나 일부 극단적인 상황에서 실패했습니다. 따라서 우리는 검색을 깊이 파고들어 이러한 극단적인 상황을 극복하기 위한 몇 가지 다른 고급 방법을 논의할 것입니다.

- 벡터 스토어에서 데이터 액세스/색인화
- 기본 의미 유사성
- 최대 주변 유사성
- 메타데이터 포함

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

## 2. LLM 지원 검색

## 3. 문맥적 압축

## 1. 최대 주변 유사성(MMR)

MMR은 검색 결과에서 다양성을 강화하는 중요한 방법입니다. 의미적 검색의 경우, 임베딩 공간에서 쿼리와 가장 유사한 문서를 얻게 되며, 우리는 다양한 정보를 놓칠 수 있습니다. 예를 들어, 쿼리가 "큰 열매체를 가진 모든 흰색 버섯에 대해 말해주세요"인 경우, 첫 두 번째로 유사한 결과를 얻어 쿼리와 관련된 정보인 과일체와 모두 흰색에 대한 정보를 얻을 것입니다. 그러나 첫 두 문서와 유사하지 않지만 중요한 정보를 놓칠 수 있습니다. 여기서 MMR은 다양한 문서를 선택하는데 도움을 주어 이 문제를 해결하는 데 도움이 됩니다.

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

![Image](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_9.png)

MMR의 아이디어는 먼저 벡터 저장소를 쿼리하고 "fetch_k" 가장 유사한 응답을 선택하는 것입니다. 이제 "fetch_k" 문서의 작은 집합에서 작업을 수행하여 쿼리에 대한 관련성과 결과물 간의 다양성을 동시에 달성합니다. 마지막으로, 이러한 "fetch_k" 응답 중에서 "k"개의 가장 다양한 응답을 선택합니다. 처음 2개의 문서의 처음 100자를 출력하면, 위와 같은 유사도 검색을 사용할 경우 동일한 결과를 얻는 것을 발견할 것입니다. 이제 MMR로 검색 쿼리를 실행하고 처음 몇 가지 결과를 확인해 보겠습니다.

```js
texts = [
    """The Amanita phalloides has a large and imposing epigeous (aboveground) fruiting body (basidiocarp).""",
    """A mushroom with a large fruiting body is the Amanita phalloides. Some varieties are all-white.""",
    """A. phalloides, a.k.a Death Cap, is one of the most poisonous of all known mushrooms.""",
]

smalldb = Chroma.from_texts(texts, embedding=embedding)
question = "Tell me about all-white mushrooms with large fruiting bodies"
smalldb.max_marginal_relevance_search(question, k=2, fetch_k=3)
```

여기서 우리는 MMR 검색을 사용하여 결과를 다양하게 만들 수 있었습니다. 이제 유사도 검색과 최대 여유성 검색 결과를 비교해 보겠습니다.

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

# 유사도 검색과 MMR 검색 결과를 비교하세요.

```bash
question = "matlab에 대해 말한 내용은 무엇인가요?"
docs_ss = vectordb.similarity_search(question, k=3)
docs_ss[0].page_content[:100]
docs_ss[1].page_content[:100]

docs_mmr = vectordb.max_marginal_relevance_search(question, k=3)
docs_mmr[0].page_content[:100]
docs_mmr[1].page_content[:100>
```

유사도 검색에서 처음 2개 문서의 처음 100자가 동일한 것을 확인할 수 있지만, MMR 검색으로는 처음 2개 문서의 처음 100자가 다른 것을 확인할 수 있습니다. 따라서 쿼리 결과에서 다양성을 얻을 수 있습니다.

## 2. 메타데이터

메타데이터는 검색의 특이성을 조정하는 데 사용됩니다. 이전에 "세 번째 강의에서 회귀에 대해 어떤 내용을 이야기했습니까?"라는 질문에 대한 답변이 세 번째 강의뿐만 아니라 첫 번째와 두 번째 강의에서도 반환된 것을 발견했습니다.

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

위 문제를 해결하기 위해 메타데이터 필터를 지정할 것입니다. 많은 벡터 저장소는 메타데이터에 대한 작업을 지원합니다. 따라서 소스가 세 번째 강의 PDF와 같아야 한다는 정보를 전달할 것입니다. 여기서 메타데이터는 각 포함된 청크에 대한 맥락을 제공합니다.

```js
question = "세 번째 강의에서 회귀에 대해 얘기한 내용은 무엇인가요?"

docs = vectordb.similarity_search(
    question,
    k=3,
    filter={"source":"docs/cs229_lectures/MachineLearning-Lecture03.pdf"}
)

# 검색된 문서의 메타데이터 출력
for d in docs:
    print(d.metadata)
```

이제 검색된 문서의 메타데이터를 살펴보면 모든 문서가 세 번째 강의에서 검색되었음을 확인할 수 있습니다.

## 3. 직접 쿼리

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

셀프 쿼리는 쿼리 자체에서 메타데이터를 추론하려는 경우 중요한 도구입니다. 우리는 SelfQueryRetriever를 사용할 수 있습니다. 이는 LLM을 사용하여 다음을 추출합니다.

- 벡터 검색에 사용할 쿼리 문자열
- 전달할 메타데이터 필터

여기서는 메타데이터를 기준으로 결과를 필터링하기 위해 언어 모델을 사용합니다. 하지만, 이전에 수동으로 필터를 지정할 필요는 없고 대신 메타데이터와 함께 셀프 쿼리 리트리버를 사용할 수 있습니다.

```js
from langchain.llms import OpenAI
from langchain.retrievers.self_query.base import SelfQueryRetriever
from langchain.chains.query_constructor.base import AttributeInfo
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

이 방법은 우리가 의미론적으로 찾으려는 콘텐츠 이외에도 적용할 메타데이터도 포함하는 쿼리를 가지고 있을 때 사용됩니다.

메타데이터에는 source와 page 두 필드가 있습니다. 이러한 속성 각각에 대해 이름과 유형에 대한 설명을 제공해야 합니다. 이 정보는 언어 모델에서 사용되므로 이 설명을 최대한 상세하게 만들어야 합니다.

```js
metadata_field_info = [
  AttributeInfo(
    (name = "source"),
    (description =
      "The lecture the chunk is from, should be one of `docs/cs229_lectures/MachineLearning-Lecture01.pdf`, `docs/cs229_lectures/MachineLearning-Lecture02.pdf`, or `docs/cs229_lectures/MachineLearning-Lecture03.pdf`"),
    (type = "string")
  ),
  AttributeInfo(
    (name = "page"),
    (description = "The page from the lecture"),
    (type = "integer")
  ),
];
```

또한 문서 저장소에 실제로 들어 있는 정보에 대해 구체적으로 명시해야 합니다. 여기서 LLM은 메타데이터 필터와 함께 전달해야 하는 쿼리를 추론합니다.

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
document_content_description = "강의 노트";
llm = OpenAI((temperature = 0));
retriever = SelfQueryRetriever.from_llm(
  llm,
  vectordb,
  document_content_description,
  metadata_field_info,
  (verbose = True)
);
```

이제 다음 질문으로 검색기를 실행합니다.

```js
question = "세 번째 강의에서 회귀에 대해 언급한 내용은 무엇인가요?";
docs = retriever.get_relevant_documents(question);
```

예를 들어, "1980년에 만들어진 외계인 영화는 어떤 것들이 있나요?"라는 쿼리를 가질 수 있습니다. 이 쿼리는 2가지 구성 요소를 가지고 있으며, 원본 질문을 메타데이터 필터와 검색 용어로 나누기 위해 언어 모델을 사용할 수 있습니다.

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

![Using langchain for Question Answering on Own Data](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_10.png)

예를 들어, 이 경우에는 영화 데이터베이스에서 외계인을 검색하고 각 영화의 메타데이터를 1980년도로하는 형태로 필터링합니다. 대부분의 벡터 스토어는 메타데이터 필터를 지원하기 때문에 새로운 데이터베이스나 인덱스가 필요하지 않습니다. 대부분의 벡터 저장소가 메타데이터 필터를 지원하므로, 예를 들어 영화의 년도가 1980년인 경우와 같이 메타데이터를 기반으로 레코드를 쉽게 필터링할 수 있습니다.

## 4. 컨텍스트 압축

압축은 검색된 문서의 품질을 향상시키는 또 다른 방법입니다. 전체 문서를 응용 프로그램을 통해 전달하면 더 많은 비용이 들고 더 나쁜 응답이 될 수 있으므로 검색된 단락의 가장 관련성이 높은 부분만 추출하는 것이 유용합니다.

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

```python
def pretty_print_docs(docs):
    print(f"\n{'-' * 100}\n".join([f"Document {i+1}:\n\n" + d.page_content for i, d in enumerate(docs)]))



from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor

# Wrap our vectorstore
llm = OpenAI(temperature=0)
compressor = LLMChainExtractor.from_llm(llm)

compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=vectordb.as_retriever()
)

question = "what did they say about matlab?"
compressed_docs = compression_retriever.get_relevant_documents(question)
pretty_print_docs(compressed_docs)


Through compression, all documents are processed using a language model to extract the most relevant segments, which are then passed into a final language model call.
```

![Using langchain for Question Answering on Own Data](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_11.png)

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

이는 랭귀지 모델을 더 많이 호출할 수 있게 되는 대신, 최종 답변을 가장 중요한 내용에만 집중할 수 있도록 도와줍니다. 그래서 이것은 조금은 교환해야 할 부분이 있습니다.

# 질문응답

우리는 검색에서 방금 검색한 문서를 사용하여 질문응답을 하는 방법을 논의했습니다. 이제 이러한 문서와 원래의 질문을 가져와서 랭귀지 모델로 전달하여 모델에게 질문에 답변을 하도록 요청합니다.

![이미지](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_12.png)

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

## 검색 QA 체인

먼저 벡터 저장소에서 여러 관련 분할이 검색된 후 질문 응답을 어떻게 수행하는지 살펴봅니다. 또한 관련 분할을 LLM 컨텍스트에 맞게 압축해야 할 수도 있습니다. 마지막으로 이러한 분할을 시스템 프롬프트와 인간 질문과 함께 언어 모델로 전송하여 답변을 가져옵니다.

![image](/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_13.png)

기본적으로 우리는 모든 청크를 동일한 컨텍스트 창에, 동일한 언어 모델 호출에 넣습니다. 그러나 문서 수가 많을 경우 모두를 동일한 컨텍스트 창에 넣을 수 없을 때 다른 방법도 사용할 수 있습니다. MapReduce, Refine 및 MapRerank는 문서 수가 많을 경우 사용할 수 있는 세 가지 방법입니다. 이제 우리는 이러한 방법을 자세히 살펴보겠습니다.

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

이전에 유지했던 벡터 데이터베이스를 먼저 로드할 거에요.

```js
# 이전에 유지했던 벡터 데이터베이스를 로드하고 컬렉션 수를 확인해요
from langchain.vectorstores import Chroma
from langchain.embeddings.openai import OpenAIEmbeddings
persist_directory = 'docs/chroma/'
embedding = OpenAIEmbeddings()
vectordb = Chroma(persist_directory=persist_directory, embedding_function=embedding)
print(vectordb._collection.count())
```

데이터베이스가 제대로 작동하는지 확인하기 위해 유사성 검색을 먼저 수행할 거에요.

```js
question = "이 수업의 주요 주제는 무엇인가요?";
docs = vectordb.similarity_search(question, (k = 3));
len(docs);
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

이제 우리는 이 질문에 대한 답변을 얻기 위해 RetrievalQA 체인을 사용할 것입니다. 첫 번째로, 언어 모델 (ChatOpenAI 모델)을 초기화합니다. 온도를 영으로 설정합니다. 온도를 영으로 설정하면 모델이 낮은 변이성, 최고의 충실도 및 신뢰할 수 있는 답변을 얻기에 좋습니다.

```js
from langchain.chat_models import ChatOpenAI
llm = ChatOpenAI(model_name=llm_name, temperature=0)
```

또한 리트리벌QA 체인이 필요합니다. 이것은 리트리버를 사용하여 지원되는 질문 답변을 수행하는 과정입니다. 언어 모델과 벡터 데이터베이스를 리트리버로 전달하여 생성됩니다.

```js
from langchain.chains import RetrievalQA

qa_chain = RetrievalQA.from_chain_type(
    llm,
    retriever=vectordb.as_retriever()
)
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

지금, 우리는 묻고 싶은 질문을 가지고 qa_chain을 호출합니다.

```js
# 질문을 qa_chain에 전달
question = "이 수업의 주요 주제는 무엇인가요?"
result = qa_chain({"query": question})
result["result"]
```

## Prompt로 RetrievalQA 체인 사용하기

이제 좀 더 자세히 이 프로세스를 이해해 봅시다. 먼저 prompt 템플릿을 정의합니다. prompt 템플릿에는 컨텍스트를 사용하는 방법에 대한 지침이 포함되어 있습니다. 또한 컨텍스트 변수를 위한 자리 표시자도 있습니다. 우리는 질문에 대한 답변을 얻기 위해 prompts를 사용할 것입니다. 여기서 prompt는 문서와 질문을 받아서 언어 모델에 전달합니다.

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
from langchain.prompts import PromptTemplate

# Prompt 템플릿 생성
template = """아래의 문맥을 사용하여 마지막 질문에 대답하세요. 답을 모른다면 그냥 모르는 것으로 말하고 대답을 꾸미지 마세요. 세 문장 이내로 답하세요. 답변은 최대한 간결하게 유지하세요. 답변 끝에 "질문해 줘서 고마워!"라고 항상 말씀해 주세요.
{context}
질문: {question}
도움이 되는 답변:"""
QA_CHAIN_PROMPT = PromptTemplate.from_template(template)

# 체인 실행
qa_chain = RetrievalQA.from_chain_type(
    llm,
    retriever=vectordb.as_retriever(),
    return_source_documents=True,
    chain_type_kwargs={"prompt": QA_CHAIN_PROMPT}
)
```

언어 모델과 벡터 데이터베이스를 사용하여 새로운 검색 QA 체인을 생성했어요.

```js
# 체인 초기화
# source 문서를 받으려면 return_source_documents를 True로 설정하세요
# chain_type을 prompt template으로 정의하세요
qa_chain = RetrievalQA.from_chain_type(
    llm,
    retriever=vectordb.as_retriever(),
    return_source_documents=True,
    chain_type_kwargs={"prompt": QA_CHAIN_PROMPT}
)
```

이번에는 새로운 질문을 시도해보고 결과를 확인해 볼 거예요.

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
질문 = "확률은 수업 주제인가요?"
결과 = qa_chain({"query": 질문})
# 쿼리 결과 확인
결과["result"]
# 참고 문서 확인
결과["source_documents"][0]
```

지금까지 기본적으로 "stuff" 메서드를 사용했습니다. 이 방법은 모든 문서를 최종 프롬프트에 넣습니다. 이는 언어 모델에 한 번의 호출만을 의미합니다. 그러나 문서가 너무 많은 경우, 문서가 컨텍스트 창 안에 맞지 않을 수 있습니다. 이러한 경우, 맵-리듀스, 정제, 그리고 map_rerank와 같은 다른 기술을 사용할 수 있습니다.

<img src="/assets/img/2024-05-18-UsinglangchainforQuestionAnsweringonOwnData_14.png" />

이 기술에서 각 개별 문서는 먼저 언어 모델로 전송되어 원본 답변을 얻은 후, 이러한 답변이 최종 답변으로 구성되며 최종적으로 언어 모델에 대한 호출이 이루어집니다. 이는 더 많은 언어 모델 호출을 필요로 하지만, 임의의 많은 문서에 대해 작동할 수 있는 장점이 있습니다.

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

이 방법의 두 가지 제한 사항이 있습니다. 첫째, 이전 방법보다 속도가 느리고 둘째, 결과물이 이전 결과물보다 나쁠 수 있습니다. 이는 두 개의 문서에 정보가 분산되어 있을 때 동일한 맥락에서 정보가 나타나지 않을 수 있기 때문입니다.

```js
qa_chain_mr = RetrievalQA.from_chain_type(
  llm,
  (retriever = vectordb.as_retriever()),
  (chain_type = "map_reduce")
);
result = qa_chain_mr({ query: question });
result["result"];
```

RetrievalQA 체인이 하부에서 MapReduceDocumentsChain을 호출할 때 발생합니다. 이는 각 문서에 대해 언어 모델(ChatOpenAI의 경우)에 대해 네 번의 별도 호출을 수행합니다. 이러한 호출의 결과는 최종 체인(StuffedDocumentsChain)에 결합되며, 이는 이러한 응답을 모두 최종 호출에 삽입합니다. StuffedDocumentsChain은 시스템 메시지, 이전 문서의 네 가지 요약 및 사용자 질문을 사용하여 답변을 얻습니다.

```js
qa_chain_mr = RetrievalQA.from_chain_type(
  llm,
  (retriever = vectordb.as_retriever()),
  (chain_type = "refine")
);
result = qa_chain_mr({ query: question });
result["result"];
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

만약 우리가 검색을 위해 "refine"을 체인 유형으로 사용한다면, RetrievalQA 체인은 RefineDocumentsChain을 호출하며, 이는 LLM 체인에 대한 네 번의 연속적인 호출을 수반합니다. 이 네 번의 호출 각각은 언어 모델로 전송되기 전에 프롬프트를 포함합니다. 이 네 번의 호출에는 이전에 정의된 프롬프트 템플릿에 따른 시스템 메시지가 포함됩니다. 시스템 메시지에는 문맥 정보, 검색한 문서 중 하나, 사용자 질문 및 답변이 포함됩니다. 우리는 다음 언어 모델을 호출합니다. 다음 언어 모델에 보내는 최종 프롬프트는 이전 응답과 새 데이터를 결합하고 추가된 문맥을 포함하여 향상된/정제된 응답을 요청하는 시퀀스입니다. 이 작업은 이전 응답을 새 데이터와 결합하여 정보를 순차적으로 결합함으로써 정보의 지속 전달을 더 많이 유도하는 정제 체인에서 더 나은 답변을 얻을 수 있습니다. 이 과정은 네 번 실행되며 최종 답변에 도달하기 전 모든 문서를 대상으로 실행됩니다. Refine 체인에서는 정보를 순차적으로 결합하여 정보의 지속적 전달이 더 많아져 MapReduce 체인보다 더 나은 답변을 얻을 수 있습니다.

## RetrievalQA 한계

RetrievalQA 체인의 가장 큰 단점 중 하나는 QA 체인이 대화 내력을 보존하지 못하는 것입니다. 이를 확인할 수 있습니다:

```js
# QA 체인 생성
qa_chain = RetrievalQA.from_chain_type(
    llm,
    retriever=vectordb.as_retriever()
)
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

이제 체인에 질문하겠습니다.

```js
question = "확률은 수업 주제입니까?";
result = qa_chain({ query: question });
result["result"];
```

이제 두 번째 질문을 체인에 하겠습니다.

```js
question = "왜 해당 선수과목이 필요한가요?";
result = qa_chain({ query: question });
result["result"];
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

이전 답변과 관련이 없는 체인으로부터 답변을 받을 수 있었습니다. 기본적으로 RetrievalQA 체인은 상태 개념을 가지고 있지 않습니다. 이전 질문이나 이전 답변이 무엇이었는지 기억하지 않습니다. 이전 질문이나 이전 답변을 기억하게 하려면 메모리 개념을 도입해야 합니다. 이전 질문이나 이전 답변을 기억하는 능력은 챗봇의 경우 필요합니다. 챗봇에 후속 질문을 할 수 있거나 이전 답변에 대해 설명을 요청할 수 있기 때문입니다.

LangChain을 사용하여 다양한 문서에서 데이터를로드하는 방법에 대해 토론했습니다. 또한 문서를 청크로 분할하는 방법을 배웠습니다. 그 후에 이러한 청크에 대한 임베딩을 생성하고 벡터 저장소에 저장했습니다. 이 벡터 저장소를 사용하여 의미 검색을 수행했습니다. 의미 검색은 특정 엣지 케이스에서 실패합니다. 그런 다음, 엣지 케이스를 극복하기 위한 다양한 검색 알고리즘을 논의하는 검색을 다루었습니다. 우리는 검색을 LLM과 결합하여 질문 응답에서 사용했는데, 여기서 우리는 검색된 문서와 사용자 질문을 가져와 LLM에 전달하여 우리가 한 질문에 대한 답변을 생성했습니다. 질문 응답의 대화식 측면에 대해서는 논의하지 않았으며, 나중에 데이터 위에 종단간 챗봇을 생성함으로써 그에 대해 나중에 논의할 것입니다.
