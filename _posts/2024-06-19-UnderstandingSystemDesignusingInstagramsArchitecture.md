---
title: "인스타그램 아키텍처를 활용한 시스템 디자인 이해하기 "
description: ""
coverImage: "/assets/img/2024-06-19-UnderstandingSystemDesignusingInstagramsArchitecture_0.png"
date: 2024-06-19 11:33
ogImage:
  url: /assets/img/2024-06-19-UnderstandingSystemDesignusingInstagramsArchitecture_0.png
tag: Tech
originalTitle: "Understanding System Design using Instagram’s Architecture 💡"
link: "https://medium.com/@nolitarego/understanding-system-design-using-instagrams-architecture-51ffc8edbcf6"
---

시스템 디자인은 소프트웨어 엔지니어에게 중요한 기술이에요, 특히 직무 면접 중에요. 이는 중요한 개념을 이해하고 실전 상황에서 제한 사항과 함께 활용할 수 있는 능력을 시험해요. 시스템 디자인을 마스터하려면 서로 다른 구성 요소와 그들이 어떻게 작동하여 확장 가능하고 신뢰성 있고 유지보수가 쉬운 시스템을 구축하는지 이해해야 해요. 이 안내서는 시스템 디자인의 중요성을 설명하고 시스템 아키텍처의 세부적인 분석을 제공할 거에요: Instagram! 🚀

![시스템디자인](/assets/img/2024-06-19-UnderstandingSystemDesignusingInstagramsArchitecture_0.png)

## 시스템 디자인이란? 💡

시스템 디자인은 시스템이 어떻게 작동할지 계획하는 것이에요. 전반적인 구조, 개별 부분, 상호 작용 방식, 사용하는 데이터를 결정하는 것을 포함해요. 이 과정은 시스템 내 모든 요소가 특정 기능, 성능, 확장성 및 신뢰성을 충족하기 위해 잘 작동하는지 확인해요. 이는 고수준 아키텍처적 결정(큰 그림)뿐만 아니라 전체 시스템이 원활하게 실행되도록 상세한 저수준 구성 요소를 포함해요.

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

## 시스템 설계가 왜 중요한가요?

시스템 설계는 수백만 명의 사용자를 처리하면서도 원활하게 작동하고 신뢰할 수 있는 앱을 만드는 데 필수적입니다. 중요한 결정을 내려 구조, 기술 스택 및 설계 방법에 대해 고민해야 합니다. 시스템 설계 인터뷰는 이러한 선택 사항을 얼마나 잘 분석하고 효율적이고 확장 가능하며 신뢰할 수 있는 시스템을 만들 수 있는지를 테스트합니다.

## 정복해야 할 주요 개념:

시스템 설계를 다루기 전에 이러한 기본 개념을 숙지하는 것이 중요합니다:

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

- 도메인 이름 시스템 (DNS): 글로벌 접근성을 위해 도메인 이름을 IP 주소로 변환합니다.
- 로드 밸런서: 다수의 서버로 들어오는 네트워크 트래픽을 분산하여 높은 가용성과 성능을 보장합니다.
- API 게이트웨이: 다양한 마이크로서비스들을 위한 통합된 입구를 제공하여 통신과 관리를 간편하게 합니다.
- CDN (콘텐츠 전송 네트워크): 사용자에게 더 가까운 곳에 콘텐츠를 캐싱함으로써 전 세계적으로 미디어 콘텐츠를 낮은 지연 시간으로 제공합니다.
- 포워드 프록시 vs 리버스 프록시: 보안과 성능을 위해 클라이언트와 서버 사이 (포워드) 또는 서버와 클라이언트 사이 (리버스)에서 요청을 관리합니다.
- 캐싱: 자주 액세스되는 데이터를 저장하여 지연 시간을 줄이고 성능을 향상시킵니다.
- 데이터 파티셔닝: 대량의 데이터를 효율적으로 처리하기 위해 데이터 세트를 여러 기기에 분할합니다.
- 데이터베이스 복제: 여러 서버 간에 데이터를 복제하여 가용성과 내고장 허용성을 보장합니다.
- 분산 메시징 시스템: 실시간 데이터 처리를 위해 분산된 응용 프로그램 간 통신을 지원합니다.
- 마이크로서비스: 독립적으로 개발, 배포 및 확장할 수 있는 작은 서비스로 구성된 아키텍처 스타일입니다.
- 데이터베이스: 구조화된(SQL) 또는 비구조화된(NoSQL) 방식으로 데이터를 저장하여 다양한 유형의 데이터를 처리하는 확장성과 유연성을 제공합니다.
- 데이터베이스 색인화: 빠른 데이터 검색을 가능케 하여 쿼리 성능을 향상시킵니다.
- 분산 파일 시스템: 확장성과 신뢰성을 위해 네트워크의 여러 노드에 걸쳐 저장소를 관리합니다.
- 알림 시스템: 사용자나 다른 시스템에 이벤트를 알리기 위해 경보 또는 메시지를 전송합니다.
- 전체 텍스트 검색: 대량의 텍스트 데이터에 대한 효율적인 검색 기능을 제공합니다.
- 하트비트: 서비스나 노드의 생존 여부를 점검하여 실행 중이고 이용 가능한지 확인합니다.

