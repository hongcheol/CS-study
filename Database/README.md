# Database

- [키](#키)
- SQL - 기초 Query
- SQL - JOIN
- SQL Injection
- SQL vs NoSQL
- Anomaly
- [인덱스](#인덱스)
- 트랜잭션(Transaction)
- 트랜잭션 격리 수준
- 레디스
- 이상 현상의 종류
- Hint
- 클러스터링
- 리플리케이션
- DB 튜닝

# Hint

아래 Hint에 대한 모든 내용들은 MySQL을 기준으로 작성되었습니다.

힌트는 옵티마이저의 실행 계획을 원하는대로 바꿀 수 있게 해준다. <br>
옵티마이저라고 반드시 최선의 실행계획을 수립할 수는 없기 때문에, 조인이나 인덱스의 잘못된 실행 계획을 개발자가 직접 바꿀 수 있도록 도와주는 것이 힌트이다. 

힌트의 문법이 올바르더라도 힌트가 반드시 받아 들여지는 것은 아니며, 옵티마이저에 의해 선택되지 않을 수도 있고 선택될 수도 있다.
 
Hint는 크게 2가지로 구분할 수 있다.
1. 옵티마이저 힌트
2. 인덱스 힌트

옵티마이저 힌트와 인덱스 힌트는 서로 다르며, 함께 사용할 수도 있고 별도로 사용할 수도 있다.

## 옵티마이저 힌트

옵티마이저를 제어하는 방법 중 하나는, optimizer_switch 시스템 변수를 설정하는 것 이다. 이는 모든 후속 쿼리 실행에 영향을 주기 때문에, 일반적인 사용자들에게는 권장되지 않은 방법이다.

그래서 옵티마이저를 더 세밀하게 선택적으로 제어해야 할 땐, 옵티마이저 제어를 원하는 부분을 지정할 수 있는 옵티마이저 힌트를 사용하는 것이다.<br>
즉, 명령문의 한 테이블에 대한 최적화를 활성화하고 다른 테이블에 대한 최적화를 비활성화할 수 있다.<br>
명령문 내의 옵티마이저 힌트는 optimizer_switch 보다 우선시 되어 적용된다.

옵티마이저 힌트는 다양한 범위 수준에서 적용된다.

- 전역: 힌트가 전체 문에 영향을 줌
- 쿼리 블록: 힌트가 명령문 내의 특정 쿼리 블록에만 영향을 줌
- 테이블 수준: 힌트가 쿼리 블록 내의 특정 테이블에민 영향을 줌
- 인덱스 수준: 힌트가 테이블 내의 특정 인덱스에만 영향을 줌

### 사용 방법
옵티마이저 힌트는 /*+ .... */주석 내에 지정해야 한다.

|힌트 명|힌트 설명|적용 범위|
|:------------------------------------------------------------:|:------------------------------------------:|:---------------------:|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">BKA</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_BKA</a> | 일괄 처리된 키 액세스 조인 처리에 영향을 줌.| 쿼리 블록, 테이블   |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">BNL</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_BNL</a> | MySQL 8.0.20 이전: 블록 중첩-루프 조인 처리,  MySQL 8.0.18 이상: 해시 조인 최적화,  MySQL 8.0.20 이상: 해시 조인 최적화에만 영향을 줍니다. | 쿼리 블록, 테이블   |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">DERIVED_CONDITION_PUSHDOWN</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_DERIVED_CONDITION_PUSHDOWN</a> | 구체화된 파생 테이블에 대한 파생 조건 푸시다운 최적화 사용 또는 무시(MySQL 8.0.22에 추가) | 쿼리 블록, 테이블|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">GROUP_INDEX</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_GROUP_INDEX</a> | GROUP BY 작업(MySQL 8.0.20에 추가)에서 인덱스 검색에 대해 지정된 인덱스를 사용하거나 무시합니다. | 인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">HASH_JOIN</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_HASH_JOIN</a> | 해시 조인 최적화에 영향을 미칩니다(MySQL  8.0.18만 해당).    | 쿼리 블록, 테이블   |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">INDEX</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_INDEX</a> | JOIN_INDEX,  GROUP_INDEX 및 ORDER_INDEX의 조합으로  작동하거나 NO_JOIN_INDEX, NO_GROUP_INDEX 및 NO_ORDER_INDEX(MySQL 8.0.20에 추가됨)의  조합으로 작동합니다. | 인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">INDEX_MERGE</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_INDEX_MERGE</a> | 인덱스 병합 최적화에 영향을 줍니다.| 테이블,  인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-join-order">JOIN_FIXED_ORDER</a> | 조인 순서에 대해 FROM 절에  지정된 테이블 순서 사용          | 쿼리 블록 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">JOIN_INDEX</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_JOIN_INDEX</a> | 모든 액세스 방법에 대해 지정된 인덱스 또는 인덱스를 사용하거나 무시(MySQL 8.0.20에 추가됨) | 인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-join-order">JOIN_ORDER</a> | 조인 순서에 힌트에 지정된 테이블 순서 사용| 쿼리 블록|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-join-order">JOIN_PREFIX</a> | 조인 순서의 첫 번째 테이블에 대해 힌트에 지정된 테이블 순서 사용 | 쿼리 블록|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-join-order">JOIN_SUFFIX</a> | 조인 순서의 마지막 테이블에 대해 힌트에 지정된 테이블 순서 사용 | 쿼리 블록|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-execution-time">MAX_EXECUTION_TIME</a> | 구문 실행 시간 제한| 전역 범위|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">MERGE</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-table-level">NO_MERGE</a> | 외부 쿼리 블록으로 병합되는 파생 테이블/뷰에  영향을 줍니다. | 테이블|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">MRR</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_MRR</a> | 다중 범위 읽기 최적화에 영향을 줍니다.| 테이블,  인덱스|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_ICP</a> | 인덱스 조건 푸시다운 최적화에 영향을 줍니다.| 테이블,  인덱스|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_RANGE_OPTIMIZATION</a> | 범위 최적화에 영향을 줍니다.| 테이블,  인덱스|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">ORDER_INDEX</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_ORDER_INDEX</a> | 행을 정렬하기 위해 지정된 인덱스 또는 인덱스 사용하거나 또는 무시(MySQL 8.0.20에 추가됨) | 인덱스 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-query-block-naming">QB_NAME</a> | 쿼리 블록에 이름 할당                                        |쿼리 블록|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-resource-group">RESOURCE_GROUP</a> | 구문을 실행하는 동안 리소스 그룹 설정                        | 전역 범위|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-subquery">SEMIJOIN</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-subquery">NO_SEMIJOIN</a> | Semijoin 전략에 영향을 줍니다. MySQL 8.0.17부터는 안티조인에도  적용됩니다. | 쿼리 블록 |
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">SKIP_SCAN</a><strong>,</strong> <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-index-level">NO_SKIP_SCAN</a> | 스킵 검색 최적화에 영향을 줍니다.| 테이블,  인덱스|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-set-var">SET_VAR</a> | 구문을 실행하는 동안 변수 설정| 전역 범위|
| <a href="https://dev.mysql.com/doc/refman/8.0/en/optimizer-hints.html#optimizer-hints-subquery">SUBQUERY</a> | 구체화에 영향을 미침,  IN-to-EXISTS 하위 쿼리 전략| 쿼리 블록|

```sql
/*+ BKA(table1) */ 
// 일괄 키 액세스 조인 처리에 영향을 줌
// 적용 범위 : 쿼리 블록, 테이블

/*+ BNL(table1, table2) */
// MySQL 8.0.20 이전: 블록 중첩 루프 조인 처리에 영향을 줌
// MySQL 8.0.18 이상: 해시 조인 최적화에도 영향을 줌
// MySQL 8.0.20 이상부터 해시 조인 최적화에만 영향을 줌
// 적용 범위 : 쿼리 블록, 테이블

/*+ NO_RANGE_OPTIMIZATION(table3 PRIMARY) */
// 범위 최적화에 영향
// 적용 범위 : 테이블, 인덱스

/*+ QB_NAME(queryblock1) */
// 쿼리 블록에 이름 할당
// 적용 범위 : 쿼리 블록

SELECT /*+ BNL(t1) BKA(t2) */ ...
// 하나의 쿼리 블록에서 여러 힌트를 사용할 땐, 하나의 힌트 주석안에 여러개의 힌트를 선언하여 사용해야 한다.
// 즉, SELECT /*+ BNL(t1) */ /* BKA(t2) */ ... 는 안됨.

옵티마이저 힌트는 SELECT, UPDATE, INSERT, REPLACE, DELETE문에서 허용된다.

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
조인 순서 최적화 힌트는 옵티마이저가 테이블을 조인하는 순서에 영향을 준다.

### 사용 방법
```sql
HINT_NAME([@query_block_name])

HINT_NAME([@query_block_name] TABLE_NAME [, tbl_name] ...)
HINT_NAME(TABLE_NAME[@query_block_name] [, TABLE_NAME[@query_block_name]] ...)
```

HINT_NAME에 올 수 있는 조인 순서 최적화 힌트는 4가지가 있다.
- JOIN_FIXED_ORDER: FROM절에 나타나는 순서대로(FIXED) 테이블을 조인하도록 지시
- JOIN_ORDER: 가능하다면 나타나는 순서대로 조인하도록 지시
- JOIN_PREFIX: 조인 순서의 첫 번째 테이블에 대해 힌트에 지정된 테이블 순서를 사용하도록 지시
- JOIN_SUFFIX: 조인 순서의 마지막 테이블에 대해 힌트에 지정된 테이블 순서를 사용하도록 지시

## 인덱스 힌트

MySQL 힌트는 다른 DBMS보다 옵티마이저에 미치는 영향이 크다.<br>
자주 사용되는 힌트는 대략 5개가 정도로 추릴 수 있다.

힌트사용법

MySql 힌트는 오라클과 같이 SQL의 주석으로 해석되는 것이 아니라 SQL의 일부로 해석되기 때문에 잘못사용하면 에러가 발생한다. 힌트를 표기하는 방법은 2가지가 있다.

1)주석표기 없이 SQL의 일부로 사용
```sql
select USE INDEX (PRIMARY) * from employees where emp_no=10001;
```

2)주석표기 방법
```sql
select /*! USE INDEX (PRIMARY) com.aaa.ss.class 날찌  */ * from employees where emp_no=10001;
```


Mysql를 사용을 하다보면 원하는 인덱스가 아니고 다른 인덱스를 사용하여 쿼리 성능이 느린 경우가 있다. 

이때 Mysql에서 제공하는 Index Hints를 쓰면 강제적으로 할당한 Index를 이용하여 쿼리가 실행이 된다. 

하지만 JPA(hibernate)에서 사용이 불가능하기 때문에 JdbcTemplate 등을 이용하여 Native Query로 활용해야 된다.

### 사용 방법
```sql
table_name [[AS] alias] [index_hint_list]

index_hint_list:
    index_hint [index_hint] ...

index_hint:
    USE {INDEX|KEY}
      [FOR {JOIN|ORDER BY|GROUP BY}] ([index_list])
  | IGNORE {INDEX|KEY}
      [FOR {JOIN|ORDER BY|GROUP BY}] (index_list)
  | FORCE {INDEX|KEY}
      [FOR {JOIN|ORDER BY|GROUP BY}] (index_list)

index_list:
    index_name [, index_name] ...
```

- USE 키워드 : 특정 인덱스를 사용하도록 권장
- IGNORE 키워드 : 특정 인덱스를 사용하지 않도록 지정
- FORCE 키워드 : USE 키워드와 동일한 기능을 하지만, 옵티마이저에게 보다 강하게 해당 인덱스를 사용하도록 권장

- USE INDEX FOR JOIN : JOIN 키워드는 테이블간 조인뿐 아니라 레코드 검색하는 용도까지 포함
- USE INDEX FOR ORDER BY : 명시된 인덱스를 ORDER BY 용도로만 사용하도록 제한
- USE INDEX FOR GROUP BY : 명시된 인덱스를 GROUP BY 용도로만 사용하도록 제한

### 사용 예제
```sql
SELECT * FROM table1 USE INDEX (col1_index,col2_index)
  WHERE col1=1 AND col2=2 AND col3=3;

SELECT * FROM table1 IGNORE INDEX (col3_index)
  WHERE col1=1 AND col2=2 AND col3=3;
```

복잡한 쿼리의 경우 서로의 인덱스가 물고 물려서 필요한 인덱스를 안타고

엉뚱한 인덱스를 사용하는 경우가 있거든요.

예를 들어서 A, B, C의 인덱스가 순서대로 사용되어야 하는데

옵티마이저가 B, C, A 순으로 처리먹혀서 속도가 느려지는 경우에 이런 순서를 잡기 위해서 힌트를 사용하기도 한다.


힌트를 써야한다 -> 정확한 작동. 단, 쿼리 작성자의 스킬을 믿어야 하고 조회조건 칼럼 변경이나 인덱스 변경시 힌트 확인 필요.

힌트를 쓰지 말아야 한다. -> DB 변화의 유연성과 무분별한 힌트 사용을 방지하기 위해.

MySQL 5.7까지는 힌트를 줘도 힌트 외의 실행계획을 평가합니다.
참고로 8.0부터는 힌트가 있을 때, 옵티마이저가 다른 실행계획을 만들지 않기 때문에 성능에 도움이 됩니다.

MySQL 8.0은 이전 버젼보다 훨씬 강력하고 편의성이 강한 Optimizer hint를 제공한다. 새롭게 추가된 Hint 중 유용한 Hint는 다음과 같다.



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
