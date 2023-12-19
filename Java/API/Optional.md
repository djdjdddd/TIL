# Optional API

> 참고 자료

1. 더 자바, Java 8 (백기선, 인프런 강의)

Optional 1

자바 프로그래밍에서 NullPointerException을 종종 보게 되는 이유
- null을 리턴하니까! && null 체크를 깜빡해서!

메서드에서 작업 중 특별한 상황에서 값을 제대로 리턴할 수 없는 경우 선택할 수 있는 방법
- 예외를 던진다. (비싸다. 스택트레이스를 찍어두니까)
- null을 리턴한다. (비용 문제는 없으나, 그 코드를 사용하는 클라이언트 코드가 주의해야 한다.)
- (자바 8부터) Optional을 리턴한다. (이는 클라이언트 코드에 명시적으로 빈 값일 수도 있다는 걸 알려주고, 빈 값인 경우에 대한 처리를 강제한다.)

Optional 
- 오직 값 1개가 들어있을 수도 아예 없을 수도 있는 Container

주의사항
- 리턴 값으로만 사용하는 걸 적극 권장. (메서드 매개변수 타입, 맵의 키 타입, 인스턴스 필드 타입으로 쓰지 말자)
- Optional을 리턴하는 메서드에서 null을 리턴하지 말자.
- primitive type용 Optional이 따로 있다. (ex. OptionalInt, OptionalLong ...)
- List와 같은 Collection, 그리고 Stream, Map, Optional 등등 애초에 Container 타입인 걸 또 Optional이라는 Container로 2번 감싸는 건 좋지 못하다. (애초에 저런 Container가 Optional의 역할을 갖고 있다고 봐도 무방하지 않나??)

참고
- 이펙티브 자바 3판, 아이템 55 적절한 경우 Optional을 리턴하라.
- 오라클 공식 문서

Optional 2

Optional 만들기
- Optional.of()
- Optional.ofNullable()
- Optional.empty()

Optional에 값이 있는지 없는지 확인하기
- isPresent()
- isEmpty() (Java 11부터 제공)

Optional에 있는 값 가져오기
- get()
- 만약에 비어있는 Optional에서 무언가를 꺼낸다면??

Optional에 값이 있는 경우에 그 값을 가지고 ~~를 하라. 
- ifPresent(Consumer)

Optional에 값이 있으면 가져오고 없는 경우에 ~~를 리턴하라.
- orElse(T)

Optional에 값이 있으면 가져오고 없는 경우에 ~~를 하라.
- orElseGet(Supplier)

Optional에 값이 있으면 가져오고 없는 경우 에러를 던져라.
- orElseThrow()

Optional에 들어있는 값 걸러내기
- Optional filter(Predicate)

Optional에 들어있는 값 변환하기
- Optional map(Function)
- Optional flatMap(Function) : Optional 안에 들어있는 인스턴스가 Optional인 경우에 사용하면 편리하다. 