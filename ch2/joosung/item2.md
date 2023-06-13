
# 생성자에 매개변수가 많다면 빌더를 고려하라

- 빌더 패턴을 통해 가독성을 높이고, 매개변수의 순서를 신경쓰지 않아도 된다. 또한, 불변 객체를 만들 수 있다.

- 단점은 코드가 다소 길어질 수 있다.

## 매개변수가 많은 생성자의 문제점

- 생성자나 정적 팩토리 메서드에서 매개변수가 너무 많아지면, 클라이언트 코드에서 그 객체를 생성할 때 문제가 발생할 수 있다. 

- 각각의 매개변수가 무슨 역할을 하는지 파악하기 어렵고, 매개변수의 순서를 바꾸면 의도하지 않은 결과를 초래할 수 있다. 

- 이를 해결하기 위한 전통적인 방법으로 자바빈즈 패턴과 텔레스코피 생성자 패턴이 있다. 

- 하지만, 자바빈즈 패턴은 객체의 일관성을 보장하기 어렵고, 텔레스코피 생성자 패턴은 코드가 지나치게 복잡해질 수 있다.

- 이런 문제를 해결하는 가장 좋은 방법 중 하나가 바로 빌더 패턴이다.

## 빌더 패턴

- 빌더 패턴은 객체를 생성하기 위한 별도의 '빌더' 객체를 이용한다. 
 
- 빌더는 생성하려는 객체의 모든 매개변수를 설정할 수 있는 메서드를 제공하며, 빌더의 'build' 메서드를 호출하면 빌더가 생성한 객체를 반환합니다.


### 빌더 패턴 만들기

- 다음 예제를 통해 빌더 패턴을 만드는 방법을 알아보자. 

- 첫째, 빌더 클래스를 따로 만들어야 한다. 보통 내부 정적 클래스로 정의한다.

- 아래 클래스는 직원을 표현한다. 클래스의 인스턴스 변수 중 이메일과 부서 정보는 필수적이지 않다.

```java

public class Employee {
	
	private final Long id;    // required
	private final String name;  // required
	private final String email; // optional
	private final String dept;   // optional


    public static class Builder {
        // 필수 파라미터
        private final String id;
        private final String name;

        // 옵셔널 파라미터
        private String email; 
	    private String dept;   

        // 1. 빌더 객체를 사용하려면 가장 먼저 필수 파라미터를 입력하도록 한다.
        public Builder(Long id, String name) {
            this.id = id;
            this.name = name;
        }

        // 2. 옵셔널 파라미터는 선택적으로 호출되도록 한다.
        public Builder email(String email) {
            this.email = email;
            return this;
        }

        public Builder dept(String dept) {
            this.height = height;
            return this;
        }
		
        // 3. 옵셔널 세팅 작업이 완료되면 완성 메서드를 호출한다.
        public Employee build() {
            return new Employee(this); 
        }
    }
  
    // 4. 객체는 반드시 해당 객체의 Builder 객체로만 생성할 수 있도록 한다.
    private Employee(Builder builder) {
        id = builder.id;
        name = builder.name;
        email = builder.email;
        dept = builder.dept;
    }
}
```

```java
# 클라이언트에서 Employee 생성 예제
Employee employee = new Builder(1, "홍길동")
                .dept("dev")
                .build();
```

빌더 객체를 만드는 방법은 다음 4가지 과정을 따른다.

> 1. 빌더 객체를 사용하려면 가장 먼저 필수 파라미터를 입력하도록 한다.

> 2. 옵셔널 파라미터는 선택적으로 호출되도록 한다.

> 3. 옵셔널 세팅 작업이 완료되면 완성 메서드를 호출한다.

> 4. 객체는 반드시 해당 객체의 Builder 객체로만 생성할 수 있도록 한다.

### 빌더 패턴으로 생성하는 객체는 불변인가?

- 위에서 정의한 `Employee` 클래스를 살펴보면, 필드를 모두 final로 정의했다.
- `Employee` 클래스의 생성자 또한 private이며 setter 메서드는 존재하지 않는다.
- 다만, 자유롭게 조절할 수 있다. 옵셔널 파라이터에 null이 들어갈 수 있으니 주의하자.

### 빌더 패턴을 사용하는 클라이언트는 가독성 있게 객체를 생성할 수 있는가?

- 다음 코드를 통해 클라이언트가 `Employee` 객체를 생성하는 예를 살펴보자.
- 클라이언트는 옵셔널 파라미터 중 height 값에만 값을 지정할 수 있으며, 명시적으로 값을 설정하였다.





