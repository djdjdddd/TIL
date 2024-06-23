# 10장 클래스

### 개요

이전까지 살펴본 내용은 다음과 같다.
1. 코드 행과 코드 블록의 clean code (5장)
2. 함수의 clean code (3장)

하지만 위 2가지만 신경 쓴다고 하여 clean code가 되는 것은 아니다. <br/>
**클래스**, <u>즉 코드 블록과 함수를 넘어 더 높은 단계인 클래스</u>의 clean code도 굉장히 중요하다.

### 클래스 체계

클래스를 정의하는 표준 자바 관례에 따르면 체계는 다음과 같다.

1. 변수 목록
    1. 정적 공개 상수 (public static)
    2. 정적 비공개 변수 (private static)
    3. 비공개 인스턴스 변수 (private)
2. 함수 목록
    1. 공개 함수
    2. 비공개 함수 (자신을 호출하는 공개 함수 직후에 넣는다)

```java
public class Example{
    public static String 정적_공개_상수;
    
    private static String 정적_비공개_변수;
    
    private String 비공개_인스턴스_변수;
    
    public void 공개_함수(){
        비공개_함수();
    }
    
    private void 비공개_함수(){
        
    }
}
```

### 클래스는 작아야 한다!

클래스도 함수와 마찬가지로 **'작게 만들어야 한다!'** 라는 규칙이 있다.

얼마나 작아야 할까??

**함수는 4~5줄 정도**로 작아야 한다면 <br/>
**클래스는 맡은 책임이 1개일 정도로 작아야 한다!** &rightarrow; **SRP**

#### (1) 책임이 1개라는 의미

단순히 하나의 클래스가 가진 메서드 수가 적으면 괜찮은 걸까?

그렇지 않다. 메서드 수가 적어도 책임이 많을 수 있다.

```java
Version 정보를 얻는 책임, Component를 얻는 책임 등 여러 개의 책임을 지닌 클래스이다.

public class SuperDashboard extends JFrame implements MetaDataUser{
    public Component getlastFocusedComponent()
    public void setLastFocused(Component lastFocused)
    public int getMajorVersionNumber()
    public int getMinorVersionNumber()  
    public int getBuildNumber()
}
```

**클래스 이름은 해당 클래스 책임을 기술해야 한다.** <br/>
실제로 작명은 클래스 크기를 줄이는 첫 번째 관문이다.  <br/>
예를 들어, <u>클래스 이름에 Processor, Manager, Super 등과 같이 모호한 단어가 있다면 클래스에다 여러 책임을 떠안겼다는 증거</u>다.


#### (2) 단일 책임 원칙 (SRP)

