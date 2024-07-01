---
title: "Python weakref 모듈을 사용한 약한 참조 가이드"
description: ""
coverImage: "/assets/img/2024-07-01-AGuidetoPythonsWeakReferencesUsingtheweakrefModule_0.png"
date: 2024-07-01 16:06
ogImage: 
  url: /assets/img/2024-07-01-AGuidetoPythonsWeakReferencesUsingtheweakrefModule_0.png
tag: Tech
originalTitle: "A Guide to Python’s Weak References Using the weakref Module"
link: "https://medium.com/towards-data-science/a-guide-to-pythons-weak-references-using-weakref-module-d3381b01db99"
---



<img src="/assets/img/2024-07-01-AGuidetoPythonsWeakReferencesUsingtheweakrefModule_0.png" />

아마 당신은 파이썬의 weakref 모듈에 대해 들어본 적이 없을 것입니다. 여러분의 코드에서 자주 사용되지는 않지만, 많은 라이브러리, 프레임워크 및 심지어 파이썬 자체의 내부 작업에 기본적입니다. 그래서, 이 글에서는 weakref 모듈이 무엇인지, 어떻게 유용한지, 그리고 여러분의 코드에 어떻게 통합할 수 있는지 살펴보겠습니다.

# 기본 사항

weakref 모듈과 약한 참조를 이해하기 위해 먼저 파이썬에서 가비지 컬렉션에 대해 간단히 소개해야 합니다.


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

파이썬은 쓰레기 수집을 위한 메커니즘으로 참조 카운팅을 사용합니다. 간단히 말해, 파이썬은 우리가 생성한 각 객체에 대한 참조 카운트를 유지하며, 코드에서 객체가 참조될 때마다 참조 카운트가 증가하고, 객체가 해제될 때(e.g. 변수가 None으로 설정될 때) 감소합니다. 참조 카운트가 0이 되면, 해당 객체의 메모리가 할당 해제(가비지 수집)됩니다.

조금 더 이해하기 쉽도록 코드를 살펴봅시다:

```js
import sys

class SomeObject:
    def __del__(self):
        print(f"(Deleting {self=})")

obj = SomeObject()

print(sys.getrefcount(obj))  # 2

obj2 = obj
print(sys.getrefcount(obj))  # 3

obj = None
obj2 = None

# (Deleting self=<__main__.SomeObject object at 0x7d303fee7e80>)
```

여기서는 __del__ 메서드만 구현한 클래스를 정의하는데, 이 메서드는 객체가 가비지 수집될 때 호출되며, 가비지 수집이 발생했을 때를 확인할 수 있도록 합니다.

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

이 클래스의 인스턴스를 만든 후에는 sys.getrefcount를 사용하여이 객체에 대한 현재 참조 수를 가져옵니다. 여기에서는 1을 기대하지만 getrefcount가 반환하는 카운트는 일반적으로 예상보다 한 단계 높습니다. getrefcount를 호출하면 참조가 함수의 인수로 값에 의해 복사되어 객체의 참조 수가 일시적으로 증가하기 때문입니다.

다음으로, obj2 = obj를 선언하고 다시 getrefcount를 호출하면 이제 obj와 obj2 둘 다에 의해 참조되기 때문에 3을 얻습니다. 반대로, 이러한 변수에 None을 할당하면 참조 수가 0으로 감소하고, 마침내 객체가 가비지 수집되었다는 __del__ 메서드에서 메시지를 받게 됩니다.

자, 그럼 약한 참조는 어떻게 여기에 맞는 것인가요? 객체에 대한 유일한 남은 참조가 약한 참조인 경우, Python 인터프리터는 이 객체를 가비지 수집할 수 있습니다. 다른 말로하면 — 객체에 대한 약한 참조만으로는 객체를 살리는 데 충분하지 않습니다:

