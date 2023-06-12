## finalizer와 cleaner 사용을 피하라
---
finalizer와 cleaner 둘 다 객체 소멸자 역할을 한다.
하지만 사용하지 않는 것이 권장된다. GC의 대상이 되지만 바로 수거해가는게 아니다.

- 즉시 수행된다는 보장이 없다.
	- 파일 리소스를 반납해야 하는 경우 파일 리소스를 언제 반납할지 모르고, 반납되지 않으면 새로운 파일을 열 수 없는 문제 발생할 수 있음
    
- 빠르게 실행될지 알 수 없다.
	- GC 알고리즘에 따라 다르고, 현재 JVM에서는 완벽히 동작해도 고객의 시스템에서는 안그럴 수 있다...
- 우선순위가 낮아 실행될 기회를 얻지 못할 수 있다.
- 수행 여부를 보장하지 않아 실행되지 않을 수 있다.
- finalizer는 동작중 발생한 예외를 무시하고 처리할 작업이 있더라도 그 순간 작업이 종료되어 예외를 잡지 못하고, 경고도 출력하지 않는다.


### AutoCloseable을 구현하고 close 메소드를 호출하자
```java
public class Test implements AutoCloseable {
	@Override
    public void close() {
    	...
    }
}
```
try-finally나 try-with-resource 를 사용해 자원을 종료시킬 수 있다.
```java
// AutoCloseable 인터페이스를 구현하고 있는 자원에 대해 try-with-resources를 적용 가능

try(FileInputStream fs = new FileInputStream("a.txt"); BufferedInputStream bis = new BufferedInputStream(fs)) {

	int data;
    while ((data = bis.read() != -1) {
    	System.out.print(data);
    }

}
```

### 그래도 finalizer와 cleaner를 쓸 곳!
1. 안전망 역할로 자원을 반납하려 할 때
	- 자원의 소유자가 close메소드를 호출하지 않는 것에 대비하여 쓸 수 있다. (안하는 것보단 낫자나!)
2. 네이티브 자원을 정리할 때
	- 네이티브 피어와 연결된 객체에서 쓸 수 있다.
    - 네이티브 피어란 일반 자바 객체가 네이티브 메서드를 통해 기능을 위임한 네이티브 객체로 GC 회수 대상이 아니다. (C나 C++로 작성된 것)


<br>

?? 내부 클래스는 왜 쓰고 어떤점이 좋은거지 ??