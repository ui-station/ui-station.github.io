---
title: "게임 엔진을 위한 저지연 스크립팅 소개, 파트 2"
description: ""
coverImage: "/assets/img/2024-06-19-AnIntroductiontoLow-LatencyScriptingforGameEnginesPart2_0.png"
date: 2024-06-19 11:54
ogImage: 
  url: /assets/img/2024-06-19-AnIntroductiontoLow-LatencyScriptingforGameEnginesPart2_0.png
tag: Tech
originalTitle: "An Introduction to Low-Latency Scripting for Game Engines, Part 2"
link: "https://medium.com/@fwsgonzo/an-introduction-to-low-latency-scripting-with-libriscv-part-2-4fce605dfa24"
---


고급 예제, 게스트 할당, RPC

이전 파트에서는 모든 것을 설정하고 샌드박스 안팎에서 기본 호출을 만드는 방법을 보여 주었습니다. 라이브리스브 레포지터리의 예제 폴더에서 게임 개발 예제를 사용함으로써 RISC-V 컴파일러를 찾았다면 제시된 코드 예제를 즉시 실행할 수 있었습니다.

이 파트 2에서는 고급 예제를 살펴보겠습니다.

## 1. 고급 API 설계와 호스트 제어 힙

<div class="content-ad"></div>

게스트 힙은 샌드박스 바깥에서 완전히 제어됩니다. 게스트는 알려진 시스템 호출을 실행하여 메모리를 할당하고 해제하며 각각 할당과 해제를 처리합니다. 또한 calloc 및 realloc도 지원됩니다.

우리는 이것을 어떻게 활용할 수 있는지 보여주기 위해 가벼운 추상화를 만들 것입니다. 예를 들어, 위치에 연결된 데이터 개념을 만들어볼 것입니다. 다음과 같은 것입니다:

```js
struct LocationData {
   int x, y, z;
   std::unique_ptr<uint8_t[]> data = nullptr;
   std::size_t size = 0;
};
```

위치의 의미는 여기서 중요하지 않습니다. 중요한 것은 어딘가에 해당하는 구조체라는 것뿐입니다. 계속 진행하기 전에 몇 가지 알아야 할 사항이 있습니다.

<div class="content-ad"></div>

- 모든 힙 할당은 64비트로 정렬됩니다. 그것은 우리가 제어합니다.
- 데이터를 수정하고 모든 작업이 완전히 완료된 후에만 커밋할 수 있는 능력을 원합니다.
- ... 따라서 우리는 데이터의 사본을 스크립트 프로그램으로 가져 오고 싶습니다.

최소 API에 대해 2가지 기능이 필요합니다:

```js
// 위치 (x, y, z)의 내용을 검색하는 함수,
// 또는 위치가 발견되지 않으면 (nullptr, 0)를 반환합니다.
struct LocationGet {
  uint8_t* data;
  size_t size = 0;
};
DEFINE_DYNCALL(10, location_get, LocationGet(int, int, int));
// 위치의 내용을 커밋하는 함수
// 오류를 반환 할 수 없으며 대신 예외가 발생합니다.
DEFINE_DYNCALL(11, location_commit, void(int, int, int, const void*, size_t));
```

ABI는 레지스터에 직접 2개의 원소 구조체를 반환할 수 있다고 합니다. 이는 효율적입니다. 따라서 x, y 및 z를 레지스터에 유지하려고 시도할 것입니다.모두 인수로 전달합니다.

<div class="content-ad"></div>

아래는 간단하고 직관적인 클래스를 만드는 것이 좋습니다:

```js
#include <span>
struct LocationData {
  LocationData(int x, int y, int z)
    : x(x), y(y), z(z)
  {
    auto res = location_get(x, y, z);
    if (res.data) {
       m_data.reset(res.data);
       m_size = res.size;
    }
  }
  void commit() {
    location_commit(x, y, z, m_data.get(), m_size);
  }

  bool empty() const noexcept {
    return m_data == nullptr || m_size == 0;
  }
  std::span<uint8_t> data() {
    return { m_data.get(), m_size };
  }
  void assign(const uint8_t* data, size_t size) {
    m_data = std::make_unique<uint8_t[]>(size);
    std::copy(data, data + size, m_data.get());
    m_size = size;
  }

  const int x, y, z;
private:
  std::unique_ptr<uint8_t[]> m_data = nullptr;
  std::size_t m_size = 0;
};
```

