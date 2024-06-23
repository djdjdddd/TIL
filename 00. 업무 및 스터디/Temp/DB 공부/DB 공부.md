# DB 공부


### 정규화
https://mangkyu.tistory.com/110

### OLTP 와 OLAP
- ★ 오라클 공식문서 설명
  https://www.oracle.com/kr/database/what-is-oltp/
- 기사
  https://m.fashionbiz.co.kr:6001/index.asp?idx=191163
- `OLTP(Online Transaction Processing : 온라인 거래처리) 시스템 : 은행 입출금 처리와 온라인 쇼핑처럼 온라인으로 데이터를 읽고 쓰는 작업을 하고 결과를 즉시 회신하는 DBMS`
- `OLAP(Online Analytical Processing : 온라인 분석처리) 시스템 : 대량 데이터를 읽고 다차원으로 통계 분석하는 업무를 위한 DBMS  `

### TOP-N 쿼리
`Top N 쿼리 : 상위 n개의 데이터를 추출하는 쿼리`
https://developer-ping9.tistory.com/28


### 부장님 설명 ★
#### 본인만의 쿼리 튜닝 방식
- `INDEX`를 걸어 조회 속도를 최대한 줄이는 것부터 한다.
- `실행계획`
    - 오라클 디벨로퍼 상에서는 한 줄, 한 줄 지워가며 `실행계획`을 딸 수밖에 없다. 그렇게 하면서 오래 걸리는 쿼리가 어느 부분인지 찾는 수밖에 없다.
    - 쿼리 안에 `FUNCTION`을 가급적 사용하지 않아야 한다. 시간을 많이 잡아 먹는다. 당연한 소리지만 펑션 안에 쿼리가 길면 길수록.
- 실행계획을 땄을 때 `NESTED LOOP`와 `HASH JOIN`을 위주로 본다.

#### NESTED LOOP vs HASH JOIN
- `NESTED LOOP` : 속도가 생명일 경우(ex. 일반적인 클라이언트 서비스 로직)에는 join 방식이 `NL`을 사용하는 게 좋다.
- `HASH JOIN` : 배치를 돌릴 때에는 `HASH JOIN`을 거는 게 좋다.

이유는 뭘까??

레퍼런스
- https://dataonair.or.kr/db-tech-reference/d-guide/sql/?mod=document&uid=368
- http://www.jidum.com/jidums/view.do?jidumId=167
  ![](https://velog.velcdn.com/images/djdjdddd/post/bdd267b7-c67f-4c33-95fa-b4b8ec78e13a/image.png)
