---
title: "ALTRAD8UD-1L2T에 GPU를 사용할 수 있도록 하는 빠른 업데이트"
description: ""
coverImage: "/assets/img/2024-05-18-AquickupdateonALTRAD8UD-1L2TmakingitworkwithGPU_0.png"
date: 2024-05-18 19:02
ogImage:
  url: /assets/img/2024-05-18-AquickupdateonALTRAD8UD-1L2TmakingitworkwithGPU_0.png
tag: Tech
originalTitle: "A quick update on ALTRAD8UD-1L2T (making it work with GPU)"
link: "https://medium.com/@boredengineer/a-quick-update-on-altrad8ud-1l2t-making-it-work-with-gpu-9cc925e6b769"
---

동일한 주제에 대한 다른 기사들:

- 첫 번째: ASRock Rack ALTRAD8UD-1L2T (Ampere Altra)의 첫 인상
- 두 번째 (이 기사): ALTRAD8UD-1L2T에 대한 빠른 업데이트 (GPU와 함께 작동시키기)

# 소개

이것은 Ampere Altra 보드에 대한 첫 인상의 빠른 업데이트일 것입니다. 그래서 그림은 게시글의 어딘가에서 찍은 것이 아니라 제 고양이 중 한 마리와 함께 있을 것입니다.

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

모든 것이 도착할 때까지 기다리는 동안 서버를 구축하고 있어요 (샤시를 구하고 보드의 절반 가격을 내지 않아도 시간이 걸릴 것 같아서...); 아파트에 있는 랜덤한 PCIe 장치를 연결해서 작동시키려고 노력하고 있어요. 당연히 먼저 시도할 것은 GPU에요.

솔직히 말해서 많지는 않아요. HDMI/DP 또는 DVI 출력만 합리적으로 사용할 수 있는 몇 개가 있어요. 그러나 Altra에서 게임을 할 기회를 갖고 싶다면 몇 가지 선택지가 있어요:

- RX 550 — 오래 동안 amdgpu에서 지원된 Polaris입니다; 또한 DCN의 Floating Point 명령 같은 특별한 것은 없어서 비교적 좋은 호환성을 가지고 있어요.
- RX 5700은 채굴 랙에서 뽑아낸 것일 가능성이 높은 싸게 구한 버전이에요; 랜덤한 시스템에 꽂아도 충분히 좋아요. 작동 상태이지만 얼마나 오래 갈지는 모르겠어요. 이 카드는 DCN을 사용하고 있어요.
- Intel Arc 750 — RX 5700와 성능적으로 대체로 비슷한 양호한 GPU인 것 같아서 가져왔어요. 상대적으로 좋은 RayTracing을 가지고 있고, Intel은 Linux(x86/x86-64)에서 잘 작동한다는 신뢰를 가지고 있어요.

# RX 550

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

RX550은 지루하지만 제대로 작동하는 것 같아요:

스크린샷의 품질이 떨어져서 죄송해요; 저는 싸고 간단한 USB-HDMI 캡처 카드를 사용해서 찍었어요. 그리고 제가 곧 글을 쓸 계획은 없었기 때문에, 제가 누군가에게 보여준 여러 종류의 메시지에서 이 스크린샷을 얻었어요.

해당하는 커널은 기본 커널을 실행하고 있어요, 그래서 거기서 아무것도 바꾸지 않았어요.

# RX 5700

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

실행하는 것은 훨씬 더 어려웠어요. 먼저, 6.1 커널은 ARM에서 FP 레지스터를 저장하지 않는다(잘못 기억했다면 고쳐주세요), 이는 DCN 1.0 이상을 지원하는 카드에서 필요합니다. 더욱이, Ampere Altra에는 PCIe에 관한 버그가 있어서 일부 장치가 작동하지 않습니다. 이를 "Ampere Altra erratum #82288 PCIE_65"라고 부르며, 이미 일부 Linux 배포판에 통합되어 있습니다. 이 문제에 대한 커뮤니티 포럼에서 토론이 진행 중입니다.

만약 그 패치를 적용하지 않으면, amdgpu가 카드를 초기화하지 못할 것입니다. 6.9-rc6 커널(내가 시도한 가장 최신 버전)을 사용해도 다음과 같은 메시지가 표시됩니다:

때로는 부팅이 되기는 하지만, 그래픽 오류나 시스템 멈춤이 발생할 수 있습니다.

하지만 패치를 적용하면 잘 작동합니다:

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

![Image](/assets/img/2024-05-18-AquickupdateonALTRAD8UD-1L2TmakingitworkwithGPU_0.png)

# Intel Arc 750

이건 웃긴 게 한 가지 있어요. 그 이유는 인텔 i915 커널 드라이버가 비-x86 장치에서 동작하지 않기 때문이에요. 그들은 최근 GPU에 더 맞춰진 새로운 Xe 커널 드라이버를 업스트림으로 올리기 시작했어요. 하지만, Altra에서 그것을 사용하려고 하면, 기본적으로 다음과 같은 결과를 얻을거에요:

조금 더 찾아보았더니, 초기화하고 이미지를 출력하는 지저분한 해킹을 발견했어요 (2D만 가능하며, 더 복잡한 메사 부분이 있을 수도 있어요):

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

![이미지](/assets/img/2024-05-18-AquickupdateonALTRAD8UD-1L2TmakingitworkwithGPU_1.png)

어떤 부분을 효과적으로 주석 처리했나요? 설명에 따르면 intel_vga_reset_io_mem은 VGA Console이라는 모듈과 호환성을 보장하기 위한 함수인데, 해당 모듈이 사용하는 레지스터를 건드리지 않으면 락업이 발생한다고 합니다. 그 함수가 하는 일에 대해 자세히 설명하는 주석이 있습니다. 그러나 (오래된 플랫폼 중 하나를 제외하고는) ARM에서는 VGA Console이 작동하지 않습니다. 따라서 테스트를 위해 주석 처리해도 무방할 것입니다.

