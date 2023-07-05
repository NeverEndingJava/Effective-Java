# 태그 달린 클래스보다는 클래스 계층구조를 활용하라

`요약`

- 태그 달린 클래스는 장황하고, 오류를 내기 쉽고, 비효율적이다

- 태그 달린 클래스를 사용하지 말고 클래스 계층구조를 고려하자

## 태그 달린 클래스

- 두 가지 이상의 의미를 표현할 수 있으며, 그중 현재 표현하는 의미를 태그 값으로 알려주는 클래스를 태그 달린 클래스라고 한다.

```java
public class Figure {
    enum Shape {RECTANGLE, CIRCLE}

    // 태그 필드
    private final Shape shape;

    // 사각형일때만 사용
    private final double length;
    private final double width;

    // 원형일때만 사용
    private final double radius;

    public Figure(Shape shape, double radius) {
        this.shape = shape;
        this.length = 0;
        this.width = 0;
        this.radius = radius;
    }

    public Figure(Shape shape, double length, double width) {
        this.shape = shape;
        this.length = length;
        this.width = width;
        this.radius = 0;
    }

    public double area() {
        switch (shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new IllegalArgumentException();
        }
    }
}
```

### 단점

- 쓸데없는 코드가 많아 가독성이 안좋다.

- 다른 의미의 코드가 항상 같이 하니, 메모리도 많이 사용한다.

- 불변으로 하려면 쓰지 않는 필드까지 불변으로 만들어줘야 한다.

- 생성자가 태그 필드를 설정하고 해당 의미에 쓰이는 데이터 필드들을 초기화하는 데 컴파일러가 도와줄 수 있는 건 별로 없다.

- 인스턴스 타입만으로는 현재 나타내는 의미를 알수가 없다.

- 인스턴스 타입만으로 현재 나타내는 의미를 알 수 없다는 것은 SRP인 단일 책임 원칙을 위반한 것으로 볼수가 있다.

- 즉, 장황하고, 오류 내기 쉽고, 비효율적이다.

- 결론은 태그 달린 클래스는 클래스 계층구조를 어설프게 흉내낸 아류일 뿐이다.

## 클래스 계층구조

- 클래스 계층구조를 활용하는 서브타이핑(subTyping)이 있다.

- 태그 달린 클래스의 모든 단점을 해소할 수 있다.

```java
public abstract class Figure {
    public abstract double area();
}

public class Circle extends Figure {
    private final double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double area() {
        return Math.PI * (radius * radius);
    }
}

public class Rectangle extends Figure{
    private final double length;
    private final double width;

    public Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }

    @Override
    public double area() {
        return length * width;
    }
}
```

## 태그 달린 클래스 → 클래스 계층 구조

1. 계층구조의 루트(root)가 될 추상 클래스 정의

2. 태그 값에 따라 동작이 달라지는 메서드들을 루트 클래스의 추상 메서드로 선언

3. 태그 값에 상관없이 동작이 일정한 메서드들을 루트 클래스에 일반 메서드로 추가

4. 하위 클래스에서 공통으로 사용하는 데이터 필드도 전부 루트클래스로 올리기

## 유연성

- 정사각형이라는 새로운 도형을 추가해봅시다.

### 태그달린 클래스

```java
public class Figure {
    enum Shape {RECTANGLE, CIRCLE, SEQURE}

    // 태그 필드
    private final Shape shape;

    // 사각형일때만 사용
    private final double length;
    private final double width;

    // 원형일때만 사용
    private final double radius;

    // 정사각형일때만 사용
    private final double side;

    public Figure(Shape shape, double side) {
        this.shape = shape;
        this.side = side;
        this.length = 0;
        this.width = 0;
        this.radius = 0;
    }

    public Figure(Shape shape, double radius) {
        this.shape = shape;
        this.radius = radius;
        this.length = 0;
        this.width = 0;
        this.side = 0;
    }

    public Figure(Shape shape, double length, double width) {
        this.shape = shape;
        this.length = length;
        this.width = width;
        this.radius = 0;
        this.side = 0;
    }

    public double area() {
        switch (shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            case SEQURE:
                return side * side;
            default:
                throw new IllegalArgumentException();
        }
    }
}
```

- 위 클래스는 가장 큰 문제가 있다. 바로 생성자 오버로딩이다. 

- 이미 원형에서 매개변수 2개를 받고 있는 생성자가 있기 때문에 생성자를 만들수가 없다.

### 클래스 계층 구조

```java
public class Square extends Figure{

    private final double side;

    public Square(double side) {
        this.side = side;
    }

    @Override
    public double area() {
        return side * side;
    }
}
```

- 전혀 문제 없이 새로운 객체를 생성할 수 있다.
