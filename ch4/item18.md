## 상속보다는 컴포지션을 사용하라
---
### 상속의 문제점
- 캡슐화를 깨트린다. (상위 클래스의 구현이 하위 클래스에 노출되기에)
- 상위 클래스에 의존적이여서 결합도가 높아진다. (상위 클래스와 하위 클래스 관계가 컴파일 시점에 결정되어 실행시점에 객체 종류를 변경하는 것이 불가능)

```java
/* 부모 클래스 취미를 String으로 가지고 있음 */
public class Parent {
	
    private String hobby;
    
    public Parent(String hobby) {
    	this.hobby = hobby;
    }
}
```

```java
/* 자식 클래스 Parent 클래스를 상속하고 직장를 String으로 가지고 있음 */
public class Child extends Parent{
	
    private String job;
    
    public Child(String hobby, String job) {
    	super(hobby);
        this.job=job;
    }
}
```

>만약 부모 클래스에서 취미 타입이 String에서 Hobby 로 변경된다면?? Child 클래스 생성자에서 컴파일 에러가 발생할 것이다. 상위 클래스에 의존적인 문제 때문에 상위 클래스의 변경에 따른 하위 클래스의 변경을 개발자가 직접 수정해줘야 한다.


### 컴포지션을 사용하자
- 새로운 클래스를 만들고 private 필드에 기존 클래스의 인스턴스를 참조하게 하자

```java
public class Child {
	private Parent parent;
    private String job;
    
    public Child(Parent parent, String job) {
    	this.parent = parent;
        this.job = job;
    }
}
```

장점
- 기존 클래스 내부 구현방식의 영향에서 벗어나, 기존 클래스에 영향받지 않는다.

단점
- 클래스 각각을 확장해야 하고, 생성자를 별도로 지정해 줘야 해서 귀찮다.
- 콜백 프레임워크와는 어울리지 않는다. (?)


### 그래도 상속을 쓰면 좋은 곳
- 확실히 `is-a` 관계일 때
```java
public class Earth extends Planet {
	
    protected void 자전() { }
    
    protected void 공전() { }
}
```

지구 = 행성, 변할 가능성이 없고 자전 공전 역시 변할 가능성이 없다. 확실한 is-a 관계에서 상위 클래스는 변할 가능성이 거의 없다!
