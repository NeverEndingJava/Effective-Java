
# 생성자 대신 정적 팩터리 메서드를 고려하라

## 요약

- 생성자를 통해서 인스턴스를 생성하는 것보다 정적 팩토리 메스드를 통해 제공하는 방식이 다양한 장점이 있다.

#### 장점

1.  이름을 가질 수 있다.

2.  호출될 때마다 인스턴스를 새로 생성하지않아도된다.

3.  반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

4.  입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

5.  정적팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

#### 단점

1. 상속을 하라면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.

2. 정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

---

일반적으로, 클래스의 인스턴스를 얻는 가장 흔한 방법은 new 연산자를 통해 public 생성자를 호출하는 것이다. 

하지만, 이러한 방식이 항상 최선의 선택이라고 할 수는 없다.

책에서는 정적 팩토리 메서드를 통한 인스턴스 생성을 권장하고 있다. 

정적 팩토리 메서드를 활용하는 것은 일반적인 public 생성자를 통한 인스턴스 생성보다 다양한 장점을 제공한다. 

지금부터 정적 팩토리 메서드의 사용법과 그 장점에 대해 자세히 살펴보고자 한다.

## 정적 팩터리 메서드란 무엇인가?

- 정적 팩터리 메서드(Static Factory Method)는 인스턴스 생성을 담당하는 정적 메서드를 말한다. 이 메서드를 사용하여 클라이언트는 생성자를 직접 호출하지 않고, 해당 메서드를 통해 객체를 생성할 수 있다.

예를 들어, Boolean 클래스의 정적 팩터리 메서드 `valueOf(boolean)`는 다음과 같이 사용된다.

```java
Boolean flag = Boolean.valueOf(true);
```

그렇다면 왜 사용하는 것인지?, 어떤 장점, 단점이 있는지 좀 더 자세히 살펴보자

## 장점

### 1. 이름을 가질 수 있다.

생성자에 넘기는 매개변수와 생성자 자체로 반환될 **객체의 특성을 잘 설명하지 못하는 경우** 정적 팩터리 메서드를 사용하면 반환될 **객체의 특성을 명확하게 나타낼 수 있다.**

또한 생성자는 메서드 시그니처의 제약이 존재하는 반면에 정적 팩터리 메서드에는 제약이없다.

### 2. 호출될 때마다 인스턴스를 새로 생성하지 않아도 된다.

불변(immutable) 클래스 는 인스턴스를 만들어놓거나 새로 생성한 인스턴스를 캐싱하여 재활용 하는 식으로 **불필요한 객체생성을 피할 수 있다.**

불필요한 객체의 생성을 피할 수 있다. -> 객체가 자주 요청되는 상황이라면 성능을 향상시킬 수 있다.

### 3. 반환 타입의 하위 타입 객체를 반환할 수 있는 능력이 있다.

반환할 객체의 클래스를 자유롭게 선택할 수 있다. -> 유연성 증가

유연함이 생김에 따라 어떠한 API를 사용하기위해 프로그래머가 익혀야 하는 개념의 수와 난이도가 낮아진다.


### 4. 입력 매개변수에 따라 매번 다른 클래스의 객체를 반환할 수 있다.

위의 장점의 연장선이라고 볼 수 있다. 반환 타입의 하위 타입이기만 하면 어떤 클래스의 객체를 반환할 수 있게 된다.

### 5. 정적팩터리 메서드를 작성하는 시점에는 반환할 객체의 클래스가 존재하지 않아도 된다.

이 또한 위의 장점의 연장선이라고 볼 수 있는데 반환 타입을 인터페이스로 두면 구현 클래스가 없더라도 작성 시점에는 문제가 없다.


#### 3,4,5 장점은 비슷한데 결국 생성자를 통해 생성하는 것은 고정적인데 반해 정적 팩터리 메서드를 통해 생성하면 반환하는 인스턴스와 타입에 대한 유연성을 가질 수 있게 된다는 의미이다.


## 단점

### 1. 상속을 하라면 public이나 protected 생성자가 필요하니 정적 팩터리 메서드만 제공하면 하위 클래스를 만들 수 없다.

해당 단점은 불변 타입으로 만들려면 이 제약을 지켜야 한다는 점이 오히려 장점이으로 받아 들일 수도 있다.

### 2.정적 팩터리 메서드는 프로그래머가 찾기 어렵다.

생성자 처럼 API 설명에 명확히 드러나지 않기 때문에 개발자는 클래스를 인스턴스화 할 방법을 직접 찾아야한다.

해당 문제는 API 문서를 잘 작성하고 **메서드 이름도 널리 알려진 규약에** 따라 짓는 식으로 어느정도 완화가 할 수 있다.

### 정적 팩토리에서 흔히 사용하는 명명 방식.

| **명명 규칙**                  | **설명**                                                                                               |
| ------------------------------ |------------------------------------------------------------------------------------------------------|
| **from()**                     | 매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메소드.                                                              |
| **of()**                       | 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메소드.                                                               |
| **valueOf()**                  | from 과 of 의 더 자세한 버전                                                                                 |
| **instance()  getInstance()**  | (매개 변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만 같은 인스턴스임을 보장하지는 않는다.                                             |
| **create()** **newInstance()** | instance 혹은 getInstance와 같지만 매번 새로운 인스턴스를 생성해 반환함을 보장한다.                                             |
| **getType()**                  | getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩토리 메소드를 정의할 때 쓴다.                                             |
| **newType()**                  | newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩토리 메소드를 정의할 때 쓴다.                                             |
| **type()**                     | getType과 newType의 간결한 버전                                                                             |


### 나의 생각

지난 프로젝트에서 별 생각 없이 정적 팩토리 메서드를 남들이 하는 걸 보고 따라했었다. 그 당시에는 그냥 이렇게 하는게 좋으니까 라고 생각하고 넘어갔다. Collections 라이브러리에서도 제공하다 보니 신뢰가 더욱 생겼다. 이번 기회에 이펙티브 자바를 읽게 되면서 왜 이렇게 했고, 무슨 장점이 있고, 이래서 그 전에 이런식으로 사용했구나 하는 깨달음을 많이 얻었다. 그런 한편, 내가 얼마나 생각 없이 사용했었는지도 반성하게 되었다. 앞으론 깊이 생각하면서 생성자를 남발하기 보다는 상황에 맞게 정적 팩토리 메서드를 사용할 수 있도록 해야겠다.