```js
import weakref

obj = SomeObject()

reference = weakref.ref(obj)

print(reference)  # <weakref at 0x734b0a514590; to 'SomeObject' at 0x734b0a4e7700>
print(reference())  # <__main__.SomeObject object at 0x707038c0b700>
print(obj.__weakref__)  # <weakref at 0x734b0a514590; to 'SomeObject' at 0x734b0a4e7700>

print(sys.getrefcount(obj))  # 2

obj = None

# (Deleting self=<__main__.SomeObject object at 0x70744d42b700>)

print(reference)  # <weakref at 0x7988e2d70590; dead>
print(reference())  # None
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

우리는 다시 클래스의 obj 변수를 선언합니다. 이번에는 이 객체에 대한 두 번째 강력한 참조 대신에 참조 변수에 약한 참조를 만듭니다.

그런 다음 참조 카운트를 확인하면 증가하지 않음을 알 수 있고, obj 변수를 None으로 설정하면 약한 참조가 여전히 남아 있음에도 즉시 쓰레기 수집됨을 확인할 수 있습니다.

마지막으로 이미 쓰레기로 처리된 객체에 대한 약한 참조에 액세스하려고 하면 “무효한” 참조 및 None이 반환됩니다.

또한 객체에 액세스하기 위해 약한 참조를 사용할 때 함수로 호출해야 했음을 주목하십시오(reference()). 따라서 객체 속성에 액세스해야 하는 경우 특히 프록시를 사용하는 것이 훨씬 편리합니다.

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
obj = SomeObject()

reference = weakref.proxy(obj)

print(reference)  # <__main__.SomeObject object at 0x78a420e6b700>

obj.attr = 1
print(reference.attr)  # 1
```

# 사용 시기

이제 약한 참조가 어떻게 작동하는지 알았으니, 어떻게 유용할 수 있는지 몇 가지 예시를 살펴보겠습니다.

약한 참조의 일반적인 사용 사례 중 하나는 트리와 같은 데이터 구조입니다.

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


class Node:
    def __init__(self, value):
        self.value = value
        self._parent = None
        self.children = []

    def __repr__(self):
        return "Node({!r:})".format(self.value)

    @property
    def parent(self):
        return self._parent if self._parent is None else self._parent()

    @parent.setter
    def parent(self, node):
        self._parent = weakref.ref(node)

    def add_child(self, child):
        self.children.append(child)
        child.parent = self

root = Node("parent")
n = Node("child")
root.add_child(n)
print(n.parent)  # Node('parent')

del root
print(n.parent)  # None


여기서는 약한 참조를 사용하여 자식 노드들이 부모 노드를 가리키는 Node 클래스를 통해 트리를 구현했습니다. 이 관계에서 자식 Node는 부모 Node 없이 살 수 있어 부모가 소리 없이 제거되거나 가비지 수집될 수 있게 합니다.

또 다른 방법으로 이를 구현해보겠습니다:


class Node:
    def __init__(self, value):
        self.value = value
        self._children = weakref.WeakValueDictionary()

    @property
    def children(self):
        return list(self._children.items())

    def add_child(self, key, child):
        self._children[key] = child

root = Node("parent")
n1 = Node("child one")
n2 = Node("child two")
root.add_child("n1", n1)
root.add_child("n2", n2)
print(root.children)  # [('n1', Node('child one')), ('n2', Node('child two'))]

del n1
print(root.children)  # [('n2', Node('child two'))]



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

여기서 부모는 자식들을 약한 참조를 갖고 있습니다. 이는 WeakValueDictionary를 사용합니다. 사전에서 참조된 요소(약한 참조)가 프로그램의 다른 곳에서 참조를 제거하면, 자동으로 사전에서도 제거됩니다. 따라서 사전 항목의 수명주기를 관리할 필요가 없습니다.

weakref의 또 다른 사용 예는 Observer 디자인 패턴에 있습니다:

```js
class Observable:
    def __init__(self):
        self._observers = weakref.WeakSet()

    def register_observer(self, obs):
        self._observers.add(obs)

    def notify_observers(self, *args, **kwargs):
        for obs in self._observers:
            obs.notify(self, *args, **kwargs)


class Observer:
    def __init__(self, observable):
        observable.register_observer(self)

    def notify(self, observable, *args, **kwargs):
        print("Got", args, kwargs, "From", observable)

subject = Observable()
observer = Observer(subject)
subject.notify_observers("test", kw="python")
# Got ('test',) {'kw': 'python'} From <__main__.Observable object at 0x757957b892d0>
```

Observable 클래스는 옵저버에 대한 약한 참조를 유지합니다. 이는 그들이 제거되었는지 여부를 신경쓰지 않기 때문입니다. 이전 예제와 마찬가지로, 이를 통해 종속 객체의 수명주기를 관리할 필요가 없습니다. 아마도 주목했을 것이지만, 이 예제에서는 WeakSet을 사용했습니다. 이는 WeakValueDictionary와 비슷하지만 Set을 사용하여 구현되었습니다.

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

