# 18장 동시성2

### 개요
225쪽에서 소개한 동시성(Concurrency)에 대해 더 자세히 설명하는 장

### 가능한 실행 경로
아래 IdGenerator 인스턴스 1개를 스레드 2개가 공유할 때 가능한 실행 경로는? (초기값 0)
```java
public class IdGenerator{
    int lastIdUsed;
    
    public intincrementValue(){
        return ++lastIdUsed;
    }
}
```
- 스레드 1이 1을 얻고, 스레드 2가 2를 얻어, lastIdUsed가 2가 된다.
- 스레드 1이 2를 얻고, 스레드 2가 1을 얻어, lastIdUsed가 2가 된다.
- **스레드 1이 1을 얻고, 스레드 2가 1을 얻어, lastIdUsed가 1이 된다.**

놀랍게도 마지막 경우도 가능한데, 이는 **'가능한 실행 경로 수'**와 **'JVM의 동작 방식'**을 알아야 한다.

#### (1) 경로 수
> 루프나 분기가 없는 명령 N개를 스레드 T개가 차례로 실행할 때 가능한 경로 수<br/>
> - 가능한 경로 수 : (NT)! / (N!)^T

**가능한 경로 수를 계산하기 위해 자바 컴파일러가 생성한 바이트 코드를 살펴봐야** 한다.  <br/>
`return ++lastIdUsed;`라는 자바 코드 한 줄은 바이트 코드 명령 8개에 해당한다.  <br/>
따라서 2개의 스레드가 명령 8개를 뒤섞어 실행하는 것과 같다.


#### (2) 가능한 순열 수 계산하기
우리가 예제로 사용한 자바 코드 한 줄은 N=8이고 T=2이다. 따라서 가능한 경로 수는 12,870개다.

만약 lastIdUsed가 long 정수라면 읽기/쓰기 명령이 한 단계가 아니라 두 단계로 실행되기에 가능한 경로 수는 2,704,156개로 늘어나게 된다.

만약 메서드를 아래처럼 synchronized를 걸어준다면 가능한 경로 수는 2개로 줄어든다. 스레드가 N개라면 가능한 경로 수는 N!이다.
```java
public synchronized void incrementValue(){
    ++lastIdUsed;    
}
```

#### (3) 결론

스레드가 서로의 작업을 덮어쓰는 과정을 이해하기 위해 바이트 코드를 속속들이 이해할 필요는 없으나, 어떤 연산이 안전하고 안전하지 못한지 파악할 만큼 메모리 모델을 이해하고 있어야 한다.

특히 (전처리 연산, 후처리 연산 둘 다) ++ 연산은 원자적 연산이라 오해하는 사람이 많은데, 이는 분명 원자적 연산이 아니다.

즉, 다음을 알아야 동시성에 대한 이해가 가능해지는 것이다.
1. 공유 객체/값이 있는 곳
2. 동시 읽기/수정 문제를 일으킬 소지가 있는 코드
3. 동시성 문제를 방지하는 방법


### 라이브러리를 이해하라
#### (2) 스레드를 차단하지 않는(non blocking) 방법 - Atomic 클래스!

다음은 synchronized 키워드로 다른 스레드를 차단하여 동기화를 수행하는 코드다.
```java
public class ObjectWithValue{
    private int value;
    
    public void synchronized incrementValue(){
        ++value;
    }
    
    public int getValue(){
        return value;
    }
}
```

자바 5는 위와 같은 상황에 적합한 새로운 클래스를 제공한다.
- AtomicBoolean, AtomicInteger, AtomicReference 등의 클래스

```java
public class ObjectWithValue{
    private AtomicInteger value = new AtomicInteger(0); // 초기값 0으로 설정
    
    public void incrementValue(){ // AtomicInteger 클래스로 synchronized 키워드 없이 동시성 문제를 해결
        value.incrementAndGet();  
    }
    
    public int getValue(){
        return value.get();
    }
}
```
<br/>

성능 비교
- 결과 : AtomicInteger를 쓴 경우가 synchronized를 쓴 경우보다 거의 항상 더 빠르다. <br/>(느린 경우는 사실상 없다)
- (1) synchronized는 언제나 lock을 건다. 둘째 스레드가 같은 값을 갱신하는 경우든 아니든 무조건 락부터 건다. 자바 버전이 올라감에 따라 내장 락의 성능이 좋아지긴 했어도 여전히 락을 거는 대가는 여전히 비싸다.
- (2) 스레드를 차단하지 않는(non blocking) 버전은 **여러 스레드가 같은 값을 수정해 문제를 일으키는 상황이 그리 잦지 않다는 가정에서 출발한다.** 그래서 그런 상황이 발생했는지 효율적으로 감지해 갱신이 성공할 때까지 재차 시도한다.

