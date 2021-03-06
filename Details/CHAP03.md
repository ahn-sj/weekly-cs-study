## CH03. 21.12.14(화)
### &nbsp;&nbsp;&nbsp;&nbsp; |-- 래퍼클래스와 오토박싱, 언박싱 그리고 HashSet

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
            |-- equals(), hashCode() 오버라이딩


<br>

## 03-1) 래퍼클래스와 오토박싱, 언박싱

- 자바의 자료형
    - 기본 타입(`primitive type`)
        - `int, short, long, float, double, char, boolean`
    - 참조 타입(`reference type`)
        - `Array, String, class, ...`

<br>

- 래퍼 클래스(`Wrapper class`)
    - 자바의 기본 타입을 객체로 다루기 위해 사용하는 클래스들

    <br>

    래퍼클래스의 상속 계층도

    ![wrapper2](https://user-images.githubusercontent.com/64416833/144604298-bfcb5194-014b-4c88-8c4d-a3cde7bff770.jpg)

    <img src="https://user-images.githubusercontent.com/64416833/144602692-1a82264f-ce9b-4d8b-a4a8-008280273b74.jpg" width="620">

<br>

- 래퍼클래스를 사용하는 이유는??
    1. 래퍼 클래스는 기본 타입을 `Object`로 변환할 수 있다. 이를 통해, 지네릭스(`Generics`)에서도 사용할 수 있다. <br> `ex) HashMap<Integer, String> hashMap = new HashMap<>();`

    2. 래퍼 클래스는 `java.lang`패키지에 포함되어 있어서 패키지에 있는 메서드들을 사용할 수 있다.<br>
    `ex) .equals(), .toString(), getClass, ... `

    3. `ArrayList`등과 같은 `Collection` 프레임워크의 데이터 구조는 기본 타입이 아닌 객체만 저장 가능하기 때문에 래퍼클래스를 이용해서 사용할 수 있다.<br> `ex) boxing`, `unboxing`

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
        
        // AutoUnBoxing
        // int num = num.intValue();
        int n = num;        // 언박싱        
        ```    


<br>

- 래퍼클래스 값 비교
    ```java
    Integer num1 = new Integer(10); //래퍼 클래스1
    Integer num2 = new Integer(10); //래퍼 클래스2

    int i = 10; //기본타입
            
    System.out.println("래퍼클래스 == 기본타입 : " + (num1 == i));              // true
    System.out.println("래퍼클래스 == 기본타입 : " + (num2 == i));              // true
    System.out.println("래퍼클래스 == 래퍼클래스 : " + (num1 == num2));         // false
    System.out.println("래퍼클래스.equals(기본타입) : " + num1.equals(i));      // true
    System.out.println("래퍼클래스.equals(래퍼클래스) : " + num1.equals(num2)); // true
    ```

    래퍼 클래스와 기본 자료형간의 비교는 `== 연산`과 `equals연산` 모두 가능하다. <br>
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

    `Map`은 **Key와 Value의 쌍으로 값이 저장되는 형태**이며 **순서를 보장하지 않고 Key Object는 중복을 허용하지 않고 Value Object는 중복을 허용하는 데이터 구조**이다.

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
    }        
    ```    


    위 코드를 통해 `HashSet`은 `HashMap`으로 구현되어 있고, 값을 저장할 때 `Key Object`에 `e`의 값이 들어가게 되면서 `Key Object`에 입력한 객체가 저장되고, `Value Object`에는 `dummy data`가 저장되게 된다.

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


    ### 그렇다면 데이터는 어떻게 저장되는가 - 1 / 3
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

    ### 그렇다면 데이터는 어떻게 저장되는가 - 2 / 3
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

    ### 그렇다면 데이터는 어떻게 저장되는가 - 3 / 3
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
    5. HashSet과 HashMap은 기본적으로 배열의 크기가 16이다. 또한, 이 값으로 hashCode % 배열의 크기(16)를 한 결과로 해당 배열의 INDEX에 데이터를 저장한다.
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


2. equals()와 hashCode() 오버라이딩

<br>

### `equals()`와 `==`

<br>

#### - 공통점
`equals()`와 `==`는 기본적으로 양쪽에 있는 대상을 비교한 다음 boolean으로 반환한다.

<br>

#### - 차이점
1. 형태의 차이
    - `equals()`는 객체끼리 내용을 비교할 수 있는 **메서드**이다.
    - `==`는 비교를 위한 **연산자**이다.
2. 주소값 비교와 내용 비교
    - `equals()`는 비교하고자 하는 두 대상의 **내용 자체를 비교**한다.
    - `==`연산자는 비교하고자 하는 두 대상의 **주소값을 비교**한다.

<br>

Example 1)