이 간단하고 직관적인 클래스는 특정 위치의 데이터를 관리합니다. 객체를 생성하면 공백을 채우려고 시도하며 비어 있으면 원하는 대로 데이터를 할당할 수 있습니다. 데이터를 커밋하면 크기에 관계없이 엔진 데이터를 덮어씁니다.

게임 엔진 쪽에서 이를 아주 빠르게 구현할 수 있습니다:

<div class="content-ad"></div>


```js
struct Location {
  int x = 0, y = 0, z = 0;

  bool operator==(const Location& other) const {
    return x == other.x && y == other.y && z == other.z;
  }
};
namespace std {
  template<> struct hash<Location> {
    std::size_t operator()(const Location& loc) const {
      return std::hash<int>()(loc.x) ^ std::hash<int>()(loc.y) ^ std::hash<int>()(loc.z);
    }
  };
}
struct LocationData
{
  std::vector<uint8_t> data;
};
static std::unordered_map<Location, LocationData> locations;
```

그리고 게임 엔진의 location_get 및 location_commit 콜백 함수들:

```js
// sys_location_get 콜백 함수입니다
register_script_function(10, [](Script& script) {
  auto [x, y, z] = script.machine().sysargs<int, int, int>();
  auto it = locations.find(Location(x, y, z));
  if (it != locations.end()) {
    auto alloc = script.guest_alloc(it->second.data.size());
    script.machine().copy_to_guest(alloc, it->second.data.data(), it->second.data.size());
    script.machine().set_result(alloc, it->second.data.size());
  } else {
    script.machine().set_result(0, 0);
  }
});
// sys_location_commit 콜백 함수입니다
register_script_function(11, [](Script& script) {
  auto [x, y, z, data] = script.machine().sysargs<int, int, int, std::span<uint8_t>>();
  // 새로운 위치를 생성하거나 기존 위치를 업데이트합니다
  auto& loc = locations[Location(x, y, z)];
  loc.data = std::vector<uint8_t>(data.begin(), data.end());
});
```

이제 조금 설명이 필요합니다. 게임 엔진의 콜백 함수는 프로그램에서 발생하는 호출로부터 인수를 검색한 다음 호출된 함수에 따라 유용한 작업을 수행합니다. 프로그램이 이렇게 수행한다고 가정해보세요:


<div class="content-ad"></div>

```cpp
LocationData loc(1, 2, 3);
if (!loc.empty()) {
  printf("위치 (1, 2, 3)에 %zu바이트가 있습니다\n", loc.data().size());
  location_commit(1, 2, 3, loc.data().data(), loc.data().size());
} else {
  printf("LocationGet(1, 2, 3) 비어 있었어요!\n");
}

std::vector<uint8_t> data = { 0x01, 0x02, 0x03, 0x04 };
loc.assign(data.data(), data.size());
loc.commit();

LocationData loc2(1, 2, 3);
if (!loc2.empty()) {
  printf("위치 (1, 2, 3)에 %zu바이트가 있습니다\n", loc2.data().size());
  location_commit(1, 2, 3, loc2.data().data(), loc2.data().size());
} else {
  printf("LocationGet(1, 2, 3) 비어 있었어요!\n");
}
```

그리고 예상대로 출력 결과가 나왔어요:

```cpp
LocationGet(1, 2, 3) 비어 있었어요!
위치 (1, 2, 3)에 4 바이트가 있습니다
```

그래서 이 프로그램은 먼저 (1, 2, 3) 위치에 있는 LocationData를 생성하고 비어 있는지 확인합니다. 비어 있었습니다. 그런 다음 4바이트 벡터에서 할당하고, 커밋하며, 새로운 (1, 2, 3) 위치에 있는 LocationData를 만들고 이제 4바이트를 포함하였다는 것을 확인할 수 있습니다.


<div class="content-ad"></div>

LocationData의 생성자는 다음과 같습니다:

```js
auto res = location_get(x, y, z);
if (res.data) {
  m_data.reset(res.data);
  m_size = res.size;
}
```

이 코드는 게임 엔진에 location_get을 실행하도록 요청하며, 이를 콜백 10으로 번호를 매깁니다. 콜백 10이 실행되고, 즉시 첫 번째로 받은 3개의 인자를 정수형으로 가져와 x, y, z를 만듭니다. 그런 다음 이를 위치들 지도에 찾아보고, 찾지 못하면 (0, 0)를 반환합니다. 이것은 다른 한 쪽에 포인터가 있고, 다른 하나는 정수인 두 레지스터를 0으로 설정하는 것으로 상상할 수 있습니다.

