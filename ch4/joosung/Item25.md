# 톱레벨 클래스는 한 파일에 하나만 담으라

`요약`

- 한 소스 파일안에 톱레벨 클래스를 하나만 만들자.

- 사실 여러 톱레벨 클래스를 만들어도 이점이 없다.

- 터미널을 통해서 javac 명령어를 이용하지 않으면 인텔리제이는 어떤 순서든 컴파일을 해주지 않는다.

## 톱레벨 클래스란?
     
다음은 oracle 공식 페이지에 나온 톱레벨 클래스에 대한 설명이다.
```
A top level class is a class that is not a nested class.
```
톱 레벨 클래스는 **중첩 클래스가 아닌 클래스**이다.

즉 우리가 일반적으로 사용하는 단일 형태의 클래스를 말한다.

그렇다면 중첩 클래스는 무엇일까?
```
A nested class is any class whose declaration occurs within the body of another class or interface.
```
중첩 클래스란 다른 클래스/인터페이스 **내부에 선언된 클래스**를 뜻한다.

즉, 톱레벨 클래스란 중첩 클래스가 아닌 것을 말한다.

## 코드 구성

- 그렇다면 한 파일에 여러개의 톱레벨 클래스가 담긴 것은 무엇을 뜻할까?

- 바로 중첩되지 않은 클래스가 하나의 java 파일에 여러개 담긴 것을 뜻한다.

``` java
// Main.java
public class Main {

    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
}
```

``` java
// Utensil.java
class Utensil {
    static final String NAME = "pan";
}

class Dessert {
    static final String NAME = "cake";
}
```

``` java
// Dessert.java
class Utensil {
    static final String NAME = "pot";
}

class Dessert {
    static final String NAME = "pie";
}
```

### 컴파일 에러

`javac Main.java Dessert.java`

- 컴파일 에러가 각 클래스를 중복해서 정의했다고 알려준다.

그 이유는 컴파일러는 다음과 같은 순서로 작동했기 때문이다.

1. 먼저 `Main.java` 컴파일

2. 그 안에서 `System.out.println(Utensil.NAME + Dessert.NAME);` 구문을 만나기 때문에 `Utensil.java`를 컴파일

3. 2번째 인수로 넘어온 `Dessert.java`을 컴파일 하려고 할 때 같은 클래스가 이미 정의되어 있는 것을 알게된다.

## 해결책

### 톱레벨 클래스들을 서로 다른 소스 파일로 분리

- 한 개의 파일에 한 개의 톱레벨 클래스만 둔다.

``` java
// Utensil.java
public class Utensil {
    static final String NAME = "pan";
}
```
``` java
// Dessert.java
public class Dessert {
    static final String NAME = "cake";
}
```

### 톱레벨 클래스를 굳이 한 파일에 담고 싶을 때

- 정적 멤버 클래스 방식을 사용한다.

``` java
public class Main {

    public static void main(String[] args) {
        System.out.println(Utensil.NAME + Dessert.NAME);
    }
    
    private static class Utensil{
        static final String NAME = "pan";
    }
    
    private static class Dessert{
        static final String NAME = "caks";
    }
}
```