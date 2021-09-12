# Database

- [키](#키)
- [SQL - 기초 Query](#sql---기초-query)
- SQL - JOIN
- SQL Injection
- SQL vs NoSQL
- Anomaly
- [인덱스](#인덱스)
- 트랜잭션(Transaction)
- 트랜잭션 격리 수준
- [레디스](#레디스)
- [Hint](#hint)
- 클러스터링
- 리플리케이션
- DB 튜닝


# 키
## 정의

  테이블에서 튜플을 구별하는 역할을 하는 속성
  
## 특성

- 유일성

  한 릴레이션에서 모든 튜플은 서로 다른 키 값을 가져야 한다.
  
- 최소성

  꼭 필요한 최소한의 속성들로만 키를 구성한다.


## 종류

1. 후보키

    유일성과 최소성을 만족하는 키

2. 슈퍼키
    
    유일성을 만족하지만 최소성을 만족하지 못하는 속성
    
4. 기본키
    
    후보키 중에서 기본적으로 사용하기 위해 선택한 키
    
    개체 무결성 제약조건: 기본키를 구성하는 모든 속성은 널 값을 가질 수 없다.
    
5. 대체키
    
    기본키로 선택되지 못한 후보키

7. 외래키
  
    다른 릴레이션의 기본키를 참조하는 속성
    
    참조 무결성 제약조건: 외래키는 참조할 수 없는 값을 가질 수 없다.
    
<br>

----

# SQL - 기초 Query

## SQL이란?
SQL이란 Structed Query Language(구조적 질의 언어)의 줄임말로, **관계형 데이터베이스 관리 시스템(RDBMS)** 의 데이터를 관리하기 위해 설계된 특수 목적의 프로그래밍 언어이다.

관계형 데이터베이스 관리 시스템에서 자료의 검색과 관리, 데이터베이스 스키마 생성과 수정, 데이터베이스 객체 접근 조정 관리를 위해 고안되었다. 

많은 데이터베이스 관련 프로그램들이 SQL을 표준으로 채택하고 있다.

SQL의 구성요소로는 크게 3가지 **데이터 정의어(DDL)**, **데이터 조작어(DML)**, **데이터 제어어(DCL)** 으로 구성된다. 

<br>

**+) 테이블이란?** 

테이블이란 항상 이름을 가지고 있는 리스트로, 데이터가 저장되어있는 공간을 의미한다.
   
테이블은 **행(ROW)** 과 **열(COLUMN)** 그리고 거기에 대응하는 **값(FIELD)** 으로 구성되어 있다. 

<p align="center">
<img src="https://user-images.githubusercontent.com/33649908/130323991-29b24271-f172-462c-8772-c656794bc2d1.png" width="50%">
</p>

<br>

## SQL의 언어적 특성
1. SQL은 대소문자를 가리지 않는다. (단, 서버 환경이나 DBMS 종류에 따라 데이터베이스 또는 필드명에 대해 대소문자를 구분하기도 한다)
2. SQL 명령은 반드시 `세미콜론(;)`으로 끝나야 한다.
3. 고유의 값은 `따옴표(")`로 감싸준다.
   ```sql 
   SELETE * FROM EMP WHERE NAME = 'James';
   ```

4. SQL에서 객체를 나타낼 때는 백틱(\`\`)으로 감싸준다.  
   ```sql 
   SELETE \`COST\`, \`TYPE\` FROM \`INVOIVE\`;
   ```

5. 주석은 일종의 도움말로, 주석 처리된 문장은 프로그램에서 동작하지 않는다. 한 줄 주석은 문장 앞에 `--`를 붙여서 사용한다.
   ```sql 
   -- SELETE * FROM EMP;
   ```

6. 여러 줄 주석은 `/* */`으로 감싸준다.

<br>

## SQL과 일반 프로그래밍 언어의 차이점
<p align="center">
<img src="https://user-images.githubusercontent.com/33649908/130322932-e1487a2d-c2bd-44d1-9d5c-8b8f35fca9cd.png" width="70%">
</p>
<br>

## SQL 데이터 종류
1. **int** 

   정수 자료형으로, 예를 들어 물건의 가격, 수량 등을 저장하는 데 사용된다.

   해당 자료형을 통해 -2147483648 ~ 2144483647의 값을 저장할 수 있다.

2. **float** 

   실수 자료형으로, int는 소수점 이하의 부분이 없지만 float는 3.14와 같이 소숫점 이하의 부분까지 저장한다.

   따라서 사람들의 키나 몸무게 등처럼 소숫점 아래까지 저장해야 하는 경우에 사용한다.

3. **char**

   문자열 자료형으로, char(nn)과 같은 방식으로 사용한다. 이 경우 nn글자를 저장하게 된다.

   예를 들어 char(5) 일 경우, 5글자를 저장하며 5글자가 되지 않을 경우 공백을 추가하여 5글자를 맞춘다.

   'elice'와 같이 작은따옴표를 이용해 문자열인 것을 표시한다.

4. **varchar**

   문자열 자료형으로, char과 다른 점은 char의 경우 정해진 글자보다 짧으면 공백을 추가하지만 varchar은 공백을 추가하지 않고 그대로 저장한다.
   
   varchar2와 동의어이다.

5. **date**

   년, 월, 일을 저장하는 날짜 자료형으로, 예를 들어 1945년 8월 15일을 나타내기 위해서는 '1945-08-15'라고 작성한다.

   해당 자료형을 통해 '1001-01-01'~ '9999-12-31'까지 저장할 수 있다.

6. **datetime**

   년, 월, 일, 시, 분, 초까지 저장하는 날짜와 시각 자료형이다.

   예를 들어 1945년 8월 15일 12시 0분 0초를 나타내기 위해서는 '1945-08-15 12:00:00'라고 작성한다.

   해당 자료형을 통해 '1001-01-01 00:00:00'~ '9999-12-31 23:59:59'까지 저장할 수 있다.

7. **time**

   시간, 분, 초를 저장하는 시간 자료형이다.

   예를 들어 10시간 13분 35초를 나타내기 위해서는 '10:13:35'라고 작성한다.

   해당 자료형을 통해 '-838:59:59' ~ '838:59:59'까지 저장할 수 있다.

<br>

## 데이터 정의어(DDL) - CREATE, ALTER, DROP, TRUNCATE문

테이블이나 관계의 구조를 생성하는데 사용하며 CREATE, ALTER, DROP, TRUNCATE문 등이 있다.

### CREATE 문

* 테이블을 구성하고, 속성과 속성에 관한 제약을 정의하며, 기본키 및 외래키를 정의하는 명령이다.

* **PRIMARY KEY** 는 기본키를 정할 때 사용하고, **FOREIGN KEY** 는 외래키를 지정할 때 사용하며, **ON UPDATE** 와 **ON DELETE** 는 외래키 속성의 수정과 튜플 삭제 시 동작을 나타낸다.

* **NOT NULL** (NULL 값을 가질 수 없음), **UNIQUE** (같은 값이 있으면 안됨), **DEFAULT** num(값이 입력되지 않은 경우 기본값 num을 저장) 등 속성에 제약사항을 추가할 수 있다.

``` sql
CREATE TABLE NewBook (
bookname    VARCHAR2(20)   NOT NULL,
publisher    VARCHAR2(20)   UNIQUE,
price    NUMBER   DEFAULT 10000,
PRIMARY KEY (bookname, publisher));
```

<br>

### ALTER 문

* ALTER문은 생성된 테이블의 속성과 속성에 관한 제약을 변경하며, 기본키 및 외래키를 변경한다.

* **ADD, DROP** 은 속성을 추가하거나 제거할 때 사용한다.

* **MODIFY** 는 속성의 기본값을 설정하거나 삭제할 때 이용한다.

``` sql
-- NewBook 테이블에 VARCHAR2(13)의 자료형을 가진 isbn 속성 추가
ALTER TABLE NewBook ADD isbn VARCHAR2(13);

-- NewBook 테이블의 isbn 속성의 데이터 타입을 NUMBER형으로 변경
ALTER TABLE NewBook MODIFY isbn NUMBER;

-- NewBook 테이블의 isbn 속성을 삭제
ALTER TABLE NewBook DROP COLUMN isbn;
```

<br>

### DROP 문

* DROP문은 테이블 자체를 삭제하는 명령이다.

* 테이블의 구조와 데이터를 모두 삭제하므로 사용에 주의해야 함(데이터만 삭제하려면 DELETE)

``` sql
DROP TABLE NewBook;
```

<br>

### TRUNCATE 문

* 테이블에 있는 데이터를 모두 제거하는 명령이다. (한번 삭제시 돌이킬 수 없음.)

``` sql
TRUNCATE TABLE NewBook;
```

<br>

## 데이터 조작어(DML) - SELECT, INSERT, DELETE, UPDATE문

테이블에 데이터를 검색, 삽입, 수정, 삭제하는데 사용하며 SELECT, INSERT, DELETE, UPDATE문 등이 있다.

여기서 SELECT 문은 특별히 Query문(질의어)라고도 한다.

### CRUD란?

Create(생성), Retrieve(검색), Update(수정), Delete(삭제)의 첫 자를 따서 만든 단어이다.

* Create : 데이터베이스 객체 생성

   : INSERT INTO (새로운 레코드 추가)

* Update : 데이터베이스 객체 안의 데이터 수정

   : UPDATE (특정 조건의 레코드의 컬럼 값 수정)

* Delete : 데이터베이스 객체의 데이터 삭제

   : DELETE (특정 조건의 레코드 삭제)

* Retrieve : 데이터베이스 객체 안의 데이터 검색

   : SELETE (조건을 만족하는 레코드를 찾아 특정 컬럼 값(모두 표시하려면 * )을 표시)

<br>

### INSERT 문

* 테이블에 새로운 튜플을 삽입하는 명령으로, 대량삽입(Bulk Insert)란 한번에 여러 개의 튜플을 삽입하는 방법이다.

* **INSERT INTO** 테이블(필드이름1, 필드이름2) **VALUES** (값1, 값2);

``` sql
-- Book 테이블에 새로운 도서 '스포츠 의학' 삽입
INSERT INTO Book(bookid, bookname, publisher)
       VALUES (14, '스포츠 의학', '한솔의학서적');
```

<br>

### UPDATE 문

* UPDATE문은 특정 속성 값을 수정하는 명령이다.

* **UPDATE** 테이블 **SET** 필드이름1=값1, 필드이름2=값2 **WHERE** 조건문;

``` sql
-- Customer 테이블에서 고객번호가 5인 고객의 주소를 '대한민국 부산'으로 변경
UPDATE Customer
SET address='대한민국 부산'
WHERE custid = 5;
```

<br>

### DELETE 문

* DELETE문은 테이블에 있는 기존 튜플을 삭제하는 명령이다.

* **DELETE FROM** 테이블 **WHERE** 조건문;

``` sql
-- Customer 테이블에서 고객번호가 5인 고객 삭제
DELETE FROM Customer
WHERE custid=5;

-- 모든 고객 삭제
DELETE FROM Customer;
```

<br>

### SELETE 문

* 테이블에 저장된 데이터를 검색하는 명령어이다.

* **SELETE** 컬럼명 **FROM** 테이블명 **WHERE** 조건문;

* WHERE절에 조건으로 사용할 수 있는 술어

<p align="center">
<img src="https://user-images.githubusercontent.com/33649908/130325669-40425f7e-6783-4fbb-af5a-3fdbf85b80b7.png" width="60%">
</p>

* 집계 함수의 종류 

<p align="center">
<img src="https://user-images.githubusercontent.com/33649908/130325783-09cbaede-4bbe-451b-9c2e-d32141c399ad.png" width="60%">
</p>

<br>

``` sql
-- 가격이 10,000원 이상 20,000원 이하인 도서 검색
SELETE *
FROM Book
WHERE price BETWEEN 10000 AND 20000;

-- 도서 이름에 '축구가 포함된 출판사 검색
SELETE bookname, publisher
FROM Book
WHERE bookname LIKE '%축구%';

-- 도서를 가격의 내림차순으로 검색, 만약 가격이 같다면 출판사의 오름차순으로 검색
SELETE *
FROM Book
ORDER BY price DESC, publisher ASC;

-- 2번 김연아 고객이 주문한 도서의 총 판매액 구하기
SELETE SUM(saleprice) AS 총매출
FROM Orders
WHERE custid=2;

-- 서점의 도서 판매 건수 구하기
SELECT COUNT(*)
FROM Orders;
```

<br>

## 데이터 제어어(DCL) - GRANT, REVOKE문

데이터 접근을 통제하기 위해 데이터의 사용 권한을 관리하는데 사용하며, GRANT, REVOKE문 등이 있다.

* 권한의 유형과 종류
  
1. 시스템 권한
   
   * CREATE USER : 계정 생성 권한

   * DROP USER : 계정 삭제 권한
  
   * DROP ANY TABLE : 테이블 삭제 권한
  
   * CREATE SESSION : 데이터베이스 접속 권한
  
   * CREATE TABLE : 테이블 생성 권한
  
   * CREATE VIEW : 뷰 생성 권한
  
   * CREATE SEQUENCE : 시퀀스 생성 권한
  
   * CREATE PROCEDURE : 함수 생성 권한

2. 객체 권한
   
   * ALTER : 테이블 변경 권한
  
   * INSERT : 데이터 조작 권한

   * DELETE : 데이터 조작 권한
  
   * SELECT : 데이터 조작 권한
  
   * UPDATE : 데이터 조작 권한
  
   * EXECUTE : PROCEDURE 실행 권한
  
<br>

### GRANT 문

* 특정 데이터베이스 사용자에게 특정 작업에 대한 수행 권한을 부여한다.

```sql
GRANT 권한1, 권한2 TO 사용자계정;

GRANT 권한1, 권한2 ON 객체명 TO 사용자계정;
```

<br>

### REVOKE 문

* 특정 데이터베이스 사용자에게 특정 작업에 대한 수행 권한을 박탈하거나 회수 한다.

```sql
REVOKE 권한1, 권한2 FROM 사용자계정;

REVOKE 권한1, 권한2 ON 객체명 FROM 사용자계정;
```

<br>

### 트랜잭션 제어 (COMMIT, ROLLBACK, CHECKPOINT문)

안전한 거래를 보장하기 위해, 즉 동시에 다수의 작업을 안전하게 처리하기 위해 사용한다.

* COMMIT : 트랜잭션 확정
* ROLLBACK : 트랜잭션 취소
* CHECKPOINT : 복귀지점 설정

<br>

----

# 인덱스

## 정의
테이블에 대한 동작속도를 높이는 기술로 데이터베이스 조회 기능 성능을 높인다.

인덱스를 이용하면 Full Scan과 Table Scan을 줄이거나 없애서 속도를 높인다.

Full Scan 시 시간복잡도 - O(N)

인덱스 사용 시 시간복잡도 - O(logN)

테이블의 컬럼의 색인화가 일어나서 인덱스를 관리하는 파일(MYI)에서 따로 컬럼을 관리한다.

> ### 테이블 생성 시, 3개의 파일이 만들어진다.
> - MYI: 인덱스 관리 파일
> - MYD: 실제 데이터 관리 파일
> - FRM: 테이블 구조에 관한 파일
>
> 인덱스를 생성하지 않으면 MYI는 <b>빈 파일(empty)</b>이다.
> 
> MYI는 Key-RowId의 구조를 가진다. 
> 
> - Key: 인덱스로 생성한 컬럼의 값
> - RowId: 데이터가 저장된 주소 값

## 인덱스를 사용하면 좋을 상황

- SELECT와 Join이 잦은 경우
- 테이블의 데이터가 많은 경우
- SELECT를 제외한 DML(=변경)이 잘 일어나지 않는 테이블

## DML이 일어날 때 인덱스의 변화

### 1. INSERT

데이터 추가 시, 새로운 인덱스 추가

### 2. DELETE

데이터 삭제 시, 해당 인덱스가 삭제되는 대신 사용하지 않는다는 표시를 남긴다.

-> Table의 레코드 개수와 Index 개수가 달라진다.

### 3. Update

<b>DELETE + INSERT</b>

UPDATE 기능이 따로 있지 않고 DELETE 수행 후 INSERT가 일어난다.

-> 작업이 두 번 일어난다.

## 인덱스 생성
```MySQL
-- 생성 방식 2가지
-- CREATE 이용
CREATE INDEX 인덱스명 ON 테이블명(컬럼명);

-- ALTER 이용
ALTER TABLE 테이블명 ADD INDEX 인덱스명(컬럼명);
```

## 인덱스 조회
```MySQL
SHOW INDEX FROM 테이블명;
```

## 인덱스 삭제
```MySQL
ALTER TABLE 테이블명 DROP INDEX 인덱스명(컬럼명);
```
## 장점

인덱스 파일은 항상 정렬된 상태이다.

1) 원하는 데이터를 찾기 위해 테이블을 검색하는 작업을 줄이거나 없앨 수 있다.
2) 색인화된 인덱스 파일 검색으로 검색 속도를 향상시킨다.

> ### 색인화
> 특정 내용이 들어있는 정보를 쉽게 찾아볼 수 있도록 표지를 넣거나 일정한 순서에 따라 배열한 것

## 단점

테이블에 새로운 데이터를 추가하거나 갱신, 삭제가 자주 일어나면 좋지 않다.

테이블에 들어있는 데이터에만 변화가 있는 게 아니라 인덱스에도 추가, 갱신이 일어나기 때문에 오버헤드가 발생할 수 있다.


<hr>


# 레디스

## 레디스란?

- 인 메모리 데이터베이스로 메모리에 위치하고 있는 데이터베이스이다.

- 비관계형 데이터베이스(NoSQL)로 비정형 데이터를 저장한다.

- Key와 Value의 구조로 데이터를 저장한다.

- 문자열, Hash, List, Set, Sorted Set 총 5가지의 데이터 형을 지원한다.

## 레디스는 어디에 사용할까?

1. 데이터 캐싱
2. 메시지 브로커

### 데이터 캐싱

데이터베이스에 접근하지 않고 빠르게 접근할 수 있는 저장 공간에 데이터를 저장해둠으로써 빠른 Read와 Write를 가능하게 하는 방법

### 데이터 캐싱에 적합한 데이터

> 1) 자주 조회하는 데이터
>
> 2) 업데이트가 잘 일어나지 않는 데이터
>
> 3) 반복적으로 자주 요청이 들어오며 동일한 결과를 리턴하는 기능의 결과값

