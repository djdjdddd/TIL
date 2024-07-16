# Stream 실전 사용 케이스 2

## 코드
### 서비스 로직
```java
public class Service {

	private Promotion.Info findPromotionInfo(List<Promotion> promotionList){
		Promotion.Info info = new Promotion.Info();

		promotionList.stream()
				.map(p -> {
					if(Type.PAID.code == p.getType()){
						info.ofPaid(p);
					}else if(Type.PAID.code == p.getType()){
						info.ofFree(p);
					}
					return info;
				})
				.forEach(o -> {});    // 애매한 부분

		return info;
	}

	/** 판촉 유형 */
	@Getter
	@AllArgsConstructor
	private enum Type {
		PAID("10"),
		FREE("20");
		private String code;
	}

}
```

### Promotion 클래스
```java
@Getter
public class Promotion {
	private String type;					  // 판촉 유형
	private Long limitAmount;				// 한도 금액
	private Long adjustmentAmount;	// 조정 금액
	private Long usedAmount;				// 사용 금액

	@Getter
	public static class Info{
		private Long paidLimitAmount;		  // 유상 한도 금액
		private Long paidAvailableAmount;	// 유상 주문가능 금액
		private Long freeLimitAmount;		  // 무상 한도 금액
		private Long freeAvailableAmount;	// 무상 주문가능 금액

		/** 유상 판촉 정보 세팅 */
		public void ofPaid(Promotion p){
			this.paidLimitAmount = p.getLimitAmount();
			this.paidAvailableAmount = p.getLimitAmount() + p.getAdjustmentAmount() - p.getUsedAmount();
		}

		/** 무상 판촉 정보 세팅 */
		public void ofFree(Promotion p){
			this.freeLimitAmount = p.getLimitAmount();
			this.freeAvailableAmount = p.getLimitAmount() + p.getAdjustmentAmount() - p.getUsedAmount();
		}
	}

}
```
