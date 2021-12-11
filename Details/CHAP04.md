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