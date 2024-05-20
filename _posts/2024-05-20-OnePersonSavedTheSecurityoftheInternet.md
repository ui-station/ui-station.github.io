---
title: "한 사람이 인터넷의 보안을 구했다"
description: ""
coverImage: "/assets/img/2024-05-20-OnePersonSavedTheSecurityoftheInternet_0.png"
date: 2024-05-20 21:23
ogImage: 
  url: /assets/img/2024-05-20-OnePersonSavedTheSecurityoftheInternet_0.png
tag: Tech
originalTitle: "One Person Saved The Security of the Internet"
link: "https://medium.com/@billatnapier/one-person-saved-the-security-of-the-internet-9e8ed6d40df6"
---


```markdown
![Image](/assets/img/2024-05-20-OnePersonSavedTheSecurityoftheInternet_0.png)

태양 피해 공격은 위협 요소가 소프트웨어 공급망 공격으로 이동하고, 소프트웨어의 전달 과정에서 취약한 활동을 침투하는 것을 보여주었습니다. 이러한 위협 요소들은 종종 고급 공격 도구를 만들기 위해 시간, 자금 및 전문성을 갖고 있으며, 그들의 공격 방법은 소프트웨어 패치와 시스템 업그레이드를 통해 감지되지 않을 수 있습니다.

그래서 누군가 (아니면, 더 정확히는 일부 국가/법 집행 기관)가 인터넷에 백도어를 설치하려고 했으며, 방송 매체는 거의 전혀 이야기에 언급하지 않았습니다. 놀랍게도, 사이버 보안 정보 기관 중 많은 기관들은 기본적으로 조언만 반복하는 것 외에는 조용했습니다. 루머를 퍼뜨리고 싶지 않지만... [거기로 가면 안 되겠죠, "그들"이 감시 중일 수 있으니까].

## 안드레스 프로인트
```  

<div class="content-ad"></div>

사회공학의 이야기이자 APT(Advanced Persistent Threat)의 가장 좋은 예시 중 하나로, 뛰어난 기술력을 자랑하는 사례입니다. 그리고 마치 Scooby Doo에서 말했던 것처럼, "그 성가신 개발자가 방해 안 한다면 우리는 성공했을 거야" 그랬죠. 그 성가신 개발자는 Microsoft의 소프트웨어 엔지니어인 38세의 Andres Freund입니다. 그의 주요 업무는 PostgreSQL 소프트웨어를 개발하는 것입니다.

Andres는 이를 통해 어떤 소프트웨어 도구가 왜 느린지에 대해 몇 가지 테스트를 진행했고, Linux 운영 체제에서 서드파티 라이브러리 내에 백도어를 발견했는데, 이는 SSH 서버 응용 프로그램(OpenSSH)과 관련이 있었습니다. 이 응용 프로그램은 클라우드 시스템에 로그인하거나 개인 GitHub 저장소에 인증하는 데 사용됩니다.

## Jia Tan

문제는 LZMA라는 일반적으로 사용되는 압축 방법과 관련이 있으며 XZ 프로젝트에서 제공됩니다. 성공적으로 공격자가 백도어를 설치하여 원격으로 제어할 수 있는 시스템에 침입할 수 있었습니다. 이제 취약점 등급이 10으로 지정된 CVE–2024–3094로 정의되었습니다. 악의적인 코드는 이제 "Jia Tan"이라는 위협 요소에게로 연결되었는데, 그의 GitHub 계정은 2021년부터 거슬러 올라가며, 2022년에 처음 XZ 프로젝트에 참여했습니다.

<div class="content-ad"></div>

그것은 악의를 품은 천재가 인터넷의 핵심 부분에 침투하여 후문을 삽입하는 할리우드 시나리오 중 하나 같은 느낌이었죠. 그 후 그들은 누구도 감지되지 않고 모두의 비밀 통신을 청취할 수 있습니다. 하지만 이것은 공상 과학이 아니에요. 실제로 "Jia Tan"이라는 사람이 SSH에 후문을 만들었고 거의 미궁에 빠져들 뻔했습니다. 그래서 하트블리드 이후 가장 심각한 취약점인 XZ 후문을 조사해봅시다.

