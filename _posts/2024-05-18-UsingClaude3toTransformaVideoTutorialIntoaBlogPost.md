---
title: "클로드 3를 사용하여 비디오 튜토리얼을 블로그 포스트로 변환하기"
description: ""
coverImage: "/assets/img/2024-05-18-UsingClaude3toTransformaVideoTutorialIntoaBlogPost_0.png"
date: 2024-05-18 20:37
ogImage: 
  url: /assets/img/2024-05-18-UsingClaude3toTransformaVideoTutorialIntoaBlogPost_0.png
tag: Tech
originalTitle: "Using Claude 3 to Transform a Video Tutorial Into a Blog Post"
link: "https://medium.com/towards-artificial-intelligence/using-claude-3-to-transform-a-video-tutorial-in-a-blog-post-d2c1e04e7a7b"
---


## Anthropic이 Karpathy의 비디오 요약 도전에 대한 해결책 재현

이 문서를 작성하는데 출발점은 Andrej Karpathy가 LLM 토큰화에 관한 2시간 13분 비디오 강의를 게시한 직후에 X에서 게시한 글입니다. 이 강의를 책 장/chapter 또는 블로그 글로 자동으로 변환하는 작업에 대한 해결책이 필요한 도전을 받았습니다.

![Image](/assets/img/2024-05-18-UsingClaude3toTransformaVideoTutorialIntoaBlogPost_0.png)

그 후에 Anthropic의 Emmanuel Ameisen과 동료들이 특히 Anthropic의 최신 모델인 Claude 3을 통해 이 작업을 수행할 것을 제안하는 것으로 보이는 해결책이 게시되었습니다.

<div class="content-ad"></div>


![image](/assets/img/2024-05-18-UsingClaude3toTransformaVideoTutorialIntoaBlogPost_1.png)

사소한 문제와 일관성 부족이 있었지만, 이 방법은 꽤 효율적인 것으로 보였습니다. 결과적으로 생긴 블로그 포스트는 원본 비디오에서 다룬 대부분의 요소와 관련 스크린샷 및 코드 예제를 포함했습니다.

이 작업을 재현하는 데 얼마나 쉽고 비싼지 궁금해졌습니다. 결과적으로, 처음에 기대했던 것보다 더 복잡한 과정이었습니다. 프롬프트는 공유되었으나 코드는 그렇지 않았습니다.

본 문서는 내 구현 방식을 공유하고, 각 단계를 자세히 설명하며, 주요 어려움에 대해 논의합니다. 코드 및 데이터는 이 Github 저장소에서 확인할 수 있습니다.


<div class="content-ad"></div>

요약:

- 비디오를 블로그 포스트/책 챕터로 변환하는 것은 대형 멀티모달 모델(LMMs)의 또 다른 매력적인 활용 사례이며, 비디오 콘텐츠를 텍스트 형식으로 제공하여 읽기 쉽고 빠르게 살펴보고 검색할 수 있게 만듭니다.
- 그러나 LMMs를 기반으로 한 텍스트 변환은 다양한 부정확성과 불일치가 포함될 수 있어 철저한 검토와 교정이 필요합니다. 다른 어려움은 결과의 재현 불가능성과 효과적인 프롬프트 식별에 관려된 것입니다.
- Claude 3 Opus와 같은 LMM을 활용하여 비디오를 텍스트 형식으로 변환하는 것은 저렴하지 않습니다. 본 문서에서 제시된 솔루션은 이 블로그 포스트로 이 비디오를 변환하는 데 약 4달러의 비용을 소요했습니다.

# 워크플로 개요 및 기술적 제약사항

Claude 3 Opus는 Anthropic에서 제공하는 최신이자 가장 성능이 뛰어난 대형 멀티모달 모델(LMM)입니다. 이 모델은 3월 4일에 발표되었으며, claude.ai 웹 인터페이스 또는 API를 통해 액세스할 수 있습니다.

