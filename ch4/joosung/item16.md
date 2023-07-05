# public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

`요약`
- public 클래스는 절대 가변 필드를 직접 노출해서는 안된다.
  
  - 불변 필드라면 노출해도 덜 위험하지만 완전히 안심할 수는 없다.
  
  - package-private 클래스나 private 중첩 클래스에서는 종종 필드를 노출하는 편이 나을 때도 있다.


### 캡슐화의 이점을 제공하지 못하는 클래스

```Java
class Point {
	public int x;
	public int y;

	public Point(int x, int y) {
		this.x = x;
		this.y = y;
	}
}
```

- API를 수정하지 않고는 내부 표현을 바꿀 수 없음

    - getter,setter 를 이용하면 표현 변경 가능

```Java
public double getX() {
	return x;
}

public double getY() {
	return y;
}
```


- 불변식을 보장하지 못함

  - 클라이언트가 직접 값을 변경할 수 있음


```JAVA
public static void main(String[] args) {
	Point point = new Point(1, 2);
	System.out.println(point.x); // 1

	point.x += 1;
	System.out.println(point.x); // 2
}
```

### 접근자 제공시 이점

- getter/setter 메서드를 통해 언제든지 내부 표현을 바꿀 수 있음(유연성을 얻음)

- 불변식은 보장할 수 있음

- 외부에서 필드에 접근할 때 부수 작업을 수행할 수 있음
