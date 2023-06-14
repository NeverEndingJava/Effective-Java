# toString을 항상 재정의하라

## 요약

- Object 클래스의 toString()을 그대로 사용하면 클래스이름과 해시코드가 있는 문자열이 나온다

- 해당 문자열은 개발자가 알아보기 힘드니 사용하려는 객체의 toString()을 재정의해서 알아보기 쉽게 만들자

- toString()을 재정의할 때는 '객체 스스로를 표현하고 있나'를 생각하고 순환 참조를 조심하자

## toString()이란?

`toString()`이란 `Object` 클래스의 메서드이다. 

- 자바의 모든 객체는 `Object` 클래스를 상속하기 때문에 모든 객체는 `toString()` 메서드를 가진다.

`toString()`은 아래와 같은 방식으로 객체를 표현하는 문자열을 리턴해준다.

```java
public String toString() {
    return getClass().getName() + "@" + Integer.toHexString(hashCode());
}
```

위 코드처럼 `클래스_이름@16진수로_표시한_해시코드` 를 반환한다.

## toString()을 언제 사용할까?

`toString()`은 기본적으로 객체를 표현하는 문자열을 리턴해주기 때문에 개발할 때 다양하게 활용할 수 있다.

대표적으로

1. 콘솔로 객체를 확인할 때
    1. `System.out.println()`
    2. `System.out.print()`
    3. 객체에 문자열을 연결(`SomeObject + ""`)
    4. assert 구문 사용 시
    5. 등등...
2. 디버깅 할 때
3. 로깅할 때

## toString()을 왜 항상 재정의해야할까?

사용하는 예시로 콘솔로 확인하거나, 디버깅 하거나, 로깅할 때가 있다.

즉, **개발자에게 객체가 보여지는데 `클래스_이름@16진수로_표시한_해시코드` 와 같은 방식으로 보여진다면 해당 객체 안에 뭐가 들었는지 알 수가 없다.**

**사용하려는 객체에 toString()을 재정의하면 로깅 및 디버깅 시 개발자에게 좀 더 유익한 정보를 전해줄 수 있기 때문에 재정의 하는 것이 장점이 너무 많다.**

## toString()을 어떻게 재정의해야할까요?

재정의할 때 가장 중요한 건 **객체 스스로를 완벽히 설명하는 문자열**이어야 한다.

보통은 **객체가 가진 주요 정보를 모두 반환**하는 것이 좋다.

전화번호처럼 포맷이 정해져 있는 경우, 아래 코드와 같이 재정의하고 주석을 달아서 문서화를 해줄 수도 있다.

```java
/** 
 * 전화번호의 문자열 표현을 반환합니다.
 * 이 문자열은 XXX-YYYY-ZZZZ 형태의 11글자로 구성됩니다.
 * XXX는 지역코드, YYYY는 접두사, ZZZZ는 가입자 번호입니다.
 * 블라블라~
*/
@Override
public String toString() {
    return String.format("%03d-%04d-%04d", areaCode, prefix, lineNum);
}
```

다만 포맷을 정해준다면 앞으로의 유지보수에도 항상 이 포맷을 써야하기 때문에 신중히 포맷을 정할 필요가 있다.

보통은 포맷 여부와 상관없이 아래와 같은 방식으로 `toString()`을 재정의한다. (IDE에서 자동으로 만들어주는 포맷)

```java
@Override
public String toString() {
    return "PhoneNumber{" +
                "areaCode='" + areaCode + '\'' +
                ", prefix='" + prefix + '\'' +
                ", lineNum='" + lineNum + '\'' +
                '}';
}
```

유틸리티 클래스는 `toString()`을 사용할 이유가 없고, `Enum` 은 `toString()` 이미 정의되어 있으므로 `toString()`을 재정의하지 않아도 된다.

보통 `toString()`을 항상 정의하기보다는 필드를 표현할 일이 있는 `Entity`나 `VO`, `DTO` 같은 성격의 객체에 `toString()`을 해놓으면 디버깅할 때 편한 것 같다.


### toString() 재정의할 때 순환 참조를 조심하자

IDE에서 지원해주는 `toString()` 혹은 Lombok에 있는 `@ToString` 을 무분별하게 사용하다가는 `StackOverflowError` 가 일어날 수 있다.

서로가 서로를 참조하는 필드를 가지고 있다면, 서로 출력하기 위해서 순환 호출하게 되기 때문에 무한 반복의 호출이 발생한다.

서로가 서로를 참조하는 필드를 `toString()` 에서 빼서 해결할 수 있다.
