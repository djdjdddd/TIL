# 9장 단위 테스트

### 개요

1997년만 해도 TDD(Test Driven Development)라는 개념을 아무도 몰랐다. <br/>
그 당시의 단위 테스트란 자기 프로그램이 '돌아간다'는 사실만 확인하는 일회성 코드에 불과했다.

애자일과 TDD 덕택에 단위 테스트를 자동화하는 프로그래머들이 이미 많아졌고 더 늘어나는 추세다. <br/>
하지만 <u>급하게 테스트를 추가해야 한다는 점 때문에</u> **제대로 된 테스트 케이스를 작성해야 한다는 중요한 사실을 놓치고 있다.**

### TDD 법칙 3가지

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

### 깨끗한 테스트 코드 유지하기
> 요약 : 실제 코드뿐만 아니라 테스트 코드도 clean code 해야 한다!

테스트 코드를 깨끗하게 유지(clean code)해야 하는 이유는 단순하다. <br/>
clean code하지 않으면 결국 유지보수가 힘들어지고, 결국 테스트 코드를 잃게 된다. <br/>
테스트 케이스가 없으면 코드를 변경하기 두려워진다. 모든 변경이 잠정적인 버그이기 때문이다. <br/>
테스트 코드가 dirty해지면 실제 코드도 dirty해진다. <br/>
결국 테스트 코드를 잃어 버리게 되고 실제 코드도 망가지게 된다.

### 깨끗한 테스트 코드 예시

실제로 테스트 코드를 많이 짤 일이 있을 때 이 부분을 다시 보도록 하자... (책 158~164p)

### 테스트당 assert 하나

JUnit으로 테스트 코드르르 짤 때는 함수 1개마다 assert문을 단 1개만 사용해야 한다고 주장하는 학파가 있다. <br/>
훌륭한 지침이라고 생각되나 때로는 여러 개의 assert문을 넣어야 할 때도 있긴 있다.

#### 테스트당 개념 하나

따라서 <u>가장 좋은 규칙은 "테스트 함수 1개마다 1개의 개념만을 테스트하라"는 것</u>이다. <br/>

* addMonth() 메서드를 테스트하는 안 좋은 예시
```java
public void testAddMonths(){
    SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

    SerialDate d2 = SerialDate.addMonths(1, d1);
    assertEquals(30, d2.getDayOfMonth());
    assertEquals(6, d2.getMonth());
    assertEquals(2004, d2.getYYYY());

    SerialDate d3 = SerialDate.addMonths(2, d1);
    assertEquals(31, d3.getDayOfMonth());
    assertEquals(7, d3.getMonth());
    assertEquals(2004, d3.getYYYY());
    
    SerialDate d4 = SerialDate.addMonths(1, d1);
    assertEquals(30, d4.getDayOfMonth());
    assertEquals(7, d4.getMonth());
    assertEquals(2004, d4.getYYYY());
}
```


위 테스트 메서드의 문제점은 1개의 메서드에서 여러 개의 개념을 테스트한다는 것이다.
- 31일로 끝나는 달의 마지막 날짜가 주어지는 경우
- 30일로 끝나는 달의 마지막 날짜가 주어지는 경우
- ...

### F.I.R.S.T

깨끗한 테스트 코드의 5가지 규칙

#### Fast (빠르게)

테스트 코드 수행 시간이 빨라야 한다. 느리면 테스트를 자주 안 돌리게 되기 때문이다.

#### Independent (독립적으로) ★

각 테스트는 서로 의존하면 안 된다. <br/>
즉, 한 테스트가 다음 테스트가 실행될 환경을 준비해서는 안 된다. <br/>
각 테스틑 독립적으로 그리고 어떤 순서로 실행해도 괜찮아야 한다.

#### Repeatable (반복가능하게)
테스트는 어떤 환경에서도 반복 가능해야 한다.  <br/>
실제 환경, QA 환경, 네트워크에 연결되지 않은 노트북 환경에서도 실행할 수 있어야 한다.

#### Self-validating (자가검증하는) ★

테스트의 결과 값은 true/false인 boolean으로 내야 한다. (그래야 빠르다) <br/>
테스트 결과를 로그로 읽는 수고로움을 남겨서는 안 된다.


#### Timely (적시에)

테스트는 적시에 작성해야 한다.<br/>
즉, 단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다.
