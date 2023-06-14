## Comparable을 구현할지 고려하라

---

### Comparable

- 이를 구현한 클래스의 인스턴스들에는 순서가 있음을 뜻함
- `Arrays.sort` 는 compareTo() 메소드를 호출해 사용
- TreeSet, LinkedHashSet과 같이 순서가 있는 자료구조에 사용

### compareTo를 구현하는 방식

1. 간단, 가독성이 떨어짐 + 오류를 유발할 수 있음 (왜?? -> 언더, 오버플로우??)

```java
public int compareTo(PhoneNumber pn) {
	int res = this.areaCode - pn.areaCode;
    if (res == 0)
    	res = this.prefix - pn.prefix;
        if (res == 0)
        	res = this.lineNum - pn.lineNum;
    return res;
}
```

2. 자바에서 제공하는 비교자를 사용 여전히 가독성 안좋음

```java
public int compareTo(PhoneNumber pn) {
	int res = Short.compare(areaCode, pn.areaCode);
    if (res == 0)
    	res = Short.compare(prefix, pn.prefix);
        if (res == 0)
        	res = Short.compare(lineNum, pn.lineNum);
}
```

3. 메서드 체이닝 방식으로 Comparator를 구성할 수 있게 되었다, 하지만 약간의 성능저하가 발생한다. (왜인지 아나..?)

```java
prlvate static final Comparator<PhoneNumber> COMPARATOR =
		comparinglnt((PhondMumberpn) ->pn.areaCode)
			.thenComparinglnt(pn->pn.prefix)
        	.thenComparinglnt(pn->pn.1indVum);

public int compareTO(PhoneNumber pn) {
	return COMPARATOR.compare(this, pn);
}
```

### Equal과 비교

- compareTo는 단순 동치성 비교 + 순서 비교 가능
- 제네릭하게 특정 타입을 정의해 사용할 수 있다.
- -1, 0, 1 로 순서를 비교. -1은 객체가 주어진 객체보다 작을 경우, 0은 같을 경우, 1은 클 경우다. => 0 을 반환한다면 Equal의 결과도 true가 나오도록 고려하라 (compareTo로 비교하는 클래스도 있고 equal로 비교하는 클래스도 있어서)
- 타입이 다른 객체가 주어지면, `ClassCastException` 발생

### 확장에 있어 유의사항

- 기존 클래스를 확장한 클래스에서 새로운 값이 추가되면 단순 비교에 의한 Comparable 이 일관되지 않을 수 있음.
- 독립된 클래스를 만들고 이 클래스에 원래 인스턴스를 가리키는 필드를 두고 내부 인스턴스를 반환하는 뷰 메서드를 두면 된다.
