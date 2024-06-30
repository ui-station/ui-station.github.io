---
title: "Python을 사용하여 GitHub 개발 활동 분석하는 방법"
description: ""
coverImage: "/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_0.png"
date: 2024-06-30 22:01
ogImage: 
  url: /assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_0.png
tag: Tech
originalTitle: "How to Analyse GitHub Development Activity with Python"
link: "https://medium.com/ai-advances/how-to-analyse-github-development-activity-with-python-ef00157ff46d"
---


<img src="/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_0.png" />

GitHub 개발 데이터는 특정 프로젝트나 회사의 전체 활동을 평가하려는 기관, 투자자, 경쟁업체에게 소중한 통찰을 제공합니다. 이 데이터를 분석함으로써 이해관계자들은 특정 시간대의 최고 기여자를 식별하고 가장 활발한 저장소를 파악하며 프로젝트의 단계별 목표와 활동 수준 사이의 관계를 발견할 수 있습니다.

다음 글에서는 web3 생태계의 여섯 개의 오픈 소스 AI 프로젝트의 GitHub 개발 데이터를 살펴보겠습니다. 이들은 다음과 같습니다:

- Ocean Protocol: 이 프로젝트는 암호화폐 가격 예측 알고리즘부터 데이터 과학 도전과 시장을 위한 다양한 솔루션을 보유하고 있습니다.
- Bittensor: 이 프로젝트는 탈중앙화된 컴퓨팅으로 AI 민주화를 목표로 하는 모듈식 아키텍처를 가지고 있습니다.
- Fetch.ai: 이 프로젝트는 SDK를 사용하여 AI 기반 프로젝트를 통해 개발자들이 수익을 창출할 수 있도록 합니다.
- Numerai: 이 프로젝트는 주식 시장 예측 토너먼트를 주최하며 우승자에게 토큰을 보상합니다.
- Oraichain: AI의 다차원 신뢰도를 검증하고 독특한 AI 오라클을 활용하여 Web3 애플리케이션을 구축하기 위한 IBC 활성화된 Layer 1입니다.
- SingularityNET: 이 프로젝트의 임무는 탈중앙화 방식으로 인공 일반 지능(AGI)을 개발하는 것입니다.

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

Python을 사용하여 분석을 진행하기 위해 위에서 언급한 각 프로젝트에 대한 GitHub의 공개 데이터를 활용할 것입니다. 데이터 조작 및 정리에는 Pandas를 사용하고, 시각화에는 Matplotlib 및 Seaborn을 활용하여 가치 있는 통찰을 제공하는 정보적인 차트를 생성할 것입니다. 커밋 빈도, 저장소 생성, 개발자 참여 등과 같은 주요 메트릭을 조사함으로써 개발 참여 수준이 프로젝트 성공의 신뢰할 수 있는 지표인지를 평가할 수 있습니다.

저희의 분석은 GitHub에서의 개발 활동의 중요성과 해당 활동이 이해관계자, 투자자, 경쟁 업체에게 가치 있는 통찰을 제공할 수 있다는 점을 탐색하는 데서 시작할 것입니다. 그 다음으로, 개발 활동에 대한 시간적 분석을 탐구한 다음, 커밋 빈도를 기준으로 프로젝트를 순위 매기겠습니다. 또한 가장 활발하게 활동하는 개발자를 식별하고 순위를 매기며, 주목받는 저장소도 확인하여 각 프로젝트의 역학을 포괄적으로 이해할 수 있도록 할 것입니다.

# GitHub의 개발 활동 데이터의 중요성

개발 활동을 연구하는 것이 왜 중요한지 궁금하다면, 이 섹션에서 이해관계자, 투자자 및 경쟁사에게 특히 중요할 수 있는 몇 가지 핵심 요소를 강조하겠습니다. 함께 살펴보겠습니다:

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

## 이해관계자들

- 프로젝트 건강 및 진행 상황에 대한 통찰력을 얻을 수 있습니다: 이해관계자들은 프로젝트가 활발히 개발되고 있는지, 어떤 기능이 우선 순위에 있는지 이해할 수 있습니다.
- 프로젝트 역학 이해하기: 시간적 분석을 통해 이해관계자들은 프로젝트 수명주기를 이해하고 중요한 진전 또는 정체 기간을 식별할 수 있습니다. 또한 프로젝트의 주요 이정표들과의 상관 관계를 파악하는 데 도움이 됩니다.
- 개발자 성과 평가: 많은 커밋 수는 언제나 생산성이 높다는 것을 의미하는 것은 아니지만, 이는 이해관계자들이 개발자의 참여 정도를 이해하고, 그가 회사의 건강한 성장에 기여하는지를 이해하는 데 좋은 지표입니다.

## 투자자들

- 안내된 투자 결정: 커밋 빈도 및 활동 트렌드를 분석함으로써, 투자자들은 활발히 개발되고 유지되는 유망한 프로젝트를 식별할 수 있습니다. 높은 개발 활동은 투자 수익 가능성에 대한 긍정적인 신호가 될 수 있습니다.
- 프로젝트 장기성 이해하기: 시간적 분석을 통해 투자자들은 프로젝트의 지속 가능성과 장기적 잠재력을 평가할 수 있습니다.
- 개발자 품질: 개발자들의 활동 및 GitHub 프로필을 알게 되면 투자자들은 프로젝트 뒤에 있는 재능을 볼 수 있습니다.

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

## 경쟁사

- 경쟁 전략 수립: 경쟁사는 경쟁 프로젝트의 개발 활동을 분석하여 그들의 장단점을 이해할 수 있습니다. 이 정보를 활용하여 경쟁사를 앞서가는 전략을 개발할 수 있습니다.
- 트렌드 파악: 개발 활동의 시계열 분석을 통해 경쟁사는 시장 트렌드를 식별하고 그에 맞게 전략을 조정할 수 있습니다. 어떤 저장소가 가장 많은 관심을 받고 있는지 파악함으로써 경쟁사는 주목받고 있는 분야에 초점을 맞출 수 있습니다.

