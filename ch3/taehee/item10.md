# equals 는 일반 규약을 지켜 재정의하라.

equals() 메서드를 재정의하는 것은 쉬워 보이지만 함정이 많이 숨어 있어서 조심해야 합니다. 
가장 쉬운 방법은 equals()를 아예 재정의하지 않는 것입니다. 
이렇게 하면 해당 클래스의 인스턴스는 자기 자신과만 동일하다고 간주됩니다.
```java
public class Object {
	public boolean equals(Object obj) {
		return (this == obj);
	}
}
```

하지만 equals()를 재정의해야 하는 경우도 있습니다. 논리적 동치성을 확인해야 하는 상황이 그 중 하나입니다.
값 클래스는 논리적인 동등성을 비교하기 위해 equals()를 재정의해야 할 때가 많습니다.
값 클래스는 값을 표현하는 것이 주요 목적이며, 값이 같은 객체들은 동등한 것으로 간주됩니다.

> 값 클래스의 경우에도 싱글톤과 같은 인스턴스 통제 클래스, Enum 클래스의 경우에는 하지 않아도 됩니다.

하지만 equals()를 재정의할 때에는 반드시 일반 규약을 따라야 합니다.

## `equals`는 메서드는 동치 관계(equivalence relation)를구현하며, 다음을만족한다
### 1. 반사성 : null 이 아닌 모든 참조 값 x에 대해, x.equals(x)는 true 이다.
```java
public class Car {
	private String name;

	public Car(String name) {
		this.name = name;
	}
}

public class App {
	public static void main(String[] args) {
		Set<Car> set = new HashSet<>();
		Car avante = new Car("avante");
		set.add(avante);
		System.out.println(set.contains(avante)); // true 를 반환한다
	}
}
```

### 2. 대칭성 : null 이 아닌 모든 참조 값 x, y에 대해 x.equals(y)가 true 면 y.equals(x) 도 true 이다.
```java
public class CaseInsensitiveString {
	private final String temp;

	public CaseInsensitiveString(String temp) {
		this.temp = temp;
	}

	
	@Override
	public boolean equals(Object obj) {
		if(obj instanceof CaseInsensitiveString) {
			return temp.equalsIgnoreCase(((CaseInsensitiveString)obj).temp);
		}
		if(obj instanceof  String) {
			return temp.equalsIgnoreCase((String)obj);
		}
		return false;
	}
}

public class App {
	public static void main(String[] args) {
		String s = "hihi";
		CaseInsensitiveString hihi = new CaseInsensitiveString("hihi");

		// 동치 관계 - 대칭성 확인
		System.out.println(s.equals(hihi));  //false
		System.out.println(hihi.equals(s));  //true
	}
}
```
- 위와 같이 equals 를 재정의 했을 경우 String 은 CaseInsensitiveString 객체에 대해 equals 를 정의한적이 없기떄문. 
- 이럴 경우 동치 관계 - 대칭성을 위반하게 된다.

```java
@Override
public boolean equals(Object obj){
	return obj instanceof CaseInsensitiveString && ((CaseInsensitiveString) obj).temp.equalsIgnoreCase(temp);
}
```
- String 까지 동치 비교를 하겠다는 욕심을 버리면 해결 할 수 있습니다.

### 3. 추이성 : 추이성 : null 이 아닌 모든 참조 값 x, y, z 에 대해 x.equals(y) true 이고, y.equals(z) 가 true 라면 x.equals(z) 도 true 이다.
```java
public class Point {
	private final int x;
	private final int y;

	public Point(int x , int y) {
		this.x = x;
		this.y = y;
	}

	@Override
	public boolean equals(Object obj) {
		if(!(obj instanceof Point p)) {
			return false;
		}
		return p.x == x && p.y == y;
	}
}

public class ColorPoint extends Point{
	private final Color color;

	public ColorPoint(int x, int y, Color color) {
		super(x,y);
		this.color = color;
	}

	@Override
	public boolean equals(Object obj) {
		if(!(obj instanceof Point))
			return false;
		if(!(obj instanceof ColorPoint)) {
			return obj.equals(this);
		}
		return super.equals(obj) && ((ColorPoint)obj).color == color;
	}
}

public class App {
	public static void main(String[] args) {
		ColorPoint p1 = new ColorPoint(1,2, Color.RED);
		Point p2 = new Point(1,2);
		ColorPoint p3 = new ColorPoint(1,2, Color.BLUE);
		System.out.println(p1.equals(p2));    // true
		System.out.println(p2.equals(p3));    // true
		System.out.println(p3.equals(p1));    // false
	}
}
```
- 위에 예시 코드처럼 대칭성을 지키기 위해 Point 타입이 매개변수로 들어왔을 경우에는 `Point.equals()` 로 x,y 값을 비교하였고, ColorPoint 타입이 들어온다면 color 도 비교하도록 재정의를 하였습니다.
- 결국 x,y 의 값은 같지만 Color 의 값이 다를땐 위와 같이 추이성이 꺠집니다. 
**_만약 위치와 색상이 같을 때만 true를 반환하는 equals를 작성한다면 이것은 대칭성 위반이 발생합니다._** 
- 이 현상은 모든 객체 지향 언어의 동치관계에서 나타나는 근본적인 문제로 클래스를 확장해 새로운 값을 추가하면서 equals 를 만족 시킬 방법은 존재하지 않습니다.
- if(obj.getClass()) 로 바꿔도 이땐, 리스코프 치환 원칙을 위반합니다.
> **리스코프 치환 원칙이란 상속 관계에 있는 클래스들은 부모 클래스의 인스턴스를 대체하여 사용해도 프로그램의 정확성을 해치지 않아야 한다는 원칙입니다.**
> 이 말은 `Point point = new ColorPoint();` 가 가능해야 한다는 뜻입니다. 
- 하위 클래스에서 값을 추가할 방법은 없지만 우회 방법은 존재한다.
### 3.1 컴포지션(Has ~ A)을 활용하는 방법