## 시스템 디자인 인터뷰에서 뛰어나기 위한 전략 💯

1. 요구 사항 이해 및 명확화:
   — 기능 요구 사항: 시스템이 수행해야 하는 주요 기능을 파악합니다. 인스타그램의 경우 사진 업로드, 게시물 좋아요 및 댓글 작성, 다른 사용자 팔로우, 사용자 정의 피드 생성 등을 의미합니다.
   — 비기능 요구 사항: 확장성, 신뢰성, 가용성, 성능, 일관성, 보안, 유지 관리를 포함합니다.

2. 부하 및 트래픽 예상: 시스템을 사용할 사용자 수, 정보를 추가하거나 가져올 때의 속도, 모든 데이터를 저장하는 데 필요한 공간, 필요한 인터넷 용량 등을 파악합니다. 이 정보는 예상 활동량을 관리할 수 있는 시스템을 디자인하는 데 중요합니다.

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

3. 데이터 흐름과 아키텍처 설계: 시스템이 어떻게 작동하는지 보여주는 다이어그램을 만드세요. 중요한 부분을 강조하고 서로 어떻게 작동하는지 알려주세요. 이렇게 하면 설계를 더 명확하게 설명할 수 있습니다.

4. 적절한 기술 선택: 필요한 것과 일치하는 데이터베이스, 캐싱 시스템, 부하 분산 장치 및 기타 기술을 선택하세요.

5. 트레이드오프 논의: 설계 결정에서 한 트레이드오프를 설명하세요. 시스템이 얼마나 일관적인지 대비 가능한지, 대답 속도와 한 번에 처리할 수 있는 작업 양을 균형있게 고려하고, 복잡성 대비 사용하기 쉽고 이해하기 쉬운 정도에 대해 이야기해주세요.

6. 반복 및 최적화: 고수준 설계로 시작하여 단계별로 개선하세요. 피드백과 추가 요구사항에 따라 최적화하세요.

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

# Instagram 시스템 디자인

이제 Instagram의 아키텍처에 대해 자세히 살펴보겠습니다:

![이미지](/assets/img/2024-06-19-UnderstandingSystemDesignusingInstagramsArchitecture_1.png)

## 기능 요구 사항:

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

- 포스트 서비스: 사용자가 포스트를 업로드하고 가져올 수 있습니다.
- 좋아요/댓글 서비스: 포스트의 좋아요와 댓글을 관리합니다.
- 팔로우 서비스: 팔로우 및 언팔로우 작업을 처리하고 팔로워 데이터를 검색합니다.
- 피드 생성 서비스: 사용자에게 맞춤형 피드를 생성합니다.
- 메시징 서비스: 사용자가 직접 메시지를 보내고 받을 수 있습니다.
- 알림 서비스: 사용자가 좋아요, 댓글, 팔로우 및 직접 메시지에 대한 알림을 받습니다.
- 검색 서비스: 사용자가 다른 사용자, 해시태그 및 위치를 검색할 수 있습니다.

## 비기능 요구사항:

- 확장성: 시스템은 수백만 명의 사용자를 지원하고 그들의 상호 작용을 처리해야 합니다.
- 신뢰성: 고가용성 및 내결함성이 필수적입니다.
- 가용성: 시스템은 높은 업타임(99.9% 이상)을 가져야 합니다.
- 성능: 포스팅, 포스트 가져오기 및 피드 생성에 대한 빠른 응답 시간을 보장해야 합니다.
- 일관성: 좋아요, 댓글, 팔로우 및 포스트 간 데이터 일관성을 보장해야 합니다.
- 보안: 시스템은 사용자 데이터와 개인 정보를 보호해야 합니다.

## 추정:

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

- 사용자 기반: 매일 활동하는 5억 명의 사용자를 가정합니다.
- 초당 게시물: 초당 5,000개의 게시물을 추정합니다.
- 초당 좋아요/댓글: 초당 50,000개의 좋아요와 댓글을 추정합니다.
- 팔로워 검색: 초당 10,000개의 팔로워 검색 요청을 추정합니다.
- 피드 요청: 초당 50,000개의 피드 요청을 가정합니다.
- 초당 검색 쿼리: 초당 20,000개의 검색 쿼리를 추정합니다.
- 초당 메시지: 초당 15,000개의 메시지를 발송하는 것을 추정합니다.

## 아키텍처 구성 요소:

1. 사용자: 사용자들은 기기를 통해 응용 프로그램과 상호 작용합니다. 사용자들은 응용 프로그램을 통해 게시, 좋아요, 댓글 및 팔로우와 같은 작업을 수행합니다.

2. 부하 분산기: 단일 서버가 병목현상을 일으키는 것을 방지하기 위해 들어오는 요청을 여러 서버로 분산합니다.

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

