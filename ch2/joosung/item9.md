# try-finally 보다는 try-with-resources를 사용하라.

- 코드는 더 짧아지고 분명해진다. 또한, 예외정보도 훨씬 유용하다.
- try-finally 보다 정확하고 쉽게 자원을 회수할 수 있다.

---
자바 라이브러리에서 close 메서드를 호출해 직접 닫아줘야하는 자원들이 있다.

- 예를 들어
    - InputStream
    - OutputStream
    - java.sql.Connection
  
- 이러한 자원들을 닫아주는 것은 클라이언트가 놓치기 쉬워 예측할 수 없는 성능 문제로 이어지기도 한다.

- 상당수가 안전망으로 finalizer를 활용하고는 있지만 아이템8에서 이야기하듯 사용하는 것이 좋지 않다.

- 예외는 try 블록과 finally 블록 모두에서 발생할 수 있다. 
- 그러면 스택추적 내역에서 첫 번째 예외의 정보는 남지 않게 되어 실제 시스템에서의 디버깅을 어렵게한다.

## try-with-resources

#### 이러한 문제들을 Java 7에서 나온 try-with-resources가 해결했다.

- try-with-resources 구조를 사용하기 위해선 `AutoCloseable` 인터페이스를 구현해야 한다.
    - 단순히 void를 반환하는 close 메서드 하나만 정의한 인터페이스
    - try-with-resoureces와 `AutoCloseable` 을 구현해놓은 클래스를 사용하면 try 문이 종료될 때 **자동으로 close를 호출**해준다.
  
- 닫아야 하는 자원을 가지는 클래스를 작성한다면 `AutoCloseable` 을 반드시 구현해야한다.

예제

- 자원이 하나인 경우

```java
static String firstLineOfFile(String path) throws Exception {
	try (BufferedReader br = new BufferedReader (
		new FileReader(path))) {
			return br.readLine();
	}
}
```

- 자원이 둘인 경우

```java
static void copy(String src, String det) throws IOException {
	try (InputStream in = new FileInputStream(src);
		OutputStream out = new FileOutputStream(dst)) {
		byte[] buf = new byte[BUFFER_SIZE];
		int n;
		while ((n == in.read(buf)) > = 0)
			out.write(buf, 0, n);
}
```

위의 예제를 보면 알겠지만, try-with-resources 버전이

- **짧고 읽기도 수월하다.**
- **문제를 진단하기도 좋다.**

`readLine` 과 `close` 에서 둘 다 예외가 발생한다고 하자.

- `close` 에서 발생한 예외는 숨겨지고 `readLine` 에서 발생한 예외가 기록된다.
    - `try-finally` 예제와 달리 `close` 예외에 덮어지지 않는다.
- 버려지지는 않고, **스택 추적 내역에 'suppressed (숨겨짐)'이 붙어 출력**된다.
- 또한, Java 7에서 `Throwable` - `getSuppressed` 메서드를 사용하면 프로그램 코드에서 가져올 수 있다.


### try-with-resources에서도 `catch` 절을 사용할 수 있다.

- try 문을 더 중첩하지 않고도 다수의 예외를 처리할 수 있다.

```java
static String firstLineOfFile(String path, String defaultVal) {
	try (BufferedReader br = new BufferedReader(
			new FileReader(path))) {
		return br.readLine();
	} catch (IOException e) {
		return defaultVal;
	}
}
```
