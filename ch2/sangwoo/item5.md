## 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라
---
많은 클래스들이 하나 이상의 자원에 의존한다.
올바른 의존 객체 주입 방식으로 필요한 자원을 생성자에 넘겨줌으로써 유연하고 테스트를 용이하게 해준다.
```java
public class BankService {
	private final CardService;
    private final BankRepository;
    
    public BankService(CardService cardService, BankRepository bankRepository) {
    	...
    }
}
```

### 잘못 사용한 경우
1. 정적 유틸리티 클래스로 구현한 경우
```java
public class BankService {
	private static final CardService = ... ;
    private static final BankRepository = ... ;
    
    private BankService() {
    
    }
}
```

2. 싱글톤으로 구현한 경우
```java
public class BankService {
	private final CardService = ... ;
    private final BankRepository = ... ;
    
    private BankService(...) {
    	...
    }
    
    public static BankService INSTANCE = new BankService(...);
}
```

- 필드 값을 하나만 사용한다는 점에서 유연하지 않다.
- final 키워드를 지우고 setter 식으로 교체할 수도 있지만, 멀티스레드 환경에서 사용하기 부적합하다.