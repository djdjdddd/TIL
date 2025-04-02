# 🧠 CORS 에러의 이해와 해결 방법 (Cross-Origin Resource Sharing)

### ✅ CORS 3줄 요약
> 1. 브라우저는 보안을 위해, 다른 출처(origin)로의 JS 요청을 기본적으로 차단
> 2. 서버가 허용하는 경우(`Access-Control-Allow-Origin`), 예외적으로 허용
> 3. 이 과정을 제어하고 허용하는 메커니즘이 바로 CORS

---

## 📚 목차
1. [🔥 CORS 에러 실제 사례](#1--cors-에러-실제-사례)
2. [🧩 CORS 에러 원인 이해하기](#2--cors-에러-원인-이해하기-부제-왜-cors-에러가-발생했을까)  
   2.1. [🔐 CORS와 동일 출처 정책(Same-Origin Policy)](#21--sop와-cors) 
   
   2.2. [출처(Origin)란?](#22-출처origin란) 

   2.3. [📦 HTTP 요청 방식에 따른 CORS 발생 여부](#23--http-요청-방식에-따른-cors-발생-여부)   

   2.4. [🧠 브라우저의 CORS 동작 방식](#24--브라우저의-cors-동작-방식) 

3. [⚙️ CORS 작동 방식의 3가지 시나리오](#3--cors-작동-방식의-3가지-시나리오)
4. [🛠️ CORS 해결 방법](#4--cors-해결-방법)
5. [🔒 보안을 고려한 권장 설정법](#5--보안을-고려한-권장-설정법)

---

## 1. 🔥 CORS 에러 실제 사례
2025년 3월, 회사 동료 문XX 군이 백엔드 API를 개발한 후, 타사 프론트엔드 개발자가 이를 테스트하던 중 발생한 실제 사례입니다.

✅ **Postman으로는 요청이 정상 동작하지만, LG TxxxQ 앱에서는 CORS 에러가 발생**
→ 이는 **CORS가 브라우저에서만 적용되는 보안 정책이기 때문**

> axios를 사용한 요청도 결국 내부적으로 `XMLHttpRequest` 혹은 `fetch()` API를 사용하는 방식입니다.  
> 따라서 axios를 사용한다고 해서 CORS 에러가 면제되는 것은 아닙니다.

```js
fetch('https://api.example.com/data')
  .then(res => res.json())
  .then(console.log)
  .catch(console.error);
```

> ❗콘솔 에러 예시:
> ```
> Access to fetch at 'https://api.example.com/data' from origin 'http://localhost:3000'
> has been blocked by CORS policy:
> No 'Access-Control-Allow-Origin' header is present on the requested resource.
> ```

---

## 2. 🧩 CORS 에러 원인 이해하기 (부제: 왜 CORS 에러가 발생했을까?)

### 2.1. 🔐 SOP와 CORS
- **SOP (Same-Origin Policy)**
  - 동일 출처 정책
  - 보안을 위해 브라우저가 **다른 출처의 리소스 접근을 제한**하는 정책
- **CORS (Cross-Origin Resource Sharing)**
  - 교차 출처 리소스 공유
  - SOP의 제한을 완화할 수 있도록 **서버가 허용한 경우에만** 다른 출처의 요청을 허용하는 **HTTP 기반의 보안 메커니즘**

### 2.2. 출처(Origin)란?
- `프로토콜 + 도메인 + 포트` 3가지 요소로 구성

| 예시 | 동일 출처 여부 |
|------|----------------|
| `http://example.com:80` ↔ `http://example.com:80/api` | ✅ 동일 |
| `http://example.com` ↔ `https://example.com` | ❌ 프로토콜 다름 |
| `http://example.com` ↔ `http://api.example.com` | ❌ 도메인 다름 |
| `http://example.com:80` ↔ `http://example.com:8080` | ❌ 포트 다름 |

> ![image](https://github.com/user-attachments/assets/1d32fba7-3ac5-4c46-a052-01787264df3c)
> <br/>
> 이미지 출처: https://opentutorials.org/module/6257/32185

---

### 2.3. 📦 HTTP 요청 방식에 따른 CORS 발생 여부
그렇다고 해서 모든 HTTP 요청에 CORS 정책이 적용되는 것은 아닙니다.
브라우저는 **요청 방식에 따라 CORS 정책을 적용할지 여부를 결정**합니다.

- ✅ **CORS 없이 가능한 요청**
  - `<img>`, `<script>`, `<link>`, `<iframe>` 등을 통한 외부 리소스 요청
- 🚫 **CORS 헤더가 필요한 요청**
  - `fetch()`, `XMLHttpRequest`, axios, FormData 등 **JS 기반 네트워크 요청**

---

### 2.4. 🧠 브라우저의 CORS 동작 방식
브라우저가 CORS 에러를 발생시키는 동작 흐름은 다음과 같습니다

1. **Origin 헤더를 포함한 요청 전송**  
   - 프론트엔드 JavaScript 코드에서 `fetch()`나 `XMLHttpRequest`로 외부 API를 호출하면, 브라우저는 자동으로 `Origin` 헤더를 요청에 추가합니다.  
   - 이 헤더는 현재 페이지의 출처(origin)를 명시합니다.  
   - (`<img>` 등의 태그도 내부적으로 Origin을 포함할 수 있으나, `no-CORS` 모드로 동작하여 제한적입니다.)

2. **서버의 응답 처리**  
   - 서버는 요청에 포함된 `Origin` 값을 확인한 후, 응답에 `Access-Control-Allow-Origin` 헤더를 포함하여 허용 여부를 명시할 수 있습니다.  
   - 예: `Access-Control-Allow-Origin: https://your-domain.com` 또는 `Access-Control-Allow-Origin: *`로 모든 도메인 허용

3. **브라우저의 검증 및 응답 처리**  
   - 브라우저는 응답을 받은 후, `Access-Control-Allow-Origin` 값이 현재 페이지의 Origin과 일치하거나 `*`로 설정되어 있는지 확인합니다.  
   - 조건을 만족하지 않으면 브라우저는 **응답을 JavaScript에 전달하지 않고 CORS 에러를 발생**시킵니다.  
   - 이때, 응답 자체는 서버에서 200 OK로 정상적으로 내려왔을 수 있으나, 브라우저가 그것을 폐기합니다.

📌 **핵심 요점**:  
- CORS 에러는 **브라우저(클라이언트)** 에서 발생합니다.
- 서버는 이러한 에러가 발생했다는 사실조차 알 수 없습니다.
- 따라서 **해결은 반드시 서버 설정을 통해 이루어져야 합니다.**

> ![CORS 흐름](https://github.com/user-attachments/assets/e9c5926b-df54-4f64-ae53-da8e3179cdd2)
> <br/>
> 이미지 출처: [인악명 높은 CORS 개념 & 해결법 - 정리 끝판왕](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-CORS-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-%F0%9F%91%8F)

---

## 3. ⚙️ CORS 작동 방식의 3가지 시나리오  
브라우저는 CORS 요청을 아래 세 가지 유형으로 구분하여 다르게 처리합니다.
- ✅ 단순 요청 (Simple Request)  
- 🧪 예비 요청 (Preflight Request)  
- 🔐 인증 요청 (Credentialed Request)

---

### ✅ 3.1 단순 요청 (Simple Request)
단순 요청은 **브라우저가 별도의 사전 확인(Preflight) 없이 직접 전송 가능한 요청**을 의미합니다. 이는 HTML 폼 전송처럼 안전하다고 판단되는 경우에 해당합니다.

**단순 요청으로 인정받기 위한 조건:**

1. **HTTP 메서드**: `GET`, `POST`, `HEAD` 중 하나여야 합니다.
2. **헤더 제한**: 다음의 **CORS-safe 헤더만** 사용해야 합니다.
   - `Accept`, `Accept-Language`, `Content-Language`, `Content-Type` (일부 타입 한정)
   - `Authorization`, `X-Custom-Header` 같은 사용자 정의 헤더는 허용되지 않습니다.
3. **Content-Type**: `Content-Type`이 존재할 경우, 다음 중 하나여야 합니다.
   - `application/x-www-form-urlencoded`
   - `multipart/form-data`
   - `text/plain`
   - 유의❗: `application/json`은 **단순 요청이 아닙니다** → 예비 요청 발생
4. **추가 조건**:
   - XHR의 경우, `upload` 객체에 이벤트 리스너를 등록하지 않아야 합니다.
   - `ReadableStream`을 바디로 사용할 수 없습니다.

**요청 흐름:**
- 위 조건을 모두 만족하면 브라우저는 **`Origin` 헤더만 포함하여 바로 서버로 요청**을 전송합니다.
- 서버는 응답에 `Access-Control-Allow-Origin`을 포함해야 하며, 그렇지 않으면 **브라우저가 응답을 차단**합니다.

**예시:**

```js
fetch('https://api.example.com/public', {
  method: 'POST',
  headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
});
```

→ 서버 응답에 `Access-Control-Allow-Origin: https://app.example.com`이 포함되어 있다면 성공합니다.  
없으면 CORS 오류가 발생합니다.

---

### 🧪 3.2 예비 요청 (Preflight Request)

단순 요청의 조건을 충족하지 못하는 경우, 브라우저는 **실제 요청 전에 OPTIONS 메서드를 사용하여 사전 확인(Preflight)** 을 수행합니다.

**예비 요청이 발생하는 상황:**
- `PUT`, `DELETE`, `PATCH`, 혹은 `POST` + `application/json` 사용
- 사용자 정의 헤더 포함 (`Authorization`, `X-API-Key` 등)

**예비 요청 단계:**
1. 브라우저가 대상 URL에 **OPTIONS 메서드로 요청**을 보냅니다.
2. **요청 헤더** 예:
   - `Origin`: 현재 페이지의 출처
   - `Access-Control-Request-Method`: 실제 요청에 사용될 HTTP 메서드
   - `Access-Control-Request-Headers`: 사용자 정의 헤더 목록

3. **서버 응답 헤더**는 다음을 포함해야 합니다:
   - `Access-Control-Allow-Origin`: 요청한 origin 또는 `*`
   - `Access-Control-Allow-Methods`: 허용된 HTTP 메서드 목록
   - `Access-Control-Allow-Headers`: 허용된 헤더 목록
   - (선택) `Access-Control-Max-Age`: 결과 캐시 시간 (초)

**예시 흐름:**
```http
요청 헤더
OPTIONS /api/data HTTP/1.1
Origin: https://app.example.com
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: Authorization
```

```http
응답 헤더
HTTP/1.1 204 No Content
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Methods: PUT
Access-Control-Allow-Headers: Authorization
Access-Control-Max-Age: 86400
```

→ 응답이 적절하면 브라우저는 **PUT 요청**을 실제로 전송합니다.  
응답이 누락되거나 잘못되었다면 **요청 자체가 취소되고 CORS 에러가 발생**합니다.

**개발자 팁:**  
- 네트워크 탭에서 실제 요청 전에 `OPTIONS` 요청이 보이면 이는 Preflight입니다.  
- 서버가 OPTIONS 요청을 명시적으로 처리하도록 설정되어 있어야 하며, 미응답 시 본 요청은 진행되지 않습니다.

---

### 🔐 3.3 인증 요청 (Credentialed Request)
**인증 요청**은 쿠키, HTTP 인증, 또는 SSL 클라이언트 인증서 등의 자격 정보를 포함한 CORS 요청입니다.

기본적으로 브라우저는 **교차 출처 요청에 자격 정보를 포함하지 않습니다.**  
이를 포함하려면 JS에서 명시적으로 설정해야 합니다:

- `fetch(..., { credentials: "include" })`
- `xhr.withCredentials = true`

**서버가 충족해야 할 조건:**
1. **반드시 응답에 `Access-Control-Allow-Credentials: true`를 포함**해야 합니다.
2. `Access-Control-Allow-Origin`은 **절대로 `*`를 사용할 수 없습니다.**
   - 반드시 명시적인 Origin 값을 사용해야 합니다.  
     예: `Access-Control-Allow-Origin: https://app.example.com`

**예시 흐름:**
```js
fetch('https://api.example.com/user-info', {
  method: 'GET',
  credentials: 'include' // 쿠키 포함
});
```

→ 서버는 아래와 같은 응답 헤더를 포함해야 합니다:

```http
Access-Control-Allow-Origin: https://app.example.com
Access-Control-Allow-Credentials: true
```

**주의할 점:**
- 위 조건 중 하나라도 누락되면 **응답이 브라우저에서 차단**됩니다.
- 쿠키도 보내지 않고, 응답 내 쿠키도 저장되지 않습니다.

**Preflight 요청이 필요한 경우**(예: Authorization 헤더 포함)에는 예비 요청에서도 `Access-Control-Allow-Credentials`를 함께 명시해야 합니다.

---

## 4. 🛠️ CORS 해결 방법

### 📌 공통적으로 설정해야 하는 응답 헤더
```http
Access-Control-Allow-Origin: https://trusteddomain.com
Access-Control-Allow-Methods: GET, POST, PUT, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
Access-Control-Allow-Credentials: true
```

---

### 🧰 환경별 설정법

#### 🔸 Java Servlet
```java
import javax.servlet.*;

public class CORSInterceptor implements Filter {

    private static final String[] allowedOrigins = {
            "http://localhost:3000", "http://localhost:5500", "http://localhost:5501",
            "http://127.0.0.1:3000", "http://127.0.0.1:5500", "http://127.0.0.1:5501"
    };

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletRequest request = (HttpServletRequest) servletRequest;

        String requestOrigin = request.getHeader("Origin");
        if(isAllowedOrigin(requestOrigin)) {
            // Authorize the origin, all headers, and all methods
            ((HttpServletResponse) servletResponse).addHeader("Access-Control-Allow-Origin", requestOrigin);
            ((HttpServletResponse) servletResponse).addHeader("Access-Control-Allow-Headers", "*");
            ((HttpServletResponse) servletResponse).addHeader("Access-Control-Allow-Methods",
                    "GET, OPTIONS, HEAD, PUT, POST, DELETE");

            HttpServletResponse resp = (HttpServletResponse) servletResponse;

            // CORS handshake (pre-flight request)
            if (request.getMethod().equals("OPTIONS")) {
                resp.setStatus(HttpServletResponse.SC_ACCEPTED);
                return;
            }
        }
        // pass the request along the filter chain
        filterChain.doFilter(request, servletResponse);
    }

    private boolean isAllowedOrigin(String origin){
        for (String allowedOrigin : allowedOrigins) {
            if(origin.equals(allowedOrigin)) return true;
        }
        return false;
    }
}
```

#### 🔸 Spring (Spring Boot)
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  public void addCorsMappings(CorsRegistry registry) {
    registry.addMapping("/api/**")
      .allowedOrigins("https://trusteddomain.com")
      .allowedMethods("GET", "POST", "PUT", "DELETE")
      .allowedHeaders("Content-Type", "Authorization")
      .allowCredentials(true);
  }
}
```

#### 기타 환경
- Node.js (Express): `cors` 미들웨어
- Apache / Tomcat / Nginx: 설정 파일에서 헤더 추가
- AWS API Gateway / Lambda: 콘솔 또는 코드로 응답 헤더 설정

---

## 5. 🔒 보안을 고려한 권장 설정법

- ✅ **화이트리스트 기반 도메인 허용**
- ✅ **필요한 메소드/헤더만 허용**
- ✅ **인증에는 `*` 사용 금지**
- ✅ **쿠키 대신 JWT 등 토큰 기반 인증 권장**
- ✅ **프록시 서버로 우회 가능 (백엔드 서버가 API 호출)**

> CORS는 사용자를 보호하기 위한 브라우저의 보안 기능입니다.  
> 이를 올바르게 이해하고 서버 설정을 통해 명시적으로 허용하는 것이 핵심입니다.

--- 

## 📚 Reference
- [**MDN Web Docs – CORS**](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)  
  → **CORS 정의와 작동 방식**, 특히 단순 요청(Simple Request)과 사전 요청(Preflight Request)의 구분 설명에 사용됨.

- [**MDN Web Docs – HTTP access control (for developers)**](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS)  
  → 브라우저가 어떻게 `Access-Control-Allow-Origin`, `Allow-Methods`, `Allow-Headers` 등의 응답 헤더를 검사하는지 상세 설명할 때 인용됨.

- [**8th Light – Same-Origin Policy Explained**](https://8thlight.com/blog/david-moore/2014/07/10/same-origin-policy.html)  
  → **Same-Origin Policy의 근본 개념**, SOP가 왜 존재하며 어떻게 동작하는지를 설명하는 데 사용됨. evil.com에서 bank.com의 정보를 가져가려는 예시가 대표적.

- [**Stack Overflow – Access-Control-Allow-Origin 작동 방식**](https://stackoverflow.com/questions/10636611/how-does-access-control-allow-origin-header-work)  
  → 서버가 `200 OK`를 응답했더라도 브라우저가 CORS 정책을 위반하면 응답을 차단한다는 점, 즉 **클라이언트에서 차단이 일어난다**는 사실 설명에 사용됨.

- [**Reddit – ELI5: CORS가 뭔가요?**](https://www.reddit.com/r/webdev/comments/5n0eyg/eli5_cors_what_is_it_and_why_does_it_break/)  
  → CORS가 **보안을 위한 브라우저의 정책**이라는 점을 쉽게 설명하는 데 사용됨. 특히 SOP와 CORS의 관계를 일반인이 이해할 수 있게 다룬 내용이 인용됨.

- [**Reflectoring.io – Spring에서의 CORS 설정**](https://reflectoring.io/spring-cors/)  
  → Spring Framework에서 `@CrossOrigin`, `WebMvcConfigurer`를 이용한 CORS 설정 방법을 보여주는 코드 예제에서 사용됨.

- [**Enable-CORS.org**](https://enable-cors.org/)  
  → CORS를 지원하지 않는 서버를 위한 해결책으로 **Nginx 설정법**, **OPTIONS 응답 처리** 등을 설명할 때 참고됨.

- [**Chrome for Developers – CORS Preflight 흐름**](https://developer.chrome.com/blog/cors-preflight/)  
  → **Preflight 요청 흐름 다이어그램** 설명에 사용됨. 브라우저가 OPTIONS 요청을 보내고 응답에 따라 실제 요청 여부를 결정하는 과정 설명에 활용됨.

- [**Spring.io – REST API에 CORS 허용 설정하기**](https://spring.io/guides/gs/rest-service-cors/)  
  → `@CrossOrigin`과 전역 CORS 설정을 포함하는 Spring Boot 예제 설명에서 사용됨.

- [**Tomcat – Built-in CORS Filter 공식 문서**](https://tomcat.apache.org/tomcat-9.0-doc/config/filter.html#CORS_Filter)  
  → Tomcat 환경에서 `web.xml`로 `CorsFilter` 설정을 할 수 있다는 부분에서 공식 문서 링크로 참고됨.

- [**Express.js CORS Middleware 공식 문서**](https://expressjs.com/en/resources/middleware/cors.html)  
  → Node.js Express 애플리케이션에서 `cors` 미들웨어를 사용하는 방법 소개에 활용됨. 화이트리스트 방식 예제도 여기에 기반함.

- [**AWS Docs – CORS support in API Gateway**](https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-cors.html)  
  → AWS API Gateway에서 **Enable CORS** 버튼을 클릭하여 CORS 헤더 및 OPTIONS 메서드를 자동 설정하는 절차 설명에 사용됨.

- [**Cloudonaut – AWS Lambda에서 CORS 헤더 처리**](https://cloudonaut.io/serverless-cors-in-a-nutshell/)  
  → AWS Lambda를 사용할 때 **응답 객체에 직접 CORS 헤더를 추가하는 방법**에 대한 예제로 활용됨.