### 메시지 브로커

Pub & Sub 패턴에서 Publisher가 발행한 메시지를 Subscriber에게 전달해주는 역할

## 레디스의 데이터 영속성

### 영속성이란?

영원히 사라지지 않는 데이터의 특성, 데이터를 생성하고 변경하던 프로그램이 종료되어도 데이터가 사라지지 않는다.

레디스는 메모리(휘발성)에 데이터베이스가 위치하기 때문에 갑작스러운 종료에 대비하기 위한 방법이 있다.

1. RDB 방식
2. AOF 방식

### RDB 방식
>
> RDB란 .rdb 라는 파일 확장자 형태로 메모리에 있는 내용을 **스냅샷** 형태로 저장하는 방식이다.
>
> 스냅샷(Snapshot)이란 한 순간의 메모리에 있는 내용을 저장하는 것이다.
>
> 스냅샷은 일정 간격마다 뜨게 된다.
>
> 장점
> - 메모리의 Snapshot을 뜨게 되면 서버가 꺼지고(stop or down) 재실행(restart) 시에 Snapshot을 load만 하면 되기 때문에 재실행(restart) 시간이 빠르다.
>
> 단점
> - Snapshot을 뜨고 시간이 흐른 후 서버가 꺼지게 되면 Snapshot 이후의 데이터는 복구할 수 없다.


