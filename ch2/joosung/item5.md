
# 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

- 클래스가 내부적으로 하나 이상의 자원에 의존한다면 또 그 자원이 클래스 동작에 영향을 준다면 직접 생성하지도, 싱글턴과 정적 유틸리티 클래스를 사용하지도 말자.

- 필요한 자원 혹은 해당 자원을 만들어주는 팩터리를 생성자에 넘겨주자. 

- 의존 객체 주입이라 하는 이 기법은 클래스의 유연성, 재사용성, 테스트 용이성을 개선해준다.

---
> 클래스가 내부적으로 하나 이상의 자원에 의존하고, 그 자원이 클래스 동작에 영향을 준다면
싱글턴(Singleton) 과, 정적 유틸리티(static util class)는 사용하지 않는 것이 좋다.

### 왜?

- 유연하지 않고 테스트하기 어렵기 때문이다. 밑의 예제를 통해 확인해보자 

#### 정적 유틸리티를 잘못 사용한 예 

```java
public class SpellChecker{
    private static final Lexicon dictionary = ...; // 사전에 의존 
    private SpellChecker() {}
    public static boolean isValid(String word){...}
    public static List<String> suggestions(String typo){...}
}
```

#### 싱글턴을 잘못 사용한 예

```java
public class SpellChecker{
    private static final Lexicon dictionary = ...; // 사전에 의존 
    private SpellChecker(...) {} 
    public static SpellChecker INSTANCE = new SpellChecker(...);

    public static boolean isValid(String word){...}
    public static List<String> suggestions(String typo){...}

}
```

- SpellChecker 클래스는 dictionary(자원)에 의존하고 있다
- 그런데, 언어별 사전, 특수 어휘용 사전이 필요하게 되면 클래스 내부 메서드 동작이 달라져야 한다.
- SpellChecker가 여러 사전을 사용할 수 있도록 만들기 위해 dictionary 필드에서 final 한정자를 제거하고 다른 사전으로 교체하는 메서드를 추가할 수는 있다.
- 그러나 이 방법은 어색하며, 오류를 내기 쉽다. 또한 멀티스레드 환경에서는 쓸 수 없다.

> 클래스가 여러 자원 인스턴스를 지원해야하고, 클라이언트가 원하는 자원을 사용하게 하는 방법을 사용해야 한다. 인스턴스를 생성할 때 생성자에 필요한 자원을 넘겨주자.

- **의존 객체 주입 방식(DI)** 이라고한다.

    ```java
    // 의존 객체 주입은 유연성과 테스트 용이성을 높여준다 
    public class SpellChecker{
    	private final Lexicon dictionary;
    	public SpellChecker(Lexicon dictionary){
    		this.dictionay = Objects.requiredNonNull(dictionay);
    	}
    	public boolean isValid(String word){...}
    	public List<String> suggestions(String typo){...}
    }
    ```

- 클래스의 유연성, 재사용성, 테스트 용이성을 개선 해준다.
- 불변을 보장해 해당 자원을 사용하려는 여러 클라이언트가 의존 객체들을 안심하고 공유할 수 있다.
- 생성자, 정적 팩터리, 빌더 아이템에 응용할 수 있다.
