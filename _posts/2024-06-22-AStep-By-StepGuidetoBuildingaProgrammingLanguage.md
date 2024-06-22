---
title: "프로그래밍 언어를 만드는 단계별 가이드"
description: ""
coverImage: "/assets/img/2024-06-22-AStep-By-StepGuidetoBuildingaProgrammingLanguage_0.png"
date: 2024-06-22 23:34
ogImage: 
  url: /assets/img/2024-06-22-AStep-By-StepGuidetoBuildingaProgrammingLanguage_0.png
tag: Tech
originalTitle: "A Step-By-Step Guide to Building a Programming Language"
link: "https://medium.com/towards-data-science/a-step-by-step-guide-to-building-a-programming-language-5f5b84246991"
---


## 몇 시간 안에 처음부터 프로그래밍 언어 구축하기

![이미지](/assets/img/2024-06-22-AStep-By-StepGuidetoBuildingaProgrammingLanguage_0.png)

세상에는 다양한 용도로 만들어진 프로그래밍 언어가 가득합니다. 이러한 언어 중 대다수는 매우 일반적인 목적을 위해 만들어졌지만 때로는 매우 특정한 용도에 맞는 언어를 설계하기를 원할 수 있습니다(예: Facebook은 React를 개발하여 웹 응용 프로그램을 보다 쉽게 개발하기 위해 설계했고, Apple은 최근 Pkl이라는 언어를 개발하여 구성을 보다 쉽게 만들었습니다. 다양한 분야에서 이와 같은 예가 많이 있습니다). 따라서 프로그래밍 언어를 구축하는 방법을 알고 있는 것은 가지고 있는 것이 유용한 기술입니다.

이 문서에서는 처음부터 해석형 프로그래밍 언어를 만들고 람다 대수와 프로그래밍 언어 전반에 대해 알아가는 과정을 조금 배웁니다. 여기서 만들어볼 언어는 꽤 독특할 수 있지만, 이 과정을 통해 당신이 자신만의 특정 용도 언어를 설계하는 방법과 프로그래밍 언어가 어떻게 작동하는지에 대해 유용한 정보를 가르칠 것입니다.

<div class="content-ad"></div>

# 게임 계획

저희는 해석형 언어¹을 개발 중이니, 우리의 전반적인 흐름은 다음과 같을 것입니다:

![프로젝트 이미지](/assets/img/2024-06-22-AStep-By-StepGuidetoBuildingaProgrammingLanguage_1.png)

기본적으로, 우리는 우리가 작성하려는 대상 언어로 된 몇 가지 구체적인 구문(코드)으로 시작하고, 이를 추상 구문 트리로 변환하는 구문 분석기에 전달합니다(이것은 작업하기 쉬운 코드의 트리 표현입니다). 그 후에 실행된 추상 구문 트리를 우리에게 최종 결과를 제공하는 해석기에 전달합니다. 구문 분석기와 해석기는 이미 존재하는 호스트 언어로 작성됩니다 — 예를 들어 C 언어의 초기 구문 분석기와 컴파일러는 어셈블리어로 작성되었습니다.

<div class="content-ad"></div>

**참고: 여기서 사용하는 "파서"는 파싱 프로세스 전체를 포괄합니다. 일반적으로 렉싱은 "파싱" 이전에 수행됩니다. 그러나 이 경우 파싱은 구체적인 구문을 추상 구문으로 변환하는 프로세스입니다. 이 프로세스에는 다양한 형태가 있을 수 있습니다.

예를 들어, 기본 산술 연산을 위한 간단한 언어에 대한 다음 명세를 고려해보십시오:

```js
EXPR = number
       | EXPR + EXPR
       | EXPR - EXPR
       | EXPR * EXPR
       | EXPR / EXPR
       | (EXPR)
```

위의 내용은 컨텍스트-프리 문법에 대한 EBNF입니다. 여기서 깊이 들어가진 않겠지만, 이 형태의 프로그래밍 언어는 모두 CYK 알고리즘을 통해 다항 시간 내에 파싱할 수 있습니다. 이 EBNF에서 (4 + 4) * 3과 같은 것은 유효한 프로그램이지만, def f(x): return 5; f(5)와 같은 것은 유효하지 않습니다.**

<div class="content-ad"></div>

이제 우리가 구체적인 구문 (4 + 4) * 3을 주어졌다고 가정해 봅시다. 파싱 후에는 다음과 같은 추상 구문 트리(AST)를 얻어야 합니다:

![AST](/assets/img/2024-06-22-AStep-By-StepGuidetoBuildingaProgrammingLanguage_2.png)

그런 다음 우리의 해석기는 루트에서 시작하여 트리를 재귀적으로 타고 내려가면서 답인 24를 얻습니다.

이 문법이 모호한 점에 대한 간단한 언급을 해보면 — 예를 들어, 표현식 4 + 4 * 3은 어떻게 파싱되어야 할까요? 이것은 위에 언급된 ((4 + 4) * 3)로 파싱되거나, 4 + (4 * 3)로 파싱될 수 있습니다 — 이 둘 다 유효한 파싱 트리이기 때문에 더 이상 “정확한” 파싱이라고 말할 수 없습니다. 이와 같은 경우에는 파서가 언어를 파싱하는 방법에 대해 임의로 결정해야 합니다.

