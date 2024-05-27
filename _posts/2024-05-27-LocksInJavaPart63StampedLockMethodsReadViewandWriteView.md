---
title: "자바에서의 락lock - 파트 63  스탬프락 메서드, 읽기 뷰 및 쓰기 뷰 "
description: ""
coverImage: "/assets/img/2024-05-27-LocksInJavaPart63StampedLockMethodsReadViewandWriteView_0.png"
date: 2024-05-27 16:00
ogImage: 
  url: /assets/img/2024-05-27-LocksInJavaPart63StampedLockMethodsReadViewandWriteView_0.png
tag: Tech
originalTitle: "Locks In Java — Part 6.3 [ Stamped Lock Methods , Read View and Write View ]"
link: "https://medium.com/@avinashsoni9829/locks-in-java-part-6-3-stamped-lock-methods-read-view-and-write-view-333579a644f4"
---


본 기사에서는 자바의 stamped lock 메서드에 대해 알아보고 이를 통해 제공되는 view 개념을 살펴볼 것입니다.

출발해 봅시다!!

![image](/assets/img/2024-05-27-LocksInJavaPart63StampedLockMethodsReadViewandWriteView_0.png)

이 메서드들은 상태 값을 확인하여 true 또는 false를 결정하기 위해 일부 검사를 계속 수행하기 때로 이해하기가 상당히 간단합니다.

<div class="content-ad"></div>

읽기 뷰:

이것은 락 인터페이스를 구현하는 내부 클래스이며 Stamped 락에 있는 메서드들을 감싸는 래퍼 역할을 합니다.

<div class="content-ad"></div>

메소드가 있습니다.

- lock() - 내부적으로 readLock() 메소드를 호출합니다.
- lockInterruptibly() - 내부적으로 readLockInterruptiblyMethod()을 호출합니다.
- tryLock() - tryReadLock() 메소드 출력값이 0이 아닌지 확인합니다.
- tryLock () - 시간 제한이 있는 잠금으로 내부적으로 tryReadLock 시간 제한이 있는 잠금이 0이 아닌지 확인합니다.
- unlock() - unstampedUnlockRead() 메소드를 호출합니다. [stamped 메소드는 view 클래스용으로 만들어지며, view 클래스 잠금 메소드에서는 스탬프가 필요하지 않습니다.]
- newCondition () - 예외를 throw합니다.

[위의 6개 메소드에 대해 자세히 알아보려면 lock 인터페이스를 확인해 주세요.]

asReadLock()

<div class="content-ad"></div>

이는 stamped lock의 Read Lock 뷰를 생성합니다.

비슷하게, 우리는 Lock 인터페이스를 다시 구현하고 내부적으로 해당 쓰기 잠금 메서드를 호출하는 writeLock 뷰를 가지고 있습니다.

이 두 뷰는 우리에게 Lock 인터페이스 아래에서 stamped lock을 사용하는 데 도움이 됩니다.

asReadLock () 및 asWriteLock () 메서드를 사용하고 ReadWriteLock 인터페이스를 구현하는 ReadWriteView도 있습니다.

<div class="content-ad"></div>

우리의 자바 락 시리즈를 완료했습니다. 자바의 대부분의 락을 다루려고 노력했습니다.

여기 완전한 시리즈 목록입니다:

- Lock Interface : [링크](https://medium.com/@avinashsoni9829/locks-in-java-part-1-lock-interface-38222d6048d3)
- Read Write Lock : [링크](https://medium.com/@avinashsoni9829/locks-in-java-part-2-read-write-locks-0a2a7c803b44)
- Lock Support : [링크](https://medium.com/@avinashsoni9829/locks-in-java-part-3-locksupport-607d8766ed1a)
- Abstract Ownable Synchronizers : [링크](https://medium.com/@avinashsoni9829/locks-in-java-part-4-abstract-ownable-synchronizers-1e4033f66d17)
- Reentrant Lock part — 1: [링크](https://medium.com/@avinashsoni9829/locks-in-java-part-5-1-reentrant-locks-in-java-sync-class-bf11e8765507)
- Reentrant Lock part — 2 : [링크](https://medium.com/@avinashsoni9829/locks-in-java-part-5-2-reentrant-locks-051e31e19a48)
- Stamped Lock Part 1 : [링크](https://medium.com/@avinashsoni9829/locks-in-java-part-6-1-stamped-lock-optimistic-reads-and-pessimistic-reads-8cb8f9858674)
- Stamped Lock Part 2 : [링크](https://medium.com/@avinashsoni9829/locks-in-java-part-6-2-stamped-lock-conversions-b2ec88e78ad1)
- Stamped Lock Part 3 and Series End : [링크](https://medium.com/@avinashsoni9829/locks-in-java-part-6-3-stamped-lock-methods-read-view-and-write-view-333579a644f4)

코드 : [GitHub 링크](https://github.com/avinashsoni9829/Threading/tree/main/locks)

<div class="content-ad"></div>

안녕하세요! 읽어주셔서 감사합니다! 피드백을 공유해 주시면 감사하겠습니다.