전반적으로, GitHub 개발 활동을 연구함으로써 이해당사자, 투자자, 경쟁사 및 다른 이야기에서 언급되지 않은 당사자들에게 맞춤형 통찰력을 제공하여 각 그룹이 정보에 근거한 결정을 내리고 소프트웨어 개발 생태계에서 경쟁력을 유지할 수 있도록 도와줍니다.

# 시간별 개발자 활동 분석

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

이 섹션에서는 각 프로젝트의 시간 경과에 따른 커밋 수와 참여자 수를 조사할 것입니다. 데이터 포인트가 매일 표시하기에 너무 많기 때문에 대신 주간 보기를 사용할 것입니다. 차트에서 범례의 각 색깔은 서로 다른 연도에 해당합니다. 우리는 활동의 증가와 주요 프로젝트 이정표간의 연결을 살펴볼 것이며, 시간이 흐름에 따라 개발 강도가 어떻게 변하는지 확인할 것입니다.

## Bittensor

<img src="/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_1.png" />

위의 Bittensor 차트에서 시간이 흐름에 따라 개발 활동이 점차 증가하는 것을 볼 수 있습니다. 2023년이 가장 많은 커밋과 다른 참여자 수가 있었던 해로, 거의 50명의 개발자가 Bittsensor의 저장소에서 업그레이드를 진행했습니다. 2024년에 관한 데이터가 충분치 않지만, 이미 개발이 크게 감소하고 참여자 수도 줄어든 것을 볼 수 있으며, 이는 2019년과 거의 동일합니다. 그렇지만 우리는 몇몇 중요한 커밋 피크를 볼 수 있습니다. 주로 연도의 처음 4주와 11, 12주쯤에 발생합니다.

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

예를 들어, 1월 17일쯤에 Bittsensor의 토큰 $TAO가 상승한 상위 그룹에 속했다는 기사를 찾을 수 있습니다. 이때가 2024년의 첫 번째 정점이 있는 시기와 비슷합니다. 그러나 토큰의 가격은 개발 활동과 항상 상관관계가 있는 것은 아니므로, 차트에서 가장 큰 정점은 2023년 말에 발생했으며, 이 기간 동안 큰 상승 추세가 없었습니다.

## Fetch.ai

![이미지](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_2.png)

Fetch.ai 차트는 Bittensor의 차트와 매우 다릅니다. 개발 활동은 주로 2017년부터 2021년 사이에 발생한 것을 볼 수 있습니다. 회사는 2017년에 설립되었고 2019년에 출시되었지만, 개발은 2014년에 더 빨리 시작된 것으로 보입니다. 커밋의 주요 정점은 출시의 거의 1년 전인 2018년 중반에 있습니다. GitHub 기여자 수는 2019년에 증가하며, 해당 해에 가장 큰 정점을 나타냅니다. 그 후 바로 2020년 1월 1일에 회사는 메인넷에서 출시되었습니다. 이 이벤트는 2019년 후반에 보다 많은 기여자와 커밋과 관련이 있을 수 있습니다.

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

지금까지 개발은 지난 세 년 동안 감소하는 것으로 보이지만, 2024년에는 참여자 수와 커밋 수가 약간 상승하는 추세를 보입니다.

## Numerai

![Numerai](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_3.png)

Numerai의 전반적인 개발 활동은 크지 않습니다. 2017년과 2021년에는 몇 가지 의미있는 정점이 있습니다. 또한 참여자/기여자 수가 다른 프로젝트와 달리 커밋 수와 관련이 없어 보입니다.

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

2021년의 고점이 Numerai 네트워크 발표와 관련이 있을 수 있습니다. 이 기사에서는 CLI의 업데이트와 예측 노드 구현이 언급되었습니다.

## Ocean Protocol

![Ocean Protocol](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_4.png)

Ocean Protocol 팀은 몇 년 동안 꾸준히 높은 커밋 수를 보여주었으며, 300개 이상의 커밋을 넘는 고점이 있었습니다. 이러한 커밋 고점과 참여자 또는 기여자 수 사이에 상관 관계가 있는 것으로 보이지만, 모든 경우에 일관되게 관찰되는 것은 아닙니다. 그러나 2022년에는 분명한 상관 관계가 있습니다.

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

Predictoor는 2023년 9월 12일 경부 출시되었습니다 (37주차), 이 날짜 이전에는 개발에서 상당한 피크가 있었는데, 이는 해당 이벤트와 관련이 있을 수 있습니다.

가장 큰 피크는 2020년 41주에서 43주에 발생했는데, 바로 Ocean Protocol V3가 출시된 후였습니다. 이에 관한 기사를 확인할 수 있습니다:

2024년에는 2023년과 비교했을 때 개발 활동이 현저히 감소했으며, 저장소에서 활발히 작업하는 기여자 수도 감소했습니다. 그러나 이는 반드시 전체 연도에 대한 지속적인 추세를 나타내지는 않을 수 있습니다. 2020년과 마찬가지로, 개발 면에서는 천천히 시작되었지만 연말에는 급증세를 지켜볼 수 있을 것입니다.

## Oraichain

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


![이미지](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_5.png)

Oraichain Labs 차트를 보면 커밋 수와 참여자/기여자 수 사이에 명확한 상관 관계가 있는 것을 볼 수 있습니다. 2020년 말에 개발이 속도를 내며, 이는 2021년 2월의 공식 발표와 관련이 있을 수 있습니다.

2023년은 개발에서 가장 활발한 해로, 이 추세가 2024년으로 이어지고 있습니다. 이 급증은 해커톤, 지갑 업데이트 및 통합과 같은 중요한 프로젝트 이정표를 반영하고 있습니다. 상대적으로 새로운 프로젝트이기 때문에, 특히 최근 몇 주간 다른 AI 프로젝트들보다 더 높은 개발 강도를 보여주고 있습니다.

## 싱귤러리티넷


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


