# 15장 JUnit 들여다보기

### 개요

이 장에서는 JUnit 프레임워크에서 가져온 코드를 평가한다.

### JUnit 프레임워크

JUnit의 시작은 켄트 벡과 에릭 감마, 두 사람이 함께 Atlanta행 비행기를 타고 가다 만든 프레임워크다...! <br/>
(TMI : 비행기에서 3시간 정도만에 JUnit 기초를 구현했다고 한다..;;)

문자열 비교 오류를 파악할 때 유용한 ComparisonCompactor라는 모듈에 대해 평가하고 리팩토링 하는 시간을 가져보겠다.

#### (1) ComparisonCompactor 간단 설명
두 문자열을 받아 차이를 리턴한다. <br/>
예를 들어, ABCDE와 ABXDE를 받으면 &rightarrow; <...B[X]D...> 를 반환한다.

#### (2) ComparisonCompactor 리팩토링

1. 조건문 캡슐화
```java
리팩토링 전

public String compact(String message){
    if(expected == null || actual == null || areStringsEqual())
        return Assert.format(message, expected, actual);
}
```

`expected == null || actual == null || areStringsEqual()` 처럼 길고 긴 조건문을 따로 메서드로 뽑아줌으로써
- (1) 조건문에 이름을 붙일 수 있어 의도 표현이 명확하고
- (2) 조건문 캡슐화의 이점도 가질 수 있다.
```java
리팩토링 후

public String compact(String message){
    if(shouldNotCompact())
        return Assert.format(message, expected, actual);
}

private boolean shouldNotCompact(){
    return expected == null || actual == null || areStringsEqual(); // return 타입은 boolean, 조건문 그대로 return
}
```

코드를 리팩토링 하다 보면 원래 했던 변경을 되돌리는 경우가 흔하다.
**리팩토링은 코드가 어느 수준에 이를 때까지 수많은 시행착오를 반복하는 작업**이기 때문이다.

1. shouldNotCompacted 조건

```java
public String formatCompactedComparison(String message){
    String compactExpected = expected;
    String compactActual = actual;
    if(shouldBeCompacted()){
        ...
    }
}

// 캡슐화한 조건문 1
private boolean shouldBeCompacted(){
    return !shouldNotBeCompacted();
}

// 캡슐화한 조건문 2
private boolean shouldNotBeCompacted(){
    return expected == null || actual == null || expected.equals(actual);
}
```