<div class="content-ad"></div>

모델은 최대 200K 토큰의 텍스트 또는 이미지를 입력으로 받아들일 수 있고, 최대 4K 토큰의 텍스트를 출력할 수 있습니다. 이것이 정확히 무엇을 의미하는지 조금 더 구체적으로 분석해 보겠습니다:

- 출력의 4K 토큰: 한 토큰이 대략 3/4 단어라는 경험 법칙을 고려하면, 4K 토큰은 대략 3K 단어에 해당합니다. 페이지 당 대략 500단어를 가정한다면, 클로드는 최대 6페이지의 텍스트를 출력할 수 있습니다.
- 입력의 200K 토큰: 동일한 통계를 따르면, 이는 15만 단어(약 300 페이지)에 해당합니다. 초당 대략 2~3단어의 발화 속도를 전제하면, 약 20시간의 오디오 트랜스크립트를 소화할 수 있으며, 이는 상당히 많은 양입니다. 반면, 1280*720 픽셀(비디오 HD) 해상도의 이미지를 인코딩하는 데에는 약 1.25K 토큰이 필요합니다. 따라서 한 번에 이론상으로는 150여 장의 이미지를 입력으로 제공할 수 있습니다. 실제로는, 토큰 사용량과는 무관하게, 현재 Anthropi API는 입력 이미지 수를 20장으로 제한하고 있음을 참고해야 합니다.

따라서, 주요 제약 사항은 입력으로 제공할 수 있는 이미지의 제한된 수와 모델이 생성할 수 있는 페이지 수의 제한에 있습니다. 해결책은 비디오를 챕터로 분할하여, 각각이 LMM에 의해 별도로 처리되게 하는 것입니다. 결과물은 그 후에 결합되어 최종 문서를 생성합니다.

아래 다이어그램은 워크플로우의 주요 단계를 요약하고 있습니다:

<div class="content-ad"></div>

Ameisen & Co는 YouTube 비디오 설명에 제시된 장을 기준으로 비디오를 분할했습니다(총 24장). 다른 전략으로는 LLM과 같은 주제 분할 도구를 활용하여 대본을 주요 부분으로 분할하는 방법이 있습니다. 몇 분 간격의 장을 목표로 설정하는 것이 좋으며, 이를 통해 명령과 대본에 함께 들어갈 10~20개의 스크린샷을 포함할 수 있습니다.

마지막으로 처리 비용을 예상해 봅시다. Claude 3 Opus의 토큰 사용 비용은 입력 토큰당 15달러이며, 출력 토큰당 75달러입니다.

5분 간격의 장을 가정하면, 2시간짜리 비디오는 24개의 장을 제공하는데, 각 장은 평균적으로 다음을 필요로 합니다:

- 13,000개의 입력 토큰(텍스트 토큰 1,000개 및 1.2K 토큰/이미지의 10개)
- 1,000개의 출력 토큰(2페이지)

<div class="content-ad"></div>

총 입력 토큰은 약 13*2≈300천 개이며, 출력 토큰은 1천 * 24 = 24천 개입니다. 백만 토큰당 비용을 곱하면 입력 비용이 15*0.3=4.5달러, 출력 비용이 75*0.024=1.8달러가 됩니다.

따라서 2시간 비디오에서 게시물을 생성하는 총 비용은 대략 5에서 10달러 정도입니다. 최적화 전략을 사용하여 어떤 스크린샷을 포함할지 신중하게 선택하고 입력 비용을 줄일 수 있습니다.

# 구현

이제 우리의 구현으로 넘어가 봅시다. 이는 우리의 워크플로우에서 제시한 네 가지 주요 단계를 따릅니다.

<div class="content-ad"></div>

- 비디오를 다운로드하고 텍스트를 획득합니다.
- 텍스트와 스크린샷을 정렬한 챕터로 분할합니다.
- 챕터의 LMM 처리를 수행합니다.
- LMM 출력을 결합하고 블로그 글을 작성합니다.