### AOF 방식
>
> AOF란 Append Only File을 뜻하며 write와 update와 같은 쓰기 명령을 모두 log 파일에 저장하는 형태이다.
>
> 서버가 꺼지고 재실행하는 경우 log 파일에 저장된 명령들이 순차적으로 실행하며 데이터를 복구한다.
>
> 장점
> - RDB의 문제점인 Snapshot 이후의 데이터 유실을 보완할 수 있다.
>
> 단점
> - log 파일의 명령을 다시 실행해야 하기 때문에 서버 재실행 시간이 느리다.

---

# Hint

아래 Hint에 대한 모든 내용들은 `MySQL`을 기준으로 작성되었음.

힌트는 옵티마이저의 실행 계획을 원하는대로 바꿀 수 있게 해준다. <br>
옵티마이저라고 반드시 최선의 실행계획을 수립할 수는 없기 때문에, 조인이나 인덱스의 잘못된 실행 계획을 개발자가 직접 바꿀 수 있도록 도와주는 것이 힌트이다. 

힌트의 문법이 올바르더라도 힌트가 반드시 받아 들여지는 것은 아니며, 옵티마이저에 의해 선택되지 않을 수도 있고 선택될 수도 있다.

Hint는 크게 2가지로 구분할 수 있다.
1. `옵티마이저 힌트`
2. `인덱스 힌트`

