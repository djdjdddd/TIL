# 17장 냄새와 휴리스틱

### 개요

이번 장에서는 다음 2가지에 대해 모두 정리하였다.
1. 코드 냄새 (안 좋은 의미)
2. 기교와 휴리스틱

> cf. 휴리스틱이란? <br/> <br/>
휴리스틱이란 의미를 사전에서 찾아보면 '시간이나 정보가 불충분하여 합리적인 판단을 할 수 없거나, 굳이 체계적이고 합리적인 판단을 할 필요가 없는 상황에서 신속하게 사용하는 어림짐작의 기술’로 표현된다. <br/><br/>
출처 : 세이프티퍼스트닷뉴스(https://www.safety1st.news)

### 주석

#### C5 : 주석 처리된 코드
"코드를 읽다가 주석으로 처리된 코드가 줄줄이 나오면 신경이 아주 거슬린다.
얼마나 오래된 코드인지, 중요한 코드인지 알 수가 없으며, 쉬이 삭제하기도 어렵다."

이 '코드 냄새'는 상당히 공감이 됐다.
실제로 내가 일하는 곳에서도 이러한 '주석 처리된 코드'를 아주 쉽게 찾아볼 수 있었는데,
그때마다 지우지 못하고 그저 바라만 볼 수밖에 없었다.
**따라서 만약 코드를 작성하다가 필요가 없어져 주석 처리했다면 나중에 꼭 지우는 습관을 가지도록 해야겠다.**

### 함수

#### F3 : 플래그 인수
**boolean 파라미터를 갖는 함수는 최대한 피하는 것이 옳다.**

왜냐하면 boolean 파라미터는 함수가 1가지가 아니라 여러 기능을 수행한다는 의미이며,
또한 true/false에 따라 output이 달라질 수 있다는 건 클라이언트 입장에서 굉장히 혼란스러울 수 있다.

### 일반
#### G10 : 수직 분리
변수와 함수는 사용되는 위치에 가깝게 정의한다.

1. 지역 변수
- 처음으로 사용하기 직전에 선언, 수직으로 가까운 곳에 위치해야 한다.
- 선언한 위치로부터 몇백 줄 아래에서 사용하면 안 된다.

2. 비공개 함수 (private 메서드)
- 마찬가지로 처음으로 호출한 직후에 정의한다.

#### G13 : 인위적 결합
서로 무관한 개념을 인위적으로 결합하지 않는다. **예를 들어, 일반적인 enum은 특정 클래스에 속할 이유가 없다.**

#### G14 : 기능 욕심
마틴 파울러가 말하는 코드 냄새 중 하나다. **클래스 메서드는 자기 클래스의 변수와 함수에 관심을 가져야지 다른 클래스의 변수와 함수에 관심을 가져서는 안 된다.**


```java
예시 

public class HourlyPayCalculator{
    public Money calculateWeekelyPay(HourlyEmployee e){ // HourlyEmployee라는 클래스의 범위를 욕심내고 있다. 
        int tenthRate = e.getThenthRate().getPennies(); // 왜냐하면 해당 객체를 파라미터로 받아 사용하고 있긴 때문이다.
        int tenthsWorked = e.getTenthsWorked();
        ...
        return new Money(stratightPay + overtimePay);
    }
}
```

#### G18 : 부적절한 static 함수

`Math.max(double a, double b)`는 좋은 static 메서드다. 특정 인스턴스와 관련된 기능이 아니기 때문이다. `new Math().max(a,b)`라 하면 오히려 우습다.

그러나 재정의할 가능성이 있다면 static으로 정의하면 안 된다.

일반적으로 static 함수보다 인스턴스 함수가 더 좋다. **반드시 static 함수로 정의해야겠다면 재정의할 가능성은 없는지 꼼꼼히 따져보도록 하자.**

#### G28 : 조건을 캡슐화하라

조건의 의도를 분명히 밝히는 함수로 표현하자. 일반적인 부울 논리는 이해하기 어렵기 때문이다.

즉, `if(timer.hasExpired() && !timer.isRecurrent())`가 아니라

`if(shouldBeDeleted(timer))`라는 코드가 좋다.

#### G29 : 부정 조건은 피하라
마찬가지로 부정 조건은 이해하기 어려우므로 긍정 조건으로 표현하는 것이 좋다.

```java
긍정 조건
if(buffer.shouldCompact())
```

```java
부정 조건
if(!buffer.shouldNotCompact())
```

#### G30 : 함수는 1가지만 해야 한다.

나도 이론적으로는 함수는 1가지 일만 할 수 있게 하는 것이 좋다고 생각하나, 현실적으로 어려울 때가 많다. **이는 이 책 clean code와 관련된 아래 영상을 보고 느낀 점이다.**

코딩애플 - [코딩 책 한 권만 읽으면 이렇게 됩니다](https://www.youtube.com/watch?v=th7n1rmlO4I)

**'함수가 1가지 일만 하게끔'** 코드를 짜다 보면 하나의 함수가 4~5줄 이내로 짧고 이뻐보일 순 있지만, 가독성 측면에서 굉장히 불리하다는 걸 느끼고 있다.
또한, 코드의 재사용성 측면을 고려한다면 단순히 함수를 여러 개로 늘리는 것보단 '일정 기능을 유지한 함수 1개를 재사용하는 것이 더 좋지 않나' 라는 생각이 들고 있다.


#### G31 : 숨겨진 시간적인 결합
때로는 시간적인 결합이 필요한데, 이때 그걸 명백히 드러내는 것이 좋다.

```java
예를 들어, 단순히 이렇게 짜면 함수 호출 순서가 명확하지 않아 순서를 강제할 수 없다.

public void dive(String reason){
    saturateGradient();
    reticulateSplines();
    diveForMoog(reason);
}
```

```java
따라서 아래처럼 파라미터를 적절히 사용해준다면 순서를 강제해줄 수 있다. 

public void dive(String reason){
    Gradient gradient = saturateGradient();
    List<Spline> splines = reticulateSplines(gradient);
    diveForMoog(splines, reason);
}
```

### 자바

#### J1 : 긴 import 목록을 피하고 와일드카드를 사용하라

`import java.util.*;`

개인적인 생각이지만 요즘은 이클립스나 인텔리제이 같은 IDE가 import 영역을 자동으로 가려주고, 필요할 때만 확장해볼 수 있어서 굳이..라는 생각이 들긴 한다.


#### J2 : 상수는 상속하지 않는다

**상속을 이렇게 사용하면 안 된다.** 상수를 상속받아 사용하면 해당 상수가 어느 클래스에서 왔는지 알기 어렵다. 참으로 끔찍한 관행이다.

#### J3 : 상수 대 Enum

자바 5부터는 enum을 제공한다.

**이제 더이상 `public static final`이라는 옛날 기교를 부릴 필요가 없다!**
```java
public static final int JANUARY = 1; // no more SF(static final) plz..
public static final int FEBRUARY = 2;
```

적극적으로 enum을 사용하라. **일반 class처럼 필드/생성자/메서드를 가질 수 있기 때문에 아래 예시처럼 다양한 표현이 가능하다.**

```java
public enum MONTH{
    // Enum 인스턴스
    JANUARY(1, "1월"),  // JANUARY라는 객체
    FEBRUARY(2, "2월"); // FEBRUARY라는 객체
    
    // Enum 필드
    private int number;
    private String korName; 

    // Enum 생성자
    MONTH(int number, String korName) {
        this.number = number;
        this.korName = korName;
    }
}
```

### 테스트

#### T2 : 커버리지 도구를 사용하라!
커버리지 도구는 테스트가 빠뜨리는 공백을 알려준다. 커버리지 도구를 사용하면 테스트가 불충분한 모듈, 클래스, 함수를 찾기가 쉬워진다.

대다수 IDE는 테스트 커버리지를 시각적으로 표현한다. (ex. 테스트되는 행은 초록색, 되지 않는 행은 붉은색)