![Image](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_6.png)

이 차트는 SingularityNET의 개발이 2018년부터 2020년 사이에 정점에 이른 것을 보여줍니다. 이후 커밋 활동 및 기여자 수가 감소하는 추세를 보였습니다. 이 기사에 따르면, 백엔드 소프트웨어는 2019년에 성숙기에 이르렀으며 이는 또한 가장 많은 개발이 이루어진 해입니다.

2024년에는 2023년과 비교했을 때 개발이 증가하는 것을 볼 수 있습니다. 이는 2018년부터 2020년 사이와 유사한 고개발의 새로운 이후를 나타낼 수 있습니다.

# 시간에 따른 개발자 활동 분석 (코드)


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

제공된 데이터셋은 즉시 분석에 사용할 수 있는 상태가 아니라 데이터 조작 기술을 적용하여 작업해야 합니다. 먼저 각 행별로 데이터셋에서 의미 있는 정보를 추출하고, 분석을 위해 다른 정리된 데이터프레임을 생성하기 위해 두 가지 함수를 만듭니다.

```js
import pandas as pd

def process_commits_row(row):
    """ 이 함수는 데이터셋의 각 행에서 의미 있는 정보를 추출하여 딕셔너리로 반환합니다. 'repo' 변수가
    나중에 저장소 데이터셋과 병합하는 데 사용됨에 유의하세요. """
    repo = \
        row['url'].split('repos')[1].split('commits')[0].split("/")[-2]
    return {
        'sha': row['sha'],
        'repo': repo,
        'node_id': row['node_id'],
        'author': row['commit']['author']['name'],
        'date': row['commit']['author']['date'],
        'message': row['commit']['message']}

def gen_df_commits_filt(df):
    """ 이 함수는 데이터셋을 반복하면서 'process_commits_row' 함수를 각 행에 적용합니다. 결과는
    딕셔너리의 목록으로 반환되어 최종적으로 DataFrame으로 반환됩니다. """
    all_features = []
    for row in df.iterrows():
        for i in range(1, len(row[1]) - 1):
            try:
                all_features.append(process_commits_row(row[1][i]))
            except Exception:
                pass
    return pd.DataFrame(all_features)
```

위의 함수들은 각 프로젝트의 커밋 데이터셋을 대상으로 합니다. 이 두 함수를 함께 사용하여 sha, repo, node_id, author, date 및 message의 특징을 가진 필터링된 데이터프레임을 생성합니다. 그런 다음 데이터셋을 호출하고 각각의 데이터셋에 gen_df_commits_filt() 함수를 적용할 수 있습니다.

```js
# 데이터셋 호출
df_commits_bittensor = pd.read_json('datasets/bittensor_commits.json')
df_commits_fetchai = pd.read_json('datasets/fetchai_commits.json')
df_commits_numerai = pd.read_json('datasets/numerai_commits.json')
df_commits_ocean = pd.read_json('datasets/oceanprotocol_commits.json')
df_commits_oraichain = pd.read_json('datasets/oraichain_commits.json')
df_commits_singular = pd.read_json('datasets/singularitynet_commits.json')

# 데이터셋 필터링
df_commits_bittensor_filt = gen_df_commits_filt(df_commits_bittensor)
df_commits_fetchai_filt = gen_df_commits_filt(df_commits_fetchai)
df_commits_numerai_filt = gen_df_commits_filt(df_commits_numerai)
df_commits_ocean_filt = gen_df_commits_filt(df_commits_ocean)
df_commits_oraichain_filt = gen_df_commits_filt(df_commits_oraichain)
df_commits_singular_filt = gen_df_commits_filt(df_commits_singular)
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

날짜별로 그룹화된 데이터셋을 사용하여 시간별 사용자 활동을 관찰할 수 있습니다. 이전에 언급했듯이 날짜 데이터가 너무 많아서 차트에 표시하기에는 많았기 때문에 주차별, 연도별로 그룹화했습니다. 아래 함수를 사용하여 그룹화했습니다:

```js
def gen_group_week(filt_dataframe):
    """ 이 함수는 필터링된 데이터셋을 주별로 그룹화합니다 """
    df_commits_temp = filt_dataframe.sort_values(
        by='date')[['date', 'sha', 'repo', 'author', 'message']]
    df_commits_temp.date = df_commits_temp.date.apply(
        lambda x: datetime.strptime(x, '%Y-%m-%dT%H:%M:%SZ'))
    df_commits_temp['week'] = df_commits_temp['date'].dt.strftime('%W-%Y')
    df_commits_temp = df_commits_temp.groupby('week').agg(
        {
            'sha': 'count',
            'repo': 'nunique',
            'author': 'nunique'}).reset_index().rename(
            columns={
                'sha': 'n_commits',
                'author': 'n_participants',
                'repo': 'n_repos'})
    df_commits_temp['week_num'] = \
        df_commits_temp['week'].str.split('-').str[0].astype(int)
    df_commits_temp['year'] = \
        df_commits_temp['week'].str.split('-').str[1].astype(int)
    df_commits_temp = df_commits_temp.sort_values(by=['year', 'week_num'])
    return df_commits_temp
```

마지막으로, 필터링된 각 데이터셋에 이 함수를 적용했습니다.

```js
df_commits_bittensor_week = gen_group_week(df_commits_bittensor_filt)
df_commits_fetchai_week = gen_group_week(df_commits_fetchai_filt)
df_commits_numerai_week = gen_group_week(df_commits_numerai_filt)
df_commits_ocean_week = gen_group_week(df_commits_ocean_filt)
df_commits_oraichain_week = gen_group_week(df_commits_oraichain_filt)
df_commits_singular_week = gen_group_week(df_commits_singular_filt)
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

이제 이전에 표시된 막대 차트를 구동한 코드를 살펴보겠습니다.