`옵티마이저 힌트`와 `인덱스 힌트`는 서로 다르며, 함께 사용할 수도 있고 별도로 사용할 수도 있다.

## 옵티마이저 힌트

옵티마이저를 제어하는 방법 중 하나는, optimizer_switch 시스템 변수를 설정하는 것 이다. 이는 모든 후속 쿼리 실행에 영향을 주기 때문에, 일반적인 사용자들에게는 권장되지 않은 방법이다.

그래서 옵티마이저를 더 세밀하게 선택적으로 제어해야 할 땐, 옵티마이저 제어를 원하는 부분을 지정할 수 있는 옵티마이저 힌트를 사용하는 것이다.<br>
즉, 명령문의 한 테이블에 대한 최적화를 활성화하고 다른 테이블에 대한 최적화를 비활성화할 수 있다.<br>
명령문 내의 옵티마이저 힌트는 optimizer_switch 보다 우선시 되어 적용된다.

옵티마이저 힌트는 다양한 범위 수준에서 적용된다.

- `전역`: 힌트가 전체 문에 영향을 줌
- `쿼리 블록`: 힌트가 명령문 내의 특정 쿼리 블록에만 영향을 줌
- `테이블`: 힌트가 쿼리 블록 내의 특정 테이블에민 영향을 줌
- `인덱스`: 힌트가 테이블 내의 특정 인덱스에만 영향을 줌