명확성을 위해 각 단계에 대해 가장 직관적인 구현 방법을 제시합니다. 동반 노트북에는 때로는 데이터를 처리하는 고급 방법을 사용한 추가 코드가 포함될 수 있습니다.

## 비디오를 다운로드하고 오디오 텍스트를 가져옵니다

YouTube에 동영상이 있는 경우, 먼저 pytube 라이브러리를 사용하여 비디오를 다운로드합니다. 나중에 블로그 글을 생성하기 위해 비디오 프레임이 필요하므로 오디오 스트림만이 아닌 전체 비디오를 다운로드합니다.

<div class="content-ad"></div>

```python
import pytube

# Andrej Karpathy: GPT Tokenizer 만들기 – https://www.youtube.com/watch?v=zduSFxRajkE
youtube_video_id = "zduSFxRajkE"
def download_youtube_video(video_id, output_path):
    """
    YouTube 비디오를 다운로드하고 output_path에 저장한 후 비디오 ID를 파일 이름으로 반환합니다.
    """
    # 비디오 ID로 YouTube 객체 생성
    youtube = pytube.YouTube(f"https://www.youtube.com/watch?v={video_id}")
    # 가장 높은 해상도의 비디오 스트림 가져오기
    stream = youtube.streams.get_highest_resolution()
    # 비디오 다운로드
    video_path = stream.download(output_path=output_path, filename=video_id+".mp4")
    return video_path
# 330MB 비디오에 대해 약 20초 소요
video_path = download_youtube_video(youtube_video_id, DATA_DIR)
```

대부분의 Youtube 비디오에는 대본이 이미 제공되어 있습니다. youtube_transcript_api와 같은 라이브러리를 사용하여 Youtube 비디오 ID를 제공함으로써 단순히 대본을 얻을 수 있습니다.

```python
from youtube_transcript_api import YouTubeTranscriptApi

transcript = YouTubeTranscriptApi.get_transcript(youtube_video_id)
```

2시간 13분의 오디오 스트림 전체가 3422개의 세그먼트로 대본화되었습니다.

<div class="content-ad"></div>


```bash
len(transcript)
3422

transcript[0:4]
[{'text': "hi everyone so in this video I'd like us",  'start': 0.04,  'duration': 4.04}, {'text': 'to cover the process of tokenization in',  'start': 2.04,  'duration': 4.4}, {'text': 'large language models now you see here',  'start': 4.08,  'duration': 4.2}, {'text': "that I have a set face and that's",  'start': 6.44,  'duration': 3.88}]
```

만약 트랜스크립트를 사용할 수 없다면, 오디오 스트림을 음성 인식 모델을 사용하여 텍스트로 변환해야 합니다. 🤗 Open ASR Leaderboard는 성능이 우수한 모델을 찾을 수 있는 좋은 장소입니다. 동반 노트북에 위스퍼 모델과 효율적인 faster-whisper 구현을 사용하여 트랜스크립트를 가져오는 코드를 제공합니다. 이 프로세스는 Google Colab의 T4에서 약 25분이 걸리며(RTX 4090에서는 12분) 완료됩니다.

# 텍스트와 스크린샷이 정렬된 장이로 나누기

장은 수동으로 식별하거나 YouTube가 제공하는 자동 비디오 장 도구와 같은 도구를 사용하여 식별할 수 있습니다. 예제 비디오의 경우, 비디오 설명에 개요된 24개의 장을 복사하여 Python chapters_list 객체에 저장했습니다. 아래에 설명된 것과 같이요.

<div class="content-ad"></div>

```json
chapters_list = [
{'timestamp': 0, 'topic': 'Tokenization을 이해하기 위한 소개 및 동기 부여'},
 {'timestamp': 262, 'topic': 'GPT-2에서 토큰화를 위해 바이트 수준 인코딩을 소개한 논문 소개'},
 {'timestamp': 933, 'topic': '유니코드, UTF-8 인코딩 및 어휘 크기'}
...
]
```

