# 4장 주석

> 나쁜 코드에 주석을 달지 마라. 새로 짜라.
> - 브라이언 W> 커니핸, P.J. 플라우거

### 느낀 점
부끄럽게도 이 책을 읽기 전까지는 주석을 남기는 게 무조건 좋은 줄 알았다.
그래서 주석으로 해당 코드에 대한 설명을 최대한 많이 남기려고 했고, 그게 옳은 건 줄 알았다.
그러나 3장 함수와 4장 주석을 읽으면서 깨달은 점은 <u>코드 >> 주석</u> 이라는 점이다.

주석으로 장황하게 코드 설명을 적을 시간에
- (1) 변수명과 함수명을 더 이해하기 쉽게 바꾸고
- (2) 함수를 쪼개고 (너무 길어지지 않게)
- (3) parameter와 return 값의 의미를 분명하게

하는 것이 훨씬 좋다는 걸 깨달았다. <br/>
앞으로는 주석을 <u>정말 필요할 때 1번</u>이라는 마음가짐으로 달아야겠다.

### 주석에 대한 저자의 관점 요약

- 주석은 필요악이다.
- 코드로 의도를 제대로 표현할 능력만 있다면 주석은 거의 필요하지 않다.
- 내가 이렇게 주석을 무시하는 이유는 프로그래머들이 주석을 유지보수하기란 현실적으로 불가능하기 때문.
- 부정확한 주석은 아예 없는 주석보다 훨씬 더 나쁘다.

### 본문 내용

(1) 주석은 나쁜 코드를 보완하지 못한다

나쁜 코드에 주석을 달 시간에 차라리 코드를 새로 짜든가 보완하라.
보통 주석을 다는 이유는 코드 퀄리티가 떨어지기 때문이다.

(2) 코드로 의도를 표현하라

많은 경우 주석으로 설명을 하는 대신에 **함수로 표현(ex.서술적인 함수명)** 해도 충분하다.

```java
// 변경 전
// 직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
if((employee.flags && HOURLY_FLAG) && (employee.age > 65))
    
// 변경 후    
if(employee.isEligibleForFullBenefits())
```

(3) 좋은 주석

나름 좋다고 생각하는 주석을 소개하겠다. 하지만 주석을 최대한 달지 않는 게 가장 좋다는 걸 명심하자.

1. 법적인 주석

2. 정보를 제공하는 주석
- 정규표현식이 시각과 날짜를 뜻한다고 설명하는 주석이다. 확실히 도움이 된다.
- **물론 아예 DateUtil과 같은 class를 만들어 따로 빼는 게 주석을 다는 것보다 훨씬 좋긴 하다.**

    ```java
    // kk:mm:ss EEE, MMM dd, yyyy 형식이다.
    Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*");
    ```
3. 의도를 설명하는 주석
- 이렇게 코드를 짠 프로그래머의 의도를 표현하는 주석도 좋다.
    ```java
    public int compareTo(Object o){
        if(o instanceof WikiPagePath){
            WikiPagePath p = (WikiPagePath)o;
            String compressedName = StringUtil.join(names, "");
            String compressedArgumentName = StringUtil.join(p.names, "");
            return compressedName.compareTo(compressedArgumentName);
        }
        return 1; // 옳은 유형이므로 정렬 순위가 더 높다.
    }
    ```

4. 의미를 명료하게 밝히는 주석
- 애매모호한 파라미터나 리턴값에 대한 의미를 밝혀주는 것도 좋다.
    - 예를 들어, a.compareTo(b) 에서 파라미터인 a, b에 대한 설명도 없고 리턴값인 -1, 0, +1 도 의미가 불분명하므로 이럴 땐 주석을 달아주면 좋다.

      ```java
      assertTrue(a.compareTo(a) == 0);    // a == a
      assertTrue(a.compareTo(b) != 0);    // a != b
      assertTrue(a.compareTo(b) == -1);   // a < b 
      assertTrue(a.compareTo(b) == 1);    // a > b 
      ```