### 사용 방법
옵티마이저 힌트는 /*+ .... */주석 내에 지정해야 한다.

|힌트 명|힌트 설명|적용 범위 수준|
|:----------------------------:|:------------------------------------------:|:---------------------:|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">BKA</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_BKA</a> | 일괄 처리된 키 액세스 조인 처리에 영향| 쿼리 블록, 테이블   |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">BNL</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_BNL</a> | MySQL 8.0.20 이전: 블록 중첩-루프 조인 처리,  MySQL 8.0.18 이상: 해시 조인 최적화,  MySQL 8.0.20 이상: 해시 조인 최적화에만 영향 | 쿼리 블록, 테이블   |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">DERIVED_CONDITION_PUSHDOWN</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_DERIVED_CONDITION_PUSHDOWN</a> | 구체화된 파생 테이블에 대한 파생 조건 푸시다운 최적화 사용 또는 무시(MySQL 8.0.22에 추가) | 쿼리 블록, 테이블|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">GROUP_INDEX</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_GROUP_INDEX</a> | GROUP BY 작업(MySQL 8.0.20에 추가)에서 인덱스 검색에 대해 지정된 인덱스를 사용하거나 무시 | 인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">HASH_JOIN</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_HASH_JOIN</a> | 해시 조인 최적화에 영향 (MySQL8.0.18만 해당)| 쿼리 블록, 테이블|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">INDEX</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_INDEX</a> | JOIN_INDEX,  GROUP_INDEX 및 ORDER_INDEX의 조합으로  작동하거나 NO_JOIN_INDEX, NO_GROUP_INDEX 및 NO_ORDER_INDEX(MySQL 8.0.20에 추가)의 조합으로 작동| 인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">INDEX_MERGE</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_INDEX_MERGE</a> | 인덱스 병합 최적화에 영향| 테이블,  인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">JOIN_INDEX</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_JOIN_INDEX</a> | 모든 액세스 방법에 대해 지정된 인덱스 또는 인덱스를 사용하거나 무시(MySQL 8.0.20에 추가) | 인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-join-order">JOIN_FIXED_ORDER</a> | FROM절에 지정된 순서대로(FIXED) 테이블을 조인하도록 지시          | 쿼리 블록 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-join-order">JOIN_ORDER</a> |  가능하다면 힌트에 지정된 순서대로 조인하도록 지시| 쿼리 블록|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-join-order">JOIN_PREFIX</a> | 가장 먼저 조인을 시작 할 테이블 지정 | 쿼리 블록|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-join-order">JOIN_SUFFIX</a> | 가장 마지막으로 조인 할 테이블 지정 | 쿼리 블록|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-execution-time">MAX_EXECUTION_TIME</a> | 구문 실행 시간 제한| 전역 범위|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">MERGE</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_MERGE</a> | 외부 쿼리 블록으로 병합되는 파생 테이블/뷰에  영향 | 테이블|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">MRR</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_MRR</a> | 다중 범위 읽기 최적화에 영향| 테이블,  인덱스|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_ICP</a> | 인덱스 조건 푸시다운 최적화에 영향| 테이블,  인덱스|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_RANGE_OPTIMIZATION</a> | 범위 최적화에 영향| 테이블,  인덱스|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">ORDER_INDEX</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_ORDER_INDEX</a> | 행을 정렬하기 위해 지정된 인덱스 또는 인덱스 사용하거나 또는 무시(MySQL 8.0.20에 추가) | 인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-query-block-naming">QB_NAME</a> | 쿼리 블록에 이름 할당                                        |쿼리 블록|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-resource-group">RESOURCE_GROUP</a> | 구문을 실행하는 동안 리소스 그룹 설정                        | 전역 범위|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-subquery">SEMIJOIN</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-subquery">NO_SEMIJOIN</a> | Semijoin 전략에 영향, MySQL 8.0.17부터는 anti조인에도 적용 | 쿼리 블록 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">SKIP_SCAN</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_SKIP_SCAN</a> | 스킵 검색 최적화에 영향| 테이블,  인덱스|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-set-var">SET_VAR</a> | 구문을 실행하는 동안 변수 설정| 전역 범위|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-subquery">SUBQUERY</a> | 구체화에 영향,  IN-to-EXISTS 하위 쿼리 전략| 쿼리 블록|