이 섹션의 최종 예제는 weakref 문서에서 빌려왔습니다:

```js
import tempfile, shutil
from pathlib import Path

class TempDir:
    def __init__(self):
        self.name = tempfile.mkdtemp()
        self._finalizer = weakref.finalize(self, shutil.rmtree, self.name)

    def __repr__(self):
        return "TempDir({!r:})".format(self.name)

    def remove(self):
        self._finalizer()

    @property
    def removed(self):
        return not self._finalizer.alive

tmp = TempDir()
print(tmp)  # TempDir('/tmp/tmp8o0aecl3')
print(tmp.removed)  # False
print(Path(tmp.name).is_dir()) # True
```

이를 통해 weakref 모듈의 또 다른 기능을 확인할 수 있습니다. 바로 weakref.finalize입니다. 이름에서 암시하는 바대로 종속 객체가 가비지 수집될 때 최종화 함수/콜백을 실행할 수 있습니다. 이 예제에서는 가비지 수집될 때 TempDir 객체에 대해 rmtree를 자동으로 실행할 최종화가 실행됩니다. 프로그램이 완전히 종료될 때를 포함하여 TempDir이 가비지 수집될 때 실행되는 finalizer를 통해 TempDir를 정리하기를 항상 기억해야하지만 잊어버릴 경우에 대비할 수 있습니다.

# 실제 예제

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

앞서 섹션에서 weakref의 실용적인 사용 예를 보여드렸지만, 실세계 예제도 함께 살펴보겠습니다. 그 중 하나는 캐시된 인스턴스를 생성하는 것입니다:

```js
import logging
a = logging.getLogger("first")
b = logging.getLogger("second")
print(a is b)  # False

c = logging.getLogger("first")
print(a is c)  # True
```

위의 코드는 Python의 내장 logging 모듈의 기본적인 사용법입니다. 우리는 특정 이름에 하나의 로거 인스턴스만 관련시킬 수 있음을 알 수 있습니다. 즉, 동일한 로거를 여러 번 검색해도 항상 동일한 캐시된 로거 인스턴스가 반환됩니다.

만약 이를 구현하고 싶다면, 다음과 같이 할 수 있습니다:

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
class Logger:
    def __init__(self, name):
        self.name = name

_logger_cache = weakref.WeakValueDictionary()

def get_logger(name):
    if name not in _logger_cache:
        l = Logger(name)
        _logger_cache[name] = l
    else:
        l = _logger_cache[name]
    return l

a = get_logger("first")
b = get_logger("second")
print(a is b)  # False

c = get_logger("first")
print(a is c)  # True
```

마지막으로, Python 자체도 약한 참조를 사용합니다. OrderedDict의 구현 예시를 보겠습니다:

```python
from _weakref import proxy as _proxy

class OrderedDict(dict):

    def __new__(cls, /, *args, **kwds):
        self = dict.__new__(cls)
        self.__hardroot = _Link()
        self.__root = root = _proxy(self.__hardroot)
        root.prev = root.next = root
        self.__map = {}
        return self
```

위는 CPython의 collections 모듈에서의 일부 코드입니다. 여기서 weakref.proxy가 순환 참조를 막는 데 사용된다는 점을 유의하십시오 (더 많은 세부 정보는 doc-strings를 참조하세요).


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

weakref는 꽤 낯설지만 때때로 매우 유용한 도구로 여러분의 도구상자에 넣어두어야 할 가치가 있습니다. 이는 캐시를 구현하거나 이중 연결 리스트와 같이 서로 참조하는 데이터 구조를 다룰 때 매우 도움이 될 수 있습니다.

그렇지만 여기서 말한 모든 것과 문서에서 언급된 사항은 CPython에 특화되어 있으며 다른 Python 구현체에서는 weakref 동작이 다를 수 있음을 인식해야 합니다. 또한 리스트, 튜플 또는 정수와 같은 많은 내장 타입이 약한 참조를 지원하지 않는다는 사실을 알아두어야 합니다.

본 문서는 원문이 [martinheinz.dev](https://martinheinz.dev)에 게시되었습니다.

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

다음 항목을 추천해 드려요...