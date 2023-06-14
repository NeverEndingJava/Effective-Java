# equals를 재정의 하려거든 hashCode 도 재정의 하라

### hashCode 는 논리적으로 같다(equals)고 판단했다면, 두 객체의 hashCode 는 같아아합니다.
hashCode는 객체의 해시 코드를 반환하는 메서드로, 동일한 객체는 항상 동일한 해시 코드를 반환해야 합니다.

> __equals 메서드는 두 객체의 논리적 동치성을 비교하는 반면, hashCode는 논리적 동치성을 지원하기 위해 필요한 메서드입니다.__

- 따라서 equals 메서드를 재정의한 클래스에서는 hashCode 메서드도 적절하게 재정의해야 합니다.

만약 equals 는 재정의 하였지만, hashCode를 재정의 하지 않았다면, 재정의한 equals 에서는 논리적으로 같다고 판단하여도, hashCode는 다른 코드를 반환하게 됩니다.

```java
Map<PhoneNumber, String> map = new HashMap<>();
map.put(new PhoneNumber(010,1234,5678), new Person("리치"));
```
- 위 코드를 보면 `.get` 메소드를 실행 시 `null` 을 반환하게 되는데 재정의 되지 않은 해시코드로 둘의 객체는 논리적으로 같지만
- 서로 다른 `hashCode`를 반환하여 `Person("리치")` 를 찾지않고, 엉뚱한 해시 버킷을 조회 한 이유때문입니다.
> `HashMap`은 해시코드가 서로 다른 엔트리끼리는 동치성 비교를 시도조차 안하도록 최적화되어있습니다.
- 따라서 `hashCode` 재정의가 필요합니다. 아래 코드 처럼 hasCode 를 재정의 할 수있습니다.
```java
@Override
public int hashCode() {
    return 42;
}
```

- 같은 객체는 모두 42라는 해시코드 값을 반환하게 하여 해결 할 수도 있습니다.
- 하지만 모든 객체가 해시테이블 버킷 하나에 담겨 마치 연결리스트처럼 동작하게 된다. 사용에는 문제가 없어보일수도 있습니다.
- 실제로 성능면으로 굉장히 안좋기 때문에 객체가 늘어남에 따라 해시테이블 조회 속도가 굉장히 느려져 사용이 불가능한 수준이 된다.

### 좋은 hashCode 재정의 방법
```java
@Override
public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNumber);
    return result;
}
```
- 위 코드는 가장 전형적인 재정의된 `hashCode` 입니다.
- `result` 선언한 후 가장 첫번째 핵심필드를 기본 타입의 박싱클래스로 `hashCode` 를 계산합니다.
- 이후 핵심필드들을 기본 타입의 박싱클래스로 `hashCode` 를 계산 후 `31 * result`의 값을 더하여 갱신합니다.
    > 이때 31을 곱하는 이유는 일반적으로 사용되는 소수(prime number) 중 하나로, hashCode 값을 더 잘 분배하기 위해 곱셈 연산에 사용됩니다.
- 모든 핵심필드의 hashCode 계산이 끝났다면 `result` 를 반환합니다.

### 위 예시 코드는 핵심 필드가 기본 타입 인 경우입니다. 따라서 참조필드 일 때와 배열필드 일 경우에 따라 다르게 `result`를 초기화 해줘야 합니다.

1. 참조 필드 일 경우
```java
public int hashCode() {
    int result = (areaCode != null) ? areaCode.hashCode() : 0;
    return result;
}
```
- 해당 필드의 hashCode 값을 재귀적으로 호출해야 합니다.
- null인 경우에는 전통적으로 0을 사용합니다.
2. 배열 필드 일 경우
```java
public int hashCode() {
    int result = (areaCode != null) ? Arrays.hashCode(areaCode) : 0;
}
```
- 배열의 핵심원소 각각을 별도의 필드처럼 `hashCode`를 재귀적으로 호출하여 계산하도록 합니다.
- 위 예시코드처럼 만약 모든 원소가 핵심 원소라면 `Arrays.hascCode` 를 사용하도록 합니다.
- 위와 마찬가지로 null 인 경우 0을 사용하도록 합니다.

위에 예시코드로도 성능면으로 충분히 좋은 `hashCode` 메서드 입니다.
> Object의 정적 메서드로 `hash` 메서드를 제공합니다만, 입력 인수에 따라 배열을 생성하며, 기본타입이 들어있을경우 박싱과 언박싱의 과정도 있기때문에
> 성능면으로 좋지 못합니다.
> ```java
> public int hashCode() {
>   return Objects.hash(lineNum, prefix, areaCode);
> }
> ```

### hashCode 고도화 방법
만약 클래스가 불변이고 해시코드를 계산하는 비용이 크다면, 해시코드를 캐싱하는 방법도 고려 할 수 있습니다.
```java
private int hashCode; // 자동으로 0으로 초기화된다.

@Override
public int hashCode() {
    int result = hashCode;
    if (result == 0) {
        result = Short.hashCode(areaCode);
        result = 31 * result + Short.hashCode(prefix);
        result = 31 * result + Short.hashCode(lineNumber);
        hashCode = result;
    }
    return result;
}
```
- 위 예시 코드는 hashCode 를 캐싱하는 방법과 지연 초기화까지 적용된 코드입니다.
- `result` 는 0으로 자동 초기화를 합니다.
- `if(result == 0)` 조건으로 처음 호출 할 경우에 hashCode 를 계산 하며, -> 지연 초기화
- 이후 `hashCode` 를 호출 할 경우 캐싱되어있는 `result` 를 반환합니다. -> 캐싱

### hashCode 재정의 시 주의 사항
#### 성능을 높인다고 해시코드를 계산할 때 핵심 필드를 생략해서는 안 됩니다.

위 예시코드처럼 계산을 3번 하는게 아닌 "`areaCode` 만 계산하면 되지 않나?" 라고 생각 할 수 있지만 해쉬 테이블의 성능을 굉장히 떨어뜨릴수 있습니다.
> 하나의 필드만 사용하게 된다면 해쉬 테이블이 한곳에만 몰릴 가능성이 있기 떄문입니다.
> 
> __필드를 고르게 사용한다면 해쉬 테이블을 고르게 사용 할 확률이 높아집니다.__