```java
public class chap09_1 {
	public static void main(String[] args) {
		Num n1 = new Num(10);
		Num n2 = new Num(10);
		
		System.out.println(n1.equals(n2));
	}
}

class Num {
	private int number;

	public Num() {
		this(100);
	}
	
	public Num(int number) {
		this.number = number;
	}
}
// false
```

위 예제의 경우 두 객체가 동일한 내용을 가지고 있음에도 `false` 의 결과를 반환한다.<br>
위에서 말한 대로면 `equals`는 내용을 비교하기 때문에 `true`가 나와야 맞지만 `false`를 반환한다.

<br>

`Object의 equals()`메서드를 보게되면 다음과 같다.

```jsx
public boolean equals(Object obj) {
	return (this == obj);
}
```
함수가 내부적으로 두 객체를 `==`연산자로 동작하기 때문에 주소를 비교하기 때문에 주소가 다르면(객체를 new로 생성)`false`를 반환하게 된다. <br>
이러한 이유로 `equals()`를 오버라이딩 해주지 않을 경우 `this`와 `obj`의 주소를 비교하기 때문에 위 코드의 결과가 `false`가 나오게 된다.

<br>

만약, 서로 다른 객체라도 값이 같을 때 `true`를 반환하고 싶다면 아래 코드처럼 equals를 오버라이딩 해주어야 한다.

```jsx
public boolean equals(Object obj) {
	Value v = (Value) obj;
	return (this.value == v.value);
}
```
그러나 참조변수 형변환을 수행할 때 어떠한 값이 들어올 지 모르기 때문에 '형변환 오류'가 발생할 수도 있다. 그래서 `instanceof`연산자로 형변환이 가능한지 확인한 후에 수행하는 것이 바람직하다. 또한, 비교할 대상이 여러 개 존재하게 되면 대상의 멤버변수와 비교하도록 '오버라이딩'해야 한다.

1. 참조변수의 형변환전에는 반드시 instanceof로 확인해야 한다.
2. 입력받은 obj의 값을 클래스의 멤버변수(인스턴스 변수)로 비교한다(형변환)

\-    

1. `obj -> Value`변환할 때 `Value객체`가 아니면 `false` 를 반환하도록한다 <br> 
`if(!(obj instanceof Value)) return false;`

```jsx
public boolean equals(Object obj) {
	if(!(obj instanceof Value)) return false;

	Value v = (Value) obj;
	return (this.value == v.value);
}
```

<br>

Q. 우리가 그동안 String을 사용할 때 new로 생성해도 동일한 값을 가질 경우 true를 반환한 이유는 무엇인가?<br>
A. String클래스는 Object의 equals()를 오버라이딩 하기 때문이다.

<br>

String클래스에 equals메서드의 코드는 아래와 같다.
```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String aString = (String)anObject;
            if (!COMPACT_STRINGS || this.coder == aString.coder) {
                return StringLatin1.equals(value, aString.value);
            }
        }
        return false;
    }
```
`Object equals`와 `String equals`
```jsx
public class equalsTest {
	public static void main(String[] args) {
		Stud s1 = new Stud();
		Stud s2 = new Stud();
		// Stud equals > false
		System.out.println("Stud equals > " + s1.equals(s2));
		
		String str1 = new String("123");
		String str2 = new String("123");
		// String equals > true
		System.out.println("String equals > " + str1.equals(str2));
	}
}

class Stud {
	private String name;
	
	public Stud() { this("papago");	}
	public Stud(String name) { this.name = name; }
}
```

정리하자면

