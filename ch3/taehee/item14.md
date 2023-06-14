# Comparable 을 구현할지 고려하라

Comparable 인터페이스의 compareTo 메서드는 Object의 equals 와 같이 동치성을 비교할 수 있습니다.
또한, 순서를 비교 할 수 있으며 제너릭 타입입니다.
```java
public interface Comparable<T> {
    public int compareTo(T o);
}
```
Comparable 인터페이스를 구현했다는 것은 클래스의 인스턴스틀에는 자연적인 순서가 있음을 뜻합니다.
이 말은 곧, Comparable 을 구현한 객체들의 배열은 `Arrays.sort(a)` 로 손쉽게 정렬 할 수 있습니다.
> Comparable 을 구현하면 제너릭 알고리즘과 컬렉션의 다양한 메서드들을 손쉽게 사용할 수 있습니다.

### compareTo 메서드 규약

equals 규약과 매우 비슷합니다.
1. 일관성
   a.compareTo(b)와 b.compareTo(a) 의 결과는 서로 반대일 수 없습니다.
2. 대칭성
   a.compareTo(b)와 b.compareTo(a)의 결과는 같아야 합니다. 즉,두 객체를 순서를 바꿔서 비교해도 결과는 동일해야 합니다.
3. 추이성
   a.compareTo(b)가 0보다 크고, b.compareTo(c)도 0보다 크다면, a.compareTo(c)는 반드시 0보다 커야 합니다.

- 위 내용은 equals 규약과 똑같습니다. 따라서 주의 해야 할 사항도 똑같습니다.
> 새로운 값을 추가 하면서 추이성을 유지 할 순 없으며, 우회 하는 방법 또한 컴포지션으로 똑같습니다.

### compareTo 메서드로 수행한 동치성 테스트의 결과는 equals 와 같아야 한다.

compareTo() 메서드와 equals() 메서드의 일관성을 유지해야 합니다. 
이를 지키지 않으면 정렬된 컬렉션에서 예상과 다른 동작이 발생할 수 있습니다. 
예를 들어, BigDecimal 클래스에서 equals()와 compareTo()의 일관성을 유지하지 않으면 동일한 객체로 간주되어야 할 상황에서 서로 다른 객체로 처리될 수 있습니다. 
이로 인해 정렬된 컬렉션의 동작이 예상과 다를 수 있습니다. 
따라서, compareTo()와 equals()의 일관성을 유지하는 것은 객체 비교와 정렬된 컬렉션에서 올바른 동작을 보장하기 위해 중요합니다.