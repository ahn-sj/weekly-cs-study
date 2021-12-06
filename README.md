# weekly-cs-study

## cobak-study
### 스터디 진행 방식 (Google Meet, 매주 화요일 22:00~23:00)
#### 주제 선정 -> 준비 -> 발표 -> 질의응답

<br>

---

<br>

## 01차 - 21.11.16(화)
## 주제 : ROWNUM (Oracle)

<br>

### **ROWNUM의 정의는 아래와 같다.**
1. 쿼리의 결과에 가상으로 행번호를 1부터 부여한 가상컬럼(슈도컬럼, Psuedo Column)
2. 어떤 테이블 내에 물리적으로 저장되어 있는 컬럼이 아니라 SYSDATE나 USER 등과 같이 모든 SQL에 그대로 삽입해서 사용할 수 있는 가상 컬럼(Pseudo Column) <br>

Ex) 오라클에서 사용 가능하고 주로 페이징처리, TOP-N Query 등에서 사용

<br>

---

<br>

### **ROWNUM의 사용은 아래와 같다.**
```sql
-- 기본적인 쿼리
SELECT *
FROM EMP
WHERE ROWNUM <= 10;
```

```sql
-- 기존 테이블 데이터
23	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
22	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
...(생략)
2	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
1	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
```

```sql
-- 기본적인 쿼리 결과
1	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
2	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
...(생략)
9	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
10	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
```

<br>

---

<br>

`ROWNUM`의 순번 할당은 WHERE절이 처리된 이후에 할당된다.<br>
ㄴ 이 말은 즉, 쿼리에 정렬(`order by`)/그룹 조건(`group by`)이 있다면 ROWNUM이 먼저 실행되어 원하는 데이터를 추출 할 수 없다는 말과 같다

<br>

### **SELECT문 쿼리 실행 순서**
SQL 쿼리문을 작성할때 사용되는 WHERE, GROUP BY, ORDER BY 절과 같은 구문을 실행할 때 실행 순서가 존재한다.

쿼리 구문은 총 6가지가 존재한다.

1. SELECT
2. FROM
3. WHERE
4. GROUP BY
5. HAVING
6. ORDER BY

위 구문들은 쿼리에서 아래와 같은 순서로 작동한다

**FROM -> WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY**

<br>

---

<br>

### **ROWNUM 주의사항**
위의 쿼리에서 WHERE절에 바로 ROWNUM조건을 `BETWEEN 6 AND 10`과 같은 조건은 불가능하다.

<br>

#### 위 WHERE절에 조건을 저렇게 주면 데이터 추출이 불가능한 이유는 아래와 같다.

- **ROWNUM은 첫 번째 값이 할당된 이후부터 증가한다.**<br>

    예시 코드) `SELECT * FROM EMP WHERE ROWNUM > 1` <br>
    ㄴ 위 쿼리는 아무런 데이터도 추출되지 않는다.
           
    이유를 설명하자면 다음과 같다.<br>
    - 1)쿼리를 실행하면 첫번째 행을 가져오게 되고 ROWNUM은 1이다.
    - 2)그러나, WHERE절에 의해 조건이 맞지 않기에 추출되지 않는다.
    - 3)첫번째 행이 조건에 맞지 않기 때문에 첫번째 행은 버려지고 두번째 행을 가져오는데 첫번째 행에서 ROWNUM이 할당되지 않았기 때문에 두번째 행의 ROWNUM도 1이 된다.<br>
    - 5)그러나, WHERE절에 의해 조건이 맞지 않기에 두번째 행도 추출되지 않는다.
    
    <br>

    결론적으로, 이러한 이유들로 ROWNUM은 첫번째 값이 할당되지 않으면 어떠한 데이터도 추출되지 않는다.  
    ( 1이 있어야 2가 있고 2가 있어야 3이 있을 수 있는 속성을 가짐 )<br>

<br>