<div class="content-ad"></div>

# 저희 구문 설계하기

플로 차트에 따르면, 첫 번째 단계로 저희는 구체적인 구문을 설계해야 합니다. 구문을 만드는 방법은 완전히 당신에게 달려 있어요. 저는 EmojiLang이라는 (형편없는) 언어를 만들기로 결정했어요. 이 언어는 당신이 타이핑할 때 매우 다채로운 화면을 보장합니다. 문법은 아래와 같아요:

```js
grammar EmojiLang;

program: '🏃‍♂️🏃‍♂️🏃‍♂️' expr '🛑🛑🛑' EOF;

expr: '(' (ID
         | atom
         | ifCond
         | anonFunctionDefn
         | funApplication
         | varAssignment
         | READ_FLOAT
         | READ_STRING
         | printExpr
         | sequentialOp
         | baseComputation) ')';

atom: NUMBER | BOOLEAN | STRING;
ifCond: '🤔' cond=expr '❓' ontrue=expr ':' onfalse=expr;
anonFunctionDefn: '🧑‍🏭' arg=ID '⚒️' body=expr;
funApplication: '🏭' fun=expr arg=expr;
varAssignment: '📦' var=ID '🔜' val=expr;
printExpr: '🖨️' expr;
sequentialOp: '📋' first=expr second=expr;
baseComputation: left=expr op=('➕' | '➖' | '✖️' | '➗' | '🟰' | '≤') right=expr;


ID: [a-zA-Z_][a-zA-Z0-9_]*;
NUMBER: [0-9]+ ('.' [0-9]+)?;
BOOLEAN: '👍' | '👎';
STRING: '"' ~[\r\n"]* '"';
READ_FLOAT: '🔢';
READ_STRING: '🔤';


WHITESPACE: [ \t\r\n]+ -> skip;
COMMENT: '😴' .*? '⏰' -> skip;
LINE_COMMENT: '🥱' ~[\r\n]* -> skip;
```

** 참고: 위 명세서는 ANTLR이라는 도구에서 사용되도록 작성되었으며, 곧 다시 이에 대해 다룰 거예요.

<div class="content-ad"></div>

물론 언어 자체가 우스꽝스러워 보이지만 몇 가지 이유로 흥미로운 점들이 있어요. 우선, 모든 표현은 괄호로 둘러싸야 하는 것이 필수적이에요. 이렇게 하니 코드 작성이 매우 귀찮지만, 문법이 모호하지 않다는 장점이 있어요. 둘째로, 익명 함수만 정의할 수 있다는 점을 주목해봐요 — 파이썬의 def와 같은 구문이 없어요. 마지막으로, 이 언어의 모든 함수 (기본 계산 외)는 정확히 하나의 인수를 가져야 해요. 조금 후에 이 두 가지 설계 결정의 영향을 살펴볼게요.

# 파싱

물론, 우리 스스로 파서를 작성할 수 있어요. 그러나 다행히도 임의의 문맥-자유 문법을 구문 분석할 수 있는 도구들이 있어요. 이 튜토리얼에서는 ANTLR을 사용할 거에요 (여기서 다운로드할 수 있어요). ANTLR은 위와 같은 문법 명세를 받아들여 이를 다양한 대상 언어의 파서로 생성해주는 매우 좋고 쉬운 도구에요 (이 튜토리얼에서는 Python을 사용할 거예요).

사용법은 꽤 간단해요. 아래 단계를 따라해보세요:

<div class="content-ad"></div>

- 여기에서 ANTLR Java 이진 파일을 다운로드하세요.
- 문법에 맞는 .g4 파일(위와 같은)을 만드세요. ANTLR는 이모지를 잘 처리하지 못하기 때문에, 언어에 이모지를 사용할 계획이라면 다음 파이썬 스크립트를 실행하여 문법에서 이모지를 제거해주십시오:

```js
import emoji
import sys

def demojify_file(input_file, output_file):
    with open(input_file, "r", encoding="utf-8") as f:
        input_text = f.read()

    input_text = emoji.demojize(input_text)
    
    with open(output_file, "w", encoding="utf-8") as f:
        f.write(input_text)
        
if __name__ == "__main__":
    input_file = sys.argv[1]
    output_file = sys.argv[2]
    demojify_file(input_file, output_file)
```

3. java -Xmx500M -cp `path_to_antlr.jar` org.antlr.v4.Tool -Dlanguage=Python3 `your_grammar.g4` 명령을 실행하여 파서를 생성하세요.

그런 다음 생성된 구문 분석 파일을 가져와 다음과 같이 사용할 수 있습니다:

<div class="content-ad"></div>