그렇게 하면, 어느 정도 시간이 지난 후 (특히 drm 디버그 로깅이 활성화된 상태에서는 드라이버가 초기화되기까지 일정 시간이 소요됩니다):

![이미지](/assets/img/2024-05-18-AquickupdateonALTRAD8UD-1L2TmakingitworkwithGPU_2.png)

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

사진을 받았어요! ARM에서 실행 중인 인텔 GPU의 HDMI 출력!

여전히 3D가 없는 이유는 두 가지 때문입니다:

- debian 테스팅에 포함된 Mesa의 업그레이드된 버전이 필요합니다 (지난 기사 이후로 테스팅으로 업그레이드했습니다).
- Xe를 처리하는 i915 갈리움 드라이버가 x86/x86-64 전용으로 표시되어 있습니다. 그래서 이것을 작동시키는 것이 매우 어려울 것입니다 (특히 저는 mesa 코드베이스에 익숙하지 않기 때문에).

변경: 리눅스에서 인텔 GPU를 사용한 지 얼마 되지 않아서 Xe와 인텔 Arc가 여전히 i915로 처리된다는 잘못된 인상을 가지고 있었습니다. 실제로는 더 최신 iris 드라이버에 의해 처리됩니다. 그래서 여기에서 말한 내용은 무시해 주세요.

하지만 승리는 승리입니다. 작은 승리라도요. 이제 위에 있는 수정을 upstream으로 할 필요가 있고, 솔직히 어떤 종류의 패치가 거기서 허용될지 확실하지 않습니다. 현재 제 추측은 CONFIG_VGA_CONSOLE에 빨간불을 켜면 괜찮을 것 같지만, 우리가 보겠죠.

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

## 같은 날 나중에 업데이트

Debian에서 ARM에 intel-specific 라이브러리를 활성화하지 않기 때문에 libdrm을 다시 컴파일하는 데 몇 시간을 보냈습니다. 또한, debian-testing의 mesa도 오래되었고 거기서 Xe는 x86/x86-64 아키텍처로 제한되어 있어서 24.1.0-rc에서는 aarch64로 컴파일할 때 기본 드라이버 목록으로만 작동해야 할 것이지만 RayTracing을 제외하고, 그것은 다른 이야기입니다. 그런데 결국 아무것도 작동하지 않게 되었습니다.

gdm을 시작하려고 시도하면 바로 다음과 같이 오류가 발생하고:

"guc ids might be different"라는 메시지가 표시되고 결국 다음과 같은 메시지로 잠겨 버립니다:

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

커서가 렌더링되어 있었지요.

포기하려던 찰나, 그러던 중에 오류를 찾아보기로 결정하였어요. Xe 개발자들의 자동화 시스템에서도 해당 오류가 발견되었다는 것을 알게 되었어요. 그들의 저장소에서 일부 패치가 드라이버를 전반적으로 더 안정적으로 만들었다는 것을 발견했죠. 그래서 저는 그들의 브랜치를 가져오기로 결정했어요 (커널 6.10을 위한 drm-next일 것으로 예상됩니다) 그리고 이게 작동하는지 확인해 보기로 했어요.

다시 컴파일한 후에 (그리고 이전의 패치를 다시 적용한 후), 이게... 전혀 작동하지 않았어요. 동일한 메시지로 드라이버가 실패했죠: "여러 번의 정지가 발생하고 있습니다."

BIOS 설정을 좀 만져보고(일부 조작설정이 작동하거나 꺼지는 점을 발견했어요) 저는 또 기억했어요, 해당 커널에 앰페어의 PCIe 버그 수정을 다시 적용하지 않았다는 점을요.

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

그 후에 GDM을 시작했어요...

![image](/assets/img/2024-05-18-AquickupdateonALTRAD8UD-1L2TmakingitworkwithGPU_3.png)

그리고 멈췄어요. 하지만 드라이버가 실험적이라고 생각해서 다시 시도하기로 결정했어요. 이번에는 더 멀리 갔고 OpenGL이 작동 중이라는 멋진 스크린샷을 얻었어요.

![image](/assets/img/2024-05-18-AquickupdateonALTRAD8UD-1L2TmakingitworkwithGPU_4.png)

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

실제로이 업데이트를 쓰는 동안 바로 내 옆에 놓여져 있었고 아직도 고장 나지 않았어요. 빠르지는 않지만 실험용 드라이버에서 기대할 수 있는 것입니다.

이제 아마도 Mesa 빌드 시스템 변경 사항을 upstream하고 몇 가지 버그 보고를 더 제출해야 할 것 같아요...

업데이트: upstreamd해야 할 유일한 변경 사항은 AArch64용 intel-rt를 활성화하는 것뿐이고, 다른 변경 사항들은 Debian이 Mesa를 빌드하는 방식과 관련이 있어서 upstream Mesa와는 무관합니다.

# 결론 대신

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

그저 현 상황에 대한 간단한 노트입니다. 저는 정말로 Ampere가 언젠가 PCIe 버그에 대한 해결책을 업스트림으로 올릴 것이고 사람들이 자체 커널 빌드를 유지할 필요가 없게 될 것을 희망합니다. 그것이 아직 완료되지 않았다는 것은 저를 놀라게 합니다.

만약 그 기사를 읽은 이유가 고양이의 사진을 보기 위해서라면, 여기 있습니다:

![cat](/assets/img/2024-05-18-AquickupdateonALTRAD8UD-1L2TmakingitworkwithGPU_5.png)
