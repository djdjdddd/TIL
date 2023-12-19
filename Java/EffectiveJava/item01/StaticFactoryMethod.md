# 정적 팩토리 메서드란? (Static Factory Method)

클라이언트가 클래스의 인스턴스를 얻는 전통적인 수단은 `public 생성자`다.
하지만 인스턴스를 얻는 또 다른 유명한 프로그래밍 기법이 있는데, 바로 `정적 팩토리 메서드`이다.

## 네이밍 컨벤션
다음은 정적 팩토리 메서드에 흔히 사용되는 명명법과 그 예시들이다.

1.`from` : **하나의 매개변수**를 받아 해당 타입의 인스턴스를 반환하는 메서드 <br/>
```java
Date d = Date.from(instant);
```
<br/>

2.`of` : **여러 매개변수**를 받아 적합한 타입의 인스턴스를 반환하는 메서드
```java
Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
```
<br/>

3.`valueOf` : from과 of의 더 자세한 버전
```java
BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);

Boolean.valueOf(true);
```

<br/>

4.`newInstance` 혹은 `create` : 항상 새로운 인스턴스를 생성하여 반환함을 보장한다.
```java
Object newArray = Array.newInstance(classObject, arrayLen);
```

<br/>

5.`getInstance` 혹은 `instance` : 인스턴스를 얻는 것은 동일하나, newInstance와 달리 같은 인스턴스임을 보장하지는 않는다.
```java
StackWalker luke = StackWalker.getInstance(options);
```

<br/>

6.`getType` : getInstance와 비슷하지만, 해당 메서드를 호출한 클래스가 아니라 다른 클래스(=Type)의 인스턴스를 반환하는 메서드
```java
// Files 클래스에서 FileStore의 인스턴스를 얻고 있다. (Type = FileStore)
FileStore fs = Files.getFileStore(path); 
```

<br/>

7.`newType` : newInstance와 getType의 짬뽕. 마찬가지로 항상 새로운 인스턴스를 생성하여 반환함을 보장한다.
```java
BufferedReader br = Files.newBufferedReader(path);
```

<br/>

8.`type` : getType과 newType의 간결한 버전
```java
List<Complaint> litany = Collections.list(legacyLitany);
```

## 작성법 및 예시
정적 팩토리 메서드 작성 방법과 간단한 사용 예시에 대해 알아보자.

### 예시 1
field 값이 각기 다른 인스턴스 생성하고 싶을 때 정적 팩토리 메서드를 사용하면 좋다.

왜냐하면 아래의 경우 [메서드 시그니처가 동일](#cf-메서드-시그니처가-동일하다는-의미)하여 이름이 하나로 정해져 있는 생성자만으로는 다소 어려움이 있기 때문이다.
```java
import java.util.Objects;

public class Order {
    private Product product;
    private boolean delivery;   // 배달 
    private boolean takeout;    // 포장 
    
    // 생성자 : private으로 선언했기 때문에 외부에서는 생성자를 통해 인스턴스를 생성할 수 없다.
    private Order(){} 

    // 정적 팩토리 메서드 - 배달 주문
    public static Order deliveryOrder(Product product, boolean delivery) {
        Order order = new Order(); // 1. 생성자로 새로운 인스턴스를 하나 만들고
        
        this.product = Objects.requireNonNull(product);
        this.delivery = true; // 2. 필요한 field 값 설정한 다음
        
        return order; // 3. 만든 인스턴스를 리턴한다.
    }
    
    // 정적 팩토리 메서드 - 포장 주문
    public static Order deliveryOrder(Product product, boolean takeout) {
        Order order = new Order(); 

        this.product = Objects.requireNonNull(product);
        this.takeout = true; // cf. 포장 주문 인스턴스라서 takeout 값을 true로 설정하였다.

        return order;
    }
}
```

#### cf. '메서드 시그니처가 동일하다'는 의미
Java에서 메서드 시그니처는 메서드 명과 파라미터의 순서, 타입, 개수를 의미한다. (리턴 타입은 X) <br/>
그래서 만약 정적 팩토리 메서드가 아니라 생성자로 구현할 경우 메서드 시그니처가 아래와 같게 되는데, 메서드 시그니처가 동일하므로 컴파일 에러가 나게 된다.  <br/>
```java
Order(Product product, boolean delivery)
Order(Product product, boolean takeout)
```
물론 파라미터의 순서를 바꿈으로써 컴파일 에러를 약간 우회할 순 있겠지만.. 그리 좋은 방법은 아니라고 본다. 

### 예시 2
다음은 정적 팩토리 메서드로 싱글톤 패턴을 구현한 것이다. 
항상 같은 인스턴스를 리턴하므로 `newInstance()`보단 `getInstance()`가 적절하다. 
```java
public class Example{
    // 1. Example 인스턴스를 미리 생성
    private static final Example EXAMPLE = new Example();
    
    // 2. 생성자는 private으로 선언하여 외부에서 인스턴스를 생성하지 못하게 막아준다.
    private Example(){}
    
    // 3. 오직 getIntance() 라는 정적 팩토리 메서드만을 통해 인스턴스를 얻을 수 있다.
    public Example getInstance(){
        return EXAMPLE;
    }
}
```

## 핵심 정리
정적 팩토리 메서드와 public 생성자는 각자의 쓰임새가 있으니 상대적인 장단점을 이해하고 사용하는 것이 좋다. 
그렇다고 하더라도 정적 팩토리를 사용하는 게 유리한 경우가 더 많으므로 무작정 public 생성자를 제공하던 습관이 있다면 고치자.

## 장점

## 단점