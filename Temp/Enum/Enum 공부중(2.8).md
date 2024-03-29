# 2월 8일 설날

Front <-> Back 사이에서 주고 받을 때 Enum 타입은 어떻게 처리되는지 궁금해서 이것저것 실험 중

TestEnum testEnum 에서 List<TestEnum> testEnum 으로 바꿔서 보내니까 이런 에러 남.
```java
.w.s.m.s.DefaultHandlerExceptionResolver : Resolved [org.springframework.http.converter.HttpMessageNotReadableException: JSON parse error: Cannot deserialize value of type `java.util.ArrayList<com.crabcodingtest.domain.signup.response.TestDTO$TestEnum>` from String value (token `JsonToken.VALUE_STRING`)]
```
★정리 : 그냥 저 에러는 쉽게 말해서 프론트 -> 백 했을 때 해당 Enum 타입에 선언된 객체와 매핑이 되지 않으면 나는 거임. (걍 매핑이 제대로 안 됐을 때 나는 에러로 알고 있음. 꼭 enum이 아니더라도)

---

# 어떻게 매핑이 되는지 알아보자. (★사실상 스프링 Converter에 대해 알아가는 과정..?)

## 1. Front -> Back 
★ 정말 놀라운 걸 발견 
- 객체의 필드 값으로는 인식 못함.
- but, 객체의 ordinal(서수) 값으로는 인식이 됨 ㄷㄷ;;
  - 서수란? first, second와 같은 순서를 나타내는 수 (이른바 순서수)

예시
```java
"testEnumList":["SUCCESS", "FAIL", 0, 1, 111]

"SUCCESS", "FAIL" <- 이런 건 당연히 인식하는데
0, 1 <- 이것도 ordinal로 인식하는지 0은 SUCCESS, 1은 FAIL로 매핑
111 <- 반면 필드 값으로는 인식 X 
```

### 이건 enum 관련은 아니고, @NoArgsConstructor 어노테이션과 관련
`@NoArgsConstructor`를 생략하니까 
`@RequestBody TestDTO testDTO` 형태로 프론트에서 백으로 데이터를 넘겨도...
JSON 데이터를 JAVA 객체로 변환(매핑)시켜주는 jackson 라이브러리가 매핑을 못 시키는 에러 발생.

에러 원인은 다음과 같음.
★기본 생성자와 같은 creators가 없어서 TestDTO의 인스턴스를 생성할 수 없다!! 라는 에러
```java
com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Cannot construct instance of `com.crabcodingtest.domain.signup.request.TestDTO` (no Creators, like default constructor, exist): cannot deserialize from Object value (no delegate- or property-based Creator)
```

그래서 이를 정리한 블로그를 찾았음. ★★
https://91ms.tistory.com/29
![img.png](img.png)
![img_1.png](img_1.png)
(cf. @JsonProperty, @JsonAutoDetect 를 사용하는 프로퍼티 기반 객체 or 생성자를 위임한 경우라면 필요하지 않다! 라는 건 무슨 뜻일까??)

### 객체의 ordinal로도 인식이 되는 문제
배경
- 나는 프론트에서 SUCCESS 라고만 넘겨줘야 백에서도 Enum.SUCCESS 로 convert 되게끔 해주고 싶은데... ordinal인 0 을 넘겨줘도 Enum.SUCCESS 로 인식이 되는 문제가 발생. 

해결 중
- @JsonCreator, @JsonProperty 를 사용하면 된다??
- ★아니 근데.. ConverterFactory 라는 추가 작업이 필요하네;;;
- https://velog.io/@wlgns3855/JAVA-JsonCreator%EB%A1%9C-enum-%ED%81%B4%EB%9E%98%EC%8A%A4-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0

#### cf. @JsonCreator vs @JsonProperty
`@JsonCreator` : 이 애노테이션은 JSON을 Java 객체로 역직렬화할 때 사용됩니다. 
주로 생성자나 정적 메서드에 적용되며, Jackson 라이브러리가 JSON 데이터를 사용하여 객체를 생성할 때 이 애노테이션이 붙은 생성자나 정적 메서드를 사용합니다. 
이를 통해 복잡한 객체 생성 로직을 구현하거나, JSON의 형식과 Java 객체의 형식이 다를 때 사용됩니다.

