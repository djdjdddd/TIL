# DB 종류 및 간단 설명


## 목차
1. [key-value DB](#1-key-value-db)
2. [RDBMS](#2-rdbms-relational-dbms)
3. [Graph DB](#3-graph-db)
4. [Document DB](#4-document-db)
5. [Column-family DB](#5-column-family-db)




## 1. key-value DB
말 그대로 데이터를 KEY - VALUE 형태로 저장하는 DB 입니다.
간단한만큼 특수한 기능이 없어 서브용 DB로 많이 사용합니다.

![img.png](img%2Fimg.png)

### Redis
마찬가지로 key-value DB 이지만, 많이 사용하는데 그 이유는 데이터를 하드디스크에 저장하는 게 아니라 램(메모리)에 저장하기 때문입니다.
이 덕에 데이터를 읽고 쓰는 속도가 굉장히 빠른 장점이 있습니다.
- 정확히는 일차적으로 램에 저장하고, 하드에 백업을 해둔다고 합니다.


자주 사용하는 데이터를 `Redis`에 미리 캐싱해두고, 해당 데이터를 요청할 때마다 메인 DB가 아니라 Redis를 통하게끔 하여 서비스 속도를 빠르게 처리하는 방법이 많이 쓰입니다.

![img_1.png](img%2Fimg_1.png)

![img_2.png](img%2Fimg_2.png)

사용처
- 자주 쓰는 데이터 캐싱
- 채팅을 위한 pub/sub
- 영상 스트리밍
- 로그인 기록 저장


---

## 2. RDBMS (Relational DBMS)
우리나라 말로는 '관계형 데이터베이스'라고 부르며, **표 형태로 데이터를 저장하는 DB**입니다. (엑셀과 유사)


![img_3.png](img%2Fimg_3.png)

데이터 '읽기/쓰기'를 할 때 `속도`보다 `정확성`에 더 초점을 맞춘다면 주로 RDBMS를 사용합니다.

cf. Relational 의 의미
'관계형'이라고 해서 관계를 잘 나타내는 DB라고 생각할 수 있는데, 실은 그게 아닙니다.
`Relational`은 행렬 배울 때 쓰는 relation에서 기인했습니다.


![img_4.png](img%2Fimg_4.png)

### SQL
RDBMS는 `SQL`이라는 문법을 사용하여 데이터를 CRUD 합니다.

#### SQL vs NoSQL


### 정규화 (Normalization)
RDBMS를 사용하여 데이터를 저장할 때는 주로 `정규화`하여 저장합니다.
일종의 데이터 중복을 제거하는 작업이라고 생각하면 됩니다.


### ACID Transaction 기능
RDBMS에는 기본적으로 `Transaction` 기능이 담겨있습니다.


---

## 3. Graph DB
수학(행렬, 이산수학 등)적인 의미의 '관계'가 아니라 흔히 우리가 사용하는 '관계'를 잘 나타내고 싶다면 Graph DB를 사용하면 좋습니다.

노드라는 걸 만들고, 이 노드 안에 데이터를 저장할 수 있습니다.
그리고 노드끼리 어떠한 `관계`인지 나타낼 수도 있습니다.

![img_5.png](img%2Fimg_5.png)


가장 유명한 DB로는 `neo4j`가 있습니다.

사용처
- 비행기 노선
- SNS 친구관계
- 코로나 전염맵
- 추천 서비스

### GQL
Graph Query Language


---


## 4. Document DB
일반적인 상황에서는 RDBMS를 많이 사용하지만, 좀 더 자유롭게 사용하고 싶을 땐 Document DB를 사용하기도 합니다.

가장 유명한 DB로는 `mongoDB`가 있습니다.


### 데이터 저장 방식
`collection`이라고 부르는 폴더 안에 `document`들이 있고, 그 안에는 `JSON` 형태로 데이터들이 들어있습니다.

![img_6.png](img%2Fimg_6.png)

### RDBMS와의 차이점
- RDBMS랑 다르게 어떤 데이터를 저장할지 미리 정의해놓을 필요가 없습니다.
- 구조 변경도 용이합니다.
- 보통 정규화를 하지 않습니다. (중복 데이터를 제거 X)
- 대부분 분산처리를 매우 잘해줍니다.


---

## 5. Column-family DB




## Reference
- 코딩애플 : https://www.youtube.com/watch?v=ZVuHZ2Fjkl4