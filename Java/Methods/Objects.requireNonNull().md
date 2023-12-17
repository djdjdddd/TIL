# Objects.requireNonNull()
파라미터는 객체(obj)이며, 그 객체가 `null`일 땐 `NPE(NullPointerException)`가 발생한다. 
`null`이 아니라면 그 객체를 고대로 리턴한다. 
이때 타입은 해당 객체의 타입`T`이다.
```java
/*
 * @param obj the object reference to check for nullity
 * @param <T> the type of the reference
 * @return {@code obj} if not {@code null}
 * @throws NullPointerException if {@code obj} is {@code null}
 */
public static <T> T requireNonNull(T obj) {
    if (obj == null)
        throw new NullPointerException();
    return obj;
}
```

## 학습 동기
백기선의 이펙티브 자바 강의를 듣던 중 생성자 내에서 `requireNonNull()`을 통해 필드 값을 설정해주는 코드를 보았고, 궁금증이 생겼다. 
단순히 `this.size = size`만 해도 문제는 없을텐데, 왜 이렇게 작성했을까? 
```java
public static class Builder extends Pizza.Builder<Builder>{
    private final Size size;
    
    public Builder(Size size){
        this.size = Objects.requireNonNull(size); // 궁금증이 생긴 부분
    }
    
    @Override
    NyPizza build() {
        return new NyPizza(this);
    }
    
    @Override
    protected Builder self() {
        return this;
    }
}
```

## requireNonNull 사용 목적
[Oracle docs](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Objects.html#requireNonNull(T))를 보면 `requireNonNull()` 메서드에 대해 다음과 같이 소개를 하고 있다.
> requireNonNull() 메서드는 주로 메서드나 생성자에서 해당 파라미터(객체)가 null인지 아닌지 체크하기 위한 목적으로 사용된다.
> <br/>
> <br/>
> Checks that the specified object reference is not null. This method is designed primarily for doing parameter validation in methods and constructors

### 예시
Foo 클래스의 생성자에서 사용되었으며, 파라미터(bar 객체)가 `null`인지 아닌지 체크하고 있다.
```java
import java.util.Objects;

public class Foo {
    private final Bar bar;

    public Foo(Bar bar) {
        Objects.requireNonNull(bar, "bar must not be null");
        this.bar = bar;
    }
}
```

## 장점
반대로 `requireNonNull()`을 안 썼을 때 생길 수 있는 문제점을 생각하면 장점을 쉽게 유추할 수 있다.

### 1. 디버깅이 쉽다.
알다시피 `null`인 객체를 사용(ex. 메서드 호출 등)하면 NPE가 발생한다. 보통 어디서 무엇 때문에 NPE가 발생했는지 한참 헤매기 마련인데, 
`requireNonNull()`을 사용하면 객체 생성 시점이나 메서드 사용 시점에 NPE가 발생하므로 보다 쉽게 디버깅이 가능하다.

### 2. 불필요한 null 체크 X (+ final 키워드)
`final` 키워드를 통해 불변 객체로 선언할 경우 이 메서드의 장점이 극대화된다. 
`requireNonNull()`을 통해 해당 객체가 `null`이 아님을 체크했다면, 그 객체를 사용할 때 더이상 `null` 체크를 하지 않아도 되기 때문이다.

```java
if(bar == null){ // 더이상 이러한 null 체크 코드를 쓰지 않아도 되는 장점
    bar.print();    
}
```

### 3. 코드가 깔끔하다.
```java
if(bar == null){
    throw new NullPointerException("bar 객체가 null입니다.");    
}
```

```java
Objects.requireNonNull(bar, "bar 객체가 null입니다."); // 2nd param은 NPE가 발생했을 때 사용되는 message이다. 
```


## Reference
https://hudi.blog/java-requirenonnull/ 
<br/>
https://stackoverflow.com/questions/45632920/why-should-one-use-objects-requirenonnull
<br/>
https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/Objects.html#requireNonNull(T)
