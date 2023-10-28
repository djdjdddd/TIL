# setter를 지양하는 이유와 대안

## 목차

1. [setter를 지양해야 하는 이유](#setter를-지양해야-하는-이유)
   - [객체의 불변성 보장 X](#1-객체의-불변성-보장-x)
   - [setter로 값을 변경한 이유, 의도 파악이 어렵다](#2-setter로-값을-변경한-이유-의도-파악이-어렵다)
   - [유지보수가 어렵다](#3-유지보수가-어렵다)
2. 여러가지 대안
   - [생성자 방식](#1-생성자-방식)
   - [static Factory Method 방식](#2-static-factory-method-방식)

---

## setter를 지양해야 하는 이유

코딩을 처음 배웠을 때 롬복의 편리함에 취해 무의식적으로 setter를 사용했고 굉장히 좋다고만 생각했다. 그러다 스터디원에게 setter 사용이 좋지 않을 수도 있다는 이야기를 처음 들었을 때 너무 충격적이었다. 오늘 그 이유가 무엇이며 어떤 대안들이 있는지 알아보자.

### 1. 객체의 불변성 보장 X

객체의 불변성이 반드시 보장돼야 한다면 절대 setter를 쓰면 안 된다. (<-> 가변 객체)

그래서 DTO(Data Transfer Object)와 달리 VO(Value Object)에는 getter만 있고 setter는 없어야 한다.

cf. [DTO vs VO vs Entity](https://tecoble.techcourse.co.kr/post/2021-05-16-dto-vs-vo-vs-entity/)

setter를 사용하지 않을 때 또 하나의 장점이 있는데, 바로 final 키워드를 사용할 수 있다는 것이다. 불변성을 지킬 수 있어 여러 개발자가 협업할 때 좋다.

```java
@Getter
public class Test {
    private String cntrNo;
    private final String acctNo; // setter가 있을 땐 final 키워드를 붙일 수 없다.
    private final String custNo; // setter가 있을 땐 final 키워드를 붙일 수 없다.

    public Test(String cntrNo, String acctNo, String custNo) {
        this.cntrNo = cntrNo;
        this.acctNo = acctNo;
        this.custNo = custNo;
    }
}
```

### 2. setter로 값을 변경한 이유, 의도 파악이 어렵다.

메서드 이름이 setXxx라서 "설정했다"만 알 수 있지 "**왜 설정했는지**"를 전혀 알 수 없다.

```java
test.setAcctNo("a");
test.setCustNo("b");
test.setCntrNo("c");
```

### 3. 유지보수가 어렵다.

필드 개수가 많아지면 많아질수록 setXxx의 개수도 많아지므로 유지보수가 빡세다. (필드 개수가 10개, 20개를 넘어간다고 상상해보라...)

---

## 여러가지 대안

### 1. 생성자 방식

생성자를 오버로딩(Overloading)하여

```java
public Test(String cntrNo){
    this.cntrNo = cntrNo;
}

public Test(String cntrNo, String acctNo, String custNo) {
    this.cntrNo = cntrNo;
    this.acctNo = acctNo;
    this.custNo = custNo;
}
```

#### (1) 생성자 방식의 단점

1. 파라미터 개수가 적을 땐 괜찮지만, 많아질수록 코드 가독성이 좋지 않고 실수할 가능성도 있다.
2. 메서드와 달리 생성자는 이름을 가질 수 없기에 마찬가지로 의도 파악이 어렵다.

```java
Test test = new Test("1");

Test test = new Test("1", "2", "3");

Test test = new Test("1", "2", "3" ...........??);
```

### 2. static Factory Method 방식

생성자 방식과 비슷해 보이지만 메서드라서 이름을 가질 수 있고, 이에 따른 장점이 많다.

```java
// now 메서드
LocalDateTime localDateTime = LocalDateTime.now();

// valueOf 메서드
String str = String.valueOf("객체생성");
```

아래 코드에 나와 있듯이 Singleton 작성도 편하다.

```java
@ToString
@Getter
public static class Test {

    private static Test instance; // for 싱글톤

    private final String cntrNo;

    private final String acctNo;

    private final String custNo;

    // (1) 생성자 방식
    public Test(String cntrNo, String acctNo, String custNo) {
        this.cntrNo = cntrNo;
        this.acctNo = acctNo;
        this.custNo = custNo;
    }

    // (2) static Factory Method 방식
    public static Test of(String cntrNo, String acctNo, String custNo) {
        return new Test(cntrNo, acctNo, custNo);
    }

    // (2) static Factory Method 방식
    public static Test onlyAcctNoAndCustNo(String acctNo, String custNo) {
        return new Test("", acctNo, custNo);
    }

    //싱글톤 작성도 편함
    public static Test getInstance() {
        if(instance == null){
            instance = new Test("", "", "");
        }
        return instance;
    }
}
```

cf. static Factory Method 설명 잘 나와있는 곳

- [인파님 블로그](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%A0%95%EC%A0%81-%ED%8C%A9%ED%86%A0%EB%A6%AC-%EB%A9%94%EC%84%9C%EB%93%9C-%EC%83%9D%EC%84%B1%EC%9E%90-%EB%8C%80%EC%8B%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EC%9E%90)
- [Tecoble](https://tecoble.techcourse.co.kr/post/2020-05-26-static-factory-method/)
