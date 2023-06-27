## 인터페이스는 타입을 정의하는 용도로만 사용하라
---
클래스가 인스턴스를 구현한다는 것은 자신의 인스턴스로 무엇을 할 수 있는지를 클라이언트에 이야기 해주는 것, 인터페이스는 오직 이 용도로만 사용해야 한다.

인터페이스를 잘못 사용한 예시 중에 상수 인터페이스가 있는데 상수 필드만 있는 인터페이스다.
```java
public interface PhysicalConstants {
	static final double AVOGARDROS_NUMBER = 6.022_140_857e23;
    static final double BOLTZMANN_CONSTANT = 1.380_648_52e-23;
    static final double ELECTRON_MASS = 9.109_383_56e-3;
}
```

클래스 내부에서 사용하는 상수는 내부 구현에 해당! 상수를 공개할 목적이라면, 특정 클래스나 인터페이스 자체에 추가해주면 된다. 