1. `String`클래스는 `eqauls`를 오버라이딩하기 때문에 `new`로 생성해도 `s1.equals(s2)`를 수행했을 때 `true`를 반환한다.
2. 사용자가 정의한 클래스의 `eqauls`는 기본적으로 Object의 equals를 사용하게 된다. 그러므로 `new`로 생성하게 되면 서로의 값이 아닌 주소를 비교하기 때문에 `equals`의 결과값이 올바르게 나오지 않는다.<br> 

    이러한 이유로 객체간 값이 같은지 비교를 하려면 eqauls를 오버라이딩 해주어야 원하는 결과값을 반환하게 된다.

<br><br>

Q. `equals`를 오버라이딩 해주면 `hashCode`도 오버라이딩 해주어야 한다는데 맞는가? <br>
A. **무조건.** 이라고는 대답하지 못하겠지만 `HashSet`과 `HashMap`은 데이터 추가전에 `equals`와 `hashCode`로 동일한 값이 있는지 비교하게 된다. 이렇게 `hash`함수를 사용할 때에는 `hashCode`도 오버라이딩 해주어야 하지만 <br><br>
단순히 객체와 객체간의 동일한 값을 비교하기 위해 equals를 해주어야 한다면 hashCode는 해주지 않아도 무방하다.

<br>

단, 원래라면 `equals`를 오버라이딩 하면 `hashCode`도 오버라이딩 해주는 것이 원칙이다.<br>
왜냐하면 논리적으로 두 인스턴스가 같다면 같은 해시코드를 반환하기 때문이다.

<br>

*\* ==연산자는 **기본 자료형**에서는 값을 비교하고, **참조 자료형**에서는 주소를 비교한다. **

<br>

#### hashCode()
- 객체의 해시코드(hash code)를 반환하는 메서드
- 객체의 주소를 정수로 변환해서 반환
- equals()를 오버라이딩하면 hashCode()도 오버라이딩 해야 한다.
- **equals()의 결과가 true인 두 객체의 해시코드는 같아야 한다**

즉, 이러한 이유로 `HashSet`과 `HashMap`을 이용할 때는 `equals`와 `hashCode`를 오버라이딩 해주는 것이 바람직하다.
```java
@Override
public boolean equals(Object obj) {
    if(!(obj instanceof Person)) return false;
            
    Person p = (Person)obj;
    
    return this.pno == p.pno && this.name.equals(p.name);
}

@Override
public int hashCode() {
    return Objects.hash(pno, name);
}
```

`equals()`는 `obj`와 클래스의 인스턴스 변수를 비교하도록 구현 (기본형은 `==`으로 비교하고 그 외 나머지 타입은 `equals()`로 비교)

ex) `this.age == p.age && this.name.equals(p.name)`

`hashCode()`는 `Objects`클래스의 `hash()`함수로 구현

<br><br>

#### 해싱(hashing)

키(`Key`)값을 해시 함수(`Hash Function`)에 대입시켜 계산한 후 나온 결과를 주소로 사용하여 데이터를 저장, 검색하는 방법

![hashfunc](https://user-images.githubusercontent.com/64416833/145587632-f5fed027-5e20-414e-b6df-4efd753af684.jpg)

<br>

해시함수는 같은 키에 대해 항상 같은 해시코드를 반환해야 한다. 서로 다른 키일지라도 같은 값의 해시코드를 반환할 수도 있다.<br>

1번 : 키로 해시함수를 호출해서 해시코드를 얻게 되는데 해시코드는 배열의 인덱스로 위 이미지에서 7이 배열의 인덱스에 속한다<br>
2번 : 해시코드(해시함수의 반환값, 즉 배열의 인덱스)에 대응하는 링크드 리스트를 배열에서 찾는다<br>
3번 : 링크드리스트에서 키와 일치하는 데이터를 찾는다<br>

위 사진에서 `75xxxx...`와 `72xxxx...`는 같은 해시코드를 가지지만 키값은 다르기 때문에 데이터를 찾는데에 문제는 없다

<br>

 


< 해시 테이블 ><br>
해시테이블은 아래 이미지와 같이 `Array`와 `Linked List`가 조합된 형태이다

![hashtable](https://user-images.githubusercontent.com/64416833/145587563-f4643114-056d-48c5-aa12-1e144818731b.jpg )



<br>

---

<br>

[참고자료] https://100100e.tistory.com/353 <br>
[참고자료] https://heepie.me/155 <br>
[참고자료] https://coding-factory.tistory.com/547 

<br>