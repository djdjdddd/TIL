# static Factory Method (미완)

인스턴스 생성을 위한 static method 전반을 Factory Method라고 부르는 경우가 있다. 이것은 GoF의 Factory Method 패턴과는 다르지만, Java에서 인스턴스를 생성할 때 매우 자주 사용되는 기법이다.

## 실제 예시 (Java API)

getInstance 메서드

- java.security.SecureRandom의 getInstance 메서드는 난수 생성 알고리즘 이름을 지정해서 SecureRandom 인스턴스를 생성

```java
SecureRandom random = SecureRandom.getInstance("NativePRNG");
```

of 메서드

- java.util.List의 of 메서드는 구체적인 element를 주면 List 인스턴스를 생성

```java
// Alice, Bob, Chris로 구성된 List를 얻는다.
List<String> list = List.of("Alice", "Bob", "Chris");
```

asList 메서드

- java.util.Arrays의 asList 메서드는 지정된 배열이나 열거한 요소로부터 List 인스턴스를 생성

```java
String[] arr = {"Alice", "Bob", "Chris"};
List<String> list1 = Arrays.asList.(arr);
List<String> list2 = Arrays.asList.("Alice", "Bob", "Chris");
```

valueOf 메서드

- java.lang.String의 valueOf 메서드

```java
// char형 'A'로부터 String형 "A"를 얻는다.
String string = String.valueOf('A');
```

now 메서드

- 현재 시간을 얻는 인스턴스, 생성한 인스턴스가 무엇인지 쉽게 알게끔 now(지금)라는 이름으로 구성

```java
Instant instant = Instant.now();
LocalDateTime localDateTime = LocalDateTime.now();
```