```java
public class ColorPoint {
	private final Point point;
	private final Color color;

	public Point asPoint() {
		return point;
	}

	@Override
	public boolean equals(Object obj) {
		if (!(obj instanceof ColorPoint cp))
			return false;
		return cp.point.eqals(point) && cp.color.eqauls(color);
	}
}
```
- 위에 예시 코드처럼 Point를 ColorPoint의 private 필드로 두고, ColorPoint와 같은 위치인 일반 Point 를 반환하는 뷰 메서드를 추가하는 식으로 하면 우회 할 수 있는 방법이 존재합니다.
> 사실 추상 클래스의 하위 클래스라면 equals 규약을 지키면서도 값을 추가할 수 있습니다. 
> 이 모든 문제는 상위 클래스를 직접 인스턴스 생성이 불가능하다면 위에 있던 문제는 일어나지 않기 떄문입니다.

### 4. 일관성 : null 이 아닌 모든 참조 값 x, y 에 대해 x.equals(y)를 반복해서 호출하면 항상 같은 값을 반환한다.
- 일관성은 두 객체가 같다면 두 객체중 수정이 되지 않는 한 영원히 같아야 한다 라는 의미입니다.
- 위에 말처럼 `수정이 되지 않는다면` 계속 같은 결과가 나와야한다는 뜻이며, 도중에 수정이 된다면 달라질 수 있습니다.
- 하지만 불변 객체라면 한번 다르다면 영원히 달라야 하며, 같다면 영원히 같아야 한다는 말이 됩니다.

```java
public final class ImmutablePerson {
	private final String name;
	private final int age;

	public ImmutablePerson(String name, int age) {
		this.name = name;
		this.age = age;
	}

	@Override
	public boolean equals(Object obj) {
		if (!(obj instanceof ImmutablePerson other)) {
			return false;
		}

		return this.name.equals(other.name) && this.age == other.age;
	}

}

public class App {
	public static void main(String[] args) {
		ImmutablePerson person1 = new ImmutablePerson("Alice", 25);
		ImmutablePerson person2 = new ImmutablePerson("Bob", 30);
		ImmutablePerson person3 = new ImmutablePerson("Alice", 25);

		System.out.println(person1.equals(person2)); // false
		System.out.println(person1.equals(person3)); // true
	}
}
```
- 위에 예시 코드처럼 인스턴스 생성 시점에 정해진 이름과 나이로 둘의 값을 비교 할 경우 영원히 둘은 true 를 반환 합니다.

### equals 메서드 구현 방법 단계
#### 1. == 연산자를 사용해 입력이 자기 자신을 참조인지 확인한다!
    float,double 부동 소수값등을 다뤄야 하므로 은 compare 메서드를 활용하여 비교해야합니다
    그외 기본 타입은 == 연산자를 활용하도록 합니다.
#### 2. instanceof 연산자로 입력이 올바른 타입인지 확인한다!
#### 3. 비교 객체를 올바른 타입으로 형변환한다!
#### 4. 비교 객체와 자기 자신의 객체와 대응되는 '핵심' 필드들이 모두 일치하는지 하나씩 검사한다!

### 주의 해야 할 점
**equals 판단에 신뢰 할 수 없는 자원이 끼어 들면 안된다.**
> 예시로 java.net.URL 의 equals 는 주어진 URL 과 매핑된 호스트의 IP 주소를 이용해 비교합니다.
> 이때 네트워크를 통해야 하는데, 항상 결과가 같다고 보장 할 수 없습니다. 이처럼 신뢰 할 수 없는 자원이 끼어들면 equals 규약이 깨지게 됩니다.

**모든 객체가 null 과 같이 않아야 한다.**


## equals 를 제대로 재정의를 했는지 의문이 든다면 항상 세가지를 자문하는 습관을 갖도록 합니다.
### 1. 대칭적인가?
### 2. 추이성이 있는가?
### 3. 일관적인가?