## 3.private 생성자나 열거 타입으로 싱글톤임을 보증하라
---
싱글톤이란? 인스턴스를 오직 하나만 생성할 수 있는 클래스
### 싱글톤을 만드는 방식
1. public static final 필드 방식의 싱글톤
```java
public class Person {
	public static final Person INSTANCE = new Person();
    private Person() {
    	...
    }
}
```
- 권한이 있는 클라이언트는 리플렉션 API인 `AccessibleObject.setAccessible`을 이용해 private 생성자 호출이 가능하지만, 생성자에서 두 번째 객체가 생성되려 할 때 예외를 던지게 할 수 있음
- 간결하지만, 인스턴스가 항상 생성되어 메모리 낭비

2. 정적 팩토리 방식의 싱글톤
```java
public class Person {
	private static final Person INSTANCE = new Person();
    private Person() { ... }
    public static Person getInstance() { return INSTANCE; }
}
```
- API를 바꾸지 않고 싱글톤이 아니게 바꿀 수 있음
- 정적 팩토리를 제네릭 싱글톤 팩토리로 만들 수 있음 (아직 잘 모르겠다...)
- 인스턴스가 항상 생성되어 메모리 낭비

> 1, 2 번으로 만든 싱글톤 클래스를 직렬화 하려면 단순히 Serializable을 구현한다고 선언하는 것으로는 부족하다.
readResolve 메소드를 제공하지 않는다면 직렬화된 인스턴스를 역직렬화 할 때마다 새로운 인스턴스가 만들어진다.
```java
private Object readResolve() {
	return INSTANCE;
} 
```
(아직 잘 모르겠다... 12장에서 배운다!)

3. 열거 타입 방식의 싱글톤
```java
public enum Person {
	INSTANCE;
}
```
- 자바에서 열거 타입은 일종의 클래스. 상수 하나당 인스턴스를 하나씩 만들어 public static final 필드로 공개함. 인스턴스는 런타임에 한 번만 생성된다.
- JVM 메모리 영역에서 메소드 영역에 올라감