그런 다음이 단계의 핵심은 챕터의 시작/끝 타임스탬프에 따라 텍스트와 스크린샷을 추출하는 것입니다. 이것은 chop_up_in_chapters 함수에 의해 구현되며 각 챕터마다 챕터의 시작 및 끝 타임스탬프를 식별하고 트랜스크립트에서 해당 텍스트를 추출하여 비디오에서 스크린샷을 추출합니다.

스크린샷 추출 전략은 각 주어진 챕터에 대해 최대 10장의 스크린샷을 균일하게 샘플링하여 추출하되, 스크린샷 간에 최소 한 분이 경과하도록합니다.

추출된 텍스트 및 스크린샷은 별도의 폴더에 저장됩니다(챕터 번호를 이름으로 사용).

<div class="content-ad"></div>

```python
def chop_up_in_chapters(chapters_list, video_path, transcript, timestamps_screenshots_list_seconds=None):
    """
    비디오를 장(chapter)으로 나눕니다. 비디오 장(chapter) 목록을 기준으로 합니다.
    """
    n_chapters=len(chapters_list)-1
    print(f"장 수: {n_chapters}")
    # 타임스탬프와 주제에 대해 반복합니다.
    for current_chapter in range(n_chapters):
        output_dir=CHAPTERS_DIR+"/"+str(current_chapter)
         # 해당 출력 디렉토리가 없으면 생성합니다.
        if not os.path.exists(output_dir):
            os.makedirs(output_dir)
        # 현재와 다음 타임스탬프를 가져옵니다.
        current_chunk_start_time=chapters_list[current_chapter]['timestamp']
        current_chunk_end_time=chapters_list[current_chapter+1]['timestamp']-1
        print(f"장 {current_chapter}; 시작: {current_chunk_start_time}, 끝: {current_chunk_end_time}")
        # 현재 장에 대한 텍스트 및 프레임을 추출합니다.
        get_text_chapter(transcript, current_chunk_start_time, current_chunk_end_time, output_dir)
        
        if timestamps_screenshots_list_seconds is not None:
            get_frames_chapter(video_path, current_chunk_start_time, current_chunk_end_time, output_dir,timestamps_screenshots_list_seconds[current_chapter])
        else:
            get_frames_chapter(video_path, current_chunk_start_time, current_chunk_end_time, output_dir)
```

# 대규모 다중모달 모델(LLM) 처리

이것은 핵심 단계입니다. 각 장마다 오디오 대본과 선택한 스크린샷이 LMM에 제공되어 이 입력 데이터를 교과서에 포함할 수 있는 출력으로 변환하는 것이 목표입니다.

이 단계의 핵심 요소는 우리가 다음과 같이 설계한 LLM 프롬프트입니다:


<div class="content-ad"></div>

