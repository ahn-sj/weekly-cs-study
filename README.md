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


---

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
## 주제 : Iterator와 Comparator, Comparable