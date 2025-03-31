# REST API
> 이 글에서는 엄밀한 의미의 REST API가 아니라 개발자들이 일반적으로 사용하는 의미의 REST API 개념에 대해 다룹니다.
> <br/>
> 엄밀한 의미가 궁금하신 분들은 아래 링크를 참조해주시기 바랍니다.
> <br/>
> https://www.ibm.com/kr-ko/topics/rest-apis

REST API는 REST(REpresentational State Transfer) 아키텍처 스타일의 디자인 원칙을 준수하는 API입니다. 
이러한 이유로 REST API를 RESTful API라고도 합니다.

REST API는 특정 기술을 의미하는 것이 아닙니다.  
단지 **HTTP를 이용해서 기계들이 통신을 할 때 HTTP가 가진 장점을 최대한 활용한 모범 사례**라고 할 수 있습니다.

---

## REST API란?
1. 자원(Resource) : `URI`로 표현
2. 행위(Method) : `HTTP Method`로 표현


### 1. 자원(Resource)
#### 리소스의 의미
- 회원을 등록하고 수정하고 조회하는 건 리소스가 아닙니다.
- **ex) "미네랄을 캐라"** 
  - `미네랄 = 리소스`
  - `캐라 = 행위`
- 회원이라는 개념 자체가 바로 리소스입니다.

#### 리소스 식별 방법
```
회원 목록 조회 : /members
회원 조회 : /members/{id} 
회원 등록 : /members/{id}
회원 수정 : /members/{id}
회원 삭제 : /members/{id}
```
즉, 회원 등록/수정/조회 등 행위는 모두 배제


#### cf. Collection & Element
- Collection : `members` (계층 구조상 상위를 컬렉션으로 보고 복수단어 사용 권장)
- Element : `member`



### 2. 행위(Method)
보다시피 리소스를 URI로 표현하는 것만으로는 아무것도 할 수 없습니다. 
행위에 대한 정보를 나타내줄 필요가 있습니다. 
REST API에서는 행위에 대한 정보를 `HTTP method`로 표현합니다.


#### HTTP 메서드 종류
주요 메서드
- `GET` : 리소스 조회
- `POST` : 요청 데이터 처리, 주로 등록에 사용
- `PUT` : 리소스를 대체, 해당 리소스가 없으면 생성
  - `PUT`은 파일을 폴더에 넣는 행위와 비슷합니다. 없으면 생성되고, 있으면 덮어 씌우는 형태입니다.
- `PATCH` : 리소스 부분 변경
- `DELETE` : 리소스 삭제

#### 예시
```http request
GET /members/100 HTTP/1.1
Host: localhost:8080
```
id가 100번인 회원을 조회하는 API 입니다.
- URI : `/members/100`
- Method : `GET`




### cf. 컨트롤 URI
간혹 리소스만으로 URI를 표현하기 애매한 경우도 있습니다.
그럴 때는 URI에 행위를 포함한다고 합니다.

```http request
POST /orders/{orderId}/start-delivery (컨트롤 URI)
```
`start-delivery`, '배달을 시작하라'는 행위가 포함되어 있는 URI



## Reference
- IBM : https://www.ibm.com/kr-ko/topics/rest-apis
- 김영한 `모든 개발자를 위한 HTTP 웹 기본 지식`
- 생활코딩 : https://www.youtube.com/watch?v=PmY3dWcCxXI&t=40s