2번과 같은 연산을 **CAS(Compare And Swap) 연산**이라고 한다. CAS 연산은 논리적으로 아래 코드와 유사하다.
```java
int variableBeingSet;

void simulateNonBlockingSet(int newValue){
    int currentValue;
    do{
        currentValue = variableBeingSet
    } while (currentValue != compareAndSwap(currentValue, newValue))
}

int synchronized compareAndSwap(int currentValue, int newValue){
    if(variableBeingSet == currentValue){
        variableBeingSet = newValue;
        return currentValue;
    }    
    return variableBeingSet;
}
```
해석하자면, <u>메서드가 공유 변수를 갱신하려 든다면 CAS 연산은 현재 변수 값이 최종으로 알려진 값인지 확인</u>한다.
- (1) 그렇다면 변수 값을 갱신
- (2) 아니라면 다른 스레드가 끼어들었다는 뜻이므로 변수 값을 갱신 X

<br/>

#### (3) 다중 스레드 환경에서 안전하지 않은 클래스
본질적으로 다중 스레드 환경에서 안전하지 않은 클래스가 있다. 예를 들어
- SimpleDateFormat
- 데이터베이스 연결
- java.util 컨테이너 클래스
- Servlet

### 데드락
데드락을 근본적으로 해결하려면 원인을 이해해야 한다.

데드락은 다음 4가지 조건을 만족하면 발생한다.
1. 상호 배제 (Mutual exclusion)
2. 잠금 & 대기 (Lock & Wait)
3. 선점 불가 (No Preemption)
4. 순환 대기 (Circular Wait)

#### (1) 상호 배제
여러 스레드가 한 자원을 공유하나 그 자원은
- 여러 스레드가 동시에 사용하지 못하며
- 개수가 제한적인 경우
- ex) 데이터베이스 연결, 쓰기용 파일 열기, 세마포어 등

#### (2) 잠금 & 대기
일단 스레드가 자원을 점유하면 필요한 나머지 자원까지 모두 점유해 작업을 마칠 때까지 이미 점유한 자원을 내놓지 않는 것.

#### (3) 선점 불가
한 스레드가 다른 스레드로부터 자원을 빼앗지 못하여 자원을 점유한 스레드가 스스로 내놓지 않는 이상 다른 스레드는 그 자원을 점유하지 못하는 것.
#### (4) 순환 대기
T1, T2라는 스레드 2개가 있고, R1, R2라는 자원 2개가 있다고 가정하자. T1이 R1을 T2가 R2를 점유한 상태에서 T1이 R2를 T2도 R2를 필요로 할 때 순환 대기가 걸리게 된다.

### 데드락 해결법 (각 조건 깨는 방법)
#### (4) 순환 대기
**데드락을 방지하는 가장 흔한 전략**이다. **대다수 시스템에서는 모든 스레드가 동의하는 간단한 규약이면 충분**하다.

즉 T1, T2가 자원을 똑같은 순서로 할당하게 만들면 순환 대기는 불가능해진다. 쉽게 말해 모든 스레드가 일정한 순서에 자원을 할당받게 설정하면 데드락은 불가능하다.

다만, 이것 또한 아래의 문제를 일으킬 소지가 있다.
- 자원을 할당하는 순서와 자원을 사용하는 순서가 다를 때(ex. 맨 처음 할당한 자원을 나중에 쓸 때). 이렇게 되면 자원을 필요 이상으로 오랫동안 점유할 가능성이 있다.
- 애초에 순서에 따라 자원을 할당하기 어려울 때. (ex. 첫 자원을 사용한 후에야 둘째 자원 ID를 얻는 경우)

### 다중 스레드 코드 테스트
pp.436~438에 있는 테스트 코드를 꼭 따라해보도록 하자.


### 스레드 코드 테스트를 도와주는 도구
**IBM의 ConTest라는 도구**. 스레드에 안전하지 않는 코드에 보조 코드를 더해 실패할 가능성을 높여주는 도구다.

현재는 ConTest가 Multicore SDK라는 제품으로 통합되었으며, 더 이상 단독 제품이나 오픈 소스 형태로 제공되지 않는다. 따라서 Multicore SDK를 참고하도록 하자.

### 결론
이 장에서 논한 주제
1. 동시 갱신을 방지하는 깨끗한 동기화/잠금 기법
2. 스레드가 I/O 위주 시스템의 처리율을 높여주는 이유와 실제로 처리율을 높이는 방법
3. 데드락과 데드락을 방지하는 기법

끝으로 더그 리(Doug Lea)가 쓴 **Concurrent Programming in Java: Design Principles and Patterns라는 책을 추천**한다.
