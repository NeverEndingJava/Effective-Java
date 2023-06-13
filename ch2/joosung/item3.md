
# private 생성자나 열거 타입으로 싱글턴임을 보증하라

### 싱글턴이란?

- 싱글턴은 인스턴스를 오직 하나만 생성하는 클래스를 의미한다. 

- 싱글턴 패턴은 시스템 전체에서 공유하는 자원 또는 설정 등을 관리할 때 주로 사용된다.

#### 장점
- 한번만 객체를 생성하여 재사용이 가능해져 메모리 낭비를 방지할 수 있다. 

- 전역성을 갖기 때문에 다른 객체와 공유가 용이히다.

#### 단점
- 테스트하기 어려워질 수 있다.

## 싱글턴 구현 방법

### 1. public static final 필드 방식의 싱글턴
```java
public class Singleton {
    public static final Singleton INSTANCE = new Singleton();
    private Singleton() { ... }
}
```
클래스가 로딩될 때 Singleton 인스턴스가 생성되며, JVM이 보장하는 클래스 초기화 단계에서의 스레드 안정성을 이용하므로 추가적인 동기화 처리 없이도 싱글턴을 보장한다.

public, protected 생성자가 없으므로 외부에서 생성할 수 없다.

예외적으로 권한이 있는 클라이언트는 리플렉션 API인 `AccessibleObject.setAccessible`을 사용해 private 생성자를 호출할 수 있다. 

이러한 공격을 방지하고자 한다면, 생성자에서 두 번 객체를 생성하려고 할 때 예외를 던지게 하면 된다.

#### 장점
- 해당 클래스가 싱글턴임이 API에 명백히 드러난다.
- 간결하다.

### 2. 정적 팩터리 방식의 싱글턴
```java
public class Singleton {
	private static final Singleton INSTANCE = new Singleton();

	private Singleton() { }

	public static Singleton getInstance() {
		return INSTANCE;
	}
}
```
`Singleton.getInstance`는 항상 같은 객체의 참조를 반환하므로 제 2의 인스턴스를 만들지 않는다.

첫번째 방법과 마찬가지로 리플랙션 API에 의해 예외적인 상황이 발생할 수는 있다.

#### 장점
- API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있다.
    - 호출하는 스레드별로 다른 인스턴스를 넘기게 하는 등 ...
- 정적 패터리를 제네릭 싱글턴 팩터리로 만들 수 있다.
- 정적 팩터리의 메서드 참조를 공급자(Supplier)로 사용할 수 있다는 점 -> `Singleton::getInstance` -> `Supplier<Singleton>`

#### 유의점
1번과 2번 방식으로 만들어진 싱글턴 클래스를 직렬화하려면 단순히 `Serializable`을 구현한다고 선언하는 것만으로는 부족하다.

모든 인스턴스 필드에 `transient`를 선언하고, `readResolve` 메서드를 제공해야만 역직렬화시에 새로운 인스턴스가 만들어지는 것을 방지할 수 있다. 

만약 이렇게 하지 않으면 초기화해둔 인스턴스가 아닌 다른 인스턴스가 반환된다.

```java
private Object readResolve() {
    return INSTANCE;
}
```

### 3. Enum 타입 방식의 싱글턴
```java
public enum Printer {
    INSTANCE;

    public void print() { ... }
}
```
앞의 예제들에 비해 더 간결하고, 추가 노력 없이 직렬화할 수 있으며, 아주 복잡한 직렬화 상황이나 리플렉션 공격에서도 제2의 인스턴스가 생기는 일을 막아준다.

**대부분 상황에서는 원소가 하나뿐인 열거 타입이 싱글턴을 만드는 가장 좋은 방법이다.** 

단, 만들려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없다.