```python
from antlr4 import *
from EmojiLangLexer import EmojiLangLexer
from EmojiLangParser import EmojiLangParser
from EmojiLangListener import EmojiLangListener
import emoji

if __name__ == "__main__":
    input_file = sys.argv[1]
    with open(input_file, "r", encoding="utf-8") as f:
        input_text = f.read()

    input_text = emoji.demojize(input_text)
    input_stream = InputStream(input_text)
    lexer = EmojiLangLexer(input_stream)
    token_stream = CommonTokenStream(lexer)
    parser = EmojiLangParser(token_stream)
    tree = parser.program()
    
    if parser.getNumberOfSyntaxErrors() > 0:
        exit(1)
```

아마도 이 경우에는 데모지징 단계가 필요하지 않을 것입니다. 그런 경우에는 InputStream 대신 antlr4의 FileStream를 사용할 수 있지만, 실제로 큰 차이가 없습니다. 이제 우리는 쉽게 처리할 수 있는 매우 좋은 추상 구문 트리를 갖게 되었고, 이제 어려운 부분인 해석으로 넘어갈 차례입니다³

# 해석기 구축

트리를 다루고 있기 때문에 우리의 해석기는 자연스럽게 재귀적인 개념이 될 것입니다. 그러나 일부 기능을 정확히 어떻게 구현할지에 대한 선택지가 있습니다. 이 튜토리얼에서는 변수를 주소에 바인딩하는 환경을 사용한 뒤, 주소를 값에 매핑하는 가변 스토어를 사용하는 해석기를 구축할 것입니다. 이 아이디어는 꽤 흔한데, 모든 곳에서 사용되는 것은 아니지만 적절한 범위를 유지하고 변수 변이를 지원할 수 있게 합니다. 구현의 편의를 위해 우리의 해석기는 공통 값 구조체를 반환하도록 할 것입니다.


<div class="content-ad"></div>

## 값, 저장소 및 환경

먼저, 우리의 인터프리터가 출력할 수 있는 것을 정의해 봅시다. 우리의 EBNF에는 세 가지 명백한 기본 사례가 있습니다(즉, 부울 값, 문자열 및 숫자), 따라서 해당 값을 가지고 있는 객체를 만들어 봅시다:

```js
class Value:
    def __init__(self, value):
        self.value = value
        
    def __str__(self) -> str:
        return str(self.value)
        
class NumValue(Value):
    def __init__(self, value: float):
        super().__init__(value)
        
class StringValue(Value):
    def __init__(self, value: str):
        super().__init__(value)
        
class BoolValue(Value):
    def __init__(self, value: bool):
        super().__init__(value)
```

변수를 값들로 매핑하기 위해, 우리는 환경과 저장소도 만들어 볼 것입니다:

<div class="content-ad"></div>

```python
class EnvLookupException(Exception):
    pass
        
class Environment:
    def __init__(self):
        self.vars = {}
        
    def set_var(self, name, addr: int):
        self.vars[name] = addr
        
    def get_var(self, name):
        if name not in self.vars:
            raise EnvLookupException(f"Variable {name} not found in environment")
        return self.vars[name]
    
    def copy(self):
        new_env = Environment()
        new_env.vars = self.vars.copy()
        return new_env
    
class Store:
    def __init__(self):
        self.store = []
        
    def alloc(self, value: Value):
        self.store.append(value)
        return len(self.store) - 1
    
    def get(self, addr: int):
        if addr >= len(self.store):
            raise EnvLookupException(f"Address {addr} not found in store")
        return self.store[addr]
    
    def set(self, addr: int, value: Value):
        if addr >= len(self.store):
            raise EnvLookupException(f"Address {addr} not found in store")
        self.store[addr] = value
```

우리의 환경은 변수 → 주소 바인딩을 저장하고, store는 주소 → 값 바인딩을 유지합니다. 현재 기능 세트에서 store는 필요하지 않을 수 있지만, 참조에 의한 변수 변경을 허용한다면 유용할 것입니다.

이상적으로, 함수를 변수로 전달할 수도 있기를 원하므로, 더 많은 값을 나타낼 수 있는 하나의 값이 필요합니다. 이를 위해 우리는 클로저를 생성합니다. 클로저에는 함수의 매개변수, 본문, 및 생성된 환경이 포함됩니다:

```python
class ClosureValue(Value):
    # Body should be an antlr4 tree
    def __init__(self, param: str, body: object, env: 'Environment'):
        super().__init__(None)
        self.param = param
        self.body = body
        self.env = env
        
    def __str__(self) -> str:
        return f"<function>"
```

<div class="content-ad"></div>

너가 환경을 함수에 저장해야 하는 이유에 대해 물을 수도 있겠지만, 만약에 우리가 다음과 같은 함수 값을 가지고 있는 경우를 상상해보자:

```js
class FunctionValue(Value):
    # Body should be an antlr4 tree
    def __init__(self, param: str, body: object):
        super().__init__(None)
        self.param = param
        self.body = body
        
    def __str__(self) -> str:
        return f"<function>"
```

이제, 우리 언어에서 다음과 같은 코드와 동등한 코드를 가졌다고 가정해보자:

```js
# ----------------
# ENV MUST PERSIST
# ----------------
def f(x):
  def g(y):
    return x + y
  return g(x)

print((f(4))(5))  # 9

# ----------------
# ENV MUST CLEAR
# ----------------
def f2(x):
  return x + y

def g2(y):
  return f(5)

print(f(4))  # Should crash
```

<div class="content-ad"></div>

위 케이스에서 g에 대한 여전히 y 범위를 보장하려면 동적 스코핑을 구현해야 합니다 (프로그램이 실행되는 동안 변수가 지워지지 않고 환경에 추가되는 범위). 서퍼 코드가 실제로 실행되고 9를 인쇄할 수 있도록 클로저 없이 동적 스코핑을 적용해야 합니다. 그러나 하단 코드가 제대로 충돌하려면 동적 스코프를 구현할 수 없습니다. 따라서 함수가 생성된 환경을 효과적으로 기억하도록 하려면 환경을 클로저 클래스에 추가해야 합니다⁵.

## 인터프리터

이제 실제 인터프리터를 작성할 준비가 되었습니다. ANTLR에서 인터프리터는 EmojiLangListener 클래스를 확장할 것입니다. 또한 최상위 환경을 만들고 인터프리터에 저장소를 제공해야 합니다:

```js
class EmojiLangException(Exception):
    pass
        
TOP_ENV = Environment()

class Interpreter(EmojiLangListener):
    def __init__(self):
        self.store = Store()
```

<div class="content-ad"></div>

지금은 모든 표현 사례를 처리하는 interp 메서드를 생성해야 합니다. 아래와 같이 보일 것입니다:

```js
def interp(self, prog, env: Environment) -> Value:
    if prog.ID():
        return self.interp_id(prog.ID())
    elif prog.atom():
        return self.interp_atom(prog.atom())
    elif prog.anonFunctionDefn():
        return self.interp_function_defn(prog.anonFunctionDefn())
    elif prog.READ_FLOAT():
        return self.interp_read_float()
    elif prog.READ_STRING():
        return self.interp_read_string()
    elif prog.printExpr():
        return self.interp_print_expr()
    elif prog.ifCond():
        return self.interp_if(prog.ifCond(), env)
    elif prog.sequentialOp():
        return self.interp_sequential_op(prog.sequentialOp(), env)
    elif prog.baseComputation():
        return self.interp_base_computation(prog.baseComputation(), env)
    elif prog.varAssignment():
        return self.interp_var_assignment(prog.varAssignment(), env)
    elif prog.funApplication():
        return self.interp_fun_application(prog.funApplication(), env)
```

우리의 기본 사례(ID, atom, 함수 정의, 읽기, 출력)는 꽤 단순하기 때문에, 우리는 이렇게 적을 수 있습니다:

```js
def interp(self, prog, env: Environment) -> Value:
    if prog.ID():
        return self.store.get(env.get_var(prog.ID().getText()))
    elif prog.atom():
        return self.interp_atom(prog.atom())
    elif prog.anonFunctionDefn():
        return ClosureValue(prog.anonFunctionDefn().arg.text, prog.anonFunctionDefn().body, env)
    elif prog.READ_FLOAT():
        try:
            return NumValue(float(input("> ")))
        except ValueError:
            raise EmojiLangException("Expected float input")
    elif prog.READ_STRING():
        return StringValue(input("> "))
    elif prog.printExpr():
        value = self.interp(prog.printExpr().expr(), env)
        if isinstance(value, StringValue):
            # print without quotes
            print(str(value)[1:-1])
        else:
            print(value)
        return value
    # ...

def interp_atom(self, atm):
    if atm.NUMBER():
        return NumValue(float(atm.NUMBER().getText()))
    elif atm.BOOLEAN():
        return BoolValue(atm.BOOLEAN().getText() == ":thumbs_up:")
    elif atm.STRING():
        return StringValue(atm.STRING().getText())
    # 이런 경우는 절대 발생해서는 안 됩니다.
    raise EmojiLangException(f"Unknown atom {atm.getText()}")
```

<div class="content-ad"></div>

우리의 if 조건은 꽤 간단합니다. 조건을 해석한 다음, 조건이 참이면 참인 경우를 해석한 결과를 반환하고 거짓인 경우는 거짓인 경우를 해석한 결과를 반환하면 됩니다. 코드는 다음과 같습니다.

```js
def interp_if(self, if_cond, env: Environment):
    cond = self.interp(if_cond.cond, env)
    if not isinstance(cond, BoolValue):
        raise EmojiLangException(f"Expected boolean when evaluating if condition, got {cond}")
    return self.interp(if_cond.ontrue if cond.value else if_cond.onfalse, env)
```

연속적인 동작도 마찬가지로 간단합니다. 첫 번째 표현식을 해석한 다음 두 번째 표현식을 해석하면 됩니다. 따라서 해당 블록의 코드를 다음과 같이 바꿀 수 있습니다.

