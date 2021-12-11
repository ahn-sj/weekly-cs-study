## 02차 - 21.11.30(화)

## 주제 : 컬렉션 프레임워크의 주요 인터페이스(List, Set, Map)

<br>

### 컬렉션 프레임워크(Collection Framework)
- 객체나 데이터를 효율적으로 다루기 위한(추가, 삭제, 검색, 저장) 클래스들의 집합 <br>
- 즉, 데이터를 저장하는 **자료 구조**와 데이터를 처리하는 **알고리즘**을 구조화하여 클래스로 구현해 놓은 것

<br>

### 컬렉션 프레임워크의 주요 **인터페이스**
- 인터페이스를 구분하는 건 "순서", "데이터 중복여부"로 구분

	1. List : 순서O, 중복O
	2. Set  : 순서X, 중복X
	3. Map  : 순서X, 중복(키X, 값O)

<br>

### 컬렉션 프레임워크의 계층도
![collections](https://user-images.githubusercontent.com/64416833/142761042-1796918b-4469-4d80-8773-21d3a6274fc6.jpg)

<br>

### Collection인터페이스의 주요 메서드
![collections_method](https://user-images.githubusercontent.com/64416833/142761433-e9ef6c4d-6e2c-4f9c-8f25-02f0e735e3a0.jpg)

<br>

### 컬렉션 **클래스**
- 컬렉션 프레임워크에 속하는 인터페이스를 구현한 클래스
	
1. List - **ArrayList, LinkedList**, Vector
2. Set - **HashSet, TreeSet**,
3. Map - **HashMap**, Hashtable, TreeMap

\*\*각 컬렉션 클래스별 메서드는 생략\*\*	

<br>

### 1. List 컬렉션 클래스<br>

1-1) ArrayList\<E\>

ArrayList의 특징은 다음과 같다<br>
1. List인터페이스를 구현한 것으로 **가변길이 배열**로 **저장순서가 유지**되고 **중복을 허용**한다
2. 기존의 Vector를 개선한 것으로 구현원리와 기능적으로 동일하지만  Vector(동기화O)는 ArrayList(동기화X)와 달리 동기화 처리가 되어 있다
3. ArrayList는 **배열 기반**이라 배열처럼 인덱스를 통한 접근이 가능하며 원소의 개수가 크기에 다다르면 자동으로 `initial Capacity`만큼 증가하기 때문에 추가적인 원소를 입력하더라도 `ArrayIndexOutOfBoundsException` 에러가 발생하지 않는다.

ArrayList의 생성자
1. ArrayList()<br>
기본 생성자로 initial Capacity가 10으로 설정되어 생성되는 생성자

2. ArrayList(Collection c)<br>
Collection 인터페이스를 구현한 클래스의 객체를 매개변수로 입력하고 입력받은 객체의 모든 원소로 ArrayList객체로 생성되는 생성자

3. ArrayList(int initialCapacity)<br>
initial Capacity값을 매개변수로 입력받아 그 크기만큼 ArrayList의 초기 수용량을 지정하는 생성자


```java
ArrayList list1 = new ArrayList(10);

list1.add(5);
list1.add(4);

// 위 코드는 컴파일러가 아래와 같이 오토박싱을 시켜준다
// list1.add(new Integer(5));
// list1.add(new Integer(4));
// 원래라면 객체만 담을 수 있기 때문에 기본형이 참조형으로 변환된다
```

```java
// list1:[0, 1, 2, 3, 4, 5]
list1.add(0,"1");
// list1:[1, 0, 1, 2, 3, 4, 5]

// indexOf()는 객체의 위치(인덱스)를 알려준다
// 위 add(0,"1")로 데이터를 추가한 건 문자열이라 indexOf("1")에서 0이 출력이 되고
// indexOf(1)에서 1은 객체(Integer)를 찾기 때문에 2(인덱스)를 출력하게 된다
System.out.println("index=" + list1.indexOf("1")); // 0
System.out.println("index=" + list1.indexOf("2")); // -1 -> 찾지 못함(문자열 "2"가 없음.)
System.out.println("index=" + list1.indexOf(1));   // 2
```

<br>


배열의 장단점

- 장점
    - 배열은 구조가 간단하고 데이터를 읽는데 걸리는(접근시간, access time)시간이 짧다
- 단점
    1. 프로그램이 실행 중에 배열의 크기를 변경할 수 없다.
        - 크기를 변경해야 하는 경우 새로운 배열을 생성 후 데이터를 복사하고 주소를 변경해줘야함
        - 크기 변경을 피하기 위해 충분히 큰 배열을 생성하면 메모리가 낭비됨
    2. 비순차적인 데이터의 추가, 삭제에 시간이 많이 걸린다
        - 데이터를 추가하거나 삭제하기 위해 다른 데이터를 옮겨야 함
        - 그러나 순차적인 데이터 추가(끝에 추가)와 삭제(끝부터 삭제)는 빠르다

<br>

1-2) LinkedList\<E\>
- 배열의 단점을 보완한 컬렉션
- 배열과 달리 LinkedList는 불연속적으로 존재하는 데이터를 연결(link)한 연결리스트이기 때문에 데이터의 접근성이 나쁘다
```java
class Node {
	Node next;  // 다음 노드
	Object obj; // 데이터
}
```

