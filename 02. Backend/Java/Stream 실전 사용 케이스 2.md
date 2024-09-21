# 상위 판매채널 조회 로직

## 목차
1. [개요](#개요)
2. [코드](#코드)
3. [구현 과정에서 겪은 문제점과 해결 방법](#구현-과정에서-겪은-문제점과-해결-방법)


## 개요
### AS-IS
`while`문으로 판매채널 값을 찾을 때까지`(= null이 아닐 때까지)` 조회하기 때문에 `DB N번 통신`하는 구조였습니다.
레벨은 0~4로 구성되어 있었기에 최악의 경우 판매채널 1개를 찾기 위해 DB를 4번씩 조회해야 했습니다. 

### TO-BE
1. `START WITH ... CONNECT BY`절을 이용하여 상위 조직과 판매채널 정보를 `Tree` 구조로 조회하였습니다.
2. `Stream`을 이용하여 판매채널을 찾고, 없을 경우 `BizException`을 띄워 사용자로 하여금 판매채널 등록을 유도합니다.
3. `공통 서비스`로 구현하고, `javadoc`을 상세히 적어 클라이언트 코드 사용성을 높였습니다.

---

## 코드
### 1. 계층형 쿼리를 이용하여 상위 판매채널 전체 조회
```sql
SELECT 판매채널
FROM 조직 A
START WITH A.조직코드 = (SELECT B.조직코드
                       FROM 조직원 B
                       WHERE B.조직원코드 = #{변수})
CONNECT BY PRIOR A.상위조직코드 = A.조직코드
```

### 2. Stream을 이용하여 판매채널 필터링 및 예외처리

```java
@Slf4j
@Service
public class Service {
  /**
   * @apiNote 판매채널을 조회합니다. (시스템관리자의 경우 본사와 동일한 {@code "10"}을 반환합니다.)
   * @return @notnull 판매채널
   */
  @Override
  public String getSaleChannel(ExampleVo exampleVo) {
    Objects.requireNonNull(exampleVo.getEmployeeNo(), "사번이 존재하지 않습니다.");
    String 사원유형 = 사원유형 조회;
    if (시스템관리자 == 사원유형) {
      return "10";
    }

    List<OrganizationVo> list = 판매채널 List 조회;
    return find(list);
  }

  private static String find(List<OrganizationVo> list) {
    return list.stream()
            .filter(vo -> vo.getSaleChannel() != null)
            .peek(vo -> log.debug("디버깅 메시지")
            .findFirst()
            .map(OrganizationVo::getSaleChannel)
            .orElseThrow(() -> "예외 처리");
  }
}
```

