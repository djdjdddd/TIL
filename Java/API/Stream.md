# Stream API

> 참고 자료

1. 더 자바, Java 8 (백기선, 인프런 강의)
2. 이것이 자바다 Chapter 17

공부 배경

- 처음에 자바 언어를 배우면서 어려운 개념이 몇가지 있었는데, 그중 하나가 바로 Stream API이다.
- 그냥 for문이나 Iterator와 같은 '외부 반복자'를 쓰면 될 걸 왜 굳이 Stream ~~

Stream 
- sequence of elements supporting sequential and parallel aggregate operations
- 데이터를 담고 있는 저장소(ex. Collection)가 아니다. 
- Functional in nature. 스트림이 처리하는 데이터 소스를 변경하지 않는다. 
- 무제한일 수도 있다. (대신 Short Circuit 메서드를 사용해서 제한할 수 있다.)
- 중개 오퍼레이션은 근본적으로 lazy하다.
- 손쉽게 병렬 처리할 수 있다. 

![img.png](img/img.png) 
출처 : https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/stream/package-summary.html

![img_1.png](img/img_1.png)

Stream Pipeline
- 0 또는 다수의 중개 오퍼레이션(Intermediate Operation)과 1개의 종료 오퍼레이션(Terminal Operation)으로 구성된다.
- 스트림의 데이터 소스는 오직 종료 오퍼레이션을 실행할 때만 처리한다. 

중개 오퍼레이션
- **Stream을 리턴!**
- Stateless / Stateful 오퍼레이션으로 더 상세하게 구분할 수도 있다. (대부분은 Stateless지만 distinct나 sorted처럼 이전 이전 소스 데이터를 참조해야 하는 오퍼레이션은 Stateful 오퍼레이션이다.)
- filter, map, limit, skip, sorted ... 

종료 오퍼레이션
- **Stream을 리턴하지 X !**
- collect, allMatch, count, forEach, min, max ... 

---

Stream API 2
Stream API 사용 예제

걸러내기
- filter(Predicate)

변경하기 
- 

생성하기

제한하기

스트림에 있는 데이터가 특정 조건을 만족하는지 확인

개수 세기

스트림을 데이터 하나로 뭉치기 