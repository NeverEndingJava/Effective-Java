## 멤버 클래스는 되도록 static으로 만들어라
---

### 중첩 클래스
- 다른 클래스 안에 정의된 클래스
- 자신을 감싼 바깥 클래스에서만 사용되어야 한다. 그 외 쓰임새가 생기면 톱 레벨 클래스로 생성
- 정적 맴버 클래스, 비정적 맴버 클래스, 익명 클래스, 지역 클래스
- 정적 맴버 클래스 제외한 클래스들을 내부 클래스라고 한다.

### 정적 맴버 클래스
- class 내부에 static으로 선언된 클래스
- 바깥 클래스의 private 멤버에도 접근 가능 + 일반 클래스와 똑같음

```java
public class Calculator {
	
    private String name = "hello";

	// 열거타입은 일종의 클래스, 상수 하나당 인스턴스를 하나씩 만들어 public static final 필드로 공개함
	public enum Operator {
		PLUS,
        MINUS,
        MULTIPLY,
        DIVIDE
    }
    
    public static class Sample {
    	
        public void method() {
        	Calculator outClass = new Calculator();
            System.out.println(outClass.name);
        }
    }
}
```

### 비정적 맴버 클래스
- static이 붙지 않은 맴버 클래스
- 비정적 맴버 클래스 메서드에서 `클래스명.this`를 통해 바깥 인스턴스의 메서드 호출이 가능
- 바깥 인스턴스 없이는 생성 불가
- 주로 어댑터를 정의할 때 쓰임. 어떤 클래스의 인스턴스를 감싸 다른 클래스의 인스턴스처럼 보이게 하는 뷰로 사용
- 컬렉션 인터페이스 구현들도 자신의 반복자를 구현할 때 사용
```java 
public class MySet<E> extends AbstractSet<E> {

    @Override public Iterator<E> iterator() {
    return new MyIterator();
    }

    private class MyIterator implements Iterator<E> {
    ...
    }
}

```

>맴버 클래스에서 바깥 인스턴스에 접근할 일이 없다면 무조건 static을 붙여 정적 맴버 클래스로 만들자
바깥 인스턴스로의 숨은 외부 참조를 가지게 되어 GC 대상이 아니게 된다. -> 메모리 누수 발생

### 익명 클래스
- 이름이 없다
- 쓰이는 시점에 선언과 동시에 인스턴스가 만들어진다.
- 바깥 클래스의 멤버가 아니다.

람다가 많이 사용되면서 익명 클래스는 한물 갔다~

### 지역 클래스
- 유효범위가 지역변수와 같은 클래스다
- 맴버 클래스처럼 이름이 있고 반복 사용이 가능하다
- 잘 안쓰인다!
