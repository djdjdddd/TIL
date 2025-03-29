개요
- LG의 모 앱에서 우리쪽 서버로 api... CORS 에러 발생
- 나도 예전에 드림시큐리티 개발할 때... CORS 에러 발생
- 도대체 원인은 무엇이고, **적절한** 해결방법은 무엇일까?

---
1. 악명 높은 CORS 에러 메시지

1.1. 요청 방식에 따라 다른 CORS 발생 여부
1.1.1 태그
1.1.2 XMLHttpRequest, Fetch API 스크립트
 
2. CORS 에러 한방 이해하기
2.1. 출처(Origin)란?
2.2 동일 출처 정책 (Same-Origin Policy)
2.2.1. 동일 출처 정책이 필요한 이유
2.2.2. 같은 출처와 다른 출처 구분 기준
2.2.3. 출처 비교와 차단은 브라우저가 한다
2.2.4. 그럼 죄다 차단하면 인터넷이 되는가?

3. CORS 작동 방식 3가지 시나리오
4. CORS를 해결하는 방법 총정리
5. 추가적으로 알아야 하는 CORS 사례
---
---
# (Deep Research 사용하여 작성중)

# CORS 에러의 이해와 해결 방법 (Cross-Origin Resource Sharing)

## 1. HTTP 요청 방식에 따른 CORS 발생 여부

모든 리소스 요청이 CORS 에러를 발생시키는 것은 아닙니다. 동일 출처 정책(Same-Origin Policy, SOP)은 주로 스크립트나 XHR/fetch 요청에 적용되며, 모든 HTML 요소에 적용되는 것은 아닙니다. 예를 들어 `<img>` 또는 `<script>` 태그를 통해 다른 출처(origin)의 리소스를 요청하면 브라우저의 SOP에 의해 차단되지 않고 로드됩니다. 이때 브라우저는 내부적으로 "no-CORS" 모드를 사용하며, 이 리소스들은 JavaScript에서 접근은 불가능하지만 로드는 가능합니다. 따라서 CDN에서 스크립트나 스타일을 불러오거나 이미지를 외부 도메인에서 로드할 때 별도의 CORS 헤더 없이도 가능합니다.

반면, `XMLHttpRequest`나 `fetch()` API를 통한 Ajax 호출은 브라우저가 적극적으로 제한합니다. 이러한 API를 사용하여 다른 출처에서 리소스를 요청할 때는 서버의 응답에 반드시 적절한 `Access-Control-Allow-Origin` 헤더가 있어야 합니다. 그렇지 않으면 브라우저는 요청을 실패 처리하며, 콘솔에서 CORS 에러가 발생합니다.

정리하면:

- **CORS 헤더가 필요 없는 경우:** `<img>`, 일반 `<script>`, 스타일시트, iframe, 미디어 등 정적 리소스.
- **CORS 헤더가 필요한 경우:** JavaScript의 `fetch()`, XHR 요청 등 프로그래밍 방식의 요청.

## 2. CORS 에러의 원인 이해하기

### CORS와 동일 출처 정책(Same-Origin Policy)

CORS는 웹 서버가 브라우저에게 명시적으로 다른 출처의 리소스 접근을 허용하는 HTTP 기능입니다. 이는 브라우저의 동일 출처 정책(SOP)을 완화하는 역할을 합니다. SOP는 기본적으로 페이지가 로드된 출처와 다른 도메인으로의 요청을 제한하는 보안 정책입니다.

여기서 출처(Origin)는 프로토콜(스킴), 호스트(도메인), 포트로 정의됩니다. 이 세 가지가 모두 일치해야 동일 출처로 간주됩니다.

예:
- 동일 출처: `http://example.com:80` ↔ `http://example.com:80/api`
- 다른 출처: `http://example.com` ↔ `https://example.com` (프로토콜 다름)
- 다른 출처: `http://example.com` ↔ `http://api.example.com` (서브도메인 다름)
- 다른 출처: `http://example.com:80` ↔ `http://example.com:8080` (포트 다름)

### 브라우저가 CORS 에러를 발생시키는 과정

브라우저에서 CORS 에러가 발생할 때의 과정은 다음과 같습니다:

1. **Origin 헤더를 포함한 요청**: JavaScript로 만든 크로스 오리진 요청은 브라우저가 자동으로 Origin 헤더를 요청에 추가하여 보냅니다.
2. **서버의 응답 확인**: 서버가 요청을 허용할지 결정하여 `Access-Control-Allow-Origin` 헤더를 응답에 포함해 클라이언트에게 전달합니다.
3. **브라우저의 응답 검사**: 브라우저가 이 헤더를 검사하여 적절한 Origin이 허용된 경우에만 JavaScript로 응답을 전달합니다. 헤더가 없거나 맞지 않으면 브라우저는 요청을 차단하고 CORS 에러를 발생시킵니다.

중요한 점은 CORS 에러는 서버가 아니라 브라우저에서 발생한다는 것입니다. 따라서 서버 측에서 올바른 헤더 설정을 통해 이 문제를 해결해야 합니다.

## 3. CORS 작동 방식의 3가지 시나리오

브라우저는 CORS 요청을 세 가지로 구분하여 다르게 처리합니다:

### 3.1 단순 요청 (Simple Requests)

브라우저가 사전 확인(preflight)을 생략할 수 있는 요청입니다.
- 허용된 메소드: GET, HEAD, POST
- 허용된 헤더만 포함 (`Accept`, `Accept-Language`, `Content-Type` 등)
- `Content-Type`이 다음 중 하나여야 함: `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain`

### 3.2 사전 요청 (Preflight Requests)

단순 요청이 아닌 경우, 예를 들어 PUT, DELETE, PATCH 메소드나 JSON 요청 등 브라우저는 실제 요청 전에 OPTIONS 메소드로 서버에게 요청 허용 여부를 먼저 확인합니다.

사전 요청 시 전송되는 주요 헤더:
- `Access-Control-Request-Method`: 실제 요청의 HTTP 메소드
- `Access-Control-Request-Headers`: 실제 요청에 포함될 커스텀 헤더

서버는 허용 시 응답으로 다음 헤더를 포함해야 합니다:
- `Access-Control-Allow-Origin`
- `Access-Control-Allow-Methods`
- `Access-Control-Allow-Headers`

### 3.3 인증된 요청 (Credentialed Requests)

쿠키나 인증 헤더를 포함한 요청이며, 반드시 서버 응답에 `Access-Control-Allow-Credentials: true`가 필요합니다. 이때는 절대 `Access-Control-Allow-Origin`을 `*`로 설정할 수 없습니다.

## 4. CORS 해결 방법

서버에서 다음과 같은 HTTP 응답 헤더를 설정해야 합니다:
- `Access-Control-Allow-Origin`
- `Access-Control-Allow-Methods`
- `Access-Control-Allow-Headers`

환경별 설정법:
- **Java Servlet:** Servlet 필터를 이용하여 헤더 설정
- **Spring:** `@CrossOrigin` 애노테이션 또는 `WebMvcConfigurer`를 통해 전역 설정
- **Node.js (Express):** `cors` 미들웨어 활용
- **Apache, Tomcat, Nginx:** 각 서버의 설정 파일에 헤더 추가
- **AWS API Gateway/Lambda:** AWS 콘솔이나 Lambda 응답에 직접 헤더 설정

## 5. 보안을 고려한 권장 설정법

- **화이트리스트를 통한 허용 도메인 명시**: 특정 도메인만 허용하여 불필요한 도메인의 접근을 방지합니다.
- **최소 권한의 메소드 및 헤더 허용**: 꼭 필요한 메소드 및 헤더만 허용합니다.
- **토큰 기반 인증 활용**: 쿠키 대신 JWT 또는 API 키를 사용하여 보안 강화
- **민감한 API는 와일드카드(`*`)를 피할 것**: 개발 시에는 편리하나 운영 환경에서는 허용 도메인을 제한합니다.
- **프록시 활용**: CORS 설정이 어려운 경우 자체 서버를 프록시로 활용하는 방법도 고려할 수 있습니다.

이러한 보안적 권장 사항을 통해 기능성과 보안성을 균형 있게 유지할 수 있습니다.