```sql
/*+ BKA(table1) */ 

/*+ BNL(table1, table2) */

/*+ NO_RANGE_OPTIMIZATION(table3 PRIMARY) */

/*+ QB_NAME(queryblock1) */

SELECT /*+ BNL(t1) BKA(t2) */ ...
// 하나의 쿼리 블록에서 여러 힌트를 사용할 땐, 하나의 힌트 주석안에 여러개의 힌트를 선언하여 사용해야 한다.
// 즉, SELECT /*+ BNL(t1) */ /* BKA(t2) */ ... 는 안됨.
```

옵티마이저 힌트는 SELECT, UPDATE, INSERT, REPLACE, DELETE문에서 아래와 같이 사용할 수 있다.

```sql
SELECT /*+ HINT */ ...
INSERT /*+ HINT */ ...
REPLACE /*+ HINT */ ...
UPDATE /*+ HINT */ ...
DELETE /*+ HINT */ ...
```

그리고 아래와 같이 쿼리 블록으로 구분하여 사용이 가능하다.
```sql
(SELECT /*+ ... */ ... )
(SELECT ... ) UNION (SELECT /*+ ... */ ... )
(SELECT /*+ ... */ ... ) UNION (SELECT /*+ ... */ ... )
UPDATE ... WHERE x IN (SELECT /*+ ... */ ...)
INSERT ... SELECT /*+ ... */ ...
```

