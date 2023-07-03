## 태그 달린 클래스보다는 클래스 계층구조를 활용하라
---

태그 달린 클래스란?
2가지 이상의 특정 개념을 필드를 통해서 표현하는 클래스로, 현재 표현하는 의미를 태그 값으로 알려주는 클래스다.

```java
public class Figure {
	enum Shape {RECTANGLE, CIRCLE}
    
    final Shape shape;
    
    String color;
    double width;
    double length;
    double radius;
    
    // 원
    Figure(double radius, String color) { 
        shape = Shape.CIRCLE;
        this.radius = radius;
        this.color = color;
    }
    
    // 직사각형
    Figure(double width, double length, String color) {
        shape = Shape.RECTANGLE;
        this.width = width;
        this.length = length;
        this.color = color;
    }
    
    double area() {
        switch (shape) {
            case RECTANGLE:
                return width * length;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError(shape);
        }
    }
}
```

ENUM 타입, switch, 등등 쓸데 없는 코드가 너무 많다. 여기에 다른 도형도 추가하면 코드가 길어진다. 이를 해결하는 방법은 계층 구조가 있다.

### 계층구조 서브타이핑
1. 계층 클래스의 루트가 되어줄 추상 클래스를 정의한다
2. 태그 값에 따라 동작이 달라지는 메서드들은 루트 클래스의 추상메서드로 만든다
3. 각 구현 클래스를 만들고 의미에 맞는 데이터 필드를 넣자, 추상 메서드를 알맞게 구현하면 된다.

```java
abstract class Figure {
	abstract double area();
}

class Circle extends Figure {
	final double radius;

	Circle(double radius) { this.radius = radius; }

	@Override 
    double area() { return Math.PI * (radius * radius); }
}

class Rectangle extends Figure {
	final double length;
	final double width;

	Rectangle(double length, double width) {
		this.length = length;
		this.width = width;
	}

	@Override 
    double area() { return length * width; }
}
```
