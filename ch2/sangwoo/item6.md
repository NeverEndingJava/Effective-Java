## 불필요한 객체 생성을 피하라
---
### 객체를 매번 생성하기 보다 재사용하는 것이 좋다.
무거운 객체를 매번 생성하면 많은 자원이 들어갈 것이고, GC가 동작할 확률이 높아져 애플리케이션 성능에 영향을 미친다.
```java
String str1 = new String("Hello");
String str2 = "Hello";
```

str1은 실행할 때마다 힙 영역에 String 객체를 생성한다.
str2는 실행할 때마다 하나의 String 인스턴스를 재사용한다. (힙영역의 String pool에서)

### 정적 팩토리 메서드를 통해 불필요한 객체 생성을 막을 수 있다.
생성자는 호출될 때마다 새로운 객체를 생성하지만, 팩토리 메서드는 그렇지 않다.

```java
public final class Boolean {
	public static final Boolean TRUE = new Boolean(true);
	public static final Boolean FALSE = new Boolean(false);
    
    public static boolean parseBoolean(String s) {
        return "true".equalsIgnoreCase(s);
    }
    
    public static Boolean valueOf(String s) {
        return parseBoolean(s) ? TRUE : FALSE;
    }
}
```

valueOf 메소드를 통해 미리 생성된 true, false를 반환해준다.

### 객체 생성이 비싼 경우 캐싱을 사용
생성 비용이 아주 비싼 경우 캐싱을 통해 재사용하는 것이 좋다.(메모리, 디스크 사용량이 높은 경우)

```java
static boolean isRomanNumeral(String str) {
    return str.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
```
String.matches는 정규 표현식에 매치되는지 확인하는 방법이지만, 내부적으로 `Pattern` 인스턴스를 일회용으로 만들어 GC의 대상이 된다.


```java
public class RomanNumber {
    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");

    static boolean isRomanNumeral(String str) {
        return ROMAN.matcher(str).matches();
	}
}
```
Pattern 객체를 만들고 재사용하는 것이 좋다!

### 오토 박싱을 사용할 때 주의
오토 박싱은 기본 타입과 박싱된 기본 타입을 자동으로 변환해주는 기술인데, 불필요한 메모리 할당과 재할당을 반복하기에 성능이 느려질 수 있다.


> 객체 생성은 비싸니 무조건 피해라!! 가 아니다.
JVM에서 객체의 생성과 회수는 크게 부담되지 않는다.
다만, 아주 무거운 객체가 아닌 이상 본인만의 객체 풀을 만들진 말자!!
GC가 훨씬 성능이 좋다!