## 조인 순서 최적화 힌트
MySQL 8.0은 이전 버전보다 훨씬 강력하고 편의성이 강한 Optimizer hint를 제공한다.<br>
그 중 하나가 조인 순서 최적화 힌트이다.

조인 순서 최적화 힌트는 옵티마이저가 테이블을 조인하는 순서에 영향을 준다.<br>
기존의 join 순서를 제어하던 STRAIGHT_JOIN 구문등은 사용상의 여러 문제를 만들어 냈지만, 조인 순서 최적화 힌트를통해 그러한 문제를 해결하게 되었다.

### 사용 방법
```sql
HINT_NAME([@query_block_name])
HINT_NAME([@query_block_name] TABLE_NAME [, tbl_name] ...)
HINT_NAME(TABLE_NAME[@query_block_name] [, TABLE_NAME[@query_block_name]] ...)
```

HINT_NAME에 올 수 있는 조인 순서 최적화 힌트는 4가지가 있다.
- `JOIN_FIXED_ORDER`: FROM절에 지정된 순서대로(FIXED) 테이블을 조인하도록 지시 (STRAIGHT_JOIN의 힌트화)
- `JOIN_ORDER`: 가능하다면 힌트에 지정된 순서대로 조인하도록 지시
- `JOIN_PREFIX`: 가장 먼저 조인을 시작 할 테이블 지정
- `JOIN_SUFFIX`: 가장 마지막으로 조인 할 테이블 지정
<br>

