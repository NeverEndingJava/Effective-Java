## equals는 일반 규약을 지켜 재정의하라

---

> 물리적 동치성("==")
> 논리적 동치성("equals")

### 재정의하지 않는 것이 최선인 경우

1. 각 인스턴스가 본질적으로 고유한 경우

   - 값을 표현하는게 아닌 동작하는 개체를 표현하는 클래스(ex. Thread)

2. 인스턴스의 논리적 동치성을 검사할 일이 없을 때

   - 논리적 동치성 검사 : java.util.regex.Pattern equals메소드

3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 들어맞을 경우

   - Set의 구현체들은 모두 AbstractSet이 구현한 equals를 상속받아 쓴다.

4. 클래스가 private이거나 package-private(default)이고 equals메소드를 호출하는 일이 없을 경우
   - equals 자체를 호출하는 것을 막고 싶으면 다음과 같이 에러를 발생시키자

```java
@Override
public boolean equals (Object object) {
    throw new AssertionError();
}
```

### equals 메서드를 재정의 해야 하는 경우

- 논리적인 동치성을 확인하고 싶을 때, 상위 클래스에서 equals가 논리적 동치성을 비교하도록 되어 있지 않았을 때 재정의 한다.
- `Enum`과 같이 값이 같은 인스턴스가 둘 이상 만들어지지 않을 경우는 필요없음

### equals의 일반 규약

`세상에 혼자 존재하는 클래스는 없다. 수 많은 클래스는 자신에게 전달된 객체가 equals 규약을 지킨다 가정하고 동작하므로 잘 지켜야 한다`

1. 반사성

   - x.equals(x) == true
   - null이 아닌 모든 참조값 x에 대해 x.equals(x)를 만족해야 한다.(자기 자신과 같아야 함)

2. 대칭성
   - x.equals(y) == true && y.equals(x) == true
   - null이 아닌 모든 참조값 x, y에 대해 x.equals(y)가 true면 y.equals(x)는 true

```java

// name이 같으면 같은 인스턴스라 정의
@Override
public boolean equals(Object o) {
    if (o instanceof Member)
        return name.equalsIgnoreCase(((Member)o).name);
    if (o instanceof String)
        return name.equalsIgnoreCase((String)o);
    return false;
}

Member sangwoo1 = new Member("sangwoo", 24);
String name = "sangwoo";

// 대칭성을 위반함
sangwoo1.equals(name); // true;
name.equals(sangwoo1); // false;
```

3. 추이성

```java
x.equals(y) == true
y.equals(z) == true
x.equals(z) == true
```

학생이라는 객체가 있고 멤버를 상속받는 경우

```java
/* 멤버의 equals() */
@Override
public boolean equals(Object o) {
    if (!(o instanceof Member))
        return false;
    Member member = (Member)o;
    return name.equals(member.name) && age.equals(member.age);
}

/* 학생 클래스 */
public class Student extends Member {
    private String study;

    public Study(String name, Integer age, String study) {
        super(name, age);
        this.study = study;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Student))
            return false;
        return super.equals(o) && ((Student)o).study.equals(study);
    }
}

```

문제점
-Student equals에 Obejct로 Member가 들어오면 false를 반환 (대칭성이 깨짐)
-Member.equals(Study)를 하면 study 필드값을 검사하지 않아 true가 나올 수 있음

대칭성 문제 해결

```java
/* 학생 클래스 */
@Override
public boolean equals(Object o) {

    if (!(o instanceof Member))
        return false;

    if (!(o instanceof Student))
        return o.equals(this);

    return super.equals(o) && ((Student)o).study.equals(study);
}
```

대칭성은 해결하지만 추이성이 지켜지지 않음

```java
Member member = new Member("sangwoo", 24);
Student student = new Student("sangwoo", 24, "java");
Student student2 = new Student("sangwoo", 24, "python");

member.equals(student) // true;
member.equals(student2) // true;
student.equals(student2) // false;
```

> 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않음..!!

      - 상속 대신 컴포지션을 사용하거나 뷰 메서드를 Public으로 추가하면 된다고 함
      - public Member asMember() { return member };

4.일관성

- null이 아닌 모든 참조 값 x, y에 대해, x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.
- `java.net.URL`의 equals는 주어진 URL과 매핑된 호스트 IP주소를 이용해 비교하지만, 같은 도메인 주소라도 나오는 IP정보가 달라질 수 있다. -> equals는 메모리에 있는 객체만을 사용

5 non-null
null이 아닌 모든 참조 값 x에 대해, x.equals(null)은 false
equals()를 재정의 시 타입 검사를 하게 된다면 지켜지게 된다. (묵시적 null검사)

```java
@Override
public boolean equals(Object o) {
	if (!(o instanceof Member))
    	return false;

    ...
}
```

### equals메서드 구현 방법

1. == 연산자를 사용해 입력이 자기 자신인지 참조인지 확인
   - 성능최적화용이다. 비교상황이 복잡한 경우 굿!
2. instanceof연산자로 입력이 올바른 타입인지 확인
   - 묵시적 null체크 가능
3. 입력을 올바은 타입으로 형변환
   - instanceof 검사를 했기에 100% 성공
4. 입력 객체와 자기 자신에 대응되는 필드가 일치하는지 검사

```java
@Override
public boolean equals(Object o) {
	if (this == o)
    	return true;

	if (!(o instanceof Member))
    	return false;
    Member member = (Member) o;
    return member.name == name && member.age == age;
}
```

### 정리

- 꼭 필요한 경우가 아니면 재정의하지 마!
- 재정의 할거면 핵심필드를 비교하며 다섯가지 규약을 지켜
- 최상의 성능을 위해 필드값 비교 순서도 고려
- equals 매개변수 입력을 Object가 아닌 타입으로 선언하는 것은 재정의가 아니다!
