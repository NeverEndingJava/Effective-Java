## equals를 재정의하려거든 hashCode도 재정의하라

---

hashCode : 객체의 주소값을 변환하여 생성한 객체 고유한 정수값.

equals를 재정의했다면 hashCode도 반드시 재정의해야한다. 재정의 하지 않으면, hashCode의 일반규약을 어기고 HashMap, HashSet 등 hashCode를 기반으로 하는 Collection들에서 버그가 발생한다.

Object명세에서 hashCode

- equals 비교에 사용되는 정보가 변경되지 않았다면, hashCode의 값은 항상 같은 값을 반환
- equals가 두 객체를 같다고 판단했으면 두 객체의 hashCode는 같은 값을 반환
- equals가 두 객체를 다르다고 판단했더라도 두 객체의 hashCode가 다를 필요는 없다.

모든 hashCode가 같은 값을 리턴한다면??

```java
@Override
public int hashCode() {
	return 99;
}
```

- 해당 타입을 가지는 모든 인스턴스가 같은 인스턴스로 인식
- 모든 객체가 해시테이블 버킷 하나에 담겨 헤시테이블의 성능이 감소 (시간복잡도: O(n))

### 규약에 맞는 hashCode 메서드 작성

좋은 해시함수는 서로 다른 인스턴스끼리 다른 해시코드를 반환해야 함.
주어진 인스턴스들을 32비트 정수 범위에 균일하게 분배하는게 가장 이상적임.

1. int 변수 result를 선언후 값 c로 초기화한다.
   - c는 해당 객체의 첫번째 핵심 필드를 2단계 a로 계산한 해시코드
   - equals에서 사용되지 않는 필드는 hashCode에서도 제외해야 한다.
   - `int result = c`
2. 해당 객체의 나머지 핵심필드에 대해 작업 수행
   - a. 필드의 해시코드 c를 계산
     - 기본필드라면 Type.hashCode(필드)를 수행
       - 참조필드라면 재귀적으로 호출. 계산이 복잡해진다면 표준형을 만들어 hashCode 호출
         ex) getAddress().getDong().getApartName()
       - 배열필드라면 핵심원소에 대해 위의 두 가지 방식으로 해시코드 계산하여 비교. 모든 원소가 핵심이면 Arrays.hashCode이용
   - b. a로 계산한 해시코드 c로 result갱신
   - `result = 31 * result + c` // 31곱은 관례라고 함
3. result 반환

### Object hash

위와 같은 구현을 이미 Object#hash가 구현하고 있지만, 내부적으로 Arrays.hashCode를 통해 hashCode를 계산함. 입력인수를 담기위한 배열을 만들고, 기본타입에 대한 박싱과 언박싱이 일어나 성능에 민감한 경우 다른 방안(lazy loading) 생각

```java
@Override
public int hashCode() {
    int result = hashCode;
    if (result == 0) {
        result = Integer.hashCode(number);
        result = 31 * result + Integer.hashCode(number2);
        hashCode = result;
    }
    return result;
}
```