### ArrayList(배열기반, 연속적) vs LinkedList(연결기반, 불연속적)

1. 순차적으로 데이터 추가/삭제 - ArrayList가 빠름
2. 비순차적으로 데이터 추가/삭제 - LinkedList가 빠름
3. 접근시간(access time) - ArrayList가 빠름

<br>

---

<br>

### 2. Set 컬렉션 클래스<br>
2-1) HashSet\<E\>
- Set인터페이스를 구현한 대표적인 컬렉션 클래스
- 객체를 저장하기전에 기존에 같은 객체가 있는지 확인한다(중복허용 안하기 때문)

	ㄴ 같은 객체가 없으면 저장하고 있으면 저장하지 않는다.<br>
	ㄴ 객체를 저장하는 메서드인 `boolean add(Object o)` 메서드는 객체를 저장하기 전에 `equals()`와 `hashCode()`를 호출해서 같은 객체가 있는지 확인한다.
```java
class HashSetTest {
	public static void main(String[] args) {
		HashSet set = new HashSet();

		set.add("abc");
		set.add("abc");
		set.add(new Person("David",10));
		set.add(new Person("David",10));

		System.out.println(set);
	}
}

class Person {
	String name;
	int age;

	Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	public String toString() {
		return name +":"+ age;
	}
}
// [abc, David:10, David:10]
```

Set은 중복된 값을 가지면 안되지만 `new` 로 생성할 경우 동일한 값이 들어가게 된다.

정상적인 입력을 위해선 `equals()`와 `HashCode()`(Hash가 붙은 건 다 HashCode()를 사용함)를 오버라이딩 해주어야 한다.

```java
@Override
	public int hashCode() {
		return Objects.hash(age, name);
	}

@Override
public boolean equals(Object obj) {
	if (!(obj instanceof Person)) return false;

	Person p = (Person) obj;
	// 나자신(this)의 이름과 나이를 p와 비교
	return this.name.equals(p.name) && this.age == p.age;
}
```

<br>

2-2) TreeSet\<E\>
- Set 인터페이스를 구현한 클래스로써 추가와 삭제에는 시간이 조금 더 걸리지만 **정렬, 범위 검색**(10보다 크고 20보다 작은)에 높은 성능을 보이는 컬렉션 클래스
- HashSet과 달리 TreeSet은 NavigableSet 인터페이스를 기존의 이진 검색 트리의 성능을 향상시킨 레드-블랙 트리(Red-Black tree)로 이룬다.
- HashSet보다 데이터 추가, 삭제에 시간이 더 걸림
- 데이터 저장과정은 `boolean add(Object o)`로 추가하고 Set은 중복을 허용하지 않기 때문에 데이터 저장전에 1) `equals()`를 호출하고 2) `hashCode()`를 호출한다.
- 루트부터 트리를 따라 내려가며 값을 비교해서 작으면 왼쪽, 크면 오른쪽에 저장하기 때문에 정렬이 필요(자동으로 정렬)없다.
- 클래스를 매개변수로 담을 때는 비교기준(Comparable or Comparator)이 필요한데 이를 통해 정렬 방법을 정해줄 수 있다.

장점 : 정렬과 범위검색에 유리

단점 : 트리가 커질수록 데이터 추가, 삭제에 시간이 더 걸림

![treeset](https://user-images.githubusercontent.com/64416833/143771136-b4e8baa7-b1b9-4d8d-b396-fe02784c1783.jpg)

<br>

### 3. Map 컬렉션 클래스<br>
3-1) HashMap\<E\>
- Map인터페이스를 구현한 대표적인 컬렉션 클래스로 데이터를 키와 값의 쌍(키와 값은 모두 객체)으로 저장
- HashMap은 이름 그대로 해싱(hashing)기법을 이용하기 때문에 데이터가 많아도 검색이 빠르다.
- HashMap은 해시 함수를 통해 '키'와 '값'이 저장되는 위치를 결정한다.
- 값을 제거하기 위해서는 오직 키 값으로만 Map의 요소를 삭제할 수 있는데 `remove(key)`메서드로 키를 이용해서 값을 제거할 수 있다.

키(key) : 컬렉션 내의 키(key)중에서 유일해야 한다 -> 중복X<br>
값(value) : 키(key)와 달리 데이터의 중복을 허용한다 -> 중복O<br>
엔트리(entry) : 키와 쌍<br>

```java
get(key) // 특정 key값의 value를 가져온다
entrySet() // key와 value 모두 가져오는 경우에 사용
keySet() // key값만 필요한 경우에 사용
```

```java
class Entry {
	Object key;
	Object value;
}
```
```java
HashMap map = new HashMap();
map.put("myId", "1234");
map.put("asdf", "1111");
map.put("asdf", "1234");
//        키      값
// 위 코드는 두 개의 데이터만 저장됨
// 키의 값이 같은 경우에 에러가 발생하는 것이 아닌 키의 값을 새로운 값으로 초기화됨
```
```java
//new에서 타입 파라미터 생략가능
HashMap<Integer, String> map = new HashMap<>();
map.put(1,"사과");
map.put(2,"바나나");
map.put(3,"포도");
```

<br>