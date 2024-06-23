# 스프링 AOP - 실무 주의사항 (+ 실제 사례)

## 목차
0. [AOP 용어](#0-aop-용어)
1. [프록시와 내부 호출 - 문제](#1-프록시와-내부-호출---문제)
2. [프록시와 내부 호출 - 대안1 자기 자신 주입](#2-프록시와-내부-호출---대안1-자기-자신-주입)
3. [프록시와 내부 호출 - 대안2 지연 조회](#3-프록시와-내부-호출---대안2-지연-조회)
4. [프록시와 내부 호출 - 대안3 구조 변경](#4-프록시와-내부-호출---대안3-구조-변경)

## 0. AOP 용어
- Advice(어드바이스)
  - 부가기능
- JoinPoint(조인포인트)
  - AOP를 적용할 수 있는 포인트(지점) or 어드바이스가 적용될 수 있는 위치
    - 메서드 실행, 생성자 호출, 필드 값 접근, static 메서드 접근 등
    - `스프링 AOP의 조인포인트` : 프록시 방식으로 작동하기 때문에 오로지 `메서드 실행`뿐이다.
- Pointcut(포인트컷)
  - 어드바이스가 어디에 적용될지 고르는 기능
  - ex) `execution(public * com.example.service.*.*(..))`
- Target(타겟)
  - 어드바이스를 적용받는 객체. 포인트컷에 의해 결정된다.
- Aspect(애스펙트)
  - 어드바이스(부가기능) + 포인트컷(부가기능을 어디에 적용할지)
- Advisor(어드바이저)
  - 하나의 어드바이스 + 하나의 포인트컷 (스프링 AOP에서만 사용하는 용어)

## 1. 프록시와 내부 호출 - 문제

스프링은 프록시 방식의 AOP를 사용합니다.
AOP를 적용하면 항상 프록시를 통해서 Target(대상 객체)을 호출합니다.
즉, 프록시에서 먼저 어드바이스(부가기능)를 호출하고, 이후에 대상 객체를 호출합니다.
만약 프록시를 거치지 않고 대상 객체를 직접 호출하게 되면 AOP가 적용되지 않고, 어드바이스도 호출되지 않습니다.

AOP를 적용하면 스프링은 대상 객체 대신에 프록시를 스프링 빈으로 등록합니다. 
따라서 스프링은 의존관계 주입시에 항상 프록시 객체를 주입합니다.
프록시 객체가 주입되기 때문에 대상 객체를 직접 호출하는 문제는 일반적으로 발생하지 않습니다.
하지만 대상 객체 내부에서 메서드 호출이 발생하면 프록시를 거치지 않고 대상 객체를 직접 호출하는 문제가 발생합니다.
실무에서 반드시 한번은 만나서 고생하는 문제이기 때문에 꼭 이해하고 넘어가도록 합시다.

### (1) 코드로 보는 예시

`CallServiceV0`라는 서비스가 있고, `external`과 `internal`이라는 메서드가 각각 있습니다.
둘 다 `public`이기 때문에 트랜잭션, 로깅과 같은 AOP가 적용될 것입니다.

다만 특이한 점은 `external` 메서드 내부에서 `internal` 메서드를 따로 호출하는 로직이 있다는 겁니다.

#### CallServiceV0
```java
@Slf4j
@Component
public class CallServiceV0 {

	public void external(){
		log.info("external 호출");
		internal();	// 내부 호출!!
	}

	public void internal(){
		log.info("internal 호출");
	}

}
```

#### CallServiceV0Test
이제 실제로 `CallServiceV0`라는 서비스의 메서드를 호출한다고 가정해봅시다.
```java
@Import(CallLogAspect.class)
@SpringBootTest
class CallServiceV0Test {

	@Autowired
	CallServiceV0 callServiceV0;

	@Test
	void external(){
		callServiceV0.external();
	}

}
```

#### 실행 결과 - external()
```
1. //프록시 호출
2. CallLogAspect     : aop=void hello.aop.internalcall.CallServiceV0.external()
3. CallServiceV0     : call external
4. CallServiceV0     : call internal
```

#### 실행 결과 분석
![image](https://github.com/djdjdddd/TIL/assets/126077503/7ccabba5-255e-49c3-a2f7-4373b34e626d)

```
callServiceV0.external() 실행시 
1. 프록시 호출
  - Target : CallServiceV0
  - AOP Proxy : CallServiceV0$$EnhancerBySpringCGLIB$$4c07ce03@1889
2. 어드바이스(부가기능) 호출
3. target.external() : AOP Proxy에서 external 메서드 호출
4. this.internal() : Target에서 internal 메서드 호출
```

다들 아시다시피 자바 언어에서 메서드 앞에 별도의 참조가 없으면 `this`(자기 자신 인스턴스)가 생략되어 있습니다. 
즉 `callServiceV0.external()` 내부에서 `this.internal()`을 호출하는 셈이며, 여기서 `this`는 AOP가 적용된 Proxy 객체가 아니라 CallServiceV0(쌩 객체)입니다.


### (2) WRMS 코드로 보는 예시

#### 문제가 됐던 코드
```java
@Service
//@Transactional //어노테이션 대신 xml로 설정 중
public class XxxSvcImpl {

	public void saveXxx(){
		newXxx(); // 프록시 객체를 거쳐서 호출하는 게 아니므로 AOP가 적용되지 않는다.
	}
	
	public void newXxx(){
		...
	}

}
```

#### cf. Propagation(전파단계)
```xml
<tx:advice id="txAdvice" transaction-manager="txManager">
  <tx:attributes>
    <tx:method name="save*" rollback-for="Exception" propagation="REQUIRED">
    <tx:method name="new*" rollback-for="Exception" propagation="REQUIRES_NEW">
  </tx:attributes>
</tx:advice>
```
- `REQUIRED` : 호출한 메서드의 트랜잭션을 사용하거나 새로운 트랜잭션을 시작
- `REQUIRES_NEW` : 항상 새로운 트랜잭션을 시작

#### 권장하는 해결 방법 - 대안3 이용
`saveXxx()`와 `newXxx()`를 서로 다른 서비스로 분리한 후 사용하면 됩니다. 

```java
@Service
@RequiredArgsConstructor
public class XxxSvcImpl {

	private final YyySvc yyySvcImpl;

	public void saveXxx(){
		yyySvcImpl.newXxx(); // AOP가 적용된다.
	}

}
```

---


## 2. 프록시와 내부 호출 - 대안1 자기 자신 주입
내부 호출을 해결하는 가장 간단한 방법은 자기 자신을 의존관계 주입 받는 것입니다.
한가지 주의해야 할 점은 `생성자 주입` 사용시 `순환 참조` 오류가 발생한다는 것입니다. 
이는 `setter 주입` 방식으로 해결할 수 있습니다.

#### CallServiceV1
```java
@Service
@Transactional
public class CallServiceV1 {

	private CallServiceV1 callServiceV1;

	// setter 주입
	@Autowired
	public void setCallServiceV1(CallServiceV1 callServiceV1){  // 생성자 주입시 순환 참조 오류가 발생하므로 setter 주입을 사용!
		this.callServiceV1 = callServiceV1;
	}

	public void external(){
		log.info("external 호출");
		internal();
	}

	public void internal(){
		log.info("internal 호출");
	}

}
```

---

## 3. 프록시와 내부 호출 - 대안2 지연 조회
`setter 주입` 방식 외에도 `ObjectProvider` 또는 `ApplicationContext`를 이용한 지연(LAZY) 조회 방식도 있습니다.

#### CallServiceV2
예시 코드는 `ApplicationContext`를 이용하였습니다.
```java
@Service
@Transactional
public class CallServiceV2 {

	// for 스프링 bean 조회
	private final ApplicationContext applicationContext;

	public void external(){
		log.info("external 호출");
		CallServiceV2 callServiceV2 = applicationContext.getBean(CallServiceV2.class);  // ApplicationContext를 이용하여 CallServiceV2 객체를 지연 조회
		callServiceV2.internal();  // 지연 조회한 CallServiceV2 객체를 이용
	}

	public void internal(){
		log.info("internal 호출");
	}

}
```

---

## 4. 프록시와 내부 호출 - 대안3 구조 변경
문제가 됐던 `internal` 메서드를 아예 다른 `서비스(InternalService)`로 분리하는 방법입니다.
이렇게 되면 AOP가 제대로 적용되지 않아 문제가 됐던 `this.internal()` 코드가 `internalService.internal()`로 바뀌게 됩니다. 
따라서 AOP가 적용되게 됩니다.

#### CallServiceV3
```java
@Service
@Transactional
public class CallServiceV3 {

	private final InternalService internalService;

	// 생성자 주입
	public CallServiceV3(InternalService internalService){
		this.internalService = internalService;
	}

	public void external(){
		log.info("external 호출");
		internalService.internal();
	}

}
```

#### InternalService - 서비스 분리
```java
@Service
@Transactional
public class InternalService {

	public void internal(){
		log.info("internal 호출");
	}

}
```

---

## Reference
- 김영한의 스프링 핵심 원리 - 고급편
- https://mangkyu.tistory.com/269
