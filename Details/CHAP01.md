## CH01. ROWNUM - 21.11.16(화)
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