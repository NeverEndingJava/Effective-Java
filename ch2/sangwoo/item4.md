## 인스턴스화를 막으려거든 private 생성자를 사용하라
---
정적 메소드나 정적 필드만 담은 클래스 (유틸리티 클래스) 는 인스턴스로 만들어 사용되는 클래스가 아니다. ex) `java.lang.Math`, `java.util.Arrays`


모든 메소드가 static으로 이루어진 유틸리티 클래스에서는 인스턴스화를 막아야 한다.

### 생성자를 만들지 않으면 되나??
- 생성자를 명시하지 않으면 자바 컴파일러가 자동으로 public 기본 생성자를 만든다.

### 추상 클래스로 만들면 되나??
- 추상클래스를 상속한 클래스에서 인스턴스를 만들 수 있다.
- 상속해서 사용하라는 뜻으로 유틸리티 클래스를 오해할 수 있다.

### private 생성자를 통해 인스턴스화를 막으면 되는구나!!