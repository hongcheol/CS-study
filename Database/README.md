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
테이블에 대한 동작속도를 높이는 자료구조로 데이터베이스 조회 기능 성능을 높인다.

인덱스를 이용하면 Full Scan과 Table Scan을 줄이거나 없앨 수 있다.


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
