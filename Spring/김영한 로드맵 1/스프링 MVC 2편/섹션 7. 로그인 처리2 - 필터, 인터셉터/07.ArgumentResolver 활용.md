# ArgumentResolver 활용

`@Login` 어노테이션이 있으면 직접 만든 `ArgumentResolver`가 동작해서 자동으로 세션에 있는 로그인 회원을 찾아주고, 만약 세션에 없다면 `null`을 반환하도록 개발해보자.


### HomeController
```java
@GetMapping("/")
public String homeLoginV3ArgumentResolver(@Login Member loginMember, Model model){

    // 세션에 회원 데이터가 없으면 home 화면
    if(loginMember == null){
        return "home";
    }

    // 세션이 유지되면 로그인으로 이동
    model.addAttribute("member", loginMember);
    return "loginHome";
}
```


### 1. 어노테이션 생성 - @Login 
```java
@Target(ElementType.PARAMETER)      // 파라미터에만 사용하겠다는 의미
@Retention(RetentionPolicy.RUNTIME) // 리플렉션 등을 활용할 수 있도록 런타임까지 어노테이션 정보가 남아있게 함
public @interface Login {
}
```

당연한 말이지만 이 어노테이션 클래스를 만든다고 해서 `@Login`이 작동하는 건 아니다. 
여기서 바로 오늘 배워볼 `ArgumentResolver`를 사용하는 것이다.


### 2. ArgumentResolver 생성 - LoginMemberArgumentResolver
`HandlerMethodArgumentResolver`를 implements 하고, `supportsParameter()`, `resolveArgument()` 메서드를 구현해주면 된다.

```java
@Slf4j
public class LoginMemberArgumentResolver implements HandlerMethodArgumentResolver {

    // ArgumentResolver 효용 : 공통 작업이 필요할 때 컨트롤러를 더욱 편리하게 사용할 수 있다.
    // ArgumentResolver 예시 : @Login 어노테이션 참조 (HomeController)

    @Override
    public boolean supportsParameter(MethodParameter parameter) {
        log.info("supportsParameter 실행");

        boolean hasLoginAnnotation = parameter.hasParameterAnnotation(Login.class);
        boolean hasMemberType = Member.class.isAssignableFrom(parameter.getParameterType()); // getParameterType 하면 Member 클래스를 얻게 된다.

        // 1. @Login 어노테이션을 갖고 있으며
        // 2. Member 타입일 때
        // 즉, 2개 조건을 모두 만족할 때 아래의 resolveArgument 를 실행하게 된다. (HandlerMethodArgumentResolver 인터페이스에 나와있는 설명을 읽어보자)
        return hasLoginAnnotation && hasMemberType;
    }

    @Override
    public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
        log.info("resolverArgument 실행");

        HttpServletRequest request = (HttpServletRequest) webRequest.getNativeRequest();
        HttpSession session = request.getSession(false);
        if (session == null) {
            return null; // 1. 세션이 null이면 Member 객체에 null을 넣겠다는 의미
        }

        return session.getAttribute(SessionConst.LOGIN_MEMBER); // 2. 세션이 null이 아니면 Member 객체에 로그인한 멤버 정보를 넣겠다는 의미
    }

    // 이렇게 한다고 해서 끝난게 아니다.
    // WebMvcConfigurer 인터페이스 구현체에서 addArgumentResolvers 메서드를 오버라이드하여 ArgumentResolver를 등록해야 한다.

}
```

- `supportsParameter()` : `@Login` 어노테이션이 있으면서 `Member` 타입이면 해당 `ArgumentResolver`가 사용된다.
- `resolveArgument()` : 컨트롤러 호출 직전에 호출되어서 필요한 파라미터 정보를 생성해준다. 여기서는 세션에 있는 로그인 회원 정보인 `member` 객체를 찾아서 반환해준다. 이후 스프링MVC는 컨트롤러의 메서드를 호출하면서 여기에서 반환된 `member` 객체를 파라미터에 전달해준다. 


### 3. ArgumentResolver 등록 - WebConfig
필터, 인터셉터와 마찬가지로 `ArgumentResolver`도 등록을 해줘야 한다.
```java
@Configuration
public class WebConfig implements WebMvcConfigurer{
    
    // ArgumentResolver 등록
    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        resolvers.add(new LoginMemberArgumentResolver());
    }
    
}
```