3. API Gateway: 모든 클라이언트 요청의 입구 역할을 합니다. 인증, 요청 속도 제한 및 적절한 서비스로 요청을 라우팅합니다.

4. 인증 및 요청 속도 제한: 인증된 사용자만 서비스에 액세스할 수 있도록 보장하고, 요청의 수를 제한하여 남용을 방지합니다.

5. 앱 서버 (읽기): 대량의 읽기 작업을 처리하기에 최적화되어 있습니다. 게시물, 댓글, 좋아요, 팔로워, 피드 생성, 검색 쿼리 및 메시지 검색을 위한 요청을 라우팅합니다.

6. 앱 서버 (쓰기): 대량의 쓰기 작업을 처리하기에 최적화되어 있습니다. 게시물 작성, 좋아요 및 댓글 추가, 사용자 팔로우/언팔로우, 메시지 전송, 사용자 데이터 업데이트를 위한 요청을 라우팅합니다.

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

7. Redis: 자주 액세스되는 데이터를 저장하는 캐싱 계층으로 작용하여 데이터베이스 부하를 줄이고 응답 시간을 향상시킵니다.

8. 데이터베이스: 사용자 정보, 게시물, 좋아요, 댓글 및 팔로우 데이터를 포함한 모든 영구 데이터를 저장합니다. 데이터베이스는 높은 읽기 및 쓰기 처리량을 지원해야 합니다. (각 서비스의 특정 요구 사항에 따라 더 효율적인 처리를 위해 별도의 데이터베이스를 만들 수 있습니다)

9. Blob Storage: 이미지와 비디오와 같은 대형 이진 객체을 저장합니다. 이미지 및 비디오를 효율적으로 저장하고 배포하여 빠르고 안정적인 미디어 콘텐츠 액세스를 보장합니다.

10. CDN (콘텐츠 전송 네트워크): 이미지 및 비디오를 전 세계적으로 배포하여 전 세계 사용자에 대한 빠른 액세스와 지연 시간을 줄입니다.

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

11. 포스트 서비스: 게시물의 생성 및 검색을 관리합니다. 포스트 데이터를 데이터베이스 및 블롭 저장소에 작성하고, 피드 생성 서비스를 업데이트합니다.

12. 좋아요/댓글 서비스: 게시물에 대한 좋아요 및 댓글을 처리합니다. 데이터베이스를 업데이트하고, 피드 생성 서비스에 피드를 새로 고침하도록 알립니다.

13. 팔로우 서비스: 팔로우 및 언팔로우 작업을 관리합니다. 데이터베이스와 피드 생성 서비스에서 팔로우 데이터를 업데이트합니다.

14. 피드 생성 서비스: 사용자의 팔로우 데이터와 상호 작용을 기반으로 개인화된 피드를 생성하고 제공합니다. Redis 및 데이터베이스에서 데이터를 읽어 효율적으로 피드를 생성합니다.

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

15. 알림 서비스: 새로운 팔로워, 좋아요 및 댓글과 같은 사용자 활동에 대한 실시간 알림을 전송합니다.

16. 검색 서비스: 게시물, 사용자 및 해시태그에 대한 사용자 검색 쿼리를 처리합니다. 자주 액세스되는 데이터에는 Redis를 활용하고, 드물게 발생하는 쿼리에 대해서는 데이터베이스를 사용합니다. 검색 결과가 최신이며 정확하도록 관리합니다.

17. 메시징 서비스: 사용자 간 실시간 메시징을 가능하게 합니다. 메시지 저장을 위해 전용 데이터베이스를 사용하고, 즉각적인 전달과 알림을 보장하기 위해 실시간 메시징 인프라를 활용합니다.

이러한 구성 요소 및 상호 작용을 이해하면 대규모 응용 프로그램을 위한 견고하고 확장 가능하며 효율적인 시스템을 설계할 수 있습니다. 이러한 아키텍처를 통해 인스타그램은 수백만 명의 사용자를 처리하고 콘텐츠에 빠르고 안정적으로 액세스할 수 있으며 높은 가용성과 성능을 유지할 수 있습니다. 각 구성 요소는 내결함성이 있으며 독립적으로 확장 가능하며 특정 작업 처리에 효율적으로 설계되었습니다.

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

시스템 디자인 면접을 성공적으로 통과하기 위해서는 구조적인 원리에 대한 탄탅한 이해, 복잡한 요구사항을 분해하는 능력, 그리고 합리적인 디자인 결정을 내릴 수 있는 기술이 필요합니다. 체계적인 접근 방식을 따르고 현실 세계의 예시를 활용함으로써, 확장 가능하고 효율적인 시스템을 디자인할 수 있는 능력을 보여줄 수 있습니다. 연습이 중요하니, 다양한 디자인 문제에 도전하여 스킬을 향상시키세요.

P.S: 학습 과정 동안 메모를 계속 쌓아왔어요. 본 글이 시스템 디자인에 대해 더 잘 이해하는 데 도움이 되길 바랍니다. 직접 시스템을 디자인하는 과정을 즐기세요! :D