```js
def interp(self, prog, env: Environment) -> Value:
  # ...
  elif prog.sequentialOp():
    self.interp(prog.sequentialOp().first, env)
    return self.interp(prog.sequentialOp().second, env)
  # ...
```

<div class="content-ad"></div>

다음은 기본 계산이 있습니다. 많은 작업을 처리해야하기 때문에 코드 양이 상당한 편이지만, 복잡하지는 않아요:

```js
def interp_base_computation(self, base_computation, env: Environment):
    left, right = self.interp(base_computation.left, env), self.interp(base_computation.right, env)
    if base_computation.op.text == ":plus:":
        if isinstance(left, NumValue) and isinstance(right, NumValue):
            return NumValue(left.value + right.value)
        elif isinstance(left, StringValue) and isinstance(right, StringValue):
            return StringValue(left.value + right.value)
        raise EmojiLangException(f"{left}과 {right}을(를) 더할 수 없습니다")
    if base_computation.op.text == ":heavy_equals_sign:":
        if type(left) != type(right):
            return BoolValue(False)
        if isinstance(left, ClosureValue):
            raise EmojiLangException("함수를 비교할 수 없습니다")
        return BoolValue(left.value == right.value)
    
    # 숫자만 계산
    if not isinstance(left, NumValue) or not isinstance(right, NumValue):
        raise EmojiLangException(f"기본 계산 평가 시 숫자가 예상됩니다. {left}과 {right}을(를) 받았습니다")
    if base_computation.op.text == ":minus:":
        return NumValue(left.value - right.value)
    elif base_computation.op.text == ":multiply:":
        return NumValue(left.value * right.value)
    elif base_computation.op.text == ":divide:":
        if right.value == 0:
            raise EmojiLangException("0으로 나눌 수 없습니다")
        return NumValue(left.value / right.value)
    elif base_computation.op.text == "≤":
        return BoolValue(left.value <= right.value)   
```

아마도 사전(dictionary)을 사용하여 조금 정리할 수도 있지만, 그것은 크게 중요하지 않아요. 그 다음에는 변수 할당이 있습니다. 이것도 별로 복잡하지 않죠. 여기서 우리가 정확히 무엇을 원하는지에 대한 몇 가지 선택 사항이 있습니다. 구체적으로, 새 변수가 새롭게 생성되는 것을 허용할지 기존 변수만 수정할지 결정해야 합니다. 저는 후자를 선택하겠습니다. 그 결과 코드는 다음과 같습니다:

```js
def interp_var_assignment(self, var_assign, env: Environment):
    value = self.interp(var_assign.val, env)
    addr = env.get_var(var_assign.var.text)
    self.store.store[addr] = value
    return value
```

<div class="content-ad"></div>

마지막으로, 함수 적용이 있습니다. 여기서는 네 가지 단계를 따라야 합니다. 먼저 호출하는 함수를 해석하여 클로저를 가져옵니다. 그런 다음 인수를 해석합니다. 그런 다음 인수를 클로저의 환경 복사본에 바인딩합니다. 마지막으로 새로운 환경에서 클로저의 본문을 해석합니다. 코드는 다음과 같이 보이게 됩니다:

```js
def interp_fun_application(self, fun_app, env: Environment):
    closure = self.interp(fun_app.fun, env)
    if not isinstance(closure, ClosureValue):
        raise EmojiLangException(f"함수 적용을 평가할 때 함수가 예상되었습니다. 해당 함수: {closure}")
    arg = self.interp(fun_app.arg, env)
    new_env = closure.env.copy()
    new_env.set_var(closure.param, self.store.alloc(arg))
    return self.interp(closure.body, new_env)
```

이제 우리의 해석기가 완벽히 동작합니다! 이제 우리가 실행할 프로그램에 대해 메인 프로그램을 수정하는 것만 남았습니다:

```js
if __name__ == "__main__":
    input_file = sys.argv[1]
    with open(input_file, "r", encoding="utf-8") as f:
        input_text = f.read()

    # 입력에서 이모지를 데모지로 변환하기 위해 전처리
    input_text = emoji.demojize(input_text)

    input_stream = InputStream(input_text)
    lexer = EmojiLangLexer(input_stream)
    token_stream = CommonTokenStream(lexer)
    parser = EmojiLangParser(token_stream)
    tree = parser.program()
    
    if parser.getNumberOfSyntaxErrors() > 0:
        exit(1)
    
    interpreter = Interpreter()
    interpreter.interp(tree.expr(), TOP_ENV)
```

<div class="content-ad"></div>

# 우리 언어로 놀아보기

이제 우리 언어로 프로그램 작성을 시작할 준비가 끝났어요. 이모지 언어 (EML)로 작성한 간단한 'Hello World!' 프로그램을 확인해보세요:

```js
🏃‍♂️🏃‍♂️🏃‍♂️
    (🖨️ ("Hello World!"))
🛑🛑🛑
```

그리고 이를 실행하려면 단순히 이렇게 하시면 돼요

<div class="content-ad"></div>


> python emoji_lang.py helloworld.eml
Hello World!


