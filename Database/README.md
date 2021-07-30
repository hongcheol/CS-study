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


# 키
## 정의

## 종류

1. 후보키
1. 슈퍼키
1. 기본키
1. 대체키
1. 외래키


# 인덱스

## 정의
테이블에 대한 동작속도를 높이는 기술로 데이터베이스 조회 기능 성능을 높인다.

인덱스를 이용하면 Full Scan과 Table Scan을 줄이거나 없애서 속도를 높인다.

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

## 단점
