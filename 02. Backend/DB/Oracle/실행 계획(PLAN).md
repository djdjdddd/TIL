# 실행 계획(PLAN)

## 실행 계획(PLAN) 보는 법

### 1. `EXPLAIN PLAN`문 이용
```sql
EXPLAIN PLAN FOR
실행 계획 보고 싶은 쿼리
;

SELECT * FROM TABLE(DBMS_XPLAN.DISPLAY('PLAN_TABLE'));
```

위 SQL을 실행하면 아래와 같이 GUI를 이용했을 때보다 정갈한(?) 모습으로 `실행 계획`을 볼 수 있습니다.
![image](https://github.com/djdjdddd/TIL/assets/126077503/7c071bc7-0084-4011-94e9-3e1e4443ef12)


### 2. GUI 이용
계획 설명 아이콘 클릭
![image](https://github.com/djdjdddd/TIL/assets/126077503/266b8883-eb38-4854-bac3-0729d5e4da0c)


### 3. 인텔리제이 사용시
`DB console - 우클릭 - Explain Plan`
![image](https://github.com/djdjdddd/TIL/assets/126077503/a229a9a8-07d0-43b0-a115-50fc255e4c9e)

