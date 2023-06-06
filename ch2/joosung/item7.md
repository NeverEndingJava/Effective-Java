# 다 쓴 객체 참조를 해제하라

- 자바는 가비지 컬렉터(GC)가 있어서 메모리 관리에 더 이상 신경 쓰지 않아도 된다고 생각할 수 있으나, 전혀 사실이 아니다.
- 사용하지 않는 객체를 참조하고 있는 경우가 있을 수 있으니 다 쓴 객체 참조를 해제하자

```java
package test;

import java.util.Arrays;
import java.util.EmptyStackException;

public class Stack {
	private Object[] elements;
	private int size = 0;
	private static final int DEFAULT_INITIAL_CAPACITY = 16;

	public Stack() {
		this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
	}

	public Stack(int size) {
		this.elements = new Object[size];
	}

	public void push(final Object e) {
		ensureCapacity();
		elements[size++] = e;
	}

	public Object pop() {
		if (size == 0) {
			return null;
		}
		return elements[--size];
	}

	public Object size() {
		return this.elements.length;
	}

	/**
	 * 원소를 위한 공간을 적어도 하나 이상 확보
	 * 배열 크기를 늘려야 할 때마다 2배씩 늘림
	 */
	private void ensureCapacity() {
		if (elements.length == size) {
			elements = Arrays.copyOf(elements, (size * 2) + 1);
		}
	}
}
```
- 위 코드는 특별한 문제가 없어 보이고, 테스트를 수행해도 통과할 것이다.
- 하지만, 스택이 커졌다가(push), 줄어들때(pop) 스택에서 꺼내진 객체들을 가비지 컬렉터가 회수하지 않는다.
- 스택이 해당 객체들의 다 쓴 참조를 여전히 가지고 있기 때문이다.
- 위 문제를 해결하려면 해당 참조를 다 썼을때 **null 처리**(참조 해제) 하면 된다.
    - 해당 Stack 클래스에서는 스택에서 꺼내질 때(pop), 각 원소의 참조가 더 이상 필요없음

```java
// 기존 pop 메서드
public Object pop() {
    if (size == 0) {
        throw new EmptyStackException();
    }
    return elements[--size];
}

// 수정 pop 메서드
public Object pop() {
    if (size == 0) {
        throw new EmptyStackException();
    }
    Object result = elements[--size];
    elements[size] = null;
    return result;
}
```

- 위와 같은 null 처리는 다른 이점도 있는데, null 처리한 참조를 실수로 사용하려하면 프로그램은 NPE를 던지며 종료하게 됨
- 프로그램 오류는 가능한 한 조기에 발견하는게 좋다.
- 객체 참조를 null 처리하는 일은 예외적인 경우여야 함

### Stack 클래스는 왜 메모리 누수에 취약한 걸까?
- 스택이 자기 자신의 메모리를 직접 관리하기 때문
- Stack 클래스는 배열(elements)로 저장소 풀을 만들어 원소들을 관리함
- 배열의 활성 영역부분에 속한 원소들은 사용되고, 비활성 영역은 쓰이지 않는데 문제점은 이러한 비활성 영역을 가비지 컬렉터가 알 방법이 없다는 것
- 보통 자신의 메모리를 직접 관리하는 클래스는 프로그래머가 항상 메모리 누수에 주의해야 함

### Java의 메모리 누수 예제
- Integer, Long 같은 래퍼(Wrapper) 클래스를 이용한 무의미한 객체 생성
- static field로 인한 메모리 누수
- stream과 같은 객체의 사용하고 자원을 해제하지 않는 경우
- 외부 클래스를 참조하는 내부 클래스
- finalize() 메서드