`@JsonProperty` : 이 애노테이션은 Java 객체의 필드 또는 메서드를 JSON의 속성으로 매핑할 때 사용됩니다. 
즉, Java 객체의 필드나 메서드에 이 애노테이션을 사용하여 해당 속성을 JSON의 어떤 이름으로 매핑할지를 지정할 수 있습니다. 
이는 주로 Java 객체를 JSON으로 직렬화할 때 사용됩니다.
(★chatGPT에선 이렇게 설명했는데, 사실은 역직렬화할 때도 사용됨(밸덩 예시). 그냥 단순히 매핑 용도일 뿐임. 오해 ㄴㄴ)

#### Reference
![img_2.png](img_2.png)
![img_3.png](img_3.png)

### ★ 완성본
```java
@Getter
@NoArgsConstructor(access = AccessLevel.PRIVATE)
// 참조 : ★https://91ms.tistory.com/29
// JSON <-> Java 객체 간의 변환을 해주는 Jackson 라이브러리가 변환을 하는 과정에서
// 기본 생성자와 같은 creators를 필요로 하므로 추가해줬다.
// 단, 클래스 바깥에서 함부로 생성을 막아주고 싶었기에 AccessLevel을 PRIVATE으로 주었다.
// (@Builder 어노테이션이 기본 생성자를 만들어주지 않기 때문에 이러한 과정이 따로 필요했음)
public class TestDTO {
    private String str;
    private int num;
    private boolean bool;
    private double d;
    private TestEnum testEnum;
    private List<TestEnum> testEnumList;

    @Slf4j
    @Getter
    @RequiredArgsConstructor
    public enum TestEnum {
        SUCCESS("SUCCESS"),FAIL("FAIL"),TEST("TEST"); // 애초에 123 같은 숫자를 쓰면 컴파일 에러가 뜨네??

        private final String str;

        // @JsonCreator, @JsonProperty 에 대한 공부 필요 (잭슨 라이브러리꺼네)
        @JsonCreator // ★annotation used for indicating that a constructor or static factory method should be used for creating value instances during deserialization.
        public static TestEnum from( String param){
            for(TestEnum testEnum : TestEnum.values()){
                if(param.equalsIgnoreCase(testEnum.getStr())){ // 1. 즉 FE에서 들어온 값이 "FAIL"이면
                    return testEnum; // 2. FAIL 객체를 리턴
                }
            }
//            throw new BusinessLogicException(ExceptionCode.INVALID_GENDER);
            throw new IllegalArgumentException(">>>>>>>>>>>>>>>>>>>> 잘못된 값을 입력하셨습니다 : " + param);
        }
    }

    @Builder
    public TestDTO(String str, int num, boolean bool, double d, TestEnum testEnum, List<TestEnum> testEnumList){
        this.str = str;
        this.testEnum = testEnum;
        this.testEnumList = testEnumList;
        /**
         * 생성자에 Builder 패턴을 적용한다는 건가?
         * this.num = num;
         * this.bool = bool;
         * 이런 걸 생략하니까 값이 세팅이 안되더라. (당연한 건데, 잘 이해가 안됐음. 왜 굳이 써야 하는지..)
         * ★걍 생성자에서 this.a = a; 하는 거랑 똑같은 거네
         */
    }
}
```

## ★문서화할 때 참조할 레퍼런스 목록
- 공식문서 : https://github.com/FasterXML/jackson-annotations/wiki/Jackson-Annotations
- 예시 코드 1 (밸덩) : https://www.baeldung.com/jackson-annotations
- 예시 코드 2 (블로그) : https://green-bin.tistory.com/90
- enum에 대한 기본 개념 등 (우아한 기술블로그) : https://techblog.woowahan.com/2527/
- enum API (자바 공식문서) : https://docs.oracle.com/javase/8/docs/api/java/lang/Enum.html

---

## 2. Back -> Front
문제없이 잘 됨.