지정한 TABLE_NAME의 모든 테이블에 힌트가 적용되며, TABLE_NAME은 스키마 이름으로 한정할 수 없다.<br>
TABLE_NAME에 별칭이 있는 경우 힌트는 테이블 이름이 아니라 별칭을 참조해야 한다.

```sql
SELECT
/*+ JOIN_PREFIX(t2, t5@subq2, t4@subq1)
    JOIN_ORDER(t4@subq1, t3)
    JOIN_SUFFIX(t1) */
COUNT(*) 
FROM t1 JOIN t2 JOIN t3
WHERE t1.f1 IN (SELECT /*+ QB_NAME(subq1) */ f1 FROM t4)
  AND t2.f1 IN (SELECT /*+ QB_NAME(subq2) */ f1 FROM t5);
```
`(SELECT /*+ QB_NAME(subq1) */ f1 FROM t4)` : 쿼리 블록의 이름을 subq1로 지정
`t4@subq1` : 쿼리 블록 subq1의 테이블 t4를 지정

```sql
/*+ JOIN_PREFIX(t2, t5@subq2, t4@subq1)
JOIN_ORDER(t4@subq1, t3)
 JOIN_SUFFIX(t1) */
```
`t2`, `t5@subq2`, `t4@subq1`, `t3`, `t1` 순서대로 조인

## 인덱스 힌트

Mysql를 사용을 하다보면 복잡한 쿼리의 경우 서로의 인덱스가 물리고 물려서 필요한 인덱스를 안타고 엉뚱한 인덱스를 사용하는 경우가 있다.<br>
예를 들어서 A, B, C의 인덱스가 순서대로 사용되어야 하는데 옵티마이저가 B, C, A 순으로 처리를 하여서 속도가 느려지는 경우에 이런 순서를 잡기 위해서 인덱스 힌트를 사용한다.<br>
즉, Mysql에서 제공하는 인덱스 힌트를 쓰면 강제적으로 할당한 Index를 이용하여 쿼리가 실행이 된다. <br>
하지만 JPA(hibernate)에서 사용이 불가능하기 때문에 JdbcTemplate 등을 이용하여 Native Query로 활용해야 된다.

### 사용 방법
```sql
TABLE_NAME [[AS] ALIAS] INDEX_HINT INDEX_LIST

INDEX_LIST:
    USE {INDEX|KEY}
      [FOR {JOIN|ORDER BY|GROUP BY}] (INDEX_LIST)
  | IGNORE {INDEX|KEY}
      [FOR {JOIN|ORDER BY|GROUP BY}] (INDEX_LIST)
  | FORCE {INDEX|KEY}
      [FOR {JOIN|ORDER BY|GROUP BY}] (INDEX_LIST)

INDEX_LIST:
    INDEX_NAME , INDEX_NAME ...
```

- `USE 키워드` : 특정 인덱스를 사용하도록 권장
- `IGNORE 키워드` : 특정 인덱스를 사용하지 않도록 지정
- `FORCE 키워드` : USE 키워드와 동일한 기능을 하지만, 옵티마이저에게 보다 강하게 해당 인덱스를 사용하도록 권장

<br>

- `USE INDEX FOR JOIN` : JOIN 키워드는 테이블간 조인뿐 아니라 레코드 검색하는 용도까지 포함
- `USE INDEX FOR ORDER BY` : 명시된 인덱스를 ORDER BY 용도로만 사용하도록 제한
- `USE INDEX FOR GROUP BY` : 명시된 인덱스를 GROUP BY 용도로만 사용하도록 제한

```sql
SELECT * 
FROM TABLE1 
  USE INDEX (COL1_INDEX, COL2_INDEX)
WHERE COL1=1 AND COL2=2 AND COL3=3;

SELECT * 
FROM TABLE2 
  IGNORE INDEX (COL1_INDEX)
WHERE COL1=1 AND COL2=2 AND COL3=3;

SELECT * 
FROM TABLE3
  USE INDEX (COL1_INDEX)
  IGNORE INDEX (COL2_INDEX) FOR ORDER BY
  IGNORE INDEX (COL3_INDEX) FOR GROUP BY
WHERE COL1=1 AND COL2=2 AND COL3=3;
```

---