SRP (Single Responsibility Principle)
- [객체는 단 하나의 책임만 가져야 한다.](https://inpa.tistory.com/entry/OOP-%F0%9F%92%A0-%EC%95%84%EC%A3%BC-%EC%89%BD%EA%B2%8C-%EC%9D%B4%ED%95%B4%ED%95%98%EB%8A%94-SRP-%EB%8B%A8%EC%9D%BC-%EC%B1%85%EC%9E%84-%EC%9B%90%EC%B9%99)
- 클래스나 모듈을 변경할 이유가 단 하나뿐이어야 한다는 원칙.

```java
위 SuperDashboard 클래스에서 Version이라는 책임(=클래스명)을 분리 
Version 정보를 얻는 3개의 메서드를 가진 1개의 클래스를 새로 만들었다.

public class Version{
   public int getMajorVersionNumber() 
   public int getMinorVersionNumber()
   public int getBuildNumber()
}
```

#### (3) 응집도 (Cohesion)

클래스는 인스턴스 변수 수가 적어야 한다. <br/>
일반적으로 메서드가 변수를 더 많이 사용할수록 메서드와 클래스는 응집도가 더 높다. <br/>
**응집도는 높은 게 좋은 편이다.** <br/>
왜냐면 응집도가 높다는 건 클래스에 속한 메서드와 변수가 서로 의존하며 논리적인 단위로 묶인다는 뜻이기 때문이다.

```java
응집도가 높은 클래스
- size()를 제외한 메서드들이 2개 변수를 모두 사용하므로 응집도가 높다고 할 수 있다.

public class Stack{
    private int topOfStack = 0;
    List<Integer> elements = new LinkedList<Integer>();
    
    public int siez(){
        return topOfStack;
    }
    
    public void push(int element){
        topOfStack++;
        elements.add(element);
    }
    
    public int pop() throws PoppedWhenEmpty{
        if(topOfStack == 0)
            throw new PoppedWhenEmpty();
        int element = elements.get(--topOfStack);
        elements.remove(topOfStack);
        return element;
   }
}
```

<u>'함수를 작게, 매개변수 목록을 짧게'라는 전략을 따르다 보면</u> 때때로 **일부 메서드만 사용하는 인스턴스 변수가 아주 많아진다.** <br/>

즉, 응집도가 낮아지고 있는 것이며, 이는 새로운 클래스로 쪼개야 한다는 신호다!

응집도가 높아지도록 변수와 메서드를 적절히 분리해 새로운 클래스 2~3개로 쪼개주면 좋다.

#### (4) 응집도를 유지하면 작은 클래스 여럿이 나온다

큰 함수를 작은 함수 여럿으로 나누기만 해도 클래스 수가 많아진다.

예를 들어, <br/>
변수가 아주 많은 큰 함수에서 일부를 작은 함수로 빼내고 싶다. <br/>
근데 빼내려는 코드가 큰 함수에 정의된 변수 4개를 사용한다. <br/>
그러면 파라미터 4개를 받는 새 함수로 빼면 될까??

<u>차라리 그 변수 4개를 로컬 &rightarrow; 인스턴스 변수로 승격</u>시키자. <br/>
그러면 새 함수는 파라미터가 전혀 필요 없다! (애초에 파라미터가 4개인 함수는 clean하지 못하다)

다만 이러다 보면 다른 메서드와 관련 없는 인스턴스 변수가 늘어나게 되고, 클래스는 점차 응집도가 낮아진다. <br/>
**이럴 때 몇몇 함수에 대해 독자적인 클래스로 분리**해주면 좋다.

> 예시 코드는 책을 참조 (179~184pg)

### 변경하기 쉬운 클래스 ★

해당 장과 예시에 대해서는 아주 자세히 공부를 했는데, 이는 실제로 SK매직에서 설계했던 '동적 SQL 생성 클래스'가 생각났기 때문이다.
예시에 나온 것처럼 추상화를 이용했다면 변경에 용이한 클래스가 되었을 거 같다는 아쉬움이 든다.
**시간이 난다면 DynamicQueryUtil 클래스를 리팩토링 해보도록 하자.**

```java
변경이 필요해 손대야 하는 클래스

public class Sql{
    public Sql(String table, Column[] columns)
        
    // 공개 메서드
    public String create()
    public String insert(Object[] fields)
    public String selectAll()
    public String findByKey(String keyColumn, String keyValue)
    public String select(Column column, String pattern)
    public String select(Criteria criteria)
    public String preparedInsert()
    
    // 비공개 메서드
    private String columnList(Column[] columns)
    private String valuesList(Object[] fields, final Column[] columns)
    private String selectWithCriteria(String criteria)
    private placeholderList(Column[] columns)
}
```

현재는 select문만 만들어주는 Sql 클래스이다.
이대로 사용한다면 문제가 없겠지만, update문을 추가하는 등 클래스에 손을 대야 하는 상황이 온다면 위와 같은 클래스의 형태는 **변경이 쉽지 않은 형태**다.

위 클래스를 아래와 같은 형태로 바꿔준다면 **객체 지향 설계 원칙인 SRP와 OCP를 만족**하게 된다!

변경된 설계 방식은
- 기존 Sql 클래스를 추상화
- 공개 인터페이스를 각각 Sql 클래스에서 파생하는 클래스로 변경
- 비공개 메서드는 해당 파생 클래스(사용하는)로 옮겼다
- 모든 파생 클래스가 공통으로 사용하는 비공개 메서드는 Where와 ColumnList라는 두 유틸리티 클래스에 넣었다

```java
기존 Sql 클래스를 추상화

abstract public class Sql{
    public Sql(String table, Column[] columns)
    abstract public String generate();
}
```

```java
공개 인터페이스를 각각 Sql 클래스에서 파생하는 클래스로 변경

public class CreateSql extends Sql{
    public CreateSql(String table, Column[] columns)
    @Override public String generate()
}

public class SelectSql extends Sql{
    public SelectSql(String table, Column[] columns)
    @Override public String generate()
}

public class InsertSql extends Sql{
    public InsertSql(String table, Column[] columns)
    @Override public String generate()
    // 비공개 메서드는 해당 파생 클래스(사용하는)로 옮겼다 
    private String valuesList(Object[] fields, final Column[] columns)
}


★이러한 클래스 설계의 장점은 기존 코드를 거의 변경할 필요 없이 새 기능을 추가할 수 있다는 것이다.
만약 update문을 생성하는 기능을 추가하고 싶다면 아래와 같이만 하면 된다.

public class UpdateSql extends Sql{
    구현부...
}
```

```java
모든 파생 클래스가 공통으로 사용하는 비공개 메서드는 Where와 ColumnList라는 두 유틸리티 클래스에 넣었다

public class Where{
    public Where(String criteria)
    public String generate()
}

public class ColumnList{
    public ColumnList(Column[] columns)
    public String generate()
}
```

새 기능을 수정하거나 기존 기능을 변경할 때 건드릴 코드가 최소인 시스템 구조가 바람직하다. <br/>
이상적인 시스템이라면 새 기능을 추가할 때 시스템을 확장할 뿐 기존 코드를 변경하지는 않는다.

#### 변경으로부터 격리

객체 지향 프로그래밍 입문에서 구체적인(concrete) 클래스와 추상(abstract) 클래스가 있다고 배웠다.

**상세한 구현에 의존하는 코드는 변경이 어렵다.**

예를 들어, Portfolio 클래스를 만들 때 TokyoStockExchange라는 외부 API를 사용해 포트폴리오 수익을 계산한다고 해보자.
TokyoStockExchange를 Portfolio에서 직접 호출하면 훗날 **API 변경도 어렵고, <u>테스트 코드를 짜기도 어렵다.</u>**
이는 인터페이스를 이용하여 해결할 수 있다.

```java
public interface StockExchange{
    Money currentPrice(String symbol); // 해당 주식의 수익을 리턴하는 함수
}
```
```java
public Portfolio{
    private StockExchange exchange;
    public Portfolio(StockExchange exchange){
        this.exchage = exchange;
    }
    // ...
}
```

이렇게 개선한 Portfolio 클래스는 TokyoStockExchange라는 상세한 구현 클래스가 아니라 StockExchange 인터페이스에 의존한다.
- 추상 : StockExchange 인터페이스에는 주식 기호를 받아 현재 주가를 얻는다는 개념만
- 구현 : TokyoStockExchange 클래스에는 실제로 주가를 얻어오는 출처나 얻어오는 방식 등의 구체적인 구현을 