---
title: "Interceptor를 사용한 Nestjs 사용자 정의 API 응답 방법"
description: ""
coverImage: "/assets/img/2024-06-30-NestjsCustomAPIResponsewithInterceptor_0.png"
date: 2024-06-30 22:30
ogImage: 
  url: /assets/img/2024-06-30-NestjsCustomAPIResponsewithInterceptor_0.png
tag: Tech
originalTitle: "Nest.js Custom API Response with Interceptor"
link: "https://medium.com/@zigbalthazar/nest-js-custom-api-response-with-interceptor-e313bd92a91d"
---


<img src="/assets/img/2024-06-30-NestjsCustomAPIResponsewithInterceptor_0.png" />

이전 미디엄 스토리(Nest.js 구조화된 API 응답)에서는 새로운 유형을 정의하고 class-validator 및 다국어 패키지와 호환성을 보장하여 API 응답 구조를 사용자 정의하는 방법을 탐색했습니다. 이 글에서는 인터셉터를 사용하여 사용자 지정 API 응답을 정의하는 방법에 대해 다룰 것입니다.

# 인터셉터란?

<img src="/assets/img/2024-06-30-NestjsCustomAPIResponsewithInterceptor_1.png" />

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

NestJS에서 interceptor는 컨트롤러에 도달하기 전에 요청을 변환하거나 클라이언트로 전송되기 전에 응답을 변환할 수 있는 미들웨어입니다. 이것은 로깅, 실행 시간 측정, 데이터 변환, 예외 처리, 인증 및 권한 관리에 사용됩니다. Interceptors는 응용 프로그램에 교차하는 관심사를 추가하는 유연한 방법을 제공합니다.

# 커스텀 API 응답

Interceptor를 사용하면 요청을 감싸고 처리되기 전후에 수정할 수 있습니다. 이 연습에서는 API 응답에 `timestamp`, `path`, `version` 등과 같은 추가 필드를 추가할 것입니다.

# API 응답을 처리하기 위한 Interceptor 정의

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

API 응답을 사용자 정의하는 인터셉터를 정의하는 방법을 안내해 드릴게요:

1. 필요한 모듈과 데코레이터 가져오기:
먼저 NestJS와 RxJS에서 필요한 모듈과 데코레이터를 가져오세요.

```js
import { Injectable, NestInterceptor, ExecutionContext, CallHandler, HttpException } from '@nestjs/common';
import { Observable, throwError } from 'rxjs';
import { catchError, map } from 'rxjs/operators';
```

2. CustomResponseInterceptor 클래스 생성:
CustomResponseInterceptor 클래스를 정의하고 NestInterceptor 인터페이스를 구현하세요.

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

```typescript
@Injectable()
export class CustomResponseInterceptor implements NestInterceptor {

3. **`intercept` 메서드 구현**:
`intercept` 메서드 내에서 요청과 응답 객체에 액세스합니다. 응답에서 상태 코드를 가져옵니다.

intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
  const request = context.switchToHttp().getRequest();
  const response = context.switchToHttp().getResponse();
  const statusCode = response.statusCode;
}

4. 응답 및 오류 처리:
RxJS를 사용하여 next.handle()의 결과를 처리합니다. 데이터를 사용자 정의 응답 형식으로 변환합니다. 사용자 지정 오류 응답을 생성하여 오류를 처리합니다.
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

```js
return next.handle().pipe(
   map(data => ({
     statusCode,
     message: statusCode >= 400 ? 'Error' : 'Success',
     error: statusCode >= 400 ? response.message : null,
     timestamp: Date.now(),
     version: 'v2',
     path: request.url,
     data,
   })),
   catchError(err => {
     const statusCode = err instanceof HttpException ? err.getStatus() : 500;
     const errorResponse = {
       statusCode,
       message: err.message || 'Internal server error',
       error: err.name || 'Error',
       timestamp: Date.now(),
       version: 'v2',
       path: request.url,
       data: {},
     };
     return throwError(() => new HttpException(errorResponse, statusCode));
   })
);
}
```

# 전역적으로 인터셉터 적용하기

이 인터셉터를 전역적으로 적용하려면 `main.ts` 파일에 추가하실 수 있습니다:

1. 필요한 모듈과 인터셉터 가져오기:
`@nestjs/core`에서 `NestFactory`를 가져오고 `CustomResponseInterceptor` 클래스를 가져옵니다.

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
import { NestFactory } from '@nestjs/core';
import { AppModule } from './app.module';
import { CustomResponseInterceptor } from './common/interceptors/custom-response.interceptor';
```

2. NestJS 애플리케이션 생성:
NestJS 애플리케이션을 생성하고 전역 인터셉터를 사용합니다.

```js
async function bootstrap() {
 const app = await NestFactory.create(AppModule);
 app.useGlobalInterceptors(new CustomResponseInterceptor());
 await app.listen(3000);
}
bootstrap();
```

이 설정을 통해 NestJS 애플리케이션의 모든 API 응답이 인터셉터에서 정의된 사용자 정의 형식을 따르게 됩니다. 이러한 방식으로 응답을 구조화함으로써, 클라이언트가 사용할 일관되고 명확한 API 응답 형식을 제공할 수 있습니다.