이후, LocationData가 비어있게 되고, 그것이 출력됩니다. 그런 다음에 데이터를 할당하고 commit()를 호출합니다. Commit은 location_commit인 콜백 11을 호출할 것입니다. 콜백을 확인하면 4개의 인자를 읽어옵니다: x, y, z 및 `span<uint8_t>`입니다. 이것은 동적인 스팬이므로 2개의 레지스터를 소비해서 총 5개로 만들어집니다. 이는 프로그램의 정의와 일치하는데, void(int, int, int, const void*, size_t)입니다. 즉, 포인터를 위한 하나의 레지스터, 그리고 데이터의 크기를 위한 두 번째 레지스터가 필요합니다. 그리고 이 데이터는 해당 위치의 지도로 복사됩니다.

<div class="content-ad"></div>

마침내, LocationData를 다시 (1, 2, 3)로 만들었고, 이번에는 데이터를 찾았어요. 4바이트가 저장되고 프로그램으로 검색되었으며, 데이터와 크기는 2개의 레지스터로 전달되었어요. 호스트 측의 콜백 함수에서는 다음과 같은 절차가 진행됩니다:

- 스크립트 프로그램에 대한 힙 할당이 script.guest_alloc(bytes)를 사용하여 이루어집니다.
- 그 다음, 데이터를 힙 할당 주소로 복사하기 위해 script.machine().copy_to_guest(…)를 사용합니다.
- 마지막으로, 주소와 크기를 반환합니다.

이로써 첫 번째 고급 스크립팅 예제가 마무리되었습니다.

이 예제 API에 대한 마지막 참고 사항은 상당히 남용 방지 메커니즘이 있다는 것입니다. 우리가 적대적인 행동을 기대하는 경우, 생성될 수 있는 위치의 수에 제한을 두었어야 했지만, 이를 제외하고는 libriscv API가 극단적인 값들을 예방하는 데 탁월한 역할을 합니다. 예를 들어, 방대한 범위는 즉시 실패합니다. 잘못된 메모리 읽기 또는 쓰기는 실행이 실패합니다. 너무 오랫동안 지연하는 시도도 실패합니다. 간단히 말해, 정말 딱글딱글하게 만드는 경우가 있어 무한 루프에 빠진 적도 있지만, 이로 인해 게임에 영향을 준 적은 없었습니다. 잠시 실행되다가 결국 스스로 실패하고 멈춘 지점도 알려줍니다.

<div class="content-ad"></div>

## 2. 원격 프로시저 호출

동일한 코드를 두 곳에서 실행하고 두 개의 별도 시스템에서 동일한 동작을 경험하는 것은 간단히 올바른 에뮬레이션입니다. 더 나아가, 동일한 프로그램이라고 가정한다면 함수 호출을 설명하고 그것이 동일하게 작동하도록 할 수도 있습니다. 그래서 두 개의 샌드박스에 동일한 프로그램을 실행하여 원격 프로시저 호출이 발생하도록 해 보겠습니다, 테스트 목적으로.

우선, 다른 위치에서 함수를 실행하려면 어떻게 실행할지 정의해야 합니다. 예를 들어, 하나의 가상 머신에서 모든 레지스터를 다른 가상 머신으로 복사한 다음 레지스터에 맞는 모든 인수를 원격 함수에 전달한다고 가정할 수 있습니다. 그런 다음 모든 프로그램이 동일하기 때문에 기능도 동일하게 동작할 것입니다.

우리가 하려는 다른 방법은 고정 크기 lambda 캡쳐를 사용하는 것입니다. 우리가 신경 쓰는 것들을 (값으로!) 람다 함수에 복사하여 원격 위치로 전송하고 그것을 인자로서 트램폴린에 전달할 수 있습니다. 그런 다음 트램폴린은 캡쳐와 함께 람다를 호출하고 와! 원격 프로시저 호출이 이뤄집니다.

<div class="content-ad"></div>

간단한 API를 다음과 같이 만들어 봅시다:

```js
int x = 42;
rpc([x] {
  printf("원격 가상 머신에서 안녕하세요!\n");
  printf("x = %d\n", x);
  fflush(stdout);
});
```

실제 환경에서 RPC 함수는 주변 또는 지역 플레이어에게 브로드캐스트할 수 있는 기능을 포함한 여러 가지 모드를 가질 것입니다. 하지만 이 예제에는 충분합니다. 이를 구현하기 위해서는 고정 크기 캡처 함수 구현이 필요합니다 (링크가 됨). 이를 사용하여 다음과 같이 callable을 트램폴린으로 사용하는 도우미 함수를 만들 수 있습니다:

```js
DEFINE_DYNCALL(12, remote_lambda, void(void(*)(void*), const void *, size_t));

static void rpc(riscv::Function<void()> func)
{
  remote_lambda(
  [](void* data) {
    auto func = reinterpret_cast<riscv::Function<void()>*>(data);
    (*func)();
  },
  &func, sizeof(func));
}
```

<div class="content-ad"></div>

여기서 하는 일은 inline 함수를 사용하여 전달 된 `remote_lambda`를 호출하는 것입니다. 이 함수는 포인터로 변환 할 수있는 내부 함수를 취하며 해당 포인터를 고정 크기 캡처 함수로 변환할 수 있습니다. 그런 다음 호출합니다. 그리고 두 번째 및 세 번째 인수는 함수와 해당 크기입니다. 즉, 3 개의 인수가 있습니다. : 함수 포인터, Function`void()` 포인터 및 해당 크기.

호스트에서는 캡처 스토리지를 읽어서 주소를 정수 값으로 검색합니다.

```js
static Script::gaddr_t remote_addr;
static std::array<uint8_t, 32> remote_capture;
...
register_script_function(12, [](Script& script) {
  auto [addr, capture] = script.machine().sysargs<Script::gaddr_t, std::array<uint8_t, 32>*>();

  remote_addr = addr;
  remote_capture = *capture;
});
```

여기서는 함수 포인터 주소를 검색한 다음 32 바이트 std::array에 대한 제로 복사 포인터를 얻습니다. 내부적으로 메모리가 올바르며 범위 내에 있는지 확인하려고 고정 크기 1 요소 span이 생성됩니다. 그런 다음 배열로 변환되며 정렬 확인 후에 처리됩니다.

<div class="content-ad"></div>

지금 우리는 프로그램에서 캡처 스토리지를 사용하여 함수를 호출할 수 있는 능력을 가졌습니다. 따라서 함수를 호출하기 위해 다른 Script 인스턴스를 만들겠습니다:

```js
auto script2 = script.clone("myscript2");

// 캡처를 스택에 푸시한 채로 원격 함수 호출
script2.call(remote_addr, remote_capture);
```

캡처 스토리지가 스택에 푸시된 상태로 remote_addr를 호출하면 성공적인 원격 프로시저 호출이 발생합니다:

```js
Hello from a remote virtual machine!
x = 42
```

<div class="content-ad"></div>

이것은 두 프로그램이 동일할 때 안전하고 신뢰할 수 있는 RPC를 구현하는 데 함수 주소와 캡처 저장소만 필요하다는 것을 보여줍니다. 이것은 제가 게임 개발에서 많이 활용하는 기능입니다.

## 3. 콜백 구현하기

나중에 호출할 스크립트 내에서 콜백을 만드는 두 가지 방법이 있습니다.

첫째, 단순히 이름으로 함수를 찾아 미리 합의된 매개변수를 사용하여 호출하는 것이 가장 쉽지만 약간 오류가 발생할 수 있습니다.

<div class="content-ad"></div>

```js
myscript.call("my_function", 1, 2, 3, "four");
```

요 메소드의 경우에는 스크립트에 "my_function" 함수를 extern "C"로 구현하는 것 외에는 아무것도 할 필요가 없습니다. 해당 함수가 visible 하다면 (심볼 테이블 항목이 있으면) 호출할 수 있습니다. 우리가 알 수 없는 것은 함수가 실제로 그러한 인수를 받는지 여부이지만 이 메소드는 JSON과 같은 곳에서 매우 쉽게 사용할 수 있습니다.

두 번째 방법은 스크립트 자체에서 함수 포인터를 가져오는 방법이지만, 이를 위해 콜백 등록 함수를 만들어야 합니다. 다음과 같이:

```js
DEFINE_DYNCALL(13, my_callback, void(const char*, void(*)(int)));
```

<div class="content-ad"></div>

예를 들어, 게임 안의 엔티티를 식별하는 첫 번째 문자열을 가져와서 함수 포인터를 사용합니다. 이벤트가 발생할 때 함수 포인터가 호출되며, 엔티티의 ID인 정수를 전달할 것입니다. 호스트에서 핸들러를 구현하는 예시입니다:

```js
register_script_function(13, [](Script& script) {
  auto [name, func] = script.machine().sysargs<std::string, Script::gaddr_t>();

  // 이름으로 엔티티 찾기
  auto& ent = entities.at(name);
  // 엔티티를 위한 이벤트 핸들러 등록
  ent.on_event(
  [func, &script] (auto& ent) {
    // 엔티티 ID를 인수로하여 함수 호출
    script.call(func, ent.getID());
  });
});
```

