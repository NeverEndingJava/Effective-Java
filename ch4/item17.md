## 변경 가능성을 최소화하자
---
### 불변클래스
- 클래스의 인스턴스 값을 수정할 수 없는 클래스
- String, BigInteger, ...

### 불변클래스 만드는 규칙
- 객체의 상태를 변경하는 메서드 만들지 않음 (setter 만들지마)
- 클래스를 확장할 수 없도록 함 (final 키워드 || 생성자 private으로)
- 모든 필드를 private final로 선언
	맴버의 접근 권한 최소화
- 자신 외에는 내부의 가변 컴포넌트에 접근 못하게 한다


```java
/*
setter 안해둠
plusAge -> 새로운 객체 반환 (새로운 객체를 반환하는 메서드는 명명규칙으로 전치사 쓰기)
클래스를 final로 지정해 확장 불가능
모든 필드를 private + final 로 선언
가변 참조 객체는 final로 지정해도 내부 값은 불변이 아니기에 clone 사용
*/
public final class Person {
	private final String name;
    private final int age;
    private final String[] languages;
    
    public Person(String name, int age) {
    	this.name = name;
        this.age = age;
    }
    
    public Person plusAge(int age) {
    	return new Person(name, this.age+age); // 새로운 Person 객체 반환
    }
    
    public String[] getLanguages() {
    	return languages.clone();
    }
}
```


### 불변객체 장점
- Thread safe 해서 동기화할 필요가 없다. (안심하고 공유할 수 있다)
- 생성된 시점의 상태가 파괴될때까지 간직되어 단순하다.
- 불변객체는 객체의 필드로 쓰기 좋다.
	Map 의 키나 Set 의 요소로 불변객체를 사용하면 불변식을 지키기 쉽다.
- 메서드 수행중 예외가 발생해도 객체는 상태를 유지한다.

### 불변객체 단점
- 값이 다르면 무조건 객체를 생성해야 함
	값의 가짓수가 많으면 비용이 크다
    - 해결방법1. 다단계 연산
    - 해결방법2. 가변동반 클래스(복잡한 다단계 연산을 기본으로 제공 해주는 클래스) StringBuilder