- **그러나, ROWNUM이 1보다 큰 값을 추출하고 싶다면 서브쿼리를 이용하면 된다.**<br>
 ㄴ 단, **반.드.시.** 별칭을 붙여 사용해야 한다.
 <br><br>
    예시로, 1-10까지의 게시글이 1페이지, 11-20까지의 게시글이 2페이지, ... 이러한 경우에 사용된다.<br>
    
    ```sql
    -- 서브쿼리를 이용한 ROWNUM이 1보다 큰 데이터 추출 쿼리
    SELECT * 
    FROM (SELECT ROWNUM RN, E.*
          FROM EMP E)
    WHERE RN BETWEEN 11 AND 20;
    ```

    ```sql
    -- 서브쿼리를 이용한 ROWNUM 출력
    10	10	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
    11	11	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
    12	12	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
    ...(생략)
    18	18	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
    19	19	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
    20	20	테스트 제목	테스트 내용	user00	21/11/14	21/11/14
    ```

    위 쿼리처럼 서브쿼리를 이용하면 가상컬럼인 `ROWNUM`을 실제 컬럼으로 만들어 처리하게 되면 서브쿼리가 만들어질 때 실제 컬럼을 가지기 때문에 1보다 큰 ROWNUM의 데이터를 추출할 수 있게 된다.
    
    <br>

    
    ```sql
    -- 잘못된 쿼리 (원하는 데이터 추출 불가)
    -- 먼저 5개의 행을 추출한 후, 추출된 데이터들로 정렬 수행
    SELECT * 
    FROM EMP 
    WHERE ROWNUM <= 5 
    ORDER BY SAL DESC;
    ```
    ```sql
    -- 정상 쿼리 (원하는 데이터 추출 가능)
    -- 전체 EMP테이블을 조회하고 정렬한 결과에서 상위 5개 추출
    SELECT *
    FROM (SELECT ROWNUM RN, E.* 
          FROM EMP E
          ORDER BY SAL DESC)
    WHERE RN <= 5;
    ```

<br>

데이터 하나만 가져오는 경우<br>
ㄴ `ROWNUM = 1`은 데이터 추출이 가능하지만 `ROWNUM = 2`는 데이터가 추출되지 않는다

데이터 여러개를 가져오는 경우<br>
ㄴ WHERE절에 `ROWNUM < 10`은 가능하지만 `ROWNUM > 10`은 데이터가 추출되지 않는다. <br>

<br>

[참고자료1] https://happyonion.tistory.com/70 <br>
[참고자료2] https://turing0809.tistory.com/48 <br>

<br><br>

### **1차 피드백 (완료)**
- MySQL에서 ROWNUM 사용

MySQL에는 Oracle의 번호를 매겨주는 ROWNUM함수가 존재하지 않아서 ROWNUM을 구현해야 한다.

총 3가지의 방법이 존재한다.
1. SET구문을 사용하여 초기화(쿼리문과 분리)
2. **FROM절에서 초기화(SET구문 사용X)** 
3. WHERE절에서 초기화(SET구문 사용X)

<br>

모든 결과값은 아래 테이블을 기반으로 한다

