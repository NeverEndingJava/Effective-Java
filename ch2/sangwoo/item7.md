## 다 쓴 객체 참조를 해제하라
---
자바는 GC가 있지만, 메모리 관리에도 신경을 써줘야 한다.

```java
public class Test {
	
    private static Object list = new Object[10];
    private int size = 0;
    
    public void push(Object e) {
    	list[size++] = e;
    }
    
    public Object pop() {
    	return list[--size];
    }
}
```

위와 같은 코드에서 list에 데이터를 넣었다가 pop()을 해줄 때 메모리 누수가 일어난다.
size만 줄이고 pop한 데이터를 따로 처리해주지 않았기 때문인데, 다 쓴 참조에 대해서는 해제를 해줘야 한다.
GC는 size만 가지고 활성 여부를 파악하는 Test 클래스의 list와 다르게 list내의 Object가 활성화되었는지 여부를 알 수 없기 때문이다!
```java
public Object pop() {
	Object res = list[--size];
    list[size] = null; // 참조 해제
    return res;
}
```

다 쓴 참조를 해제하는 가장 좋은 방법은 참조를 담은 변수를 유효범위 밖으로 밀어내는 것이다. (ex. 리스트에서 remove를 통해 인덱스를 줄이는 방식)

### 캐시
캐시 역시 메모리 누수를 일으키는 원인이다.
캐시가 사용되지 않음에도 놔두게 되는 경우다.

```java
Object key = new Object();
Object value = new Object();

Map<Object, Object> caches = new HashMap<>();
caches.put(key, value);
// key가 없어지면 캐시가 무의미해짐 -> 캐시의 대상이 되어야 함

Map<Object, Object> caches = new WeakHashMap<>();
caches.put(key, value);
// key가 없어지면. Map에 없어진 key가 gc 대상이 됨
```