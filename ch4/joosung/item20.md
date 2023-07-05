# 추상 클래스 보다는 인터페이스를 우선하라

`요약`

- 일반적으로 다중 구현을 할 때에는 인터페이스가 가장 적합하다.

- 만약 복잡한 경우일 때에는 골격 구현을 고려해보자!

- 단, 골격 구현은 가능한 인터페이스의 default 메서드 제공해야하며, 그 인터페이스를 구현한 모든 곳에서 사용하는 것이 좋다.

## 추상 클래스와 인터페이스

## 공통점

- 인스턴스로 생성이 불가능하다.

- 선언부만 있는 추상 메서드를 갖는다.
    - 인스턴스 메서드를 구현 형태로 제공할 수 있다.

## 차이점

### 1. 목적

- 추상 클래스
    - 추상 클래스를 상속받아 기능을 이용하고 확장시켜야한다.
  
- 인터페이스
    - 구현을 강제해서 구현 객체가 같은 동작을 하도록 보장한다.

### 2. 상속

- 추상 클래스
    - 단일 상속만 지원하기 때문에, **다중 클래스 상속이 불가능**하다.

- 인터페이스
    - 구현해야하는 메소드만 구현하면 어떤 클래스를 상속하던 간에 같은 타입으로 취급한다.
    - **여러 인터페이스를 상속이 가능**하다.

### 3. 기존 클래스의 확장

- 추상 클래스
    - 기존 클래스에 끼워넣기 어렵다.

- 인터페이스
    - 손쉽게 인터페이스를 구현해 넣을 수 있다.


### 4. 믹스인 정의

특정 클래스의 주 기능에 **추가적인 기능을 혼합(mixed-in)** 하는 것이다.

- 추상클래스

    - 단일 상속만 지원하기 때문에 믹스인이 들어갈만한 자리가 없다.
  
- 인터페이스
    - 손쉽게 구현이 가능하다.
        - ex. Comparable, Clonable, Serializable

### 5. 계층구조

- 추상클래스
    - 복잡한 계층구조일 경우에 많은 조합이 필요하고, **고도 비만 계층**으로 이어질 수 있다.

    ```java
    public abstract class Student {
        // ...
    }

    public abstract class Intern {
        // ...
    }

    // 단일 상속만 가능하기 때문에 인턴인 동시에 학생인 추상 클래스가 필요하다.
    public abstract class StudentIntern {
        // ...
    }

    public class Amy extends SutdentIntern {
    	// ...
    }
    ```

- 인터페이스
    - 계층구조가 없는 타입 프레임워크를 만들 수 있다.

    
### 6. 기능 추가

- 추상 클래스
    - 기능을 추가하는 방법이 상속밖에 없다. → 활용도가 떨어지고 깨지기도 쉽다 (래퍼 클래스보다)
  
- 인터페이스
    - 래퍼 클래스와 함께 사용하면 상속보다 안전하고 강력하게 기능을 향상시킬 수 있다.


### 주의사항

1. @impleSpec을 붙여 문서화하면 좋다.

2. Object 메서드 (ex. equals, hashCode)를 디폴트 메서드로 제공하면 안된다.

3. 인터페이스는 인스턴스 필드를 가질 수 없고, public이 아닌 정적 멤버도 가질 수 없다.
    - Java 9 이후부터는 private static 메서드도 구현이 가능하게 변경되었다.

## 인터페이스와 추상골격 구현 클래스

### 추상 클래스는 언제 사용하는게 좋을까?

추상 클래스와 인터페이스의 차이는 단일 상속 / 다중 구현 뿐일까?

1. 추상 클래스는 인터페이스와 달리 프로퍼티를 정의할 수 있다. 참고로 인터페이스도 프로퍼티를 정의할 수 있지만 기본적으로 `public static` 형태로 정의된다. `상수취급`

   즉, 추상 클래스의 구현 클래스는 추상 클래스가 가진 프로퍼티에 접근할 수 있다.

2. `protected` 추상 메서드를 정의할 수 있다.

   인터페이스에서는 `protected` 접근자를 사용할 수 없다.

위 두 특징으로 가장 잘 활용할 수 있는 방법이 바로 템플릿 메서드 패턴이다.

### 템플릿 메서드 패턴

- 인터페이스 + 골격 구현 클래스

- 추상 클래스처럼 구현을 도와주는 동시에, 추상클래스로 타입을 정의할 때 따라오는 심각한 제약에서 자유롭다.

#### 골격 구현 작성 방법

1. 인터페이스를 보며 다른 메서드들의 구현에 사용되는 기반 메서드 선정

2. 기반 메서드들을 사용해 구현할 수 있는 메서드들을 디폴트 메서드로 제공

3. 기반 메서드나 디폴트 메서드로 만들지 못한 메서드는 해당 인터페이스를 구현하는 골격 구현 클래스에서 작성

4. 설계, 문서화 필수!

#### 예시

- 계산기를 구현한다고 가정한다. 

- 이때 계산기는 덧셈, 나눗셈 등 여러 연산을 다루는 구현체가 있을 수 있다고 가정한다.

- 또한 0 ~ 100까지의 숫자만 연산에 사용하는 것이 요구사항이라고 가정한다.

```java
public interface Calculator {
    int calculate(int x, int y);
}

public abstract class AbstractCalculator implements Calculator {
    private final CalculateValidator calculateValidator;

    public AbstractCalculator(CalculateValidator calculateValidator) {
        this.calculateValidator = calculateValidator;
    }

    @Override
    public int calculate(int x, int y) {
        calculateValidator.validate(x, y);
        return operate(x, y);
    }

    public abstract boolean isSupport(CalculateType type);
    protected abstract int operate(int x, int y);
}
```

- 위 요구사항에 따라서 다음과 같은 템플릿을 만들 수 있을 것이다. 
- 
- 이때 0 ~ 100의 숫자에 대한 유효성 검사는 모든 연산에서 필요한 부분이므로 `calculateValidator`를 두어 처리할 수 있을것이다.

- 그외 연산은 각 연산의 구현 클래스마다 달라질 수 있는 부분이다. 

- 이 부분에 대해서는 `operate`를 `protected`로 열어두어 구현클래스에서 정의하도록 만들고 이를 `calculate`에서 사용하도록 만들 수 있다.

```java
public class PlusCalculator extends AbstractCalculator {
    public PlusCalculator(CalculateValidator calculateValidator) {
        super(calculateValidator);
    }

    @Override
    public boolean isSupport(CalculateType type) {
        return type == PLUS;
    }

    @Override
    protected int operate(int x, int y) {
        return x + y;
    }
}

public class MinusCalculator extends AbstractCalculator {
    public MinusCalculator(CalculateValidator calculateValidator) {
        super(calculateValidator);
    }

    @Override
    public boolean isSupport(CalculateType type) {
        return type == MINUS;
    }

    @Override
    protected int operate(int x, int y) {
        return x - y;
    }
}
```

이런 템플릿을 토대로 덧셈을 구현한 `PlusCalculator`나 뺄셈을 구현한 `MinusCalculator`를 위와 같이 정의할 수 있을 것이다.

> `protected`가 Java에서는 자식 클래스 뿐만 아니라 같은 패키지 안에 클래스에서도 접근가능한 부분은 아쉬운 부분이다. 최근에 등장한 JVM 언어인 Kotlin에서는 `protected`가 자식 클래스에서만 접근가능하도록 허용한다.