```js
prompt_instructions = """
<instructions>
동영상의 이미지를 다른 타임 스탬프로 제공했으며, <transcript> 안에 오디오 대본도 포함되어 있습니다.
대본은 인공지능 음성 인식 도구에 의해 생성되었으며 일부 오류/불일치가 있을 수 있습니다.
귀하의 작업은 대본을 마크다운 블로그 포스트로 변환하는 것입니다.
이 대본은 소음이 많습니다. 다음 가이드라인을 사용하여 블로그 장을 위한 마크다운 형식으로 다시 작성하십시오:
- 유효한 마크다운 출력
- 적절한 곳에 섹션 제목 및 다른 서식 삽입
- 대사 블록의 일부만 제공되어 주요 주제만 포함하세요. 소개나 결론 단락은 포함하지 마세요. 대사에서 논의된 주요 주제만 포함해주세요.
- 이미지, 텍스트, 코드, 강조 및 페이지 레이아웃 및 여백을 일반적인 블로그 게시물 또는 교과서와 같이 보이도록 스타일링
- 말투적인 속성을 제거하십시오
- 중복 정보가 있는 경우 반복되는 정보는 한 번만 제시해주세요
- 대화식 내용을 유지하되 대화의 구조를 따를 수 있도록 제목을 포함하세요
- 각 대본에는 너무 많은 이미지가 포함되어 있으므로 출력에는 가장 중요한 1-2개의 이미지만 포함해주세요
- 대사와 관련된 일부를 시각화하는 데 도움이 되는 이미지를 선택해주세요
- 이미지를 선택할 때는 대사에서 설명한 것과 관련된 완전한 코드를 표시하는 이미지를 선호해주세요
- 이미지가 대본의 일부를 설명하는 데 도움이 될 경우 포함해주세요
- 이미지를 포함하려면, <img src="xxxxx.jpg"/> 태그를 삽입하며, 여기서 xxxxx는 이미지 데이터 위에 삽입된 정확한 이미지 타임스탬프로 대체되어야 합니다
- 불필요한 정보를 추가하지 마세요. 대본이나 이미지에서 언급된 사항만 포함해주세요
최종 출력물은 교과서에 포함하기 적합해야 합니다.
</instructions>
"""
```

우리는 주로 Ameisen의 안내를 재사용했습니다. 다음과 같은 수정을 가했습니다.

- LMM 출력을 결합하기 위해 출력 형식을 HTML에서 마크다운으로 변경하여 더 직관적으로 만들었습니다 (그리고 마크다운 형식은 블로그 게시물에 시각적으로 잘 어울립니다).
- 출력 형식이 마크다운으로 정의되었음에도 불구하고 시각적인 요소와 작성 스타일 이미지를 제거했습니다. 이들이 더 유용한 정보를 추가하지 않는다는 결론에 이른 후였습니다.
- 프롬프트에서 일부 서식을 Anthropic의 가이드라인에 더 잘 따르도록 변경했습니다. 특히, 앞부분에 있는 스크린샷을 이동시키고 지침을 XML 태그로 래핑했습니다.

프롬프트는 장의 스크린샷 및 대본 앞에 오고 있습니다. 우리는 JPG 스크린샷을 Anthropic의 비전 API에 적합한 형식으로 변환하기 위해 `get_screenshots_as_messages` 도우미 함수를 정의했습니다. 이 함수는 모든 스크린샷을 반복하여 각각에 대한 두 가지 메시지를 설명합니다: 스크린샷의 타임스탬프를 지정하는 텍스트 메시지와 그것의 base64로 인코딩된 표현을 포함하는 이미지 메시지입니다. 나중에 하이퍼링크가 추가된 최종 문서에서 원본 비디오로 이동할 수 있게 해주는 타임스탬프가 포함된 텍스트 메시지입니다.


<div class="content-ad"></div>

```js
def get_screenshots_as_messages(screenshots):
  screenshots_as_messages = []
  for i in range(len(screenshots)):
    screenshots_as_messages.extend([
    {
    "type": "text",
    "text": f"The timestamp for the following image is {Path(screenshots[i]).stem}."
    },
    {
    "type": "image",
    "source": {
      "type": "base64",
      "media_type": "image/jpeg",
      "data": base64.b64encode(open(screenshots[i], "rb").read()).decode("utf-8"),
      }
    }])
  return screenshots_as_messages
```

우리는 스크린샷, 대본 및 지침을 모아서 함께 가져오는 또 다른 도우미 함수인 get_prompt_as_messages를 정의했습니다. 이 함수는 추가로 Claude의 출력을 미리 채워 마크다운 제목("#")으로 답변을 시작하도록 만듭니다.