```js
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime

def chart_user_activity(df, protocol):
    """ 사용자 활동에 대한 시간별 막대 차트 작성 """
    fig, ax1 = plt.subplots(figsize=(20, 8))
    # 필요에 따라 색상 수를 조정할 수 있습니다
    colors = sns.color_palette('tab10', len(df['year'].unique()))
    # 각 고유 연도를 색상에 매핑하는 딕셔너리 생성
    year_colors = dict(zip(df['year'].unique(), colors))
    sns.barplot(
        df,
        x='week',
        y='n_commits',
        hue='year',
        ax=ax1,
        dodge=False,
        palette=year_colors)
    ax1.set_xlabel('주', fontsize=18)
    ax1.set_ylabel('커밋 수', fontsize=18)
    ax1.tick_params('y', labelsize=12)
    ax1.set_title(f"{protocol} 개발자 활동 시간별", fontsize=30)
    ax1.grid(axis='y', which='major', linestyle='--', linewidth=0.5)
    ax2 = ax1.twinx()
    sns.lineplot(
        df,
        x='week',
        y='n_participants',
        ax=ax2,
        linestyle='--',
        marker='o',
        color='r',
        alpha=0.5)
    ax2.set_ylabel('참가자 수', fontsize=18, color='r')
    ax2.tick_params('y', colors='r', labelsize=12)
    ax2.xaxis.set_ticks(df['week'][::10])
    ax1.spines['top'].set_visible(False)
    ax2.spines['top'].set_visible(False)
    # x축 눈금 라벨에서 연도 부분 제거
    labels = [item.get_text().split('-')[0] for item in ax1.get_xticklabels()]
    ax1.set_xticklabels(labels)
    ax1.tick_params('x', labelsize=12)
    ax2.set_ylim(bottom=0)
    plt.show()
```

차트를 표시하려면 다음과 같이 간단히 수행할 수 있습니다:

```js
chart_user_activity(df_commits_bittensor_week, 'Bittensor')
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

# 프로젝트 활동 기준으로 순위 매기기

이 섹션에서는 서로 다른 프로젝트에서 시간이 지남에 따라 사용된 커밋 수와 리포지토리 수를 비교해 보겠습니다.

![이미지](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_7.png)

위 막대 차트는 시간이 지남에 따른 커밋의 진화와 연도별 사용된 다른 리포지토리의 수를 확인할 수 있습니다. 이 차트들을 검토하면 몇 가지 초기 관찰을 할 수 있습니다:

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

- 가장 많은 커밋(41465)을 가진 프로젝트는 Fetch.ai이며, 그 다음으로 Ocean Protocol이 33626개의 커밋을 가지고 있습니다. Fetch.ai가 가장 많은 커밋을 가졌다는 사실은 GitHub에서의 장기간 활동으로 인해 놀랍지 않습니다. Ocean Protocol은 4년 뒤에 시작했지만 이미 다른 최근 프로젝트와 비교하여 인상적인 개발 활동을 보여주고 있습니다.
- Bittensor, Ocean Protocol, Oraichain, 그리고 Numerai는 해마다 저장소 수를 늘리고 있으며, 2023년에는 가장 많은 저장소 사용량을 가지고 있습니다. 2024년에는 이 수가 늘어날 수 있습니다. 그러나 이 명제를 확인하기 위해 모든 데이터를 아직 갖고 있지는 않습니다.
- Oraichain은 커밋 수와 저장소 수 사이에 높은 상관 관계를 보여줍니다. Bittensor도 어느 정도 상관 관계를 보여주지만, 다른 프로젝트에는 해당되지 않습니다.
- 저장소 수에 대해서는, Oraichain과 Ocean Protocol이 가장 많으며, 명확히 연도별로 늘어나고 있습니다.
- Bittensor, Ocean Protocol, 그리고 Oraichain은 연도별로 커밋 수를 늘리고 있으며, SingularityNET은 Numerai와 함께 줄어들고 있습니다. Fetch.ai는 벨 모양 곡선을 보여주는데, 개발이 2018년, 2019년, 2020년에 매우 많았지만, 최초와 최근 연도에는 낮았습니다.

간단히 말하면, 각 프로젝트의 연도별 커밋을 합산하여 순위를 매기려면 다음과 같은 결과를 얻을 수 있습니다:

- Fetch.ai — 41465 개의 커밋.
- Ocean Protocol — 3362 개의 커밋.
- SingularityNET — 26245 개의 커밋.
- Bittensor — 23336 개의 커밋.
- Oraichain — 21136 개의 커밋.
- Numerai — 2543 개의 커밋.

# 활동에 따른 프로젝트 순위 (코드)

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

아래는 연도별로 필터된 데이터셋을 그룹화하는 함수입니다.

```js
def gen_group_year(filt_dataframe):
    """ 필터된 데이터셋을 연도별로 그룹화하는 함수입니다 """
    df_commits_temp = filt_dataframe.sort_values(
        by='date')[['date', 'sha', 'repo', 'author', 'message']]
    df_commits_temp['date'] = pd.to_datetime(df_commits_temp['date'])
    df_commits_temp['year'] = df_commits_temp['date'].dt.year
    df_commits_temp = df_commits_temp.groupby('year').agg(
            {
                'sha': 'count',
                'repo': 'nunique',
                'author': 'nunique'}).reset_index().rename(
                columns={
                    'sha': 'n_commits',
                    'author': 'n_participants',
                    'repo': 'n_repos'})
    return df_commits_temp
```

이제 각 프로젝트에 함수를 적용해봅시다:

```js
df_commits_bittensor_year = gen_group_year(df_commits_bittensor_filt)
df_commits_fetchai_year = gen_group_year(df_commits_fetchai_filt)
df_commits_numerai_year = gen_group_year(df_commits_numerai_filt)
df_commits_ocean_year = gen_group_year(df_commits_ocean_filt)
df_commits_oraichain_year = gen_group_year(df_commits_oraichain_filt)
df_commits_singular_year = gen_group_year(df_commits_singular_filt)
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

아래는 차트의 코드입니다:

```js
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime

def bar_activity_n_repos(datasets, main_title, subplot_titles):
    """ Make bar char for activity and repos over time """
    num_datasets = len(datasets)
    num_rows = (num_datasets + 1) // 2
    fig, axes = plt.subplots(num_rows, 2, figsize=(14, 7*num_rows))
    fig.suptitle(main_title, fontsize=30)
    for i, (df, title) in enumerate(zip(datasets, subplot_titles)):
        row = i // 2
        col = i % 2
        ax = axes[row, col]
        sns.barplot(
            x="year",
            y="n_commits",
            hue='n_repos',
            data=df,
            dodge=False,
            palette='Blues',
            ax=ax)
        ax.spines['top'].set_visible(False)
        ax.set_ylabel('Commits', fontsize=14)
        ax.set_xlabel('Years', fontsize=14)
        ax.set_title(title, fontsize=20)
        ax.grid(axis='y', which='major', linestyle='--', linewidth=0.5)
        legend = ax.legend()
        legend.set_title("Repositories")  # Setting legend title
        # Add a red horizontal line for the mean of commits
        mean_commits = df['n_commits'].mean()
        ax.axhline(y=mean_commits, color='red', linestyle='--', label='mean')
        # Annotate mean value above the line
        ax.text(
            0.05,
            mean_commits * 1.05,
            f'Mean: {mean_commits:.2f}',
            color='red',
            fontsize=12,
            va='bottom')
    plt.tight_layout(rect=[0, 0.03, 1, 0.95])  # Adjust subplot title position
    plt.subplots_adjust(hspace=0.2, wspace=0.2)  # Add gap between charts
    plt.show()
```

# 가장 활발한 개발자 순위

이 섹션에서는 각 프로젝트에서 가장 활발한 개발자를 살펴보고, 그들의 상호작용을 시간에 따라 살펴볼 것입니다.

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

다가오는 차트는 막대 그래프입니다. 첫 번째 차트는 연도별로 커밋 수가 가장 많은 상위 10명의 저자를 보여주며, 두 번째 차트는 매년 가장 활발한 개발자를 순위로 나타냅니다. 각 프로젝트에 대해 두 차트를 살펴봐요.

## Bittensor

![Bittensor Chart](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_8.png)

위의 막대 차트를 보면, 각 개발자가 매년 비슷한 수의 커밋을 하지 않는다는 것을 알 수 있습니다. 즉, 저자들은 매년 비슷한 수의 커밋을 하지 않는다는 것입니다. 이는 각 개발자가 자신의 전문 분야가 있고, 가장 익숙한 기술에 대해 작업하기 때문일 수 있습니다.

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

개발 초기 몇 년(2017년, 2018년 및 2029년)에는 Pierre Krieger, Robert Habermeier 및 Bastian Kocher가 프로젝트 개발에 참여했습니다. 그 중 일부는 Polkadot에서 알려져 있습니다. 이야기해야 할 것은 2019년에 Gavin Wood도 프로젝트 개발에 합류했다는 것입니다.

가장 많은 커밋이 이루어진 고점은 Carro가 2022년에 기여했습니다. 동일한 개발자가 2023년에도 일부 기여를 했으며, 전반적으로 이 분은 이러한 연도에 발생한 특정 작업에 중요한 역할을 한 것으로 보입니다.

마지막 몇 년 동안 p-ferreira가 2023년과 2024년에 가장 많은 커밋을 한 것으로 보입니다. Cameron Fairchild는 최근 몇 년간 높은 헌신을 보여주었지만 2024년에는 그렇게 많이 하지는 않은 것 같습니다.

이제 연도별 총 커밋 수를 기준으로 개발자들을 순위 매겨 보겠습니다.

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

아래 차트를 기준으로:

- 2017년에는 Robert Habermeier가 가장 많은 커밋을 하였으며, 2018년에는 Gavin Wood가 활약했습니다.
- 2019년에는 Bastian Kocher가 가장 활발하였습니다.
- unconst는 2020년, 2021년, 2023년에 1위를 기록했습니다.
- 2022년에는 Carro가 가장 높은 피크에 기여한 바 있습니다.
- 현재까지는 p-ferreira가 2024년 가장 활발하게 활동 중입니다.

전체 기간을 살펴보면, Bastian Kocher가 프로젝트에서 가장 많은 커밋을 하였으며, unconst가 그 뒤를 이었습니다.

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

## 페치.에이

![이미지](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_10.png)

위 차트에서 우리는 상위 10명의 개발자 중 아무도 2024년에 기여를 하지 않고 있다는 것을 결론 지을 수 있습니다. 가장 최근의 커밋은 2022년과 2023년이며, 이는 ali와 Yuri Turchenkov에 의해 이루어졌습니다. David Minarsch는 2021년에 매우 활발히 활동했으며 커밋의 최고 피크(2500건 이상)는 2020년에 그에 의해 이루어졌습니다. Ethan Buchman 역시 2016년부터 2018년 사이에 많은 커밋을 하였지만, 2019년 이후로 활동이 없습니다. 또한 이 프로젝트에서 일한 최초의 사람 중 한 명이기도 하지만, 최초의 커밋은 Jae Kwon에 의해 이루어졌습니다.

이제 각 연도별로 가장 활발하게 활동한 개발자들을 살펴봅시다.

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

![2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_11.png]을(를) 참고하면 다음과 같습니다:

- Jae Kwon은 프로젝트 시작 시점인 2014년과 2015년에 가장 활발하게 개발을 진행했습니다.
- Ethan Buchman은 2016년, 2017년 및 2018년에 리더였습니다.
- David Minarsch는 2019년, 2020년 및 2021년에 가장 많은 커밋을 했습니다.
- Yuri Turchenkov은 2022년에 119개의 커밋을 했습니다.
- James Riehl은 2023년과 2024년에 가장 많은 커밋을 했습니다.

전체적으로 Ethan Buchman(5911개의 커밋)이 가장 활발한 개발자였고, David Minarsch(5002개의 커밋)와 Jae Kwon(2604개의 커밋)이 이어졌습니다.

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

