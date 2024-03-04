# 스프링 핵심 원리 이해2 - 객체 지향 원리 적용

## IoC, DI, 그리고 컨테이너

### IoC 
Inversion of Control, 제어의 역전


#### 프레임워크 vs 라이브러리
- 프레임워크가 내가 작성한 코드를 제어하고 대신 실행한다면 그것은 프레임워크가 맞다. (e.g. 스프링 프레임워크, JUnit 프레임워크)
- 반면에 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니라 라이브러리다. (e.g. )


### DI 


### IoC 컨테이너, DI 컨테이너
- IoC 컨테이너, DI 컨테이너라고 해서 거창한게 아닙니다.
- 그냥 단지 `AppConfig`처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을 `IoC 컨테이너` 또는 `DI 컨테이너`라고 부르는 것입니다.
- 의존관계 주입에 초점을 맞춰 최근에는 주로 DI 컨테이너라고 합니다. (혹은 어셈블러, 오브젝트 팩토리 등으로 불림)

#### AppConfig
`AppConfig`를 보면 여러가지 객체를 생성하는 것을 볼 수 있습니다. 

이중 `OrderService` 객체 생성자 부분을 보면 `memberRespository()`와 `discountPolicy()`를 넣어주고 있는데, 
각각 `MemoryMemberRepository`, `RateDiscountPolicy` 구현체를 **사용하기 위해** 파라미터로 넣어주고 있습니다.

이런 식으로 **의존성 주입**(DI)을 하는 것이며, 이렇게 의존성 주입을 하는 것을 보고 `DI 컨테이너`라고 부릅니다.

```java
public class AppConfig{
    
    public MemberService memberService(){
        return new MemberServiceImpl(memberRespository());
    }

    public MemberRepository memberRepository(){
        return new MemoryMemberRepository();
    }

    public OrderService orderService(){
        return new OrderServiceImpl(memberRespository(), discountPolicy());
    }

    public DiscountPolicy discountPolicy(){
        //return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
    
}
```

## Reference
- 요즘IT : https://yozm.wishket.com/magazine/detail/2115/