```js
def get_prompt_as_messages(chapter_id):
    folder_path=CHAPTERS_DIR+'/'+str(chapter_id)
    with open(folder_path+'/transcript.txt', "r") as f:
        transcript = f.read()
    screenshots=sorted(glob.glob(folder_path+'/*.jpg'))
    
    screenshots_as_messages=get_screenshots_as_messages(screenshots)
    prompt_as_messages = [
        {
            "role": "user",
            "content": screenshots_as_messages+
            [
                {
                    "type": "text",
                    "text": f"<transcript>\n{transcript}\n</transcript>"
                },
                {
                    "type": "text",
                    "text": prompt_instructions
                }
            ],
        },
        {
            "role": "assistant",
            "content": [
                {
                    "type": "text",
                    "text": "#"
                }
            ]
        }
    ]
    return prompt_as_messages
```

그게 다야!


<div class="content-ad"></div>

모든 챕터는 클로드를 반복적으로 호출하여 처리한 후 결과를 해당 챕터 폴더에 있는 마크다운 파일로 작성할 수 있습니다.

```js
# 챕터 목록을 반복하여 처리
for chapter in range(len(chapters_list)-1): 
  # 현재 챕터에 대한 프롬프트 생성 (스크린샷, 대본 및 지침이 포함된 메시지 목록).
    prompt_generate_markdown = get_prompt_as_messages(chapter)
    # 프롬프트를 사용하여 메시지 생성하기
    message = client.messages.create(
        model="claude-3-opus-20240229",
        system="당신은 마크다운 블로그 글쓰기 전문가입니다.",
        temperature=0,
        max_tokens=4000,
        messages=prompt_generate_markdown
    )
    # 응답에서 생성된 마크다운 콘텐츠 추출
    answer = message.content[0].text
    markdown = "#"+answer  # 마크다운 콘텐츠 앞에 헤더 태그 추가
    
    # 현재 챕터에 해당하는 마크다운 파일 경로 정의
    markdown_file = CHAPTERS_DIR + '/' + str(chapter) + '/markdown.md'
    # 생성된 마크다운 콘텐츠를 파일에 작성
    with open(markdown_file, "w") as f:
        f.write(markdown)
```

아래는 Anthropic의 사용 로그 중 마지막 일곱 챕터 처리에 대한 부분을 보고, 처리 시간 및 입력 및 출력 토큰 수의 변동을 감지할 수 있습니다.

<img src="/assets/img/2024-05-18-UsingClaude3toTransformaVideoTutorialIntoaBlogPost_2.png" />

<div class="content-ad"></div>

가장 긴 장은 마지막에서 두 번째 장이었습니다 (여기에서 장을 확인하세요), 1시 51분부터 2시 10분까지 총 17689 토큰을 처리하는 데 거의 1분이 걸렸습니다. 전체적으로 비디오 및 24 장을 처리하는 데 약 10분이 소요되었고, 18만 토큰의 입력 및 1만 5천 토큰의 출력이 사용되었습니다. 이 과정은 약 4달러의 비용이 소요되었습니다.

# LMM 출력을 결합하여 최종 블로그 포스트 생성

워크플로우의 최종 단계는 주로 두 가지 작업으로 구성됩니다. 먼저 다양한 마크다운 출력을 병합합니다. 그 다음으로는 장 제목 및 이미지에 하이퍼링크를 추가합니다. 이를 통해 최종 마크다운 파일을 해당 타임스탬프에서 원본 YouTube 비디오에 연결할 수 있습니다.

