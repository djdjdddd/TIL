# 14장 점진적인 개선

### 개요
이 장은 점진적인 개선을 보여주는 사례 연구다. 출발은 좋았으나 확장성이 부족했던 모듈을 소개한다. 그런 다음, 모듈을 개선하고 정리하는 단계를 살펴본다.

### Args 구현 (p.247~253)

자세한 코드는 책 참조. <br/>
훗날 clean code 사례 연구시 꼭 직접 타이핑해보도록 하자.

단순한 개념을 구현한 것인데도 코드가 많이 필요한 이유는 자바가 정적 타입 언어라서 그렇다.

> #### cf. 정적 타입 언어 vs 동적 타입 언어
> (1) 정적 타입 언어란 '타입' 즉 자료형을 컴파일 시에 결정. 컴파일 시에 자료형에 맞지 않은 값이 들어있으면 컴파일 에러가 발생.
> - 장점 : 컴파일 시에 타입에 대한 정보를 결정하기 때문에 속도 ↑ , 타입 에러로 인한 문제점을 초기에 발견할 수 있어 타입의 안정성 ↑
> - 단점 : 일일이 타입을 지정해줘야 하기 때문에 코드가 장황해지는 점.
>
> (2) 동적 타입 언어란 컴파일 시 자료형을 정하는 것이 아니고 실행 시에 결정
> - 장점 : Runtime까지 타입에 대한 결정을 끌고 갈 수 있기 때문에 많은 선택의 여지가 있다.
> - 단점 : 인터프리터 언어는 배우기는 쉬우나 실행 도중에 변수에 예상치 못한 타입이 들어와 Type Error 가능성이 있다.

#### 어떻게 짰을까?!
처음부터 코드를 clean하게 짠 것이 절대 아니다.
프로그래밍은 과학보다 공예에 가깝다.
즉, **깨끗한 코드를 짜려면 먼저 지저분한 코드를 짠 뒤에 정리**해야 한다는 의미다. (이는 작문을 하는 방법과 유사하다)

### Args 1차 초안 (pp.255~261)

자세한 코드는 책 참조.

### Args 초안 및 점진적 개선 과정

#### 1. Boolean만 지원하는 버전

비록 길긴 하지만 나름 괜찮은 코드이다.

```java
public class Args{
    private String schema;
    private String[] args;
    private boolean valid;
    private Set<Character> unexpectedArguments = new TreeSet<Character>();
    private Map<Character, Boolean> booleanArgs = new HashMap<Character, Boolean>();
    private int numberOfArguments = 0;
    
    public Args(String schema, String[] args){
        this.schema = schema;
        this.args = args;
        valid = parse();
    }
    
    public boolean isValid(){
        return valid;
    }
    
    private boolean parse(){
        if(schema.lenth() == 0 && args.length == 0){
            return true;
        }
        parseSchema();
        parseArgumetns();
        return unexpectedArguments.size() == 0;
    }
    
    private boolean parseSchema(){
        for(String element : schema.split(",")){
            parseSchemaElement(element);
        }
        return true;
    }
    
    private void parseSchemaElement(String element){
        if(element.length() == 1){
            parseBooleanSchemaElement(element);
        }
    }
    
    private void parseBooleanSchemaElement(String element){
        char c = element.charAt(0);
        if(Character.isLetter(c)){
            booleanArgs.put(c, false);
        }
    }
    
    private boolean parseArgumetns(){
        for(String arg : args){
            parseArgumetns(arg);
        }
        return true;
    }
    
    private void parseArgument(String arg){
        if(arg.startWith("-")){
            parseElements(arg);
        }
    }
    
    private void parseElements(String arg){
        for (int i=1; i<arg.length(); i++){
            parseElement(arg.charAt(i));
        }
    }
    
    private void parseElement(char argChar){
        if(isBoolean(argChar)){
            numberOfArguments++;
            setBooleanArg(argChar, true);
        }else{
            unexpectedArguments.add(argChar);
        }
    }
    
    private void setBooleanArg(char argChar, boolean value){
        booleanArgs.put(argChar, value);
    }
    
    private boolean isBoolean(char argChar){
        return booleanArgs.containsKey(argChar);
    }
    
    private int cardinality(){
        return numberOfArguments;
    }
    
    public String usage(){
        if(schema.length() > 0){
            return "-["+schema+"]";
        }else{
            return "";
        }
    }
    
    public String errorMessage(){
        if (unexpectedArguments.size() > 0) {
            return unexpectedArgumentMessage();
        }else{
            return "";
        }
    }
    
    private String unexpectedArgumentMessage(){
        StringBuffer message = new StringBuffer("Argument(s) -");
        for(char c : unexpectedArguments){
            message.append(c);
        }
        message.append(" unexpected.");
        
        return message.toString();
    }
    
    public boolean getBoolean(char arg){
        return booleanArgs.get(arg);
    }
    
}
```

