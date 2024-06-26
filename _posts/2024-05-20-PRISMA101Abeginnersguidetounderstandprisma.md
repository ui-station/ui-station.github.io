---
title: "프리즈마 101 프리즈마 이해를 위한 초보자 안내서"
description: ""
coverImage: "/assets/img/2024-05-20-PRISMA101Abeginnersguidetounderstandprisma_0.png"
date: 2024-05-20 18:55
ogImage:
  url: /assets/img/2024-05-20-PRISMA101Abeginnersguidetounderstandprisma_0.png
tag: Tech
originalTitle: "PRISMA 101 : A beginner’s guide to understand prisma."
link: "https://medium.com/@fidelotieno11/prisma-101-a-beginners-guide-to-understand-prisma-7158bc45af2d"
---

<img src="/assets/img/2024-05-20-PRISMA101Abeginnersguidetounderstandprisma_0.png" />

# 소개

Node.js나 TypeScript 환경에서 데이터베이스를 다룬 적이 있다면, Prisma에 대해 들어봤을 것입니다. Prisma는 데이터베이스 관리와 상호작용을 간소화하는 도구입니다. 하지만 Prisma가 정확히 무엇인지, 어떤 일을 하며 왜 사용해야 하는지 알고 계신가요? ORM이 무엇인지도 궁금하신가요? 이 글을 읽은 후에는 이러한 질문들에 대한 답을 알게 될 것입니다.

# Prisma란 무엇인가요?

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

프리즈마는 데이터베이스 작업을 할 때 개발자 경험을 향상시키는 Node.js 및 TypeScript ORM입니다. 직관적인 데이터 모델, 자동 마이그레이션, 타입 안전성 및 자동 완성을 통해 데이터베이스를 쉽게 관리하고 상호 작용할 수 있게 해줍니다.

# Prisma는 다음으로 구성됩니다:

- Prisma Client: Node.js 및 TypeScript용 자동 생성 및 타입 안전한 쿼리 빌더.
- Prisma Migrate: 선언적 데이터 모델링 및 마이그레이션 시스템.
- Prisma Studio: 데이터베이스에서 데이터를 보고 편집할 수 있는 GUI입니다.

# 프리즈마가 하는 일이 무엇인가요?

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

지금까지 프리즈마가 애플리케이션과 데이터베이스 사이에서 중재자로 작용한다는 것을 알게 되었습니다. 이것은 주요 기능을 강조합니다:

- 스키마 정의: 프리즈마 스키마 파일에서 데이터 모델을 정의합니다.
- 데이터베이스 연결: 스키마 파일을 사용하여 선호하는 데이터베이스에 연결합니다.
- 쿼리 작성: 쿼리 빌더로 Prisma Client를 사용합니다.
- 마이그레이션: 프리즈마 마이그레이트를 사용하여 데이터베이스 마이그레이션을 자동으로 생성 및 적용합니다.
- 데이터 관리: 사용자 친화적 인터페이스를 통해 데이터를 상호 작용하기 위해 Prisma Studio를 사용합니다.

# ORM이란 무엇인가요?

ORM(Object-Relational Mapping)은 데이터베이스와 객체지향 방식으로 상호 작용할 수 있도록 해주는 프로그래밍 기술입니다. 더 간단하게 말하면 ORM은 데이터를 애플리케이션 코드와 데이터베이스와 같은 호환되지 않는 유형 시스템 사이에서 번역합니다. 이는 원시 SQL 쿼리를 작성하지 않고 익숙한 프로그래밍 개념을 사용하여 데이터베이스 작업을 할 수 있음을 의미합니다.

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

# 프리즈마를 사용해야 하는 이유

프리즈마를 사용해야 하는 이유:

- 개발자 생산성: 프리즈마를 사용하면 데이터베이스 관리에 신경 쓰지 않고 멋진 기능을 구축하는 데 더 많은 시간을 할애할 수 있습니다. 마치 모든 지루한 작업을 처리해 주는 개인 비서가 있는 것과 같습니다.
- 타입 안전성: 프리즈마를 사용하면 타입 안전한 쿼리를 얻을 수 있어 런타임 오류가 줄어듭니다.
- 자동 마이그레이션: 프리즈마 Migrate를 사용하면 데이터베이스 스키마를 시간이 지남에 따라 쉽게 변경할 수 있으며 마이그레이션을 자동으로 처리합니다.
- 더 나은 디버깅: 타입 안전한 프리즈마 클라이언트는 매우 설명이 잘 된 오류를 생성하여 코드의 디버깅을 매우 쉽게 만듭니다.
- 커뮤니티와 지원: 프리즈마에는 활발한 커뮤니티와 방대한 문서가 있어 쉽게 학습하고 도움을 받을 수 있습니다.

# 프리즈마 ORM이 어떻게 작동하는지 궁금하신가요?

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

프리즈마 스키마 파일에서 시작됩니다. 여기서 데이터 모델을 정의하고 데이터베이스 연결을 구성합니다.
간단한 예시를 보여드리겠습니다:

![프리즈마 코드](/assets/img/2024-05-20-PRISMA101Abeginnersguidetounderstandprisma_1.png)

위 코드에서 세 가지를 구성했습니다:

- Generator: 프리즈마 클라이언트 생성을 원한다는 것을 나타냅니다.
- Data source: 데이터베이스 연결 (환경 변수와 함께).
- Data model: 애플리케이션 모델을 정의합니다.

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

프리즈마 스키마의 모델은 데이터베이스의 테이블을 나타냅니다. 이 모델은 프리즈마 클라이언트 API에서 쿼리를 위한 기초를 제공합니다. 예를 들어, 위에서 정의한 User 모델을 사용하면 사용자에 대한 CRUD(Create, Read, Update, Delete) 작업을 쉽게 수행할 수 있습니다.

# 프리즈마 클라이언트

프리즈마 클라이언트는 스키마 파일을 기반으로 생성됩니다. 이를 사용하면 쿼리를 타입 안전하게 작성할 수 있어 익숙한 JavaScript/TypeScript 구문을 사용하여 데이터베이스와 상호 작용할 수 있습니다. 아래는 프리즈마 클라이언트를 사용하는 방법 예시입니다:

![프리즈마 클라이언트 예시](/assets/img/2024-05-20-PRISMA101Abeginnersguidetounderstandprisma_2.png)

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

# Prisma Migrate

프리즈마 마이그레이트는 시간이 지남에 따라 데이터베이스 스키마를 발전시키는 데 도움을 줍니다. 프리즈마 스키마 파일에서 스키마 변경 사항을 정의하고, 프리즈마 마이그레이트가 해당 SQL 마이그레이션 스크립트를 자동으로 생성해 줍니다. 이를 통해 데이터베이스 스키마가 코드와 동기화되도록 보장할 수 있습니다.

# Prisma Studio

프리즈마 스튜디오는 사용하기 편리한 인터페이스를 제공하여 데이터와 상호 작용할 수 있습니다. 코드를 작성하지 않고도 데이터를 볼 수 있고 편집하고 관리할 수 있습니다. 데이터를 검사하거나 업데이트할 때 유용할 수 있습니다.

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

Prisma는 Node.js와 TypeScript에서 데이터베이스 작업을 할 때 개발자 경험을 크게 향상시키는 강력한 ORM입니다. 우리는 Prisma가 어떻게 워크플로우를 최적화하고 데이터베이스 상호작용을 견고하고 오류 없이 만들어낼 수 있는지 살펴보았습니다.

이해한 바에 따라 이제 Prisma의 세계에 뛰어들어 빠르게 멋진 것들을 구축해보세요. 즐거운 코딩되시길 바랍니다!