## Numerai

![image](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_12.png)

위 차트에서 전체 커밋 수가 가장 많은 상위 10명의 개발자를 볼 수 있습니다. Keith Goodman과 Xander Dunn은 2017년과 2018년에 가장 많은 커밋을 보였습니다. 그러나 현재는 더이상 기여를 하지 않는 것으로 보입니다. 몇 년 동안 가장 꾸준하게 활동한 개발자는 Anson Chu로 보입니다.

지난 몇 년간 Noah Harasz는 꽤 활발히 활동했습니다.

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

이 목록의 대부분의 개발자는 1년에서 2년 사이에 기여하였으며, 참여할 때 상당히 활발하지만 오랫동안 머무르지 않았습니다.

이제 각 연도별 주요 기여자를 분석해보겠습니다.

![chart](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_13.png)

차트를 살펴보면:

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

- 첫 번째 실제 개발은 2017년에 키스 굿맨(Kieth Goodman)과 함께 시작되었고, 2018년까지 계속되었습니다.
- 2019년에는 제이슨 파리아니(Jason Paryani)가 주도하며, 2020년에는 나타샤-제이드 챈들러(Natasha-Jade Chandler)의 시기였습니다.
- 2021년에는 titbtcqash의 시기였습니다.
- 2023년과 2024년에는 노아 하라즈(Noah Harasz)가 가장 많은 커밋을 했습니다.

총적으로, 키스 굿맨이 822개의 커밋으로 현저히 가장 활발한 개발자였습니다. 그 다음으로는 노아 하라즈가 207개의 커밋, 그리고 제이슨 파리아니가 196개의 커밋을 했습니다.

## Ocean Protocol

![Ocean Protocol Image](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_14.png)

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

위의 막대 차트에서 우리는 Ocean Protocol에서 활발히 활동하는 상위 10명의 개발자를 볼 수 있습니다. Matthias Kretschmann은 처음부터 개발을 해오고 있으며, 지난 두 해 동안 커밋을 하지 않았더라도, 여전히 이전에 가장 활발한 개발자입니다. Trent McConaghy는 (trentmc) 이름으로 차트에 두 번 나타나며, 최고의 협력자 중 하나이기도 하며, 지난 몇 년간 상당히 활발하게 활동했습니다. Matthias Kretschmann은 가장 많은 커밋 피크를 보여주지만, Jamie Hewitt와 Norbert도 최근 몇 년간 두 가지 중요한 피크를 보여줍니다.

이제 연도별 최고 기여자를 살펴봅시다.

![chart](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_15.png)

위의 차트를 관찰함으로써 다음을 결론짓을 수 있습니다:

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

- Matthias Kretschmann은 2018년부터 2021년까지 가장 활발하게 활동한 개발자였습니다. 또한 프로젝트 전체에서 가장 많은 커밋을 보유한 저자입니다(4666개의 커밋).
- Jamie Hewitt은 2022년에 우세하며, 또한 두 번째로 활발한 개발자입니다(2133개의 커밋).
- Norbert은 2023년에 우세합니다.
- 2024년에는 Trent McConaghy가 지금까지 가장 많은 커밋을 가지고 있습니다.

## Oraichain

![image](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_16.png)

Oraichain의 가장 활발한 10명의 개발자가 위의 막대 차트로 나타나 있습니다. 해당 그림은 몇 개의 봉우리로 특징 지어집니다. 개발자들이 1년에서 2년 사이에 노력을 집중했음을 나타냅니다. Thunnini은 2019년부터 2021년까지 매우 활발하게 활동했으며, Le Duc Pham과 함께였습니다. 가장 높은 커밋 피크는 Pham Tu에 의해 2023년에 기록되었습니다.

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

최근 가장 많은 커밋을 한 개발자는 Hau Nguyen Van, Toan Dang, sonlha 그리고 ducphamle2라는 이름의 Le Duc Pham입니다.

이제 각 연도별로 가장 활발한 개발자들을 살펴보겠습니다.

![2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_17.png](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_17.png)

상단의 막대 차트는 다음 정보를 제공합니다:

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

- Yury Delendik은 2018년에 가장 활발히 활동했습니다.
- 2019년부터 2021년까지 Thunnini가 커밋 수에서 우세했습니다.
- 2022년에는 Le Duc Pham이 선두를 차지했습니다.
- 2023년에는 Pham Tu가 모든 기간에 가장 많은 커밋을 했습니다.
- 2024년에는 Hau Nguyen Van이 가장 활발한 개발자입니다.

예상대로, Thunnini가 총 커밋 수로 가장 많은(2458건)를 기록했습니다. 이어서 Pham Tu(2427건)와 Le Duc Pham(2273건) 순으로 나타났습니다.

## SingularityNET

<img src="/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_18.png" />

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

위의 표에서 볼 수 있듯이, 상위 10명의 가장 활발한 개발자는 대부분 2018년부터 2021년 사이에 활동했으며 몇 가지 예외가 있습니다. 일부 개발자는 몇 년 동안 일관성을 보였는데, 이들은 Prashant Gupta, anandrgitnirman, dasari-ananya 및 Sridhar Babu Kolapalli입니다. 초기 개발은 특히 Vitaly Bogdanov에 의해 표시되었으며, 커밋의 최고점은 2019년 Vivek에 의해 이루어졌습니다.

Rajeev는 최근 몇 년간 가장 활발한 개발자 중 한 명이었으며, anandrgitnirman과 함께 활동하고 있었습니다.

이제 매년 최고의 개발자들을 살펴보는 시간입니다.

![이미지](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_19.png)

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

위의 차트에서 다음을 결론지을 수 있습니다:

- Marco Argentieri는 프로젝트 초창기(2017년)에 가장 활발했습니다.
- 2018년에는 Vitaly Bogdanov가 선두를 담당했습니다.
- 가장 높은 커밋 피크는 2019년에 Vivek이 올렸습니다.
- 2020년은 Pratik의 시기였고, 2021년은 Rajeev의 시간이었으며, 2022년은 anandrgitnirman의 시기였습니다.
- Marco Capozzoli는 최근 몇 년간 가장 활발한 개발자였습니다.

