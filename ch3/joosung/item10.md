# equals는 일반 규약을 지켜 재정의하라

- equals는 꼭 필요한 경우에만 재정의 하자.
- equals를 다 구현했다면 대칭적인지? 추이성이 있는지? 일관적인지? 확인하자.

## equals을 재정의 하지 않아도 되는 경우

### 각 인스턴스가 본질적으로 고유할 경우

- 값을 표현하는 것이 아니라 동작하는 개체를 표현하는 클래스일 경우를 말한다.
- 예를들어 Thread

### 인스턴스의 '논리적 동치성'을 검사할 일이 없을 경우

### 논리적 동치성이란?

- 두 논리적 명제가 항상 같은 진리 값을 가질 때, 즉 두 명제가 모든 가능한 경우에 대해 동일한 결과를 내놓을 때 사용되는 용어입니다. 

- 즉, 두 명제 A와 B가 있을 때, 모든 가능한 상황에서 A가 참이면 B도 참이고, A가 거짓이면 B도 거짓이라면, 이 두 명제는 논리적으로 동치라고 할 수 있습니다.

#### 동치 관계

> 집합을 서로 같은 원소들로 이루어진 부분집합으로 나누는 연산


### 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 들어맞는 경우

- 예를들어 List, Set, Map 인터페이스는 각각 AbstractList, AbstractSet, AbstractMap을 상속한다.

### 클래스가 private이거나 package-private이고 equals 메서드를 호출할 일이 없을 경우

- equlas 자체를 호출하는 걸 막고 싶다면 아래와 같이 하는것이 좋다

```java
@Override
public boolean equals (Object object) {
	throw new AssertionError();
}
```

### 인스턴스 통제 클래스

- 인스턴스가 2개 이상 만들어지지 않기 때문에 논리적 동치성과 객체 식별성이 같은 의미가 되기 때문이다.
- Enum, 싱글톤

## 그렇다면 언제 equals 메서드를 재정의해야 하냐?

- 객체 식별성이 아니라 논리적 동치성을 확인해야 하는데, 상위 클래스의 equals가 논리적 동치성을 비교하도록 재정의되지 않았을 때다.
- 이러한 점을 이용해 Map 키와 Set 원소로 사용할 수 있게 된다.

## equals 메서드 일반 규약

- equals 메서드는 동치 관계를 구현하며, 다음을 만족한다.
- 여기서 모든 참조값은 null이 아닐 때이다.

### 반사성(reflexivity)

```java
x.equlas(x) == true
```

- 어떤 객체 x에 대해, x.equals(x)는 항상 true여야 한다.
- 단순히 말하면 객체는 자기 자신과 같아야 한다는 뜻이다.
- 이걸 지키는지를 확인할 수 있는 방법은 인스턴스가 들어있는 컬렉션에 contains 메서드를 호출해 true가 나오면 된다.

### 대칭성(symmetry)

```java
x.equals(y) == y.equals(x)
```

- 두 객체 x와 y에 대해, x.equals(y)가 true이면 y.equals(x)도 true여야 하며, x.equals(y)가 false이면 y.equals(x)도 false여야 한다.
- 즉, 서로에 대한 동치 여부에 똑같이 답해야 한다는 뜻입니다.
 
### 추이성(transitivity)

```java
x.equals(y) == true
y.equals(z) == true
x.equals(z) == true
```

- 세 객체 x, y, z에 대해, x.equals(y)가 true이고 y.equals(z)가 true이면, x.equals(z)도 true여야 한다. 
- 즉, 첫 번째 객체가 두 번째 객체와 동등하고, 두 번째 객체가 세 번째 객체와 동등하면, 첫 번째 객체와 세 번째 객체도 동등해야 한다.


### 일관성(consistency)

```java
x.equals(y) == true;
x.equals(y) == true;
x.equals(y) == true;
```

- 두 객체 x와 y에 대해, 객체들이 수정되지 않는 한, 여러 번 x.equals(y)를 호출하더라도 항상 동일한 값을 반환해야 한다. 
- 즉, 두 객체의 동등성 비교 결과는 시간이 지나도 변하지 않아야 한다.
- 가장 좋은 방법은 바로 불변이다.

### non-null

```java
x.equals(null) == false;
```

- 어떤 객체 x에 대해, x.equals(null)은 항상 false여야 한다.
- 이 부분은 쉽게 지킬수 있는데, equals()를 재정의 시 타입 검사를 하게 된다면 지켜지게 된다. 
- 이걸 묵시적 null 검사라고 한다.


## equals 메서드 구현 방법 단계별 정리

1. == 연산자를 사용해 입력이 자기 자신의 참조인지 확인한다.
    - 단순한 성능 최적화용으로 비교 작업이 복잡한 상황일 때 값어치를 한다.
   
2. instanceof 연산자로 입력이 올바른 타입인지 확인한다.
    - 위에서 설명했듯이 묵시적 null 체크를 할 수 있다.
    - 또한 Collection interface처럼 특정 인터페이스의 타입 체크를 할 수도 있다.
   
3. 입력을 올바른 타입으로 형변환한다.
    - instanceof 검사를 했기 때문에 100% 성공한다.
   
4. 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사한다.

### 완성 코드

```java
@Override
public boolean equals(Object o) {
    if (this == o)
        return true;
    if (o == null || getClass() != o.getClass())
        return false;
    Member member = (Member)o;
    return email.equals(member.email) && name.equals(member.name) && age.equals(member.age);
}
```

## 주의 사항

- 기본 타입은 `==` 으로 비교하고 그 중 double, float는 Double.compare(), Float.compare()을 이용해 검사해야 한다. 이유는 부동소수점을 다뤄야 하기 때문이다.

- 배열의 모든 원소가 핵심 필드이면 Arrays.equals 메서드들 중 하나를 사용하자.

- null이 정상 값으로 취급할 때는 OBjects.equals()를 이용해 NPE를 방지하자.

- 최상의 성능을 바란다면 다를 가능성이 더 크거나 비교하는 비용이 싼 필드를 먼저 비교하자.

- 동기화용 락 필드 같이 객체의 논리적 상태와 관련 없는 필드는 비굫사면 안된다.

- equals를 재정의할 땐 hashCode도 반드시 재정의 하자

- Object 타입을 매개변수로 받는 equals 메서드는 선언하지 말자.

### 참고하면 좋은 개념
- [동일성 vs 동등성](https://steady-coding.tistory.com/534) 