## 추상 클래스보다는 인터페이스를 우선하라
---
### 추상 클래스와 인터페이스
공통점
- 메서드 시그니처만 만들고 구현은 구현 클래스에게 맡긴다.
- 인스턴스로 생성할 수 없다.

차이점
- 추상클래스: 단일 상속만 가능, 구현체는 추상클래스의 하위클래스
- 인터페이스: 다중 상속 가능, 인터페이스를 구현했다면 같은 타입으로 취급


### 인터페이스는 믹스인(mixin) 정의에 안성맞춤이다
믹스인: 클래스가 구현할 수 있는 타입, 원래 주된 타입 외에도 특정 `선택적 행위`를 제공한다고 선언하는 효과를 준다.

ex) Comparable은 자신을 구현한 클래스의 인스턴스끼리 순서를 정할 수 있다고 선언하는 믹스인 인터페이스

- 추상클래스를 상속할 경우, 단일 상속만 가능하기에 믹스인으로 사용될 수 없다.

### 계층구조가 없는 타입 프레임워크가 가능하다.

```java

public interface A {
	void a();
}

public interface B {
	void b();
}

public interface C {
	void c();
}

public interface Alp extends A,B,C {
	void alp();
}

```
Alp 라는 인터페이스는 다른 인터페이스를 상속할 수 있다. 다만, 부모 자식 계층이 존재하지 않는다.
Alp 인터페이스를 구현한 클래스라면 모두 Alp 타입으로 취급할 수 있다.

반면 추상클래스로 위와같이 구현한다면 다중 상속이 안되기에 작성해야 할 클래스가 너무 많아진다. (속성이 n개면 2^n 개 클래스를 나눠야 한다고) -> 조합 폭발

### Wrapper 클래스와 함께 사용하면 인터페이스는 기능을 향상 시키는 안전하고 강력한 수단이 된다.

타입을 추상클래스로 정의해두면 그 타입에 기능을 추가하는 방법은 상속뿐. 이는 Wrapper 클래스보다 활용도가 떨어진다. (상속보다는 컴포지션을 사용하자)

### default 메서드
인터페이스 메서드 중 구현 방법이 명확한 메서드가 있다면 default 메서드를 활용할 수 있다.
- Object에서 제공하는 메서드는 default 메서드로 제공하면 안된다. (equals(), hashCode())
- default 메서드를 제공할 때는 상속하려는 사람을 위한 설명을 `@implSpec` 태그를 붙여 문서화해야 한다.
- 본인이 만들지 않은 인터페이스에는 default 메서드를 추가할 수 없다.

### 템플릿 메서드 패턴
인터페이스와 추상 골격 구현 클래스를 함께 제공하여 인터페이스와 추상클래스의 장점을 모두 취할 수 있다. (추상클래스 + 인터페이스 조합)
관례상 이름이 `Interface`라면 골격 구현 클래스 이름은 `AbstractInterface`

```java
List 인터페이스 get 메서드
E get(int index);


public abstract class AbstractList<E> extends AbstractCollection<E> implements List<E> {

	abstract public E get(int index);
    
    public E set(int index, E element) {
    	throw new UnsupportedOperationException();
    }
}
```

인터페이스로는 타입을 쉽게 정의하고 필요하다면 default 메서드 몇개도 함께 제공.
골격 구현 클래스는 나머지 메서드들까지 구현한다.



> 다중 구현용 타입으로는 인터페이스가 적합. 복잡한 인터페이스라면 구현 수고를 덜어주는 골격 구현을 함께 제공하는 방법도 고려하자