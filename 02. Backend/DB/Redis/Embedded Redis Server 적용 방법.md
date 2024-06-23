# Embedded Redis Server 적용 방법

## 목차
1. [개요](#개요)
2. [의존성 추가](#의존성-추가)
3. [Setup](#setup)
4. [Embedded Redis Server](#embedded-redis-server)
5. [실제 테스트 결과](#실제-테스트-결과)

---

## 개요
Spring Data Redis를 이용하면 Redis 인스턴스를 사용하기 쉽습니다.
하지만 `real server` 환경을 구축하기보다 `embedded server`를 사용하는 게 더 편할 때도 있습니다.
본격적으로 `Embedded Redis Server`를 설치 및 사용하는 방법에 대해 알아보겠습니다.

## 의존성 추가
```groovy
dependencies{
    // 1. Embedded Redis
    testImplementation group: 'it.ozimov', name: 'embedded-redis', version: '0.7.2'
    // 2. Spring Boot Starter Test
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    // 3. Spring Data Redis
    implementation 'org.springframework.boot:spring-boot-starter-data-redis'
}
```

### 1. Embedded Redis
우리에게 필요한 `embedded server`를 포함하고 있는 dependency입니다.

참고로 `0.7.2`버전을 사용해야 합니다. 
현재 가장 최신 버전인 `0.7.3`은 gradle build는 잘 되지만, 테스트 코드 실행시 아래와 같은 에러가 발생하는 문제가 있습니다.  
```java
SLF4J: Class path contains multiple SLF4J providers.
SLF4J: Found provider [org.slf4j.simple.SimpleServiceProvider@15bb6bea]
SLF4J: Found provider [ch.qos.logback.classic.spi.LogbackServiceProvider@8b96fde]
SLF4J: See https://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual provider is of type [org.slf4j.simple.SimpleServiceProvider@15bb6bea]
```

### 2. Spring Boot Starter Test
`spring-boot-starter-test` dependency는 우리가 테스트 코드를 짜고, 통합 테스트를 진행할 때 필요한 모든 것들을 담고 있습니다.
스프링 부트 프로젝트를 만들면 기본적으로 들어있는 dependency이기 때문에 따로 추가할 필요는 없습니다.

### 3. Spring Data Redis
Spring Application에서 Redis를 손쉽게 구성 및 접근할 수 있도록 하는 모듈


## Setup
이제 Redis Server와 우리 Application간에 Connection setting을 해줍시다.

DB server에 연결하는 것과 마찬가지로 Embedded Redis Server와의 연결을 위해 `properties (e.g. host, port, id, pw 등)`가 필요합니다.

### application.yml
```yaml
spring:
  data:
    redis:
      host: localhost # 연결에 필요한 host, port 설정값을 정의
      port: 6370
```

### RedisProperties
```java
@Getter
@Configuration
public class RedisProperties{
    
    private int redisPort;
    private String redisHost;
    
    public RedisProperties(
            @Value("${spring.data.redis.port}") int redisPort,
            @Value("${spring.data.redis.host}") String redisHost){
        this.redisPort = redisPort;
        this.redisHost = redisHost;
    }
}
```

#### 코드 설명
- `@Configuration` : RedisProperties를 설정 클래스로 등록
- `@Value("${spring.data.redis.port}")` : 외부 설정 파일(e.g. application.yml)에서 읽은 값을 변수에 주입해줌  
- `@Getter` : RedisConfiguration에서 이 RedisProperties 빈의 redisPort, redisHost 값을 사용하므로 getter가 반드시 필요  


### RedisConfiguration
```java
@Configuration
@EnableRedisProperties
public class RedisConfiguration{
    
    @Bean
    public LettuceConnectionFactory redisConnectionFactory(RedisProperties redisProperties){
        return new LettuceConnectionFactory(
                redisProperties.getRedisPort(),
                redisProperties.getRedisHost()
        );
    }
    
    @Bean
    public RedisTemplate<?, ?> redisTemplate(LettuceConnectionFactory connectionFactory){
        RedisTemplate<byte[], byte[]> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);
        return template;
    }
}
```


## Embedded Redis Server
이제 테스트 환경에서 `Embedded Redis Server`를 사용하는 예시 코드를 보여드리겠습니다.

그 전에 몇 가지 설정이 더 필요합니다.

### 1. application.yml
`src/test/resources` 디렉토리에 `application.yml` 추가
```yaml
spring:
  data:
    redis:
      host: localhost
      port: 6370
```

### 2. @TestConfiguration-annotated class 
테스트 환경에서 사용하는 것이기에 아래와 같은 `테스트용 설정 클래스`가 필요합니다.
```java
@TestConfiguration // 테스트 환경용 설정 클래스로 만들어주는 어노테이션
public class TestRedisConfiguration {

    private RedisServer redisServer;

    public TestRedisConfiguration(RedisProperties redisProperties) {
        this.redisServer = new RedisServer(redisProperties.getRedisPort());
    }

    @PostConstruct
    public void postConstruct() {
        redisServer.start();
    }

    @PreDestroy
    public void preDestroy() {
        redisServer.stop();
    }
}
```
이렇게 하면 개인 PC에 Redis를 따로 설치하지 않아도 Test 코드를 실행할 수 있습니다. 


## 실제 테스트 결과

### 테스트 코드
```java
@Slf4j
@SpringBootTest(classes = TestRedisConfiguration.class)
public class RedisServiceTest_Embedded {

    final String KEY = "key";
    final String VALUE = "value";
    final Duration DURATION = Duration.ofMillis(3000);

    @Autowired
    private RedisService redisService;

    @BeforeEach
    void setValueBeforeTest(){
        redisService.setValues(KEY, VALUE, DURATION);
    }

    @AfterEach
    void deleteValueAfterTest(){
        redisService.deleteValues(KEY);
    }

    @Test
    @DisplayName("Redis에 데이터를 저장하면 정상적으로 조회된다.")
    void saveAndFindTest(){
        // when
        String findValue = redisService.getValues(KEY);

        // then
        assertThat(VALUE).isEqualTo(findValue);
    }

    @Test
    @DisplayName("Redis에 저장된 데이터를 수정할 수 있다.")
    void updateTest(){
        // given
        String updateValue = "updateValue";
        redisService.setValues(KEY, updateValue, DURATION);

        // when
        String findValue = redisService.getValues(KEY);

        // then
        assertThat(updateValue).isEqualTo(findValue);
        assertThat(VALUE).isNotEqualTo(findValue);
    }

    @Test
    @DisplayName("Redis에 저장된 데이터를 삭제할 수 있다.")
    void deleteTest(){
        // when
        redisService.deleteValues(KEY);
        String findValue = redisService.getValues(KEY);

        // then
        assertThat(findValue).isEqualTo("false");
    }
}
```

#### cf. `@SpringBootTest(classes = TestRedisConfiguration.class)`
테스트에 필요한 Bean으로 우리가 만든 `TestRedisConfiguration` 클래스를 등록하는 코드라고 보면 된다.
`TestRedisConfiguration`에서 `Embedded Redis Server`를 실행시키도록 해놨기 때문에 반드시 필요하다.

만약 `classes`에 등록하지 않는다면 다음과 같은 에러가 발생한다.
```java
org.springframework.data.redis.RedisConnectionFailureException: Unable to connect to Redis
```


### RedisService
```java
/**
 * Redis에 저장, 조회, 삭제하는 메서드를 구현하는 클래스. RedisTemplate를 주입받아 Redis 데이터를 조작한다.
 * @author 김용희
 */
@Slf4j
@Component
@RequiredArgsConstructor
public class RedisService {

    private final StringRedisTemplate stringRedisTemplate;

    // key와 data를 Redis에 저장한다. 만약 데이터에 만료 시간을 설정하고 싶다면 세 번째 파라미터로 Duration 객체를 전달한다.
    public  void setValues(String key, String data){
        ValueOperations values = stringRedisTemplate.opsForValue();
        values.set(key, data);
    }

    public void setValues(String key, String data, Duration duration){
        ValueOperations values = stringRedisTemplate.opsForValue();
        values.set(key, data, duration);
    }

    // key 파라미터로 받아 key를 기반으로 데이터를 조회한다.
    @Transactional(readOnly = true) // DB 트랜잭션에서 단순히 select(읽기)만 하는 경우에 readOnly = true 속성을 주면 성능에 조금이라도 이점을 얻을 수 있다고 한다.
    public String getValues(String key){
        ValueOperations values = stringRedisTemplate.opsForValue();
        if(values.get(key) == null){
            return "false"; // 이거 좀 맘에 안드는데
        }
        return (String) values.get(key);
    }

    // key를 파라미터로 받아 key를 기반으로 데이터를 삭제한다.
    public void deleteValues(String key){
        stringRedisTemplate.delete(key);
    }

    public void expireValues(String key, int timeout){
        stringRedisTemplate.expire(key, timeout, TimeUnit.MILLISECONDS);
    }

    // 조회하려는 데이터가 없으면 “false”를 반환한다.
    public boolean checkExistsValue(String value){
        return !value.equals("false");
    }
}
```


## Reference
- Embedded Redis Server with Spring Boot Test https://www.baeldung.com/spring-embedded-redis
- 로컬 환경을 위한 Embedded Redis 적용하기 https://green-bin.tistory.com/77#EmbeddedRedisConfig
- Spring Data란? https://imbf.github.io/spring/2020/04/30/Spring-Data.html

