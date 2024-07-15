# Stream 실전 사용 케이스 1

## 목차
1. [개요](#개요)
2. [코드](#코드)
3. [구현 과정에서 겪은 문제점과 해결 방법](#구현-과정에서-겪은-문제점과-해결-방법)

## 개요
### 배경 및 해결
`front`에서 넘어 온 대상에 대해 재고 검증을 해야 하는 상황이나, 재고를 체크할 수 있는 key 값인 창고코드, 품목코드가 랜덤하게 넘어올 수 있는 상황

정확한 재고 체크를 위해 수행한 데이터 조작
1. Grouping 
   - K : `new Target.Key(창고코드, 품목코드)`
   - V : `summingInt()`로 주문수량 합산
2. 재고수량 조회
   - grouping한 `[창고코드, 품목코드]` 정보로 재고수량 조회
3. 실제재고 계산
   - K : 품목코드
   - V : 재고수량 - 주문수량

---

## 코드

### 재고 체크 로직
```java
public class Service {

	public List<Target> check(List<Target> targetList){
		// 1-1. [창고, 품목]별 주문수량 합산
		Map<Target.Key, Integer> sumMap = targetList.stream()
				.collect(Collectors.groupingBy(
						e -> new Target.Key(e.getWarehouseCode(), e.getItemCode()),	// K : [창고코드, 품목코드]
						Collectors.summingInt(Target::getOrderQuantity)				// V : 주문수량++
				));

		// 1-2. [창고, 품목]별 실제재고 계산
		Map<String, Integer> calculatedMap = sumMap.entrySet().stream()
				.map(e -> new Target.Sum(e.getKey().getWarehouseCode(), e.getKey().getItemCode(), e.getValue()))
				//	.peek()		// 재고수량 조회 로직
				.collect(Collectors.toMap(
						sum -> sum.getItemCode(),									// K : 품목코드
						sum -> sum.getInventoryQuantity() - sum.getOrderQuantity(),	// V : 실제재고 = 재고수량 - 주문수량
						Integer::sum												// 같은 key(품목코드)일 경우 합산 처리
				));

		// 2. 실제재고 부족시 => 재고부족 처리
		for(Target target : targetList){
			String itemCode = target.getItemCode();

			if(calculatedMap.containsKey(itemCode)){
				if(calculatedMap.get(itemCode) < 0){
					// 재고 부족에 따른 로직 처리
				}
			}
		}

		return targetList;
	}

}
```

### Target 클래스
```java
@Getter
@Setter
public class Target {
	private String warehouseCode;
	private String itemCode;
	private int orderQuantity;

	/**
	 * 재고 검증시 수량 합산을 위한 Key 객체
	 */
	@Getter
	@Setter
	@EqualsAndHashCode
	@AllArgsConstructor
	public static class Key{
		private String warehouseCode;
		private String itemCode;
	}

	/**
	 * 재고 검증시 수량 합산 결과 객체
	 */
	@Getter
	@Setter
	public static class Sum{
		private String warehouseCode;
		private String itemCode;
		private int orderQuantity;
		private int inventoryQuantity;

		public Sum(String warehouseCode, String itemCode, int orderQuantity){
			this.warehouseCode = warehouseCode;
			this.itemCode = itemCode;
			this.orderQuantity = orderQuantity;
		}
	}

}
```

---

## 구현 과정에서 겪은 문제점과 해결 방법
### 1. `equals() & hashCode() 재정의`
`HashMap` 컬렉션의 key 값으로 쓰일 `Target.Key` 클래스에 대해서 `equals()`와 `hashCode()` 메서드의 오버라이딩이 반드시 필요했습니다.
각각의 이유에 대해서 자세히 알아보겠습니다.

#### `equals()`
우선 equals 메서드를 재정의해야 객체의 `warehouseCde`, `itemCode` 필드가 모두 같을 때 두 객체를 동등하다고 판단하게 됩니다.
그렇지 않으면 객체의 참조 값으로만 비교를 하기 때문에 Stream 내부에서 `new Target.Key()`로 각각 생성된 객체는 필드 값이 같아도 동등하지 않습니다.

#### `hashCode()`
`hashCode` 메서드는 객체를 해시 기반의 컬렉션(HashMap, HashSet)에 저장할 때 사용되는 해시 코드를 제공합니다.
`hashCode` 메서드는 두 필드(warehouseCode, itemCode)를 기반으로 해시 코드를 생성하며, 두 값이 같다면 동일한 해시 코드 값을 갖게 됩니다.
즉, 제 코드처럼 각 객체를 `HashMap`과 같은 컬렉션에 담을 때는 반드시 `hashCode()`까지 재정의해야 동일한 객체로 인식될 수 있습니다.

#### hash 값을 사용하는 Collection(HashMap, HashSet, HashTable)이 객체가 논리적으로 같은지 비교하는 과정
따라서 `equals()`와 `hashCode()`를 함께 재정의해야 원하는 결과를 얻을 수 있습니다.
![image](https://github.com/user-attachments/assets/fa18785a-165d-47f9-b453-039cc7999dee)


#### 레퍼런스
- [자바의 Object 클래스와 equals, hashCode 메서드의 중요성](https://intro.f-lab.kr/insight/java-object-class-equals-hashcode?gad_source=1&gclid=CjwKCAjw7s20BhBFEiwABVIMraXHsjn2q7RL16EHxygez2CwZSD0Ywxla-ywR-mTUU3jb_c8M6IOCxoC0ywQAvD_BwE)
- [Tecoble - equals와 hashCode는 왜 같이 재정의해야 할까?](https://tecoble.techcourse.co.kr/post/2020-07-29-equals-and-hashCode/)
- [인파 - 자바 equals / hashCode 오버라이딩 - 완벽 이해하기](https://inpa.tistory.com/entry/JAVA-%E2%98%95-equals-hashCode-%EB%A9%94%EC%84%9C%EB%93%9C-%EA%B0%9C%EB%85%90-%ED%99%9C%EC%9A%A9-%ED%8C%8C%ED%97%A4%EC%B9%98%EA%B8%B0#equals%EB%A7%8C_%EC%9E%AC%EC%A0%95%EC%9D%98%ED%95%A0_%EA%B2%BD%EC%9A%B0)


### 2. `Collectors.toMap() 사용시 key 값 중복 문제`
테스트를 하는 과정에서 겪은 또 다른 문제점은 바로 `key의 중복` 문제였습니다. 오류 로그는 대략 아래와 같습니다.

`Duplicate key 6 (attempted merging values banana and carrot) java.lang.IllegalStateException: Duplicate key 6 (attempted merging values banana and carrot)`

#### `mergeFunction`
즉, 동일한 키로 인해 충돌(collision)이 발생했을 때, 어떠한 value를 취할 것인지 결정하는 `mergeFunction`을 정의하여 해결했습니다.
저 같은 경우엔 수량을 더하면 됐기 때문에 `Integer::sum`을 이용했습니다.

```java
.collect(Collectors.toMap(
    sum -> sum.getItemCode(),								
    sum -> sum.getInventoryQuantity() - sum.getOrderQuantity(),	
    Integer::sum  // 새로 정의해준 mergeFunction 부분
));
```

#### 레퍼런스
- [Collectors.toMap() 의 여러 가지 사용법](https://ttl-blog.tistory.com/1232)
- [밸덩 - Java 8 Collectors toMap](https://www.baeldung.com/java-collectors-tomap)
- [밸덩 - Handle Duplicate Keys When Producing Map Using Java Stream](https://www.baeldung.com/java-duplicate-keys-when-producing-map-using-stream)


### 3. `inner class`를 `static`으로 선언
`Target` 클래스의 내부 클래스인 `Key`와 `Sum`에 대해선 `static`으로 선언하였습니다.
이를 통해 `메모리 누수`가 일어나지 않도록 했습니다.

#### 메모리 누수
`non-static` 내부 클래스는 `static`과 달리 기본적으로 외부 클래스의 참조 값을 갖게 됩니다.
즉, 내부 클래스가 참조를 통해 외부 클래스와 연결되어 있기 때문에 외부 클래스가 필요 없어진 상황에 GC가 제거를 하려 해도 제거가 되지 않습니다.
이는 곧 메모리 누수로 프로그램이 터지게 될 수 있다고 합니다.

#### 레퍼런스
- [인파 - 내부 클래스는 static 으로 선언 안하면 큰일 난다](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9E%90%EB%B0%94%EC%9D%98-%EB%82%B4%EB%B6%80-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-static-%EC%9C%BC%EB%A1%9C-%EC%84%A0%EC%96%B8%ED%95%98%EC%9E%90#inner_%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94_%EC%99%B8%EB%B6%80_%EC%B0%B8%EC%A1%B0%EB%A5%BC_%ED%95%9C%EB%8B%A4)