```js
merged_markdown=""

# 장 폴더를 반복하여 마크다운 파일 병합
for chapter in range(len(chapters_list)-1):
    markdown_file=CHAPTERS_DIR+'/'+str(chapter)+'/markdown.md'
    with open(markdown_file, "r") as f:
        markdown = f.readlines()
    # 각 장 제목에 해당하는 비디오의 링크를 추가
    url_chapter = f"https://www.youtube.com/watch?v={youtube_video_id}&t={chapters_list[chapter]['timestamp']}s"
    markdown[0] = f"# [{chapter+1}) {markdown[0][2:].strip()}]({url_chapter})"
    markdown = '\n'.join(markdown)
    merged_markdown+="\n"+markdown
# 병합된 마크다운에서 src 속성의 타임스탬프가 포함된 모든 <img> 태그를 찾아 해당 비디오의 링크를 추가
timestamps_screenshots = re.findall(r'<img src="(\d+)\.jpg"/>', merged_markdown)
timestamps_screenshots = [timestamp for timestamp in timestamps_screenshots]
# 각 이미지에 해당하는 타임스탬프에서 올바른 비디오 링크를 추가
for timestamp in timestamps_screenshots:
    video_link = f'<a href="https://www.youtube.com/watch?v={youtube_video_id}&t={int(timestamp)}s">비디오 링크</a>'
    merged_markdown = merged_markdown.replace(f'<img src="{timestamp}.jpg"/>', f'<img src="{timestamp}.jpg"/>\n\n{video_link}')
# 병합된 마크다운에서 이미지를 기반으로한 프레임을 추출하고 merge 폴더에 저장
get_frames_chapter(video_path, None, None, MERGE_DIR, timestamps_screenshots=timestamps_screenshots)
# 병합된 마크다운을 markdown 블로그포스트.md 파일로 저장
markdown_file=MERGE_DIR+'/blogpost.md'
with open(markdown_file, "w") as f:
        f.write(merged_markdown)
```

<div class="content-ad"></div>

결합된 마크다운 파일은 MERGE_DIR 폴더에 모든 선택한 JPG 스크린샷과 함께 'markdown.md'로 저장됩니다 (최종 출력).

# 토의

결과적으로 포스트는 원래 비디오의 대부분의 내용을 성공적으로 보존하여, Ameisen 및 그의 동료가 설명한 것과 유사한 품질을 달성했습니다. 또한, 의미 있는 스크린샷 및 코드 조각을 정확하게 식별하여 오디오 대본의 이해를 돕습니다. 그러나, 텍스트 변환이 정확하지 않은 부분을 비롯해 일부 결함이 있습니다.

정확하지 않거나 모순된 부분을 해결하기 위해 철저한 편집과 교정이 여전히 필요합니다. (Ameisen의 작업에 발견된 것과 유사한) 문제의 예로는 예를 들어, "hello world" 토큰을 2가 아닌 300으로 잘못 세는 오류, "tokenization"의 첫 번째 토큰을 잘못 번호 매기는 오류, 공백을 토큰으로 오도록 잘못 인식하는 등이 있습니다 (블로그 포스트의 2장 참조). 이러한 부정확성 외에도, 이 방법론은 효과적인 프롬프트를 만드는 복잡성, 결과의 재현 불가능성 및 LMM 운영에 따른 비용과 같은 다른 어려움을 야기합니다.

<div class="content-ad"></div>

하지만 이러한 단점에도 불구하고 비디오를 접근 가능하고 쉽게 탐색할 수 있는 텍스트 블로그 포스트로 변환하는 것은 대형 다중 모달 모델의 가치 있는 응용 프로그램입니다. 특히 경쟁하는 LMM(대형 다중 모달 모델)인 GPT4-V, Gemini Pro Vision 및 오픈 소스 대규모 월드 모델의 비디오/이미지 이해 능력과 비교는 내일의 블로그 포스트 주제가 될 것입니다.

# 유용한 링크

- 동반자 Github 저장소
- Karpathy의 도전과 Ameisen 및 동료의 저장소
- 토큰화에 대한 비디오 튜토리얼 : https://www.youtube.com/watch?v=zduSFxRajkE 및 손으로 쓴 튜토리얼 요약 : https://github.com/karpathy/minbpe/blob/master/lecture.md
- Claude 3 — Vision 문서
- Misbah Syed의 다른 접근 방식

참고: 별도로 표시되지 않는 한, 모든 이미지는 작성자가 제공한 것입니다.

<div class="content-ad"></div>

이 게시물을 즐겼나요? 생각을 공유하거나 박수를 보내거나 LinkedIn에서 저와 연락하세요.