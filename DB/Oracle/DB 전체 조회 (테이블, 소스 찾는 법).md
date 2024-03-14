# DB 전체 조회 (테이블, 소스 찾는 법)


## 목차
1. [테이블 조회](#테이블-조회)
2. [소스 조회](#소스-조회)



## 테이블 조회
### ALL_TABLES (테이블)
```sql
SELECT *
FROM WHERE 1=1
    AND TABLE_NAME LIKE '%검색할이름%'
    AND OWNER = '유저명'
```

## 소스 조회
### ALL_SOURCE (함수, 프로시저, 트리거, 패키지, 타입)
```sql
SELECT * 
FROM ALL_SOURCE	--패키지(PACKAGE), 프로시저(PROCEDURE), 트리거(TRIGGER), 함수(FUNCTION), 타입(TYPE)
WHERE 1=1
    AND TEXT LIKE '%검색할이름%'
    AND OWNER = '유저명'
    AND TYPE = '타입명'    --프로시저를 찾고 싶은 경우 : PROCEDURE
```