XZ 취약점은 CVSS 값이 10으로 평가된 심각한 결함입니다. 이것은 가능한 최고 수준이며 관련 시스템에 즉시 패치되어야 합니다. 전반적으로 XZ 라이브러리의 관련 후문은 Jia Tan(별명 JiaT75)이 심어놓은 것으로 보입니다. 그들은 자신의 재능을 선보이며 XZ GitHub의 관리자 권한을 얻었습니다.

Jia Tan은 이 라이브러리에 후문을 삽입한 사람 또는 그룹입니다. 그들은 코드에 대한 사회 공학 공격에 참여했습니다. 이 공격은 수년 동안 진행된 것으로 보입니다. 많은 사람들이 이 이메일이 인공지능에 의해 생성된 것처럼 보인다고 관찰했고, 아마도 국가 주체의 활동을 의미하는 표시입니다. 목표는 Linux 서버이며 취약점은 마이크로소프트 직원 (PostgreSQL 개발자인 Andres Freund)에 의해 발견되었습니다. [여기]에서 확인하세요.

<div class="content-ad"></div>

```markdown
![One Person Saved The Security of the Internet](/assets/img/2024-05-20-OnePersonSavedTheSecurityoftheInternet_2.png)

## 타임라인

2005년부터 2008년까지, Lasse Collins와 다른 사람들은 .xz 파일 형식을 만들었으며, 이는 LZMA 압축을 사용합니다. 이는 많은 Linux 커널의 핵심 부분이 되었습니다. 2021년과 2022년에는 Jia가 xz-devel 메일링 리스트에 여러 패치를 게시합니다. “Jigar Kumar”라는 사용자가 메일링 리스트에 여러 게시물을 작성하여 Jia의 패치가 적용되지 않은 이유를 묻습니다. Lasse는 2022년 6월에 다음과 같이 응답했습니다:

Jia의 최초의 중요한 업데이트 커밋은 이 게시물 이후에 발생했으며, Jia가 저자로 정의되었습니다. 그럼에도 불구하고 Jigar는 여전히 느린 업데이트를 불평했습니다. Dennis Ens를 비롯한 다른 사람들도 Lasse에게 압박을 가하였습니다:
```

<div class="content-ad"></div>

이 시점에서 Lasse는 Jia가 프로젝트에서 더 큰 역할을 맡아야 한다고 보고했으며, Jigar는 계속해서 Jia가 더 큰 역할을 맡아야 한다고 주장했습니다. 이후 Lasse는 Jia에게 저장소 관리자의 역할을 맡길 것을 허락했습니다. Jigar나 Dennis는 인터넷 상에서 찾을 수 없으며, Jia가 액세스를 얻도록 노력한 가짜 ID일 가능성이 높습니다. 그러나 2022년 9월에는 Jia가 5.4.0 릴리스를 개괄하고, 2022년 11월까지 Lasse가 README에 프로젝트 관리자로 Lasse Collin과 Jia Tan이 있다고 기술했습니다.

GitHub 커밋에서 Jia의 첫 흔적은 2024년 1월에 나타났으며, 버전 5.4.1을 릴리스했습니다. 2024년 2월에는 백도어 코드를 테스트 파일에 숨겼으며, xz-5.6.0.tar.gz 배포로 버전 5.6.0을 배포했습니다. 백도어가 숨겨진 파일인 build-to-host.m4은 GitHub 저장소에 나타나지 않았습니다. 2024년 3월까지 Jia는 새로운 백도어를 버전 5.6.1에 만들어 배포했습니다. 2024년 3월 28일, Andres Freund가 백도어를 발견하고, RedHat은 CVE-2024–3094를 할당했습니다.

