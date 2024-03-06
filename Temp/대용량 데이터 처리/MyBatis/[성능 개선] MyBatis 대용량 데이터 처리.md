## ★중요
### 1. 일단 지금까지 이해한 내용 정리 (3.6)
fetchSize
- 지정된 수만큼의 결과를 리턴하도록 하는 드라이버 힌트 형태의 값이다. 디폴트는 셋팅하지 않는 것이고 드라이버에 따라 다소 지원되지 않을 수 있다. (참고 [MyBatis 공식문서](https://mybatis.org/mybatis-3/ko/sqlmap-xml.html))
- 현재 제가 사용하고 있는 `Oracle`의 경우 `디폴트 fetchSize`는 10이라고 합니다. 

fetchSize 부연설명
- `fetchSize(=가져오는 크기)` 말 그대로 DB에서 데이터를 요청한 쪽으로 몇 개를 제공할 것이냐?
- 마이바티스를 사용하는 경우 `sqlSession`이 해당 데이터를 `fetch`한 후 메모리에 담은 후 반환을 하는 형태라고 함. [자세한 건 아래 글 참조](https://okky.kr/questions/1458780)
```
SQL 문을 작성해서 데이터베이스에 전송하면, 

데이터베이스는 해당 데이터를 한 번에 다 조회해서 특정 저장소에 가지고 있는게 아니구요,

해당 데이터를 제공할 준비를 하는 것 입니다.

그리고, 실제 페치를 하면 DB 는 해당 데이터를 페치 수 만큼 제공합니다.

여기까지가 데이터를 요청하는 쪽과 데이터베이스 간의 기본적인 흐름이구요.

sqlSession.select(sql, param);

이렇게 하면 sqlSession은 해당 데이터를 모두 페치해서 메모리에 담은 후 반환을 합니다.

이럴 경우, 데이터가 대용량일 경우 메모리가 부족하게 되겠죠.

그래서, sqlSession.select(sql, param, handleResult); 이렇게 합니다.

그럼, sqlSession 은 해당 데이터를 모두 페치해서 반환하는게 아니라,

1000건만 페치하고 메모리에 담고 handleResult 를 호출합니다.

이런 식입니다. :)
```

![img_1.png](img_1.png)
https://caffeineoverflow.tistory.com/32


### 2. 앞으로 확인해볼 내용
`디버깅`
- (1) 쿼리를 1번만 보내는 건지, `데이터 개수/fetchSize`번 쿼리를 날리는 건지 디버깅해서 체크
- (2) `ResultHandler` 사용법 및 사용 이유
- (3) `fetchSize`를 키워 WAS <=> DB 간의 통신 횟수는 줄어들어 `조회 속도`면에서 큰 이점을 얻은 만큼, JVM 메모리에 많이 담아두기에 `OOM`이 발생할 가능성도 크다고 한다. 이는 `trade-off`라고 하는데... 해당 내용 더 알아보기

---

### implements ResultHandler ??
★★★예시
https://blog.naver.com/nosignal95/221963804744

https://dabok407.tistory.com/59
![](https://velog.velcdn.com/images/djdjdddd/post/e78f1afa-ce32-4fad-bf93-5565d30147f7/image.png)


## Reference
대용량 데이터 처리 작업에서의 성능 개선 포인트
https://btcd.tistory.com/1451

MyBatis 성능 향상시키는 방법 (with fetchSize, mapUnderscoreToCamelCase)
https://oingdaddy.tistory.com/64

What is the difference between setting statement fetch size in JDBC or firing a SQL query with LIMIT clause?
https://stackoverflow.com/questions/1440543/what-is-the-difference-between-setting-statement-fetch-size-in-jdbc-or-firing-a

MyBatis의 fetchSize와 Cursor 이야기
https://nidev.gitlab.io/2020/12/14/Mybatis%EC%9D%98-fetchSize%EC%99%80-Cursor-%EC%9D%B4%EC%95%BC%EA%B8%B0/

마이바티스 공홈 - `fetchSize` 속성 설명
https://mybatis.org/mybatis-3/ko/sqlmap-xml.html