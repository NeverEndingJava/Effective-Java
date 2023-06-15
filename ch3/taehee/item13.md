# clone 재정의는 주의해서 진행하라

`clone()` 메서드는 객체를 복사하여 새로운 인스턴스를 생성하는 기능을 제공합니다.

Java 제공 인터페이스 `Cloneable` 은 복제해도 되는 클래스임을 명시하는 용도의 인터페이스 입니다.
```java
public interface Cloneable {
}
``` 
- 일반적으로 인터페이스를 구현한다는 것은 해당 클래스가 그 인터페이스에서 정의한 기능을 제공한다고 선언하는 행위입니다.
- 하지만 Cloneable 내부 코드를 보면 아무런 기능 정의가 없습니다. 이게 전부 다입니다.

- > #### `Cloneable` 인터페이스는 `Object` 의 `protected` 메서드인 `clone()`의 동작 방식을 결정합니다.
- `Cloneable`을 구현한 인스턴스에서 `clone()` 메서드를 호출하게 되면, 해당 객체를 복사한 객체를 반환하게 됩니다.
- 다른 인터페이스와 달라 사용하기 위해선 복잡한 구조를 이해 하고 있어야합니다.
- equals,hashcode 와 마찬가지로 규약이 있긴하지만, 매우 허술합니다.

### `clone()` 메서드 규약
1. `x.clone() != x` 는 참 이어야 한다.
2. `x.clone().getClass() == x.clone().getClass()` 도 참이어야 한다.
3. `x.clone().equals()` 는 참이어야하지만, 필수는 아니다.
- 위 규약을 보면 연쇄 생성자와 비슷하다고 생각됩니다.
- 이 말은 곧 `clone` 메서드로 생성자를 호출해 인스턴스를 반환 할 수 있다는 것입니다.
- 하지만 상속 관계에서 문제가 생길 수 있습니다.
```java
class A implements Cloneable {
    private int number;

    public A(int number) {
        this.number = number;
    }

    

}

class B extends A {
    private String name;

    public B(int number, String name) {
        super(number);
        this.name = name;
    }

    @Override
    public B clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```
- 위 코드처럼 상속관계에서 `B.clone()` 을 실행하게 되면 B 클래스를 복사해오길 원하지만, 실제로는 A 클래스를 복제하게 됩니다.
- 하위 클래스를 복제해오길 원한다면 아래 코드 처럼 수정하면 됩니다.
```java
@Override
public B clone() throws CloneNotSupportedException {
    return (B) super.clone();
}
```
- 이렇게 반환하면 clone을 사용하는 클라이언트는 추가적인 형변환을 하지 않아도 됩니다.
- 자바의 공변 변환 타이핑(covariant return typing)을 지원하기때문에, 이 방법이 권장되며 이렇게 써야 합니다.

### 가변 객체 `Clone()` 메서드 재정의

만약 클래스가 예시처럼 기본 타입이나 불변 객체를 참조하는게 아닌 가변 객체를 참조하고 있으면 문제가 생길 수 있습니다. 
모든 필드가 기본 타입이나 불변 객체일지라도 일련번호나 고유 ID 같은 필드는 변경하면서 복사하는게 좋습니다.

아래와 같은 Cloneable 전화번호부 클래스를 예시로 들자면.
```java
public class PhoneBook implements Cloneable{
    
    private Map<PhoneNumber, String> book = new HashMap<>();

    public void add(PhoneNumber pn, String str) {
        book.put(pn, str);
    }

    public void delete(PhoneNumber pn) {
        book.remove(pn);
    }

    @Override
    protected PhoneBook clone() throws CloneNotSupportedException {
        return (PhoneBook) super.clone();
    }
}

public class App {
    public static void main(String[] args) {
        PhoneNumber pn1 = new PhoneNumber(123,456,7890);
        PhoneNumber pn2 = new PhoneNumber(123,456,7891);
        PhoneNumber pn3 = new PhoneNumber(123,456,7892);

        PhoneBook book = new PhoneBook();
        book.add(pn1, "jilee");
        book.add(pn2, "bongu");
        book.add(pn3, "dongu");

        PhoneBook bookClone = book.clone();
        bookClone.delete(pn1);

        System.out.println(book.getBook().containsKey(pn1));      //false
    }
}
```

