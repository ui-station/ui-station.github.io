---
title: "XZ의 사회 공학"
description: ""
coverImage: "/assets/img/2024-05-20-TheSocialEngineeringofXZ_0.png"
date: 2024-05-20 21:27
ogImage:
  url: /assets/img/2024-05-20-TheSocialEngineeringofXZ_0.png
tag: Tech
originalTitle: "The Social Engineering of XZ"
link: "https://medium.com/asecuritysite-when-bob-met-alice/the-social-engineering-of-xz-f905084438fc"
---

![Image1](/assets/img/2024-05-20-TheSocialEngineeringofXZ_0.png)

It sounds like one of those Hollywood scripts where an evil genius hacks into a core part of the Internet and inserts a backdoor. They can then listen to everyone’s secret communications without being detected. But, it’s not science fiction, as someone — “Jia Tan” — actually created a backdoor in SSH, and nearly got away with it. So, let’s investigate the most critical vulnerability since Heartbleed: the XZ backdoor.

The XZ vulnerability is a serious flaw that has been rated with a CVSS value of 10. This is the maximum level possible and requires that related systems should be urgently patched. Overall, the related backdoor in the XZ library seems to have been planted by Jia Tan (which with the nickname of JiaT75), and they managed to gain admin rights to the XZ GitHub by showcasing his/her talents:

![Image2](/assets/img/2024-05-20-TheSocialEngineeringofXZ_1.png)

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

Jia Tan은 라이브러리에 백도어를 삽입한 사람 또는 그룹으로, 코드에 소셜 엔지니어링 공격을 가했습니다. 이 공격은 수년에 걸쳐 이루어졌을 가능성이 높습니다. 또한 다수가 이 이메일이 AI에 의해 생성된 것으로 보인다고 관찰했으며, 아마도 국가 주체의 활동을 가리킬 수도 있습니다. 대상은 Linux 서버이며, 취약점은 마이크로소프트 직원(포스트그레SQL 개발자인 Andres Freund)이 발견했습니다.

![이미지](/assets/img/2024-05-20-TheSocialEngineeringofXZ_2.png)

## 타임라인

2005년부터 2008년까지 Lasse Collins 등이 .xz 파일 형식을 만들었는데, 이는 LZMA 압축을 사용했습니다. 이는 많은 Linux 커널의 핵심 부분이 되었습니다. 2021년부터 2022년에 걸쳐 Jia는 zx-devel 메일링 리스트에 여러 패치를 게시했습니다. "Jigar Kumar"라는 사용자가 메일링 리스트에 여러 게시물을 작성하면서 Jia의 패치가 적용되지 않았다고 물었습니다. Lasse는 2022년 6월 이에 대해 응답했습니다:

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

Jia의 첫 주요 업데이트 커밋은 이 글 쓰기 바로 직후 Lasse로부터 이루어졌고, 작성자로 Jia가 지정되었습니다. Jigar는 여전히 업데이트가 느리다고 불평했습니다. 또한, 다른 사람들도 Dennis Ens를 통해 Lasse에 압박을 가했습니다:

이 시점에서 Lasse는 Jia가 프로젝트에서 더 큰 역할을 맡아야 한다고 보고, Jigar는 여전히 Jia가 더 큰 역할을 맡아야 한다고 압박했습니다. 이후에 Lasse는 Jia에게 저장소 유지보수자로서의 역할을 맡길 것을 허용했습니다. Jigar나 Dennis는 인터넷 상에 어디에도 나타나지 않으며, Jia에게 접근할 수 있도록 압박을 가했던 가짜 ID일 가능성이 높습니다. 그러나 2022년 9월, Jia는 5.4.0 릴리스를 개요로 제시했으며, 2022년 11월에는 Lasse Collin과 Jia Tan이 프로젝트 유지보수자로 제시되었습니다.