(위 코드는 물론, 해당 프로그램이 helloworld.eml이라는 파일에 존재한다고 가정합니다.)

## 커링

첫 번째 섹션에서 언급했듯이, 우리의 프로그래밍 언어는 함수가 하나의 인수만을 가져야 한다는 점에서 흥미로운 특징을 가지고 있습니다. 그렇다면 다변수 함수와 유사한 효과를 어떻게 만들 수 있을까요? 예를 들어, 다음의 파이썬 코드를 살펴보십시오:

<div class="content-ad"></div>

```python
def f(x, y):
  return x + y

print(f(3, 4))
```

여기서 f 는 arity 2 — 즉, 두 개의 argument 를 가집니다. 그러나, addition 을 제외한 함수의 arity 가 1 인 함수들만 사용하는 동등한 코드를 아래와 같이 작성할 수 있습니다:

```python
def f(x):
  def g(y):
    return x + y
  return g

print((f(3))(4))
```

위와 같은 고차 arity 함수를 일변량 함수로 변환하는 개념을 currying 이라고 합니다. 이는 어떤 arity 의 함수에 대해서도 동작하며, arity 가 n 인 함수 f 에 대해 n-1 번의 currying 만을 수행하면 됩니다. 이를 이모지 언어에서 표현하면 다음과 같이 프로그램을 작성할 수 있습니다:

<div class="content-ad"></div>


🏃‍♂️🏃‍♂️🏃‍♂️
   (📋
       (🖨️ ("두 숫자를 입력하여 합계를 계산합니다."))
       (🖨️
           (🏭
               (🏭 
                   (🧑‍🏭 x ⚒️ 
                       (🧑‍🏭 y ⚒️
                           ((x) ➕ (y))
                       )
                   )
               (🔢))
           (🔢))
       )
   )
🛑🛑🛑


이것의 Python 번역은:


print("두 숫자를 입력하여 합계를 계산합니다.")
print(((lambda x: (lambda y: x + y)))(float(input()))(float(input())))


또는 더 읽기 쉽게,

<div class="content-ad"></div>

```js
print("두 숫자를 입력하여 합산합니다.")

def f(x):
  def g(y):
    return x + y
  return g

x = float(input())
y = float(input())

print(f(x)(y))
```

또한 처음의 Python 반복이 이름이 지정된 함수를 사용하지 않았다는 것에 유의하십시오. 하지만 우리가 실제로 필요하지는 않지만, 물론 가독성을 위해 유용합니다. 그런 다음,
```js
> python emoji_lang.py currying.eml
두 숫자를 입력하여 합산하시오
> 4
> 5
9.0
```

## 재귀

<div class="content-ad"></div>

그러나 똑똑한 작은 요령을 쓸 수 있어요. 함수들이 값이기 때문에, 함수를 호출할 때 자기 자신에 대한 함수를 전달할 수 있어요. 그렇게 하면 재귀 호출이 가능해져요.

예를 들어 다음 파이썬 코드를 봐봅시다:

```js
n = int(input())
while n > 0:
  print(n)
  n -= 1
```

<div class="content-ad"></div>

우리는 이를 재귀 버전으로 변환할 수 있습니다. 다음과 같은 방법을 사용할 수 있어요:

```js
def while_loop(condition, body):
  """
  while 루프의 재귀적 구현.

  Arguments
  -------------
  - condition: 부울값을 반환하는 매개변수가 없는 함수
  - body: condition이 true를 반환하는 동안 실행할 매개변수가 없는 함수
  """
  if condition():
    body()
    while_loop(condition, body)
  else:
    return False

class Box:
    def __init__(self, n):
        self.n = n
    
    def set_n(self, n):
        self.n = n
        
    def get_n(self):
        return self.n
    
n = Box(int(input()))

def body():
    print(n.get_n())
    n.set_n(n.get_n() - 1)

while_loop(lambda: n.get_n() > 0, body)
```

하지만 여기서 문제가 있습니다. while_loop 함수가 자기 자신을 호출하는 것을 주목해주세요. 익명 함수만으로는 언어에서 그렇게는 할 수 없기 때문에 어떻게 해결할까요? 답은 다음과 같이 할 수 있습니다:

```js
def while_loop(self, condition, body):
  if condition():
    body()
    self(self, condition, body)
  else:
    return False

# ...
# (상자로 n을 정의)
# ...

def body():
    print(n.get_n())
    n.set_n(n.get_n() - 1)

def call_while(loop_func, condition, body):
    loop_func(loop_func, condition, body)
    
call_while(while_loop, lambda: n.get_n() > 0, body)
```

<div class="content-ad"></div>

실제로, while_loop 함수가 자신을 매개변수로 취하도록 만듭니다. 그럼에도 불구하고 while_loop 함수 내에서 자신을 호출할 수 있게 되어 while_loop 함수가 재귀적으로 자신을 호출할 수 있게 됩니다.

물론, 우리의 언어에 lambda화하고 커링해야 하므로, 다음과 동등한 코드를 만들어야 합니다:

```js
(((lambda while_loop: 
    lambda n: 
        while_loop(while_loop)
                  (lambda bogus: n.get_n() > 0)
                  (lambda bogus: print(n.get_n()) or n.set_n(n.get_n() - 1)))
(lambda self:
    lambda cond:
        lambda body:
            (body("BOGUS") or self(self)(cond)(body)) if cond("BOGUS") else False))
(Box(int(input()))))
```

이것은 약간 복잡할 수 있지만 작동합니다. 이를 이모지 언어로 표현하면

<div class="content-ad"></div>

```js
🏃‍♂️🏃‍♂️🏃‍♂️
    (🏭
        (🏭
            (🧑‍🏭 while ⚒️
                (🧑‍🏭 n ⚒️
                    (🏭 (🏭 (🏭 (while) (while))
                        (🧑‍🏭 bogus ⚒️ (🤔 ((n) ≤ (0)) ❓ (👎) : (👍))))
                        (🧑‍🏭 bogus ⚒️ (📋
                            (🖨️ (n))
                            (📦 n 🔜 ((n) ➖ (1)))
                        )))
                ))
            😴
                아래는 while 함수입니다. 재귀 호출을 위해 자신을 인자로 받습니다 
                (이 외에도 이를 수행하는 다른 방법이 있지만, 재귀 호출을 통한 자기 전달은 상당히 간단합니다)

                ARGS: 
                1. self(자기 자신)
                2. condition_func (참 또는 거짓을 반환하는 0인자 함수)
                3. body (참일 때 실행할 아무것도 반환하지 않는 0인자 함수)

                RETURNS:
                끝날 때 거짓 반환
            ⏰
            (🧑‍🏭 self ⚒️
                (🧑‍🏭 condition_func ⚒️
                    (🧑‍🏭 body ⚒️
                        (
                            🤔 (🏭 (condition_func) ("BOGUS")) ❓ 
                                (📋 
                                    (🏭 (body) ("BOGUS")) 
                                    (🏭 (🏭 (🏭 (self) (self))
                                            (condition_func))
                                            (body))) : 
                                (👎)
                        ))))
        )
    (🔢))       
🛑🛑🛑
```

그런 다음

```js
> python emoji_lang.py while_loop.eml
> 4
4.0
3.0
2.0
1.0
```

## 추가 보너스: Y 컴비네이터


<div class="content-ad"></div>

우리가 호출할 때마다 while에 자체를 전달하는 것은 다소 귀찮은 작업일 수 있습니다. 그래서 이미 커리된 상태로 자체가 있는 while 함수를 만들 수 있다면 어떨까요? 이를 달성할 수 있는 것이 Y Combinator입니다. Y 컴비네이터는 다음과 같이 보입니다:

```js
Y = lambda g: (lambda f: g(lambda arg: f(f)(arg))) (lambda f: g(lambda arg: f(f)(arg)))
```

이것은 완전히 터무니없는 것이지만, 함수를 효과적으로 "저장"할 수 있게 해줍니다. 이에 대해 자세히 설명은 생략하겠지만, 더 알고 싶다면 이 훌륭한 기사를 참고하는 것이 좋습니다.

이 컴비네이터를 사용하면 우리의 코드를 다음과 같이 변경할 수 있습니다:

<div class="content-ad"></div>


🏃‍♂️🏃‍♂️🏃‍♂️
    (🏭
        (🏭
            (🏭
                (🧑‍🏭 y_combinator ⚒️
                    (🧑‍🏭 while ⚒️
                        (🧑‍🏭 n ⚒️
                            (📋
                                🥱 y-combinate our while
                                (📦 while 🔜 (🏭 (y_combinator) (while)))
                                (🏭 (🏭 (while)
                                        (🧑‍🏭 bogus ⚒️ (🤔 ((n) ≤ (0)) ❓ (👎) : (👍))))
                                        (🧑‍🏭 bogus ⚒️ (📋
                                                (🖨️ (n))
                                                (📦 n 🔜 ((n) ➖ (1)))
                                        ))
                                )
                            )
                        )
                    )
                )
                😴
                    Our y combinator function - this allows for recursion without e.g. self passing
                    by effectively currying the function and passing it to itself.
                ⏰
                (🧑‍🏭 fn_nr ⚒️
                    (🏭
                       (🧑‍🏭 cc ⚒️
                            (🏭 (fn_nr) 
                                (🧑‍🏭 x ⚒️ (🏭 (🏭 (cc) (cc)) (x)))
                            )
                       )
                       (🧑‍🏭 cc ⚒️
                            (🏭 (fn_nr) 
                                (🧑‍🏭 x ⚒️ (🏭 (🏭 (cc) (cc)) (x)))
                            )
                       )
                    )
                )
            )
            (🧑‍🏭 while ⚒️
                    (🧑‍🏭 condition_func ⚒️
                        (🧑‍🏭 body ⚒️
                            (
                                🤔 (🏭 (condition_func) ("BOGUS")) ❓ 
                                    (📋 
                                        (🏭 (body) ("BOGUS")) 
                                        (🏭 (🏭 (while)
                                                (condition_func))
                                                (body))) : 
                                    (👎)
                            ))))
        )  
    (🔢))    