![table](https://user-images.githubusercontent.com/64416833/142760118-a93fa7c3-5196-4c3e-b56e-2b90580945e5.jpg)


<br>

```sql
-- 1. SET구문을 사용하여 초기화
SET @ROWNUM:=0;

SELECT @ROWNUM:=@ROWNUM+1 tb.*
FROM TEST_BOARD tb;
```
![table_img](https://user-images.githubusercontent.com/64416833/142760787-3fc6ab61-b941-42f2-affa-dec4d44ca0c8.jpg)


```sql
-- 2. FROM절에서 초기화
SELECT @ROWNUM:=@ROWNUM+1 ROWNUM, tb.*
FROM TEST_BOARD tb, (SELECT @ROWNUM:=0) R
ORDER BY BNO DESC;
```
![table_img2](https://user-images.githubusercontent.com/64416833/142760785-c801b8dc-f216-401e-9c96-70d64cfd348a.jpg)

```sql
-- 2번 응용
-- ROWNUM을 10까지만 출력
SELECT * 
FROM (SELECT @ROWNUM:=@ROWNUM+1 ROWNUM, tb.*
	FROM TEST_BOARD tb, (SELECT @ROWNUM := 0) R
	ORDER BY BNO DESC) TMP
WHERE ROWNUM <= 10;
```
![table_img3](https://user-images.githubusercontent.com/64416833/142760786-861bfeb3-8b2f-4774-86dc-0249ac1f963d.jpg)

만약, `2번`과 `2번 응용`에서 `table alias`를 주지 않게 되면 아래와 같은 에러를 발생시킨다. <br>
`Error Code: 1248. Every derived table must have its own alias`<br>
이러한 이유로 MySQL에서 ROWNUM을 인라인 뷰에서 사용할 때에는 별칭을 주어야 한다.<br>

<br>

```sql
-- 3. WHERE절에서 초기화
SELECT @ROWNUM:=@ROWNUM+1 ROWNUM, tb.*
FROM TEST_BOARD tb
WHERE (@ROWNUM:=0)=0
ORDER BY BNO DESC;
```
![table_img2](https://user-images.githubusercontent.com/64416833/142760785-c801b8dc-f216-401e-9c96-70d64cfd348a.jpg)

<br>

---

<br>

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

---

<br>

## 03차 - 21.12.07(화)
## 주제 : 래퍼클래스와 오토박싱, 언박싱 그리고 HashSet

<br>

### 진행 순서

    래퍼클래스와 오토박싱, 언박싱 그리고 HashSet
        |-- 래퍼클래스와 오토박싱, 언박싱
        |-- HashSet 동작원리
            |-- HashSet의 출력이 고정된 것 같아
                |-- 중간 정리 - 1
                |-- 그렇다면 데이터는 어떻게 저장되는가 - 1
                |-- 그렇다면 데이터는 어떻게 저장되는가 - 2
                |-- 지금까지 과정 중 궁금한 점
                |-- 그렇다면 데이터는 어떻게 저장되는가 - 3
                |-- 중간 정리 - 2
                |-- Example 1) 데이터가 12개 이하
                |-- Example 2) 데이터가 13개 이상
                |-- 최종 정리
            |-- equals(), hashCode()


<br>

## 03-1) 래퍼클래스와 오토박싱, 언박싱

- 자바의 자료형
    - 기본 타입(primitive type)
        - int, short, long, float, double, char, boolean
    - 참조 타입(reference type)
        - Array, String, class, ...

<br>

- 래퍼 클래스(Wrapper class)
    - 자바의 기본 타입을 객체로 다루기 위해 사용하는 클래스들

    <br>

    래퍼클래스의 상속 계층도

    ![wrapper2](https://user-images.githubusercontent.com/64416833/144604298-bfcb5194-014b-4c88-8c4d-a3cde7bff770.jpg)

    <img src="https://user-images.githubusercontent.com/64416833/144602692-1a82264f-ce9b-4d8b-a4a8-008280273b74.jpg" width="620">

<br>

- 래퍼클래스를 사용하는 이유는??
    1. 래퍼 클래스는 기본 타입을 Object로 변환할 수 있다. 이를 통해, 지네릭스(Generics)에서도 사용할 수 있다. <br> `ex) HashMap<Integer, String> hashMap = new HashMap<>();`

    2. 래퍼 클래스는 java.lang패키지에 포함되어 있어서 패키지에 있는 메서드들을 사용할 수 있다.<br>
    `ex) .equals(), .toString(), getClass, ... `

    3. ArrayList등과 같은 Collection 프레임워크의 데이터 구조는 기본 타입이 아닌 객체만 저장 가능하기 때문에 래퍼클래스를 이용해서 사용할 수 있다.<br> `ex) boxing`, `unboxing`

    4. 메서드에 전달된 인수를 수정하려는 경우 Object가 필요하다 ~~(Call by reference와 유사)~~ <br>
    `ex) public void printObj(Object obj) { ... }`

<br>

- 박싱(Boxing)과 언박싱(UnBoxing)
    1. 박싱(Boxing)은 기본 타입을 래퍼 클래스로 변환하는 과정
    2. 언박싱(UnBoxing)은 래퍼 클래스를 기본 타입으로 변환하는 과정
        ```java
        Integer num = new Integer(17); // 박싱 
        int n = num.intValue();        // 언박싱
        ```

        <img src="https://user-images.githubusercontent.com/64416833/144611300-bc413aaf-444c-4435-b97e-5e146e8024be.jpg" width="400">
<br>

- 오토박싱(AutoBoxing)과 오토언박싱(AutoUnBoxing)
    1. 오토박싱은 기본 타입을 직접 박싱, 언박싱하지 않아도 자동으로 박싱/언박싱을 해주는 기능
    2. 오토박싱과 오토언방식은 `기본 타입 -> 래퍼 클래스 타입` 또는 `래퍼 클래스 타입 -> 기본 타입`에 값을 넣는 경우에 발생한다.
        ```java
        // AutoBoxing
        // Integer num = new Integer(17);
        Integer num = 17; // 오토박싱 
        
        // UnBoxing
        // int num = num.intValue();
        int n = num;        // 언박싱        
        ```    


<br>

- 래퍼클래스 값 비교
    ```java
    Integer num1 = new Integer(10); //래퍼 클래스1
    Integer num2 = new Integer(10); //래퍼 클래스2

    int i = 10; //기본타입
            
    System.out.println("래퍼클래스 == 기본타입 : " + (num1 == i));              // true ★★
    System.out.println("래퍼클래스 == 기본타입 : " + (num2 == i));              // true ★★
    System.out.println("래퍼클래스 == 래퍼클래스 : " + (num1 == num2));         // false
    System.out.println("래퍼클래스.equals(기본타입) : " + num1.equals(i));      // true
    System.out.println("래퍼클래스.equals(래퍼클래스) : " + num1.equals(num2)); // true
    ```

    래퍼 클래스와 기본 자료형의 비교는 `== 연산`과 `equals연산` 모두 가능하다. <br>
    그 이유는 컴파일러가 자동으로 오토박싱과 언박싱을 해주기 때문입니다.

<br><br>

## 03-2) HashSet

- Set 인터페이스의 구현 클래스로 `순서가 유지되지 않고 중복 값을 자동으로 제거`한다.

<br>

## HashSet에서 다룰 점 - 동작원리

1. HashSet의 출력이 고정된 것 같아 - [본문 이동](https://ecsimsw.tistory.com/entry/%EC%9E%91%EC%84%B1-%EC%A4%91-%EC%9E%90%EB%B0%94-HashSet%EC%9D%98-%EC%9B%90%EB%A6%AC)

    ```java
    import java.util.HashSet;

    public class HashSetExample1 {
        public static void main(String[] args) {
            HashSet<String> hashSet = new HashSet<>();
            hashSet.add("1");        hashSet.add("2");        hashSet.add("3");
            hashSet.add("4");        hashSet.add("5");        hashSet.add("6");
            hashSet.add("7");        hashSet.add("8");        hashSet.add("9");
            hashSet.add("10");        hashSet.add("11");
    
            for (String str : hashSet) {
                System.out.print(str + " ");
            }
        }
    }
    ```
    ```
    // 결과
    11 1 2 3 4 5 6 7 8 9 10
    ```

    <br>

    ---

    <br>

    ### HashSet은 HashMap으로 동작한다.

    `Map`은 **Key와 Value의 쌍으로 값이 저장되는 형태**이며 **저장 순서와 출력 순서를 보장하지 않는 데이터 구조**이다.

    <br>

    `HashSet`은 `HashMap`으로 동작하는데 아래 코드는 `HashSet.class`의 일부이다.

    ```java
    public class HashSet<E> {
        private transient HashMap<E,Object> map;
        private static final Object PRESENT = new Object();
    
        public HashSet() {
            map = new HashMap<>();
        }

        public boolean add(E e) {
            return map.put(e, PRESENT)==null;
        }

        public boolean remove(Object o) {
            return map.remove(o)==PRESENT;
        }
    }        
    ```    


    위 코드를 통해 `HashSet`은 `HashMap`으로 구현되어 있고, 값을 저장할 때 `Key Object`에 `e`의 값이 들어가면서 `Key Object`에 저장할 객체가 저장되고, `Value Object`에는 `dummy data`가 저장되게 된다.

    이것이 가능한 이유는 `Map의 Key는 중복된 값이 들어가지 못한다.`와  `Set에 중복된 값이 들어가지 못한다.`라는 공통된 특징을 가지기때문에 `HashSet은 HashMap으로 동작되지만 중복된 값이 저장되지 않는다.`라는 것을 증명할 수 있습니다.
    
    <br><br>

    ### HashMap과 HashSet은 데이터 추가 전에 equals()와 hashCode()가 실행된다.
    자바에는 객체를 식별할 수 있는 `hashCode`메서드가 존재하는데 `hashCode`메서드는 기본적으로 `객체의 주소를 이용해서 객체의 고유한 값을 정수로 만들어 반환하는 메서드`이다.

    <br>

    Q > 그렇다면 `HashMap`과 `HashSet`은 데이터 추가시에 `hashCode`와 `equals`가 사용되는데 `hashCode`메서드는 **객체의 고유한 값을 정수로 만들어 반환하는 메서드**니까 데이터가 저장될 때 hashCode가 작은 순서대로 저장되는 것인가? <br>
    A > NO. 

    ```java
    import java.util.HashSet;

    public class HashSetExample1 {
        public static void main(String[] args) {
            HashSet<String> hashSet = new HashSet<>();
            
            hashSet.add("1");        hashSet.add("2");        hashSet.add("3");
            hashSet.add("4");        hashSet.add("5");        hashSet.add("6");
            hashSet.add("7");        hashSet.add("8");        hashSet.add("9");
            hashSet.add("10");        hashSet.add("11");
            
            for (String str : hashSet) {
                System.out.println("key > " + str + ", hashCode > " + str.hashCode());
            }
        }
    }
    ```
    ```
    key > 11, hashCode > 1568
    key > 1, hashCode > 49
    key > 2, hashCode > 50
    key > 3, hashCode > 51
    key > 4, hashCode > 52
    key > 5, hashCode > 53
    key > 6, hashCode > 54
    key > 7, hashCode > 55
    key > 8, hashCode > 56
    key > 9, hashCode > 57
    key > 10, hashCode > 1567
    ```

    <br><br>

    ### 중간 정리 - 1
    지금까지의 내용들을 정리해보자면,
    1. HashSet은 HashMap으로 구현되어 있고, HashSet에 데이터가 저장될 때는 HashMap의 Key에 저장된다.
    2. HashSet과 HashMap은 데이터 추가 전에 equals()와 hashCode()가 실행된다.
    3. HashSet과 HashMap은 데이터 추가 시에 hashCode()가 실행되지만 hashCode값이 작은 순으로 데이터가 저장되는 것은 아니다.

    <br><br>


    ### 그렇다면 데이터는 어떻게 저장되는가 - 1
    - `HashMap`과 `HashSet`은 **데이터 접근의 시간복잡도가** `O(1)` **인 자료구조이다.** <br>
    - `HashMap`과 `HashSet`은 **데이터를 저장할 때 배열을 사용한다.**<br>

    <br>

    `HashMap`과 `HashSet`을 기본적으로 생성하게 되면 **'디폴트 초기 용량(=배열의 크기라고 생각)'이 16으로 지정**된다. <br>
    이말은 즉, 16개의 데이터가 들어갈 수 있는 배열이 생성된다는 것을 의미한다. <br>

    \*\*이와 관련된 내용들을 찾아보다보면 `initial capacity`를 `'버킷의 크기'`라고 불리는데 `버킷`과 `배열`은 같은 말이다.** 

    <br>
    
    위 내용에 대한 부분은 **Java 8 Document**에 `HashMap`과 `HashSet`의 기본 생성자를 보면 확인할 수 있습니다.

    `public HashMap()`

        Constructs an empty HashMap with the default initial capacity (16) and the default load factor (0.75).

        파파고 번역 :: 기본 초기 용량(16)과 기본 로드 팩터(0.75)를 사용하여 빈 해시맵을 구성합니다.

    `public HashSet()`
    
        Constructs a new, empty set; the backing HashMap instance has default initial capacity (16) and load factor (0.75).

        파파고 번역 :: 비어 있는 새 집합을 구성합니다. 백업 해시맵 인스턴스는 기본 초기 용량(16)과 로드 팩터(0.75)를 갖습니다.
    위 내용 중 `default initial capacity (16)`에 대한 내용이 위에서 말한 디폴트 버킷 사이즈. 즉, 배열의 크기이다. 

    <br>

    HashMap 생성자
    ![image](https://user-images.githubusercontent.com/64416833/144833395-a772b4d9-3c18-488a-869e-689629f3fbf3.png)

    <br>
    
    HashSet 생성자
    ![hashset2](https://user-images.githubusercontent.com/64416833/144828014-e80cb014-9523-41b1-8181-8753a1275e11.jpg)

    <br>

    ### 그렇다면 데이터는 어떻게 저장되는가 - 2
    그러나,, `HashSet`과 `HashMap`은 데이터를 저장시 한 가지 작업이 추가로 더 필요하다.
    <br>

    그 작업은 `hashCode를 배열의 크기로 나눈 나머지를 배열의 index로 사용한다.`<br><br>

    
    `key > 11, hashCode > 1568` <br>
    위 내용은 위쪽에서 예시코드의 결과 중 일부이다. 이 코드의 결과로 예시를 든다면 다음과 같을 것이다.

    1. 객체에 어떠한 조건도 주지않았을 경우 `배열의 크기는 16`이 된다.  
    2. 객체의 `hashCode값이 1568`이다.
    3. `'hashCode % 배열의 크기'`를 한 결과값이 `배열의 INDEX`이고 해당 인덱스에 객체가 저장된다.
    ( 1568 % 16 == 0. 즉, 0번째 배열에 해당 객체를 저장 )
    <br>

    이를 통해 `HashSet` 또는 `HashMap`에 조건을 주지 않았을 경우 `hashCode % 16`한 결과값으로 `배열의 INDEX에 저장`하기 때문에 저장되는 값이 인덱스 0~15 중 하나에 저장이 될 것이다.

    <br>

    위에서 `hashCode`를 확인한 예제 코드에 배열의 크기(16)만큼 나눈 결과는 다음과 같다.
    ```java
    import java.util.HashSet;

    public class HashSetExample1 {

        public static void main(String[] args) {
            HashSet<String> hashSet = new HashSet<>();
            
            hashSet.add("1");        hashSet.add("2");        hashSet.add("3");
            hashSet.add("4");        hashSet.add("5");        hashSet.add("6");
            hashSet.add("7");        hashSet.add("8");        hashSet.add("9");
            hashSet.add("10");        hashSet.add("11");
            
            for (String str : hashSet) {
                System.out.println("key > \t" + str + "\t | hashCode > \t" + str.hashCode()
                                    + "\t | hashCode % 16 > " + str.hashCode() % 16);
            }
        }
    }
    ```
    ```
    key > 	11	 | hashCode > 	1568	 | hashCode % 16 > 0
    key > 	1	 | hashCode > 	49	     | hashCode % 16 > 1
    key > 	2	 | hashCode > 	50	     | hashCode % 16 > 2
    key > 	3	 | hashCode > 	51	     | hashCode % 16 > 3
    key > 	4	 | hashCode > 	52	     | hashCode % 16 > 4
    key > 	5	 | hashCode > 	53	     | hashCode % 16 > 5
    key > 	6	 | hashCode > 	54	     | hashCode % 16 > 6
    key > 	7	 | hashCode > 	55	     | hashCode % 16 > 7
    key > 	8	 | hashCode > 	56	     | hashCode % 16 > 8
    key > 	9	 | hashCode > 	57	     | hashCode % 16 > 9
    key > 	10	 | hashCode > 	1567	 | hashCode % 16 > 15

    ```
    즉, 이 결과를 통해 `hashCode % 배열의 크기(16)`의 결과가 배열의 INDEX를 나타내고 `해당 INDEX에 입력된 객체가 저장되어 있음을 의미`한다.

    <br><br>

    ### 지금까지 과정 중 궁금한 점
    1. HashSet과 HashMap의 특징인 저장 순서와 출력 순서가 보장되지 않는다 라고 알고있는데 지금까지의 과정만 봤을 땐 틀린 것인가?
    2. 어떠한 조건도 주지 않게 되면 배열의 크기(initial capacity)가 16이라면 만약.. 데이터가 16개보다 많이 들어가게 될 경우에는 어떻게 되는가?

    <br>

    Q1 > HashSet과 HashMap은 저장 순서와 출력 순서가 보장되는데 틀린 것인가? <br>
    A1 > NO.

    Q2 > 만약.. 데이터가 16개보다 많이 들어가게 될 경우에는 어떻게 되는가? <br>
    A2 > '로드 팩터(load factor)'를 사용한다.

    <br><br>

    ### 그렇다면 데이터는 어떻게 저장되는가 - 3
    `HashMap`에는 배열의 크기를 나타내는 `initial capacity`말고도 중요한 요소가 존재한다. 
    
    ### 그것은 바로.,., **로드 팩터(load factor)** 이다. <br>
    <br>

    위에서 설명한 document의 `HashMap의 기본 생성자` 코드는 다음과 같았다.

    `public HashMap()`

        Constructs an empty HashMap with the default initial capacity (16) and the default load factor (0.75).

        파파고 번역 :: 기본 초기 용량(16)과 기본 로드 팩터(0.75)를 사용하여 빈 해시맵을 구성합니다.

    `HashSet`은 `HashMap`으로 동작하기 때문에 HashSet 또한 별도의 조건을 주지 않게되면 `'기본 로드 팩터를 0.75로 설정'`한다.

    <br>

    위 `default load factor`는 `배열 공간이 75%(를 초과)차게 되면 추가적으로 데이터를 저장할 공간을 확보한다는 것을 의미`한다.<br>
    만약, 배열의 크기가 로드 팩터(기본값 75%)만큼 차게 되면 HashMap은 `현재 배열의 크기의 2배만큼 용량을 늘리게 된다.`

    <br>

    즉, 이러한 이유로 `HashMap과 HashSet의 순서가 보장이 되지 않게 된다.`

    <br><br>

    ### 중간 정리 - 2
    지금까지의 내용들을 정리해보자면,
    1. HashSet은 HashMap으로 구현되어 있고, HashSet에 데이터가 저장될 때는 HashMap의 Key에 저장된다.
    2. HashSet과 HashMap은 데이터 추가 전에 equals()와 hashCode()가 실행된다.
    3. HashSet과 HashMap은 데이터 추가 시에 hashCode()가 실행되지만 hashCode값이 작은 순으로 데이터가 저장되는 것은 아니다. <br>
    \-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-\-
    4. HashSet과 HashMap은 데이터 접근의 시간복잡도가 O(1)이고 데이터를 저장할 때 '배열'을 사용한다.
    5. HashSet과 HashMap을 기본적으로 배열의 크기는 16이다. 또한, 이 값으로 hashCode % 배열의 크기(16)를 한 결과로 해당 위치의 INDEX에 데이터를 저장한다.
    6. HashSet과 HashMap은 배열의 크기를 나타내는 요소인 initial capacity와 배열의 크기를 조절하는 요소인 'load factory'로 이루어진다. 배열의 크기를 조절하는 시점은 기본적으로 initial capacity * load factory(16 * 0.75 = 12)의 크기만큼 데이터가 찼을 때이다.
    7. 정리하자면 HashMap이나 HashSet을 조건없이 생성하게 되면 데이터가 12(16*0.75)개가 넘어가는 순간 배열의 크기가 32(16 * 2)개가 된다.

    <br><br>

    지금까지의 내용을 코드로 보면 다음과 같다. 

        위 내용까지는 이해를 위해 HashSet<String>을 사용하였고 아래 내용부터는 가시적인 결과를 위해 HashSet<Integer>를 사용하였다.

    <br>

    ### Example 1) 데이터가 12개 이하

    ```java
    import java.util.HashSet;

    public class HashSetExample1 {

        public static void main(String[] args) {
            HashSet<Integer> hashSet = new HashSet<>();
            hashSet.add(16);		hashSet.add(1);		hashSet.add(2);
            hashSet.add(3);		hashSet.add(4);		hashSet.add(5);
            hashSet.add(6);		hashSet.add(7);		hashSet.add(8);
            hashSet.add(9);		hashSet.add(10);		hashSet.add(11);
            
            // hashSet.add(12);

            System.out.println(hashSet);
            System.out.println("hashSet.size() > " + hashSet.size());
            
            System.out.println("------------------------------------");
            
            for (Integer i : hashSet) {
                System.out.println("key > \t" + i + "\t | hashCode > \t" + i.hashCode()
                                    + "\t | hashCode % 16 > " + i.hashCode() % 16);
            }
        }
    }
    ```
    ```
    [16, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11] 
    hashSet.size() > 12
    ------------------------------------
    key > 	16	 | hashCode > 	16	 | hashCode % 16 > 0
    key > 	1	 | hashCode > 	1	 | hashCode % 16 > 1
    key > 	2	 | hashCode > 	2	 | hashCode % 16 > 2
    key > 	3	 | hashCode > 	3	 | hashCode % 16 > 3
    key > 	4	 | hashCode > 	4	 | hashCode % 16 > 4
    key > 	5	 | hashCode > 	5	 | hashCode % 16 > 5
    key > 	6	 | hashCode > 	6	 | hashCode % 16 > 6
    key > 	7	 | hashCode > 	7	 | hashCode % 16 > 7
    key > 	8	 | hashCode > 	8	 | hashCode % 16 > 8
    key > 	9	 | hashCode > 	9	 | hashCode % 16 > 9
    key > 	10	 | hashCode > 	10	 | hashCode % 16 > 10
    key > 	11	 | hashCode > 	11	 | hashCode % 16 > 11
    ```
    
    <br>

    ### Example 2) 데이터가 13개 이상
    ```java
    import java.util.HashSet;

    public class HashSetExample1 {

        public static void main(String[] args) {
            HashSet<Integer> hashSet = new HashSet<>();
            hashSet.add(16);		hashSet.add(1);		hashSet.add(2);
            hashSet.add(3);		hashSet.add(4);		hashSet.add(5);
            hashSet.add(6);		hashSet.add(7);		hashSet.add(8);
            hashSet.add(9);		hashSet.add(10);		hashSet.add(11);
            
            hashSet.add(12);

            System.out.println(hashSet);
            System.out.println("hashSet.size() > " + hashSet.size());
            
            System.out.println("------------------------------------");
            
            for (Integer i : hashSet) {
                System.out.println("key > \t" + i + "\t | hashCode > \t" + i.hashCode()
                                    + "\t | hashCode % 16 > " + i.hashCode() % 16);
            }
        }
    }
    ```
    ```
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 16]
    hashSet.size() > 13
    ------------------------------------
    key > 	1	 | hashCode > 	1	 | hashCode % 16 > 1
    key > 	2	 | hashCode > 	2	 | hashCode % 16 > 2
    key > 	3	 | hashCode > 	3	 | hashCode % 16 > 3
    key > 	4	 | hashCode > 	4	 | hashCode % 16 > 4
    key > 	5	 | hashCode > 	5	 | hashCode % 16 > 5
    key > 	6	 | hashCode > 	6	 | hashCode % 16 > 6
    key > 	7	 | hashCode > 	7	 | hashCode % 16 > 7
    key > 	8	 | hashCode > 	8	 | hashCode % 16 > 8
    key > 	9	 | hashCode > 	9	 | hashCode % 16 > 9
    key > 	10	 | hashCode > 	10	 | hashCode % 16 > 10
    key > 	11	 | hashCode > 	11	 | hashCode % 16 > 11
    key > 	12	 | hashCode > 	12	 | hashCode % 16 > 12
    key > 	16	 | hashCode > 	16	 | hashCode % 16 > 0
    ```
    
    데이터를 13개를 추가하게 되면 배열의 개수가 13이 되면서 `16 * 0.75`의 결과인 `12`를 넘기게 됨으로서 배열의 크기가 현재 크기에 2배가 증가하게 된다.<br>
    그 결과로, 배열의 공간이 `16 * 2`의 결과인 `32`가 되고 `16 % 32 == 16`이 되어 `16`이 제일 마지막으로 이동한 것을 확인할 수 있다.

    <br>

    이러한 이유들로 아래와 같은 코드를 작성했을 때 Set과 Map의 특징과는 반대로 정렬이 되었던 것이 이러한 이유 때문이다.
    ```java
    import java.util.HashSet;

    public class HashSetExample1 {
        public static void main(String[] args) {
            HashSet<String> hashSet = new HashSet<>();
            
            hashSet.add("1");            hashSet.add("3");
            hashSet.add("0");            hashSet.add("2");
            
            System.out.println(hashSet);
        }
    }
    ```
    ```
    [0, 1, 2, 3]
    ```

    <br>

    ### 최종 정리
    1. HashSet은 HashMap으로 구현되어 있고 데이터를 저장할 때 '배열을 사용'한다
    2. 기본적으로 배열의 크기는 16으로 되어있고 배열의 크기는 로드팩터에 의해 결정된다. (기본값은 0.75)
    3. HashSet 또는 HashMap이 가지는 데이터의 개수가 배열의 크기 * 로드팩터의 결과보다 크게 되면 배열의 크기를 2배 늘린다.

<br>

[참고자료] https://papimon.tistory.com/74

<br>

---

<br>

왜 hashCode의 결과값이 in)int -> out)int 그리고 in)String -> out)int인가?

1. Integer - [java Doc.](https://docs.oracle.com/javase/8/docs/api/java/lang/Integer.html#hashCode--)

![hashcode1](https://user-images.githubusercontent.com/64416833/144915350-904968a4-70b3-4f10-a49e-13763f5fc33d.jpg)

객체의 hashCode의 반환값은 int형태로 반환

<br>

2. String - [tutorialspoint](https://www.tutorialspoint.com/java/java_string_hashcode.htm)
### Description
```
The hash code for a String object is computed as −
s[0]*31^(n - 1) + s[1]*31^(n - 2) + ... + s[n - 1]
s[i]는 문자열의 i번째 문자, n은 문자열의 길이, ^는 지수
```
객체의 hashCode의 반환값은 int형태로 반환

<br>

```java
import java.util.HashSet;

public class HashSetExample11 {

	public static void main(String[] args) {
		HashSet<Object> hashSet = new HashSet<>();
		
		hashSet.add("1");		hashSet.add("3");
		hashSet.add("0");		hashSet.add("2");
		
		hashSet.add(100); 		hashSet.add(200);
		
		for(Object str : hashSet)
			System.out.println("str > \t" + str 
							+ "\t str.hashCode() > " + str.hashCode() 
							);
		
		System.out.println("--------");
		
		String tStr1 = "1"; String tStr2 = "123";
		System.out.println("tStr1 > " + tStr1 + "\t tStr1.hashCode() > " + tStr1.hashCode());
		System.out.println("tStr2 > " + tStr2 + "\t tStr2.hashCode() > " + tStr2.hashCode());
	}
}
```
```
str > 	0	 str.hashCode() > 48
str > 	1	 str.hashCode() > 49
str > 	2	 str.hashCode() > 50
str > 	3	 str.hashCode() > 51
str > 	100	 str.hashCode() > 100
str > 	200	 str.hashCode() > 200
--------
tStr1 > 1	 tStr1.hashCode() > 49
tStr2 > 123	 tStr2.hashCode() > 48690
```

<br>

---

<br>

2. equals()와 hashCode() 오버라이딩

<br>




<br>

[참고자료] https://100100e.tistory.com/353 <br>
[참고자료] https://heepie.me/155 <br>
[참고자료] https://coding-factory.tistory.com/547 

<br>

---

<br>

## 04차 - 21.12.14(화)
## 주제 : 자바 메모리 구조(static, stack, heap)와 Call by value, Call by reference

<br>

### Call by value / Call by reference
#### 함수의 호출 방식
1. 값에 의한 호출(call by value)
    - 메서드 호출 시 사용되는 인자로 값(value)사용. 즉, 메서드로 인자값을 넘길때 그 값을 복사하여 전달하는 형태
    - 메서드 내에서는 복사된 값으로 작업을 하기 때문에 원래의 값을 변경시키지 않음
```java
public Class CallByValue{    
    public static void main(String[] args) {
    int num1 = 10, num2 = 20;

    // swap() 호출 전 : a = 10, b = 20
    System.out.println("swap() 호출 전 : a = " + a + ", b = " + b);
    swap(a, b);
    //swap() 호출 후 : a = 10, b = 20
    System.out.println("swap() 호출 후 : a = " + a + ", b = " + b);
    }

    public static void swap(int x, int y) {
        int temp = x;

        x = y;
        y = temp;
    }
}
```

<br>

2. 참조에 의한 호출(call by reference)
    - 메서드 호출 시 사용되는 인자로 주소(Address)를 사용. 즉, 메서드로 인자값을 메소드로 넘겨 줄때 그 객체를 참조하는 주소를 넘겨주는 형태
    -  객체를 참조하는 주소를 전달받기 때문에 메서드 내에서도 원래의 값에 접근이 가능

<br>

[참고자료] https://devlog-wjdrbs96.tistory.com/44 <br>
[참고자료] https://hyoje420.tistory.com/6 <br>
[참고자료] https://gyoogle.dev/blog/computer-language/Java/Call%20by%20value%20&%20Call%20by%20reference.html <br>

<br>

---

<br>



- Iterator와 Comparator, Comparable (iterable <- collection <- List/Set)
- OSI 7계층<br>
[HTTP Ref.](https://www.joinc.co.kr/w/Site/Network_Programing/AdvancedComm/HTTP)
- HTTP란, HTTP 프로토콜, HTTP 동작원리, URL과 URI 
- HTTP Request Methods(GET / POST / PUT / DELETE )
- RESTful (GET / POST / PUT / DELETE )
- HTTP 상태코드
- 세션, 캐시, 쿠키 <br> 