다른 프로젝트와 달리, 대부분의 경우 연도별로 한 명의 개발자가 있으며, 한 명의 개발자가 여러 해 동안 지배하는 경우는 드뭅니다. 그래도 Marco Capozzoli는 2023년과 2024년에 1위를 차지했습니다. 전체적으로 Vivek이 1927개의 커밋으로 가장 활발했고, 그 뒤를 이어 anandrgitnirman의 1340개 커밋, 마지막으로 Vitaly Bogdanov의 1106개 커밋이 이어졌습니다.

# 활동에 따라 저장소 순위 매기기

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

이 섹션에서는 각 프로젝트에서 활동이 가장 많은 상위 10개 저장소를 살펴보겠습니다. 이는 프로젝트 저장소에 제출된 커밋 수로 측정됩니다.

## Bittensor

현재 Bittensor는 총 31개의 저장소를 보유하고 있지만, 시간상의 이유로 상위 10개 저장소만 고려하여 시각화를 더 잘하고 가장 활발한 저장소에 대해 심층적인 연구를 할 것입니다.

![이미지](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_20.png)

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

아래는 커밋 수에 따라 정렬된 상위 10개 리포지토리 목록과 각각의 GitHub 저장소가 있습니다:

- polkadot-sdk: https://github.com/opentensor/polkadot-sdk
- bittensor: https://github.com/opentensor/bittensor
- subtensor: https://github.com/opentensor/subtensor
- prompting: https://github.com/opentensor/prompting
- developer-docs: https://github.com/opentensor/developer-docs
- old-docs: https://github.com/opentensor/old-docs
- validators: https://github.com/opentensor/validators
- text-prompting: https://github.com/opentensor/text-prompting
- mem-pytorch: https://github.com/opentensor/mem-pytorch
- squid: https://github.com/opentensor/squid

polkadot-sdk는 가장 많은 커밋 수(13789개)를 가진 저장소로, Polkadot 네트워크에서 개발을 시작하는 데 필요한 모든 리소스를 제공합니다. 이것은 또한 Polkadot의 개발자들이이 프로젝트에 참여하는 이유입니다. 목록에서 두 번째로 많은 커밋을 가진 저장소인 bittensor가 뒤를 이어 나옵니다.

세 번째로는 subtensor가 차지하는데, 이는 Bittensor의 substrate-chain입니다. 이 저장소는 다음을 수행합니다:

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

- Bittensor의 합의 메커니즘 실행
- 신경 정보, IP 등을 광고합니다.
- TAO를 통해 가치 이체를 용이하게 함.

## Fetch.ai

현재 Fetch.ai는 57개의 저장소를 가지고 있습니다. 하지만 우리는 상위 10개 저장소만 살펴보고 더 나은 시각화를 위해 최고 활동적인 저장소에 대해 심층적인 연구를 진행할 것입니다.

![이미지](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_21.png)

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

아래는 커밋 수를 기준으로 정렬된 상위 10개 리포지토리와 해당 GitHub 저장소 목록입니다:

