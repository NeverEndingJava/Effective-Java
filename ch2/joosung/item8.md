
# finalizer와 cleaner 사용을 피하라

- cleaner (Java 8까지는 finalizer)는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자. 
- 물론 이런 경우라도 불확실성과 성능 저하에 주의해야 한다.

## 자바의 두 가지 객체 소멸자

### finalizer
- 예측할 수 없고 상황에 따라 위험할 수 있어서 일반적으로 불필요함.
- 오작동, 낮은 성능, 이식성 문제의 원인이 되기도 함.
- Java 9부터 deprecated API로 지정되어 `cleaner`를 대안으로 사용.

### cleaner
- finalized 보다는 덜 위험하지만 여전히 예측할 수 없고, 느리고, 일반적으로 불필요함

## 자바의 finallizer와 cleaner 부작용
1. 자바의 finalizer와 cleaner는 C++의 파괴자(destructor)와는 다른 개념이다. 자바에서 접근할 수 없게 된 객체는 가비지 컬렉터가 회수하고 프로그래머는 아무런 작업도 하지 않아도 된다. 만약 비메모리 자원을 회수해야 한다면 `try-with-resources`와 `try-finally`를 이용해 해결한다.
2. finalizer와 cleaner는 즉시 수행된다는 보장이 없다. 즉, finalizer와 cleaner로는 제때 실행되어야 하는 작업은 절대 할 수 없다.
    - 예를 들어, 파일 닫기와 같은 작업을 finalizer나 cleaner에게 맡기면 중대한 오류가 발생할 수 있다.
3. 프로그램의 생애주기와 상관 없는, 상태를 영구적으로 수정하는 작업에서는 절대 finalizer나 cleaner에 의존해서는 안된다.
    - 예를 들어 데이터베이스의 공유 자원의 lock 해제를 finalizer나 cleaner에게 맡기면 분산 시스템 전체가 서서히 멈출 수 있다.
4. finalizer 동작 중 발생한 예외는 무시되며, 처리할 작업이 남았더라도 그 순간 종료된다.
    - 보통의 경우엔 잡지 못한 예외가 쓰레드를 중단시키고 스택 추적 내역을 출력하지만, 같은 일이 finzlizer에서 일어난다면 경고조차 출력하지 않는다. (그나마 cleaner를 사용하는 라이브러리는 자신의 쓰레드를 통제하기 때문에 이러한 문제는 발생하지 않는다.)
5. finalizer와 cleaner은 심각한 성능 문제도 동반한다.
    - finalizer를 사용한 객체를 생성하고 파괴할 때 AutoCloseable 객체를 생성하고 try-with-resources로 닫는 것보다 시간이 수십 배 더 걸릴 수 있다.
6. finalizer를 사용한 클래스는 finalizer 공격에 노출되어 심각한 보안 문제를 일으킬 수 있다.
    - 생성자나 직렬화 과정(readObject, readResolve)에서 예외가 발생하면, 이 생성되다 만 객체가 악의적인 하위 클래스의 fializer가 수행될 수 있게 한다.
    - 이 finalizer는 정적 필드에서 자신의 참조를 할당하여 가비지 컬렉터가 수집하지 못하게 막을 수 있다. 이렇게 일그러진 객체가 만들어지고 나면, 이 객체의 메서드를 호출해 애초에 허용되지 않았을 작업을 수행하게 할 수도 있다.
    - 따라서, 객체 생성을 막으려면 생성자에서 예외를 던지는 것만으로 충분하지만, finalizer가 있다면 final 클래스로 만들어서 하위 클래스를 만들 수 없도록 하거나, final이 아닌 클래스는 아무일도 하지 않는 finalize 메서드를 만들고 final로 선언해야 한다.

   
### finalizer와 cleaner를 대신할 AutoCloseable

- Autocloseable을 구현하고, 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 호출하자.
  - 일반적으로 예외가 발생해도 제대로 종료되도록 try-with-resources를 사용해야 한다.

- 이 때, 각 인스턴스는 자신이 닫혔는지 추적할 수 있도록 `close 메서드`에서 이 객체가 더 이상 유효하지 않음을 필드에 기록하고, 다른 메서드는 이 필드를 검사해서 객체가 닫힌 후에 불렸다면 IllegalStateException을 던져야 한다.

### finalizer와 cleaner의 적절한 쓰임새
1. 자원의 소유자가 close 메서드를 호출하지 않는 것에 대비한 안전망 역할
   - 자바 라이브러리의 FileInputStream, FileOutputStream, ThreadPoolExecutor가 대표적으로 이런 예시에서 사용 중

2. 네이티브 피어(native peer)와 연결된 객체에서 사용
   > 네이티브 피어: 일반 자바 객체가 네이티브 메서드를 통해 기능을 위임한 네이티브 객체.
   - 자바 객체가 아니기 때문에 가비지 컬렉터에서는 이 존재를 알지 못함. 따라서 cleaner나 finalizer가 나서서 처리하기에 적당하다.
   - 단, 성능 저하를 감당할 수 없거나 네이티브 피어가 사용하는 자원을 즉시 회수해야 한다면 앞서 설명한 close 메서드를 사용해야 한다.