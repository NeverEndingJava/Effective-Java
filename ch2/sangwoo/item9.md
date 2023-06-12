## try-finally 보다는 try-with-resources를 사용하라
---
자바 라이브러리에서는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 많다. (FileInputStream, OutputStream ... )
전통적으로 자원을 닫기 위해 try-finally가 자주 쓰였다.
```java

BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
try {
	br.readLine();
} finally {
	br.close();
}
```
try-catch 블록이 끝난 뒤, 예외가 발생하더라고 finally 메소드가 실행되어 close를 해줄 수 있다.

하지만! close 해줘야 할 로직이 많아지면 코드가 많이 지저분해진다.
    
### try-with-resources
JAVA 7 버전부터 도입되었고, try-with-resources를 사용하기 위해서는 자원이 `AutoCloseable` 인터페이스를 구현해야 한다.
```java
try(BufferedReader br = new BufferedReader(new InputStreamReader(System.in))) {
	br.readLine();
}
```

추가로 try-finally, try에서 예외가 발생하고 finally에서도 예외가 발생할 경우 try블록의 예외는 무시되고 finally 블록만 catch한다. 반면 try-with-resources는 try에서 발생하는 예외가 숨겨지고 (Suppressed) getSuppressed 메서드를 통해 가져올 수 있다.