2024년 2월과 3월 사이, Jia는 5.6.0과 5.6.1 두 버전을 커밋했는데, 이는 백도어 코드를 포함하고 있었습니다. 그리고 이후에 Ubuntu, Red Hat, Debian의 개발자들에게 새 버전을 시스템에 통합하도록 요청했습니다. 통합한 사람들은 [여기]에 있습니다:

![image](/assets/img/2024-05-20-OnePersonSavedTheSecurityoftheInternet_3.png)

<div class="content-ad"></div>

## 완벽한 10점 평가

우리의 디지털 세계의 대부분은 마이크로소프트 윈도우가 아니라 리눅스로 이루어져 있습니다. 리눅스는 대부분의 서버를 구동하고 많은 스마트 기기에 내장되어 있습니다. 이제 XZ utils의 새로운 배키도어가 최고 리스크 등급으로 평가되었습니다.

![이미지](/assets/img/2024-05-20-OnePersonSavedTheSecurityoftheInternet_4.png)

배키도어는 xz의 tarballs(버전 5.6.0부터)에서 발견되었으며, 감지를 피하기 위해 은폐되어 있었습니다. 이는 liblzma 라이브러리 내의 코드를 수정하고 데이터를 가로채고 수정하는 데 사용됩니다. 하나 주목할 만한 발견은 OpenSSH 데몬이 해당 배키도어의 영향을 받았지만, 직접적으로 liblzma에 연결되어 있지 않았다는 것입니다.

<div class="content-ad"></div>

xz 코드는 XZ 파일을 생성하고 데이터 스트림과 블록을 통합하며, 읽을 때 빠른 무작위 액세스를 지원합니다. 이는 lzma 모듈 내에서 지원되지 않으며, 무작위 액세스 쿼리에서 이전 블록을 모두 읽어야 합니다. 여기서 예제가 있습니다:

```js
>>> with xz.open('example.xz') as fin:
...     fin.read(18)
...     fin.stream_boundaries  # 2 스트림
...     fin.block_boundaries   # 첫 번째 스트림에 4개의 블록, 두 번째 스트림에 2개의 블록
...     fin.seek(1000)
...     fin.read(31)
...
b'Hello, world! \xf0\x9f\x91\x8b'
[0, 2000]
[0, 500, 1000, 1500, 2000, 3000]
1000
b'\xe2\x9c\xa8 Random access is fast! \xf0\x9f\x9a\x80'
```

xz와 관련된 많은 GitHub 저장소들이 오프라인이거나 비활성화되었습니다: [여기](https://github.com/).

이제 table 태그를 Markdown 형식으로 바꿨습니다. 페이지를 다시 열어보시면 변경된 내용을 확인할 수 있습니다!

<div class="content-ad"></div>

The seriousness of the vulnerability cannot be emphasized enough. Anthony Weems successfully reverse engineered the backdoor and developed a proof-of-concept exploit for Remote Code Execution (RCE) [here]:

![Proof of Concept](/assets/img/2024-05-20-OnePersonSavedTheSecurityoftheInternet_6.png)

Under certain conditions, an external attacker could bypass sshd authentication and gain full system access. This exploit involves replacing the RSA_public_decrypt function in OpenSSH with a third-party integration. Although OpenSSH does not typically use liblzma, the malicious code enables its integration.

# 결론

<div class="content-ad"></div>

만약 GitHub를 운영하고 다른 사람들이 참여하도록 하려면, 비디오 링크를 통해 직접 상대방과 대화하고 그들의 신원을 확인하세요. APT에서는 자주 시간, 자원 및 전문지식을 가진 위협 요소들을 볼 수 있으며, 그들은 쉽게 어떤 시스템이든 침투할 수 있습니다. AI의 급부상으로, 이 모든 것이 몇 단계 높아지고 영상 및 오디오 사용이 공격의 일부로서 활용될 것을 볼 것입니다.