여기서 Script::gaddr_t은 샌드박스 내부의 포인터와 같은 크기의 부호 없는 정수입니다. 물론, 구조체를 전달할 때 문제를 피하기 위해 일치하는 포인터 크기를 사용하는 것이 좋습니다 (예: size_t의 차이).

그래서 이러한 호스트에서의 콜백을 사용하면, 호출되면 엔티티를 찾아서, 해당 엔티티의 on_event 콜백을 설정하여 이전에 제공된 함수 포인터로 스크립트 함수를 호출하고 엔티티 ID를 인수로 전달하게 됩니다.

<div class="content-ad"></div>

스크립트 안에는 이제 이렇게 이벤트 핸들러를 만들 수 있습니다:

```js
my_callback("entity1", [] (int id) {
  printf("Callback from entity %s\n", Entity{id}.getName().c_str());
});
```

이 예제에서는 Entity를 감싸는 래퍼가 있다고 가정합니다. 그럼 그 래퍼를 통해 예를 들어 Entity 이름을 가져올 수 있습니다.

콜백에 대한 세 번째이자 마지막 방법은 위와 동일하지만, 스크립트에서 Function`void()`를 사용합니다. 호스트에서 캡처 스토리지를 람다로 복사하여 이벤트 핸들러에 전달한 후, 이벤트가 트리거될 때 함수 호출 중에 캡처 스토리지를 스택에 푸시합니다. RPC의 예와 비슷합니다. 이렇게 함으로써 캡처 스토리지를 가진 이벤트를 만들 수 있습니다. 매우 편리하죠!

<div class="content-ad"></div>

그 단계별로 구현을 마무리해 봅시다. 먼저, 스크립트에서 정의를 수정하여 추가적인 const void*, size_t 매개변수를 받도록 하고, 그 다음으로 함수 포인터를 수정하여 나중에 캡처 저장소를 가져올 수 있도록 void* 인수를 추가하세요:

```js
DEFINE_DYNCALL(13, my_callback, void(const char*, void(*)(int, void*), const void*, size_t));
```

조금 복잡해 보이지만 두 단계로 진행하면 됩니다. 두 번째 단계는 항상 같은 단계입니다. callable에 const void*, size_t를 추가하고 callback 함수 포인터에 void*를 추가하세요. 이 callable을 사용하려면 도우미 함수를 만들어야 합니다:

```js
static void entity_on_event(const char* name, riscv::Function<void(int)> callback)
{
  my_callback(name,
  [] (int id, void* data) {
    auto callback = reinterpret_cast<riscv::Function<void(int)>*>(data);
    (*callback)(id);
  },
  &callback, sizeof(callback));
}
```

<div class="content-ad"></div>

도움 함수는 복잡한 호출을 간단하게 만들어줍니다. 함수를 다시 자체로 캐스팅하는 중간 함수가 추가되며, 일반적인 인수로 호출됩니다. 또한 반환 값을 전달할 수도 있습니다.

```js
int x = 42;
entity_on_event("entity1",
[x] (int id) {
  printf("Entity %s에서 콜백\n", Entity{id}.getName().c_str());
  printf("x = %d\n", x);
});
```

RPC 예제처럼 x = 42를 출력합니다. 이를 위해 호스트 측을 확장해야 합니다:

```js
register_script_function(13, [](Script& script) {
  auto [name, func, capture] = script.machine().sysargs<std::string, Script::gaddr_t, std::array<uint8_t, 32>*>();

  // 이름으로 Entity 찾기
  auto& ent = entities.at(name);
  // Entity의 이벤트 핸들러 등록
  ent.on_event(
  [func, &script, capture = *capture] (auto& ent) {
    // Entity ID를 인수로하여 함수 호출
    script.call(func, ent.getID(), capture);
  });
});
```

<div class="content-ad"></div>

지금 변경된 부분은 capture storage를 값으로 복사하여 on_event lambda에 넣은 다음, void* 인수로서 마지막 인수로 넣어 스택에 푸시하는 것입니다. 이것은 void* 인수입니다. 이 예제에서는 실제로 배열을 포인터로 반환했습니다: `std::array<uint8_t, 32>*`, 그리고 이것은 그 데이터에 대한 제로-코피 포인터를 제공합니다. 하지만, 그것을 값으로 capture해야 합니다.

읽어 주셔서 감사합니다. 나중에 더 많은 예제를 살펴보겠습니다.

-곤조