#### 2. Boolean과 String
그러나 위 코드에 String 파라미터 유형 1개를 추가하기 시작하니 코드가 엉망이 되어가는 문제가 발생했다.

**그래서 Integer를 추가하기 전에 코드를 리팩토링하기로 결정했다!**
- 첫째, 인수(=파라미터) 유형에 해당하는 HashMap을 선택하기 위해 스키마 요소의 구문을 분석한다.
- 둘째, 명령행 인수에서 인수 유형을 분석해 진짜 유형으로 변환한다.
- 셋째, getXxx 메서드를 구현해 호출자에게 진짜 유형을 반환한다.

**인수 유형은 다양하지만 모두가 유사한 메서드를 제공하므로 클래스 하나가 적합하다고 판단했고, <u>그리하여 ArgumentMarshaler라는 개념이 탄생**</u>했다.

```java
책 264~269pg 참조
```

#### 3. 점진적으로 개선하다

프로그램을 망치는 가장 좋은 방법 중 하나는 개선이라는 명목 하에 구조를 크게 뒤집는 행위다. 그래서 TDD 기법을 사용하여 개선하는 것이 좋다.


1. isxxxArg 메서드를 별도로 선언할 이유가 없어져 인라인 코드로 만듦.
```java
private boolean isIntArg(ArgumentMarshaler m){
    return m instanceof IntegerArgumentMarshaler;
}

private boolean isStringArg(ArgumentMarshaler m){
        return m instanceof StringArgumentMarshaler;
}
```

즉, 위 코드 대신에 아래 코드처럼 인라인 코드로 대체
```java
private boolean setArgument(char argChar) throws ArgsException{
    ArgumentMarshaler m = marshalers.get(argChar);
    if(m instanceof BooleanArgumentMarshaler){
        setBooleanArg(argChar);
    }else if(m instanceof StringArgumentMarshaler){
        setStringArg(argChar);
    }else if(m instanceof IntegerArgumentMarshaler){
        setIntArg(argChar);
    }else{
        return false;
    }
    
    return true;
}
```

2. 위와 같이 코드를 일부 변경했더라도 항상 테스트를 먼저 해보고, 그 다음 리팩토링을 이어서 해야 한다.


3. String과 Integer도 똑같이 변경

여전히 고칠 부분은 많다. 아까 바꾼 setIntArg, setStringArg, setBooleanArg를 해당 ArgumentMarshaler 파생 클래스로 내리는 것이 좋다.

그러나 이 과정에서 파라미터 2개(args, currentArgument)를 넘겨야 했다. **파라미터는 1개만 넘기는 편이 좋기 때문에** 이를 해결하는 과정을 진행한다!

이는 **args 배열을 list로 변환한 후 Iterator를 set 함수로 전달함으로써 해결**할 수 있고, 아래 코드는 그 결과이다.

```java
public class Args{
    private String[] args; // 삭제할 인스턴스 변수
    private Iterator<String> currentArgument; // 새로 추가한 인스턴스 변수 (1)
    private List<String> argsList; // 새로 추가한 인스턴스 변수 (2)
    
    public Args(String schema, String[] args) throws ParseException{
        this.schema = schema;
        argsList = Arrays.asList(args);
        valid = parse();
    }
    
    private boolean parse() throws ParseException{
        if(schema.length() == 0&& argsList.size() == 0){
            return true;
        }
        parseSchema();
        try{
            parseArguments();
        } catch (ArgsException e){
            
        }
        return valid;
    }
    // ...
    
    private boolean parseArguments() throws ArgsException{
        for(currentArgument = argsList.iterator(); currentArgument.hasNext();){
            String arg = currentArgument.next();
            parseArgument(arg);
        }
        return true;
    }
}
```
사실 setBooleanArg는 iterator가 필요 없다. 그런데도 iterator를 파라미터로 넘긴 이유는
- (1) setIntArg, setStringArg에서 필요하기 때문이다.
- (2) 또한 3개 함수 모두 ArgumentMarshaler의 추상 메서드로 호출하기 위해서다.

```java
private abstract class ArgumentMarshaler{
    public abstract void set(Iterator<String> currentArgument) throws ArgsExcetion; // 새로 추가한 추상 메서드
    // ...
}
```

### 결론

단순히 돌아가는 코드에 만족하는 프로그래머는 전문가 정신이 부족하다.
- **나쁜 코드**보다 더 오랫동안 더 심각하게 개발 프로젝트에 악영향을 미치는 요인도 없다.
- **나쁜 일정**은 다시 짜면 된다.
- **나쁜 요구사항**은 다시 정의하면 된다.
- **나쁜 팀 역학**은 복구하면 된다.

나쁜 코드를 깨끗한 코드로 개선할 수는 있으나, 엄청난 비용이 든다. <u>반면 처음부터 코드를 깨끗하게 유지하기란 상대적으로 쉬우니</u> **항상 코드를 최대한 깔끔하고 단순하게 정리하는 습관**을 기르자.