🛑🛑🛑


이제, y-컴비네이트 된 후 while에 대한 호출이 그 자신을 전달할 필요가 없이 조건과 본문을 전달하는 것만 포함되어 있다는 점에 주목하세요. 그리고 여전히 다음 결과를 얻을 수 있습니다.

```js
> python emoji_lang.py y_comb_while.eml
> 4
4.0
3.0
2.0
1.0
```

# 결론

<div class="content-ad"></div>

축하드려요! 이제 아마도 여러분만의 프로그래밍 언어를 만들었고 그 안에서 재미있는 일들을 코딩했을 거예요. EmojiLang 같은 것은 실제로 많은 유용성이 없지만, 여러분만의 언어를 만들어 다양한 용도로 활용해볼 수 있다는 것을 상상할 수 있어요. 예를 들어 자주 하는 작업을 빠르게 수행할 수 있는 초 특정한 스크립팅 언어를 만들어보는 것 등이 있겠죠.

도전을 좋아하신다면, 아래 연습 문제들을 시도해보세요:

**연습 1:** ANTLR를 사용하지 않고 아래 언어에 대한 간단한 파서와 인터프리터를 만드세요. 괄호가 우선순위를 갖고, 그 외 연산은 동등한 우선순위를 갖도록 해보세요. (예: 4 + 4 * 3은 24로 계산되어야 하며, 16이 아니어야 합니다)

```js
EXPR = 숫자
       | EXPR + EXPR
       | EXPR - EXPR
       | EXPR * EXPR
       | EXPR / EXPR
       | (EXPR)
```

<div class="content-ad"></div>

Exercise 2: 위의 코드를 수정하여 연산자 우선순위를 추가해보세요.

Exercise 3 (Tricky): 모든 함수를 익명으로 만들 필요는 없습니다. 다음 언어를 위한 인터프리터를 구현해보세요 (ANTLR을 사용해도 되지만, 직접 .g4 파일을 작성해야 합니다):

```js
program = (FUNDEF | EXPR)* // 하나 이상의 함수 정의 또는 표현식

// 참고: <something>은 해당 것이 문자열임을 의미합니다
// 또한, 공백을 무시하거나 세미콜론을 추가하거나 괄호를 추가하여
// 표현식 또는 함수 정의를 원하는 대로 작성하세요

EXPR = 숫자
     | 함수적용
     | 계산

FUNDEF = 'def' <name> '(' <args>* '):' EXPR

함수적용 = <name> '(' EXPR* ')' // 예: f(1, 2+2, g(3))
계산 = EXPR + EXPR
       | EXPR - EXPR
       | EXPR * EXPR
       | EXPR / EXPR
       | (EXPR)
```

Exercise 4 (Easy → Very Very Hard): 다양한 실제 언어에 대한 .g4 파일은 여기서 찾을 수 있습니다. 그 중 하나에 대한 인터프리터를 구현해보세요.

<div class="content-ad"></div>

어떤 문의 사항이 있으시면 mchak@calpoly.edu 로 연락해주세요.

부. CS 430 교수님 브라이언 존스에게 많은 것을 가르쳐 주셔서 감사합니다.

모든 이미지는 작성자 소유이나 그렇지 않은 경우를 제외하고 있습니다.

- 컴파일된 언어들은 약간 다릅니다. 생성된 추상 구문 트리는 대신 컴파일러로 전달되어 다른 기존 언어(대부분 어셈블리)로 코드로 변환됩니다. 그런 다음 결과 코드를 실행할 수 있습니다.
- 기술적으로 이것은 실제 EBNF에 있는 것은 아니지만, 충분히 가깝습니다. 궁금하시다면 EBNF에 대해 더 읽어보세요.
- 참고: 일부 언어들은 해석기로 전달하기 전에 AST에 최적화를 수행할 수 있습니다. 여기서는 그렇게 하지는 않겠습니다.
- 여전히 환경에서 상자 값을 사용하여 변이를 허용할 수는 있지만, 스토어는 대부분의 언어가 하는 것과 더 유사하며, 적어도 나에게는 더 직관적입니다.
- 자세한 정보는 7 Functions Anywhere (brown.edu) 참조하세요.

<div class="content-ad"></div>

6. 여기서 상자를 사용해야 하는 이유는 Python 환경이 우리와 같은 방식으로 작동하지 않기 때문입니다. 예를 들어, 다음을 실행할 수 없습니다:

```js
# ...
def get_body_and_cond(inp):
    n = inp
    condition = lambda: n > 0
    
    def body():
        print(n)
        n -= 1
    return condition, body
    
c, b = get_body_and_cond(float(input()))

while_loop(c, b)
```

7. 실제로 우리의 언어는 즉시 무엇을 평가하기 때문에 Z-콤비네이션이 되었습니다만, 효과는 같습니다.