- 물론 파라미터와 리턴값에 대한 의미를 분명하게 하여 함수를 설계하는 것이 가장 좋은 방법이다.
- 그러나 compareTo 처럼 표준 라이브러리나 변경하지 못하는 코드에 속한다면 위처럼 의미를 명료하게 밝히는 주석이 유용하다.

5. 결과를 경고하는 주석

6. TODO 주석

7. 중요성을 강조하는 주석

- 아래 예시처럼 중요성을 강조하는 주석도 좋은 주석이다.

```java
String listItemContent = match.group(3).trim();
// 여기서 trim은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다.
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다. 

new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end()));
```

8. Open API에서 Javadocs

- Javadocs도 좋은 주석이다. API에 대한 설명을 할 때 아주 유용하다.
- 그러나 모든 함수에 Javadocs를 다는 행동은 좋지 않다. 꼭 설명이 필요한 경우에만 달도록 하자.


(4) 나쁜 주석

1. 주절거리는 주석
2. 같은 이야기를 중복하는 주석
3. 오해할 여지가 있는 주석
4. 의무적으로 다는 주석

- Javadocs 부분에서 이미 한 번 언급하긴 했지만.. 모든 함수에 Javadocs 주석을 달 필요는 없다.
- **충분히 이해가 가능하게끔 변수명과 함수명을 설계했으므로 굳이 주석이 필요 없다.**

    ```java
    /**
     * 
     * @param title CD 제목
     * @param author CD 저자
     * @param tracks CD 트랙 숫자
     * @param durationInMinutes CD 길이(단위: 분)
     */
    public void addCD(String title, String author, int tracks, int durationInMinutes){
        CD cd = new CD();
        cd.title = title;
        cd.author = author;
        cd.tracks = tracks;
        cd.duration = cd.durationInMinutes;
        cdList.add(cd);
    }
    ```

5. 이력을 기록하는 주석

- 과거와 달리 이제는 git 등 형상 관리 시스템이 있으므로 더이상 이력을 기록하는 주석이 필요 없다.

6. 있으나 마나 한 주석

- 너무 당연한 사실을 주석으로도 달지 마라.

    ```java
  // 기본 생성자임을 모르는 개발자는 없을 것이다..
  
    /**
     * 기본 생성자
     */
    protected AnnualDateRule(){
        
    }
    ```

7. 무서운 잡음

8. 함수나 변수로 표현할 수 있다면 주석을 달지 마라

9. 위치를 표시하는 주석

- 반드시 필요할 때만 사용하도록 하자.

    ```java
    // ~~을 위한 코드 시작
    
    ...
    
    // ~~을 위한 코드 끝
    ```

10. 닫는 괄호에 다는 주석

- 이러한 주석을 달고 싶다는 생각이 들면 차라리 함수를 쪼개라. 함수가 장황하고 길다는 뜻이다.

    ```java
    if(){
        
        ...
            
    } // if end
    ```

11. 공로를 돌리거나 저자를 표시하는 주석

12. 주석으로 처리한 코드

- 주석으로 처리된 코드는 다른 사람들이 지우기를 주저한다.
- 그러다 점차 쌓여서 나쁜 코드가 되어간다. 필요 없다면 주석 처리하지 말고 꼭 지우자.

```java
response.setBody(formatter.getResultStream(), formatter.getByteCount());
// InputStream resultsStream = formatter.getResultStream(); // 이렇게 남기지 말고 미리 미리 지우자.
// StreamReader reader = new StreamReader(resultStream);
```

13. HTML 주석

14. 전역 정보

15. 너무 많은 정보

16. 모호한 관계

17. 함수 헤더

18. 비공개 코드에서 Javadocs

(5) 예제
- 예제를 통해 어떤 주석을 없애면 좋을지 직접 테스트 해보자.
- 책 90 ~ 94