# 계층형 쿼리 사용법(START WITH, CONNECT BY PRIOR, ORDER SIBLINGS BY)

## 개념 정리 
- 계층형 쿼리 : 부모, 자식 간의 수직관계를 트리 구조 형태로 보여주는 쿼리
- `START WITH` : 트리 구조의 최상위 행을 지정합니다.
- `CONNECT BY` : 부모, 자식의 관계를 지정합니다.
- `PRIOR` : CONNECT BY 절에 사용되며 PRIOR에 지정된 컬럼이 맞은편 컬럼을 찾아갑니다.
- `CONNECT BY PRIOR 자식 컬럼 = 부모 컬럼` : 부모 → 자식 순방향 전개
- `CONNECT BY PRIOR 부모 컬럼 = 자식 컬럼` : 자식 → 부모 역방향 전개
- `ORDER SIBLINGS` : 계층형 쿼리에서 정렬을 수행합니다.

## 예시 쿼리
[상위, 하위 조직코드 조회 쿼리](https://github.com/djdjdddd/private-TIL/blob/main/Code/SQL/start%20with%20connect%20by%20%EC%A0%88%20(%EC%83%81%EC%9C%84%2C%20%ED%95%98%EC%9C%84%20%EC%A1%B0%EC%A7%81%EC%BD%94%EB%93%9C).md)

## Reference
- 출처: https://goldswan.tistory.com/36 [금백조의 개발 블로그:티스토리]
