## 2.생성자에 매개변수가 많다면 빌더를 고려하라
---
생성자와 정적 팩토리는 매개변수가 많아지면 대응하기 어렵다!!

### 해결방안
1. 점층적 생성자 패턴
	```java
    public Person(int age) { ... }
    
    public Person(int age, String name) { ... }
    
    public Person(int age, String name, int money) { ... }
    ```
	- 나올 수 있는 경우마다 생성자를 만들었다.
    - 값이 무엇인지 헷갈리고 매개변수의 순서가 바뀌어도 컴파일러가 알아채지 못한다.
2. 자바 Beans 패턴
	```java
    public void setAge(int age) {
    	this.age = age;
    }
    public void setName(String name) {
    	this.name = name;
    }
    ```
    - 객체 생성을 위해 여러 메소드를 호출해야 함
    - 객체가 완전히 생성되기 전까지는 일관성이 무너짐
    
3. 빌더 패턴
	- 직접 객체를 생성하는 것이 아닌, 빌더 객체를 생성하고 빌더 객체가 제공하는 세터 메소드를 사용해 객체를 완성해간다. 빌더의 세터 메소드 반환값이 빌더 자신이므로 메소드를 연속으로 사용하는 `메소드 체이닝`이 가능하다.
    ```java
    class Person {
    	private final int age;
        private final String name;
        private final int money;
        
        private Person(Builder builder) {
        	this.age = builder.age;
            this.name = builder.name;
            this.money = builder.money;
        }
        
        public static class Builder {
        	
            // 필수 매개변수
            private final int age;
            
            // 선택 매개변수
            private String name = "상우";
            private int money = 0;
        	
            public Builder(int age) {
            	this.age = age;
            }
            
            public Builder name(String name) {
            	this.name=name;
                return this;
            }
            
            public Builder money(int money) {
            	this.money=money;
                return this;
            }
            
            public Person build() {
            	return new Person(this);
            }
        }
    }
    ```
    - 빌더 패턴은 코드가 장황하여 보통 매개변수가 4개보다 적다면 점층적 생성자 패턴이 좋다고 한다. 하지만, API는 시간이 지날수록 많은 매개변수를 가지는 경향이 있어 처음부터 빌더 패턴으로 시작하는게 좋을 수도 있다고 한다😜