- PhoneBook 클래스에서 clone 메서드를 super.clone() 만 사용하게 된다면, PhoneBook 에 인스턴스는 복제가 가능합니다.
- 하지만 PhoneBook 내부에 `private Map<PhoneNumber, String> book = new HashMap<>();` 필드는 복제된 객체와 같은 참조를 쓰는 문제가 발생합니다.
- 따라서 복제한 인스턴스에서 데이터를 변경하게 된다면 원본 데이터도 같이 변경되어, 굉장히 위험합니다.
- clone 메서드는 사실상 생성자와 같이 인스턴스를 생성해 냅니다. 즉, 복제를 한다고 하면, 원본 객체에 아무런 영향이 없는 복제된 객체라는걸 보장해야 합니다.

- 문제를 해결하려면 아래와 같이 수정 할 수 있습니다.
```java
@Override
protected PhoneBook clone() throws CloneNotSupportedException {
    PhoneBook result = (PhoneBook) super.clone();
    
    HashMap<PhoneNumber, String> book = (HashMap<PhoneNumber, String>) this.book;
    result.book = (Map<PhoneNumber, String>) book.clone();
    return result;
}
```
- clone 대신 생성자를 사용하거나 참조하는 가변 객체에 대한 복사본을 복사 객체에 넣어주고 참조도 복제하여 반환해주는 방법입니다.
- 아쉽지만 이 방법에도 문제점이 있습니다.
- > 1. `final`을 사용하지 못합니다.
   > 2. 연결구조는 복제 할 수 없습니다.

## 요약
Cloneable의 문제를 되짚어보면, 새로운 인터페이스를 만들 때나 새로운 클래스를 만들 때 Cloneable을 확장하거나 구현해서는 안 된다.
Cloneable을 구현해야 한다면 클래스는 clone 메서드를 꼭 재정의해야한다.
재정의 할 때는 public으로 클래스 타입 반환하면서 가장 먼저 super.clone을 호출한 후 필요한 필드를 전부 적절히 수정한다.
일반적으로 적절한 수정은 그 객체 내부의 '깊은 구조'에 숨어있는 모든 가변 객체를 복사하고, 복제본의 객체 참조가 이 복사된 객체들을 가리키게 하는 것이다.
이러한 방식은 주로 clone을 재귀적으로 호출하여 구현하지만, 이 방법이 항상 최선인 것은 아니다.

Cloneable을 꼭 구현해야 하는 경우나 Cloneable을 구현한 클래스를 확장하는게 아니라면 복사 생성자와 복사 팩터리라는 더 나은 객체 복사 방식을 제공할 수 있다.

복사 생성자
복사 생성자는 단순히 자신과 같은 클래스의 인스턴스를 인수로 받는 생성자이며 복사 팩터리는 복사 생성자를 모방한 정적 팩터리 메서드이다.

public Yum(Yum yum) { ... };
복사 생성자 및 복사 팩터리는 Cloneable/clone 방식보다 나은 면이 많다.
Cloneable/clone은 언어 모순적이다. Cloneable/clone을 사용하면 생성자를 쓰지 않고 객체가 생성된다.
Cloneable/clone의 규약은 엉성하다.
Cloneable/clone은 정상적인 final 필드 용법과 충돌한다.
Cloneable/clone은 재정의에서 불필요한 검사 예외를 던진다.
Cloneable/clone은 재정의에서 형변환이 필요하다.

또한 복사 생성자를 기반으로 변환 생성자, 변환 팩터리도 지원한다.

public class Yum implements Tool {
...
}
public Yum(Tool yum) { ... };
변환 생성자 나 변환 팩터리를 이용하면 클라이언트는 원본의 구현 타입에 얽매이지 않고 복제본의 타입을 직접 선택할 수 있다.
예를들어 HashSet 객체 s를 TreeSet 타입으로 복제할 수 있다 new TreeSet<>(s). clone으로는 불가능!