Jia가 GitHub 커밋에 처음 나타난 첫 번째 신호는 2024년 1월에 있었으며, 버전 5.4.1이 출시되었습니다. 2024년 2월에는 테스트 파일에 숨겨진 백도어 코드를 통합하고, xz-5.6.0.tar.gz 배포로 버전 5.6.0을 배포했습니다. 백도어가 설치된 파일인 build-to-host.m4은 GitHub 저장소에는 나타나지 않습니다. 2024년 3월에 Jia는 버전 5.6.1에 새로운 백도어를 만들어 푸시했습니다. 2024년 3월 28일, Andres Freund가 백도어를 발견하고, RedHat은 CVE-2024–3094를 할당했습니다.

2024년 2월과 3월 경 Jia는 백도어 코드를 포함한 5.6.0 및 5.6.1 두 버전을 커밋했고, 그 후 Ubuntu, Red Hat 및 Debian 개발자들에게 새 버전을 운영 체제에 통합하도록 요청했습니다. 통합한 사람들은 [여기]에서 확인할 수 있습니다:

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

![이미지](/assets/img/2024-05-20-TheSocialEngineeringofXZ_3.png)

## 완벽한 10

그래서 대부분의 디지털 세계는 Microsoft Windows가 아니라 Linux로 이루어져 있습니다. Linux는 대부분의 서버를 구동하며 많은 스마트 기기에 내장되어 있습니다. 이제 XZ utils의 새로운 백도어가 가능한 최고의 리스크 등급으로 평가되었습니다 [여기]:

![이미지](/assets/img/2024-05-20-TheSocialEngineeringofXZ_4.png)

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

백도어가 zx의 tarballs(Version 5.6.0 이후) 내에서 발견되었고, 감지를 피하기 위해 난독화되었습니다. 그럼 이는 liblzma 라이브러리 내의 코드를 수정하는 데 사용되며, 데이터를 가로채고 수정할 수 있습니다. 중요한 발견 중 하나는 OpenSSH 데몬이 백도어의 영향을 받았지만, liblzma에 직접적으로 연결되지는 않았다는 것입니다.

전반적으로 xz 코드는 XZ 파일을 작성하며 데이터 스트림 및 블록을 통합하며, 읽을 때 빠른 랜덤 액세스를 지원합니다. 이는 lzma 모듈 내에서는 지원되지 않으며, 랜덤 액세스 질의 시 이전 블록을 모두 읽어야 합니다. 여기서의 예시는 다음과 같습니다:

```js
>>> with xz.open('example.xz') as fin:
...     fin.read(18)
...     fin.stream_boundaries  # 2 streams
...     fin.block_boundaries   # 4 blocks in first stream, 2 blocks in second stream
...     fin.seek(1000)
...     fin.read(31)
...
b'Hello, world! \xf0\x9f\x91\x8b'
[0, 2000]
[0, 500, 1000, 1500, 2000, 3000]
1000
b'\xe2\x9c\xa8 Random access is fast! \xf0\x9f\x9a\x80'
```

xz에 대한 관련 GitHub 리포지토리 중 많은 리포지토리가 비활성화되었거나 오프라인으로 전환되었습니다 [여기]:

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

![이미지](/assets/img/2024-05-20-TheSocialEngineeringofXZ_5.png)

취약점의 심각성을 강조할 수 없을 만큼, Anthony Weems가 백도어를 역공학하여 RCE (원격 코드 실행)를 위한 프루프 오브 컨셉 익스플로잇을 생성했습니다. [여기]에서 확인할 수 있습니다.

![이미지](/assets/img/2024-05-20-TheSocialEngineeringofXZ_6.png)

관련 GitHub은 여기에서 확인하실 수 있습니다.

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

적절한 상황에서 외부 사용자가 sshd 인증을 회피하고 시스템에 완전 액세스를 얻을 수 있습니다. 이는 OpenSSH의 RSA_public_decrypt 함수를 제 3자 통합을 통해 대체함으로써 발생합니다. 일반적으로 OpenSSH는 liblzma를 사용하지 않지만, 악의적인 코드로 인해 통합될 수 있습니다.

## 결론

만약 GitHub을 운영하고, 다른 사용자들이 참여하길 원한다면, 영상 통화를 통해 직접 만나서 상대방을 확인해보세요. 그리고 상대방의 신원을 확인해보세요.