- agents-aea: [링크](https://github.com/fetchai/agents-aea)
- tendermint: [링크](https://github.com/fetchai/tendermint)
- cosmos-consensus: [링크](https://github.com/fetchai/cosmos-consensus)
- cosmos-sdk: [링크](https://github.com/fetchai/cosmos-sdk)
- ledger: [링크](https://github.com/fetchai/ledger)
- ledger-archive: [링크](https://github.com/fetchai/ledger-archive)
- agents-tac: [링크](https://github.com/fetchai/agents-tac)
- cosmos-explorer: [링크](https://github.com/fetchai/cosmos-explorer)
- docs: [링크](https://github.com/fetchai/docs)
- colearn: [링크](https://github.com/fetchai/colearn)

1위는 1천 개 이상의 커밋을 기록한 agents-aea입니다. 이 저장소는 자율 경제 에이전트를 만들 수 있도록 합니다. 두 번째는 tendermint이며, Cosmos 블록체인 환경에서 매우 흔한 비잔틴 장애 허용 (BFT) 미들웨어입니다. cosmos-consensus가 세 번째로 순위되어 있습니다. 일반적으로 Fetch.ai는 여러 Cosmos 블록체인 리포지토리를 사용합니다.

## Numerai

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

Numerai은 현재 21개의 저장소가 있지만 시각화를 더 잘 하기 위해 상위 10개 저장소만 살펴보고 가장 활발한 저장소에 대해 심층 연구를 할 것입니다.

아래는 커밋 수로 정렬된 상위 10개 저장소 목록과 해당 GitHub 저장소입니다:

- numerox: https://github.com/numerai/numer
- doc: https://github.com/numerai/docs
- example-scripts: https://github.com/numerai/example-scripts
- submission-criteria: https://github.com/numerai/submission-criteria
- numerai-cli: https://github.com/numerai/numerai-cli
- doc-jp: https://github.com/numerai/docs-jp
- heroku-buildpack-polymer: https://github.com/numerai/heroku-buildpack-polymer
- numerai-predict: https://github.com/numerai/numerai-predict
- tournament-data-integrity: https://github.com/numerai/tournament-data-integrity
- tournament-contracts: https://github.com/numerai/tournament-contracts

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

numerox가 가장 많은 커밋을 가진 저장소입니다. Numerai 토너먼트 도구 상자입니다. 두 번째로 활발한 것은 Numerai에 관한 자습서와 문서가 있는 docs입니다. 세 번째로는 이름 그대로 Numerai 토너먼트를 위한 코드 예제를 보여주기 위해 할애된 example-scripts입니다.

## Ocean Protocol

현재 쓰고 있는 시점에서 Ocean Protocol은 79개의 저장소를 보유하고 있지만, 가장 활동적인 저장소를 파악하고 깊은 연구를 하기 쉽도록 상위 10개 저장소만 분석하겠습니다.

![이미지](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_23.png)

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

아래는 커밋 수로 정렬된 상위 10개 저장소 목록과 해당 GitHub 저장소가 있습니다:

- pdr-docs: https://github.com/oceanprotocol/pdr-docs
- docs: https://github.com/oceanprotocol/docs
- ocean.js: https://github.com/oceanprotocol/ocean.js
- marketplace-launchpad: https://github.com/oceanprotocol/marketplace-launchpad
- market: https://github.com/oceanprotocol/market
- waves: https://github.com/oceanprotocol/waves
- df-web: https://github.com/oceanprotocol/df-web
- contracts: https://github.com/oceanprotocol/contracts
- ocean.py: https://github.com/oceanprotocol/ocean.py
- aquarius: https://github.com/oceanprotocol/aquarius

Ocean Protocol 프로젝트에서 문서 작성이 중요합니다. 가장 활발한 두 저장소는 문서와 관련이 있으며, 첫 번째는 Predictoor를 위한 것이고 두 번째는 일반 스택을 위한 것입니다. 차트는 Predictoor가 팀이 우선순위를 두고 있는 도구라는 것을 시사하며, 이는 높은 커밋 수와 매우 상세한 문서화로 확인할 수 있습니다. 세 번째로는 Ocean의 프로토콜 기술을 사용할 수 있게 해주는 ocean.js가 있습니다. 커밋 수를 보면, JavaScript가 Python보다 더 많이 요청된다고 가정할 수 있습니다. 왜냐하면 ocean.py 패키지도 있지만 활동이 훨씬 적습니다.

네 번째로, 개발자들이 자신만의 마켓플레이스를 만드는 방법을 가르치는 marketplace-launchpad이 있습니다.

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

## Oraichain

Oraichain은 109개의 저장소가 있습니다. 그러나 시각화를 용이하게 하고 가장 활발한 것을 더 깊이 연구하기 위해 상위 10개 저장소만 보겠습니다.

![이미지](/assets/img/2024-06-30-HowtoAnalyseGitHubDevelopmentActivitywithPython_24.png)

아래는 커밋 수에 따라 정렬된 상위 10개 저장소 목록이며, 각 저장소의 GitHub 저장소입니다:

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

- owallet: https://github.com/oraichain/owallet
- oraiswap-frontend: https://github.com/oraichain/oraiswap-frontend
- oraiscan-frontend: https://github.com/oraichain/oraiscan-frontend
- keplr-extension-orai: https://github.com/oraichain/keplr-extension-orai
- oraidex-sdk: https://github.com/oraichain/oraidex-sdk
- smart-studio: https://github.com/oraichain/smart-studio
- orai: https://github.com/oraichain/orai
- oraiswap: https://github.com/oraichain/oraiswap
- oraiwasm: https://github.com/oraichain/oraiwasm
- cosmosjs: https://github.com/oraichain/cosmosjs

위 차트를 보면 owallet이 가장 활동적인 프로젝트임을 알 수 있습니다. 이 지갑은 Cosmos 및 EVM 통합을 지원합니다. 두 번째로 활발한 저장소는 Oraichain DEX의 일부인 oraiswap-frontend입니다. 가장 활발한 3번째 저장소는 Oraichain을 위한 블록 익스플로러인 oraiscan-frontend입니다.

## 싱귤래리티넷

싱귤래리티넷은 목록에서 가장 많은 저장소(116개)를 보유한 프로젝트입니다. 그러나 더 나은 시각화를 위해 상위 10개 저장소만 살펴보고 각각에 대해 약간 깊이 파고들어보겠습니다.

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

아래는 커밋 수에 따라 순서가 매겨진 상위 10개 저장소 목록과 해당 GitHub 저장소가 있습니다:

- snet-marketplace-service: https://github.com/singnet/snet-marketplace-service
- snet-dapp: https://github.com/singnet/snet-dapp
- snet-daemon: https://github.com/singnet/snet-daemon
- dev-portal: https://github.com/singnet/dev-portal
- snet-cli: https://github.com/singnet/snet-cli
- airdrop-services: https://github.com/singnet/airdrop-services
- snet-betav1-dapp: https://github.com/singnet/snet-betav1-dapp
- offernet: https://github.com/singnet/offernet
- ai-dsl: https://github.com/singnet/ai-dsl
- airdrop-dapp: https://github.com/singnet/airdrop-dapp

가장 많은 커밋을 가진 저장소는 snet-marketplace-service입니다. 이 저장소는 SingularityNET의 시장을 사용하는 방법을 가르쳐 줍니다. 프로젝트의 탈중앙화 애플리케이션(snet-dapp)을 구축하는 데 사용된 저장소가 두 번째로 활동적입니다. 세 번째로 커밋 수가 많은 저장소는 SingularityNET 데몬이고, 그 다음은 플랫폼 및 시장에 대한 많은 문서를 제공하는 dev-portal입니다. 또한 두 개의 에어드랍에 전념한 저장소도 있습니다.

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

이 문서는 여러 프로젝트 내 개발자 활동에 대한 포괄적인 견해를 제공합니다. 프로젝트 성장과 개발자 참여의 지표로서 커밋 및 저장소 사용의 중요성을 강조하며, 주요 사건이 개발 활동에 미치는 영향을 보여주고, 연도별 활동을 기반으로 저장소를 순위로 나열합니다.

우리는 좋은 데이터 처리 방법론과 몇 개의 바 차트를 통해 각 프로젝트의 활동에 대해 풍부한 정보를 얻을 수 있었습니다. 요약하자면, Python과 데이터 분석에 대한 충분한 지식을 가지고 있으면 스테이크홀더, 투자자 및 경쟁업체가 GitHub 개발 활동에 기반한 정보에 근거하여 판단을 내릴 수 있습니다.

전체 보고서는 [이 링크](링크)에서 찾아볼 수 있습니다.