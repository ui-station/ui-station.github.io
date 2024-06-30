---
title: "ntscraper를 사용한 실시간 트윗 데이터 분석 방법"
description: ""
coverImage: "/assets/img/2024-06-30-ScrapingTweetsforReal-TimeDataAnalysisUsingntscraper_0.png"
date: 2024-06-30 22:12
ogImage: 
  url: /assets/img/2024-06-30-ScrapingTweetsforReal-TimeDataAnalysisUsingntscraper_0.png
tag: Tech
originalTitle: "Scraping Tweets for Real-Time Data Analysis Using ntscraper"
link: "https://medium.com/@prathamsk130/scraping-tweets-for-real-time-data-analysis-using-ntscraper-a875f6d030b9"
---


![이미지](/assets/img/2024-06-30-ScrapingTweetsforReal-TimeDataAnalysisUsingntscraper_0.png)

데이터 분석 분야는 계속 발전 중이에요. 실시간 데이터는 매우 중요하죠. 실시간 데이터의 가장 풍부한 출처 중 하나는 소셜 미디어인데, 특히 트위터(X.com)가 그 중요한 출처이죠. 트위터 데이터에 접근하려면 기존에는 복잡한 트위터 API를 통해 접근해야 했는데, 이는 종종 유료 구독과 복잡한 인증 키가 필요했어요. 그러나 새로운 Python 라이브러리인 ntscraper를 사용하면 유료 API나 키 없이도 트윗을 스크래핑할 수 있는데요. 이 블로그에서는 ntscraper를 사용하여 실시간 데이터셋을 만들어본 경험을 공유할게요.

# ntscraper 소개

ntscraper는 공식 API가 필요 없이 트위터에서 트윗을 바로 스크래핑할 수 있도록 설계된 Python 라이브러리에요. 이를 통해 데이터 과학을 시작한 사람들에게도 매우 접근하기 쉽고 사용하기 쉽습니다.

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

# ntscraper의 주요 기능:

- API 키 불필요: API 요청 제한과 인증 처리로 인한 번거로움을 피하세요.
- 실시간 데이터: 게시된 최신 트윗을 검색합니다.
- CSV 변환: 수집된 데이터를 쉽게 CSV 형식으로 변환하여 분석할 수 있습니다.

# ntscraper로 시작하기

# 단계 1: 설치

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

먼저 ntscraper 라이브러리를 설치하세요. 이 작업은 pip를 사용하여 수행할 수 있습니다:

```js
pip install ntscraper
```

# 단계 2: 트윗 스크레이핑

다음은 인도 대통령(@rashtrapatibhvn)의 공식 트위터 계정에서 트윗을 스크레이핑하고 CSV 파일로 저장한 구체적인 코드입니다:

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
from ntscraper import Nitter

# 스크레이퍼 초기화
scraper = Nitter()

# 특정 사용자의 트윗 스크랩
tweets_data = scraper.get_tweets("rashtrapatibhvn", mode='user', number=10)
print(tweets_data)

# 데이터 구조 검사
print(tweets_data.keys())
print(tweets_data['tweets'][0])

# 프로필 정보 가져오기
profile_info = scraper.get_profile_info(username='elonmusk')
print(profile_info)

# 트윗 데이터를 저장할 딕셔너리 생성
data = {
    '링크': [],
    '내용': [],
    '사용자': [],
    '좋아요': [],
    '리트윗': [],
    '댓글': []
}

# 트윗에서 데이터 추출
for tweet in tweets_data['tweets']:
    data['링크'].append(tweet['link'])
    data['내용'].append(tweet['text'])
    data['사용자'].append(tweet['user']['name'])
    data['좋아요'].append(tweet['stats']['likes'])
    data['리트윗'].append(tweet['stats']['retweets'])
    data['댓글'].append(tweet['stats']['comments'])

# 데이터프레임 생성 후 CSV로 저장
import pandas as pd
df = pd.DataFrame(data)
df.to_csv('prez.csv', index=False)
print("프리즈.csv 파일에 트윗이 저장되었습니다.")
```

# 단계 3: 데이터 분석

데이터셋을 사용하여 선호하는 데이터 분석 도구인 판다스, 맷플롯립 또는 다른 도구를 활용하여 데이터를 분석할 수 있습니다.

## 예시 분석: 감성 분석


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

다음은 TextBlob 라이브러리를 사용하여 수집된 트윗에 대한 감성 분석을 수행하는 간단한 예제입니다:

```python
import pandas as pd
from textblob import TextBlob

# CSV 파일을 DataFrame으로 불러오기
df = pd.read_csv('prez.csv')

# 감성 분석 수행
df['Sentiment'] = df['text'].apply(lambda tweet: TextBlob(tweet).sentiment.polarity)

# 처음 몇 행 표시
print(df.head())

# 감성 분석 결과 플롯하기
df['Sentiment'].plot(kind='hist', title='트윗 감성 분석 결과')
```

# 결론

ntscraper를 사용하면 실시간 데이터 분석을 위해 트윗을 수집하는 것이 이전보다 더 쉬워졌습니다. 이 도구는 Twitter 데이터에 대한 접근을 민주화시키며, 데이터 분석가와 데이터 과학자들이 API 복잡성을 다루는 대신 인사이트를 추출하는 데 집중할 수 있게 합니다. 트렌드 추적, 감성 분석 수행, 시장 조사 등을 하든지, ntscraper는 데이터 분석 도구상의 중요한 자산이 될 수 있습니다.

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

행복한 스크래핑하세요!