# equals를 재정의하려거든 hashCode도 재정의하라

- equals를 재정의한 클래스 모두에서 hashCode도 같이 재정의 하자.
- IDE에서 자동으로 생성해주는 기능이을 활용하자.

## hashCode

- Java의 모든 객체는 `hashCode` 메서드를 가진다. 
- Java에서는 HashMap, HashSet 등 hash를 활용한 Collection들을 자주 볼 수 있는데 모두 `hashCode`를 기반으로 하고 있다.

`Object#hashCode`구현은 다음과 같다.

```java
public native int hashCode();
```

 - 즉, native call을 통해 해당 객체의 메모리 해쉬 주소를 가져온다. 
 - 이 값은 `System의 identityHashCode`와 동일한 값을 가진다.

## hashCode의 3가지 규약

**equals를 재정의 했다면 hashCode도 반드시 재정의해야한다.** 

- 그렇지 않으면 hashCode의 일반 규약을 어기게 된다. 
- 이 경우 `HashMap`, `HashSet`과 같이 hashCode를 기반으로 하는 클래스의 원소로 사용하는 경우 예상치 못한 버그를 발견할 수 있다.

`Object의 hashCode`는 다음과 같은 규약을 가지고 있다.

1. equals가 변경되지 않았다면 애플리케이션이 실행되는 동안 그 객체의 hashCode는 항상 같은 값을 반환해야한다. 단, 애플리케이션이 재실행하는 경우 값이 달라질 수는 있다.

2. equals가 두 객체를 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야한다.

3. equals가 두 객체를 다르다고 판단하더라도 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없다. 단, 다른 객체에 대해서는 다른 값을 반환해야 해시 테이블의 성능이 좋아진다.

#### 위 규약 중 두번째 규약을 주의깊게 봐야한다. 

equals를 재정의하여 논리적으로 같다고 정의했을때 만약 hashCode를 기본 구현으로 사용한다면 서로 다른 값을 반환한다. 

즉, equals 재정의로 2번 규약을 어기게되는 것이다.

특히 hashCode는 hash를 기반으로 하는 컬랙션인 HashMap과 HashSet 등에 매우 중요하다.

### 만약 밑과 같이 모든 인스턴스에 같은 `hashCode`를 가진다면 어떻게 될까?

```java
@Override
public int hashCode() {
    return 1;
}
```

- 물론 Java에서는 hashCode가 같았을때 Exception을 던지지는 않기 때문에 Java 컴파일러에게는 문제가 없는 코드이다. 

- 다만, 모든 객체에게 똑같은 hashCode만을 전달하기 때문에 모든 객체가 해시테이블의 버킷 하나에 담긴다. 때문에 해시테이블의 성능이 매우 떨어지게 된다.

- 이렇게 차이가 나는 이유는 해시 충돌이 유무 때문인데 hashCode가 같다면 모든 인스턴스에서 해시충돌이 일어날 것이다. 

- 이때 HashMap에서 해시 충돌을 피하기 위한 구현인 `Separate Chaining`에 따라 하나의 버킷에 모든 데이터가 삽입되기 때문이다.

- 좋은 해시 함수라면 서로 다른 인스턴스끼리 다른 해시코드를 반환해야한다. 

- 가장 이상적인 해시 함수는 주어진 인스턴스들을 32비트 정수 범위에 균일하게 분배해야한다.
