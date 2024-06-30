---
title: "간단한 JavaScript 로거 사용 방법"
description: ""
coverImage: "/assets/img/2024-06-30-SimpleJavascriptLogger_0.png"
date: 2024-06-30 22:31
ogImage: 
  url: /assets/img/2024-06-30-SimpleJavascriptLogger_0.png
tag: Tech
originalTitle: "Simple Javascript Logger"
link: "https://medium.com/@yadaom/simple-javascript-logger-e557854d0f32"
---


# 다시 한번, Java 프로그래머가 JavaScript에서 일하며 Java 로거를 그리워하는 이야기 :-)

![이미지](/assets/img/2024-06-30-SimpleJavascriptLogger_0.png)

JavaScript와 React에 대한 사랑이 커짐에 따라, 내 React 애플리케이션에 간단한 로거가 필요하다는 필요성이 커지고 있습니다. 몇 가지 로거를 시도해봤지만, 간단한 Java 스타일의 로거에 대한 취향이 강하기 때문에 TypeScript로 나만의 간단한 Logger 클래스를 작성하게 되었고, 현재 개인 프로젝트에서 사용하고 있습니다.

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

아래에 `logger.ts` 파일을 찾으실 수 있어요. 마음껏 사용하고 수정해 보세요 :)

# 내가 Logger를 좋아하는 이유:

- 커스터마이징 가능한 로그 레벨: 각 모듈에 대해 나만의 로그 레벨을 추가할 수 있어요.
- 조절 가능한 로깅 디테일: 얼마나 많은 정보를 로깅할 지 결정할 수 있어요. 기본적으로 info 수준의 정보를 로깅하며, 필요에 따라 로그 레벨을 높일 수 있어요.
- 전문화된 Logger: 서로 다른 모듈에 대해 특별한 로거를 추가하기 쉬워요.
- 확장성: 현재의 Logger를 수정하거나 확장해서 로그를 서버로 전송할 수 있어요.

```js
import {useMemo} from "react";

export enum LogLeveL {
  ERROR = 100,
  WARN = 200,
  INFO = 400,
  AUTH = 401,
  MIDDLE_LAYER = 402,
  DEBUG = 800
}

export const GLOBAL_LEVEL = LogLeveL.INFO;

export class Logger {
  protected readonly prefix: string;

  constructor(prefix: string, protected readonly currentLevel: LogLeveL = GLOBAL_LEVEL) {
    this.prefix = prefix.replaceAll("/", ".");
  }

  info(...messages: any[]) {
    this._log(LogLeveL.INFO, ...messages);
  }

  warn(...messages: any[]) {
    this._log(LogLeveL.WARN, ...messages);
  }

  debug(...messages: any[]) {
    this._log(LogLeveL.DEBUG, ...messages);
  }

  error(...messages: any[]) {
    this._log(LogLeveL.ERROR, ...messages);
  }

  protected _log(level: LogLeveL, ...messages: any[]) {
    if (level <= GLOBAL_LEVEL)
      console.info(`[${LogLeveL[level]}][${this.prefix}]`, ...messages);
  }

  log(...messages: any[]) {
    this._log(this.currentLevel, ...messages);
  }
}

export class AuthLogger extends Logger {

  constructor(prefix: string) {
    super(prefix, LogLeveL.AUTH);
  }

  auth(...messages: any[]) {
    this._log(LogLeveL.AUTH, ...messages);
  }
}


export function useLogger(name: string) {
  return useMemo(() => {
    return new Logger(name);
  }, [name]);
}
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

# 사용 예시, 사용 방법:

```js
type AuthComponentProps = {}
const logger: AuthLogger = new AuthLogger("app/page/AuthComponent");
const AuthComponent = (props: AuthComponentProps) => {
  useEffect(() => {
    // 인증 로거입니다.
    logger.auth("인증 호출을 수행 중입니다.");// 즉 인증 로그 레벨 호출만 기록합니다.
    logger.info("이것은 정보 메시지입니다.");
    logger.warn("이것은 경고 메시지입니다.");
    logger.error("이것은 오류 메시지입니다.");
    logger.debug("이것은 디버그 메시지입니다.");
  }, []);
  return (
    <div>
      로그인 컴포넌트입니다.<br/>
      사용자: ____<br/>
      비밀번호: ____<br/>
    </div>
  );
};

// 로그 레벨을 변경하려면 GLOBAL_LEVEL 변수의 값을 조정하십시오.
```

# 주요 기능

- 사용자 정의 로그 레벨: LogLeveL 열거형을 사용하면 필요에 따라 다양한 로그 레벨을 지정할 수 있습니다.
- 전역 로그 레벨: GLOBAL_LEVEL 상수는 전역 로그 레벨을 결정하여 로그의 상세 수준을 제어할 수 있습니다.
- Logger 클래스: Logger 클래스는 다른 수준의 메시지를 기록하는 방법을 제공하며 (info, warn, debug, error), 적절한 로그 레벨인 경우 메시지를 콘솔에 출력하기 위해 _log 메서드를 사용합니다.
- 전문 로거: AuthLogger 클래스는 Logger를 확장하여 인증 관련 메시지를 기록하는 auth 메서드를 추가합니다.
- React 훅: useLogger 훅은 useMemo를 사용하여 새로운 Logger 인스턴스를 만들어 컴포넌트 당 로거가 한 번만 생성되도록 합니다.

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

당신의 프로젝트에서 이 로거를 자유롭게 사용해보세요. 즐거운 코딩하세요!

수정사항을 제안하거나 피드백을 주시면 감사하겠습니다.