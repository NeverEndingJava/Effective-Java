## 상속을 고려해 설계하고 문서화해라. 그러지 않았다면 상속을 금지하라.
---
### 상속용 클래스 설계
- 클래스 내부에 스스로 어떻게 사용하는지 모두 문서로 남겨야 한다.
- 문서화한 것은 그 클래스가 쓰이는 한 반드시 지켜야 한다. 그렇지 않으면 하위 클래스에서 오작동할 수 있다.
- 다른 이가 효율 좋은 하위클래스를 만들 수 있도록 일부 메소드를 protected로 제공해야 할 수도 있다.
- 클래스를 확장해야 할 명확한 이유가 떠오르지 않는다면 상속을 금지하자 (클래스를 final로 or 생성자를 외부에서 접근 못하게)

### 상속용 클래스는 재정의할 수 있는 메서드들을 내부적으로 어떻게 이용하는지 문서로 남겨야 한다.
- 어떤 순서로 호출하는지
- 각각의 호출 결과가 이어지는 처리에 어떤 영향을 주는지
- 재정의 가능한 메서드를 호출할 수 있는 모든 상황
- 메서드의 내부 동작

### 효율적인 하위 클래스를 만들 수 있게 하기 위해 protected 메서드 제공
- java.util.AbsractList의 removeRange 메서드
- 하위 클래스에서 부분리스트의 clear메서드를 고성능으로 만들기 쉽게 하기 위해서 이 메서드를 제공해준다.

### 상속용으로 설계한 클래스는 배포전 하위 클래스를 3개 이상 만들어 검증하자
- 꼭 필요한 protected 맴버를 놓쳤다면, 하위클래스를 작성할 때 알 수 있다. 하위 클래스를 여러개 만들었는데 protected 맴버가 안쓰였다면 이는 private이어야 할 가능성이 크다.
- 3개 이상 작성 + 제 3자가 작성해보자

### 상속용 클래스의 생성자는 직접적이든 간접적이든 재정의 가능 메서드를 호출해선 안된다.

```java
public class Parent {
	
    public Parent() {
    	overrideMe();
    }
    
    public void overrideMe() { }
}
```

```java
public class Child extends Parent{
	private final Hobby hobby;
    
    public Child(Hobby hobby) {
    	this.hobby = hobby;
    }
    
    @Override
    public void overridMe() {
    	System.out.println(hobby);
    }
}
```

Child의 overrideMe를 실행한다면, hobby를 2번 출력하는게 아니고 첫 번째는 null을 출력함
상위(Parent) 클래스의 생성자는 하위(Child) 클래스 생성자가 인스턴스 필드를 초기화하기 전에 overrideMe를 호출하기 때문.

### Cloneable, Serializable 인터페이스 조심
- 위 인터페이스를 구현한 클래스를 상속 가능하게 설계하는 것은 좋지 않음.
- Clonable의 clone()과 Serializable의 readObject()는 새로운 객체를 만들어내어 클래스의 상태가 초기화 되기 전에 메서드부터 호출되는 상황이 올 수 있다. (?)

### 상속을 금지하는 방법
- 클래스를 final
- 모든 생성자를 private or default

상속을 허용해야 한다면, 재정의 가능 메서드를 사용하지 않게 만들고 이를 문서화하자
메서드를 재정의해도 다른 메서드의 동작에 영향을 주지 않게 개발하면 된다.