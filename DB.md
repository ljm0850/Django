# DB

## 1.Database

- 체계화된 데이터의 모임
- 몇 개의 자료 파일을 조직적으로 **통합**하여 자료 항목의 **중복을 없애고** 자료를 구조화 하여 기억 시켜 놓은 **자료의 집합체**

- **장점**
  - 데이터 중복 최소화
  - 데이터 일관성
  - 데이터 표준화



### RDB

- Relational Database
- key와 value의 관계를 표 형태로 정리한 데이터 베이스
- 관계형 모델 기반



#### 용어 정리

- 스키마(schema) : 자료의 구조, 표현방법, 관계 등 명세를 기술한것
  - id INT, name TEXT, age INT ...

- 테이블(table) : 열(컬럼/필드)과 행(레코드/값)의 모델을 사용해 조직된 데이터 요소들의 집합
- 열(column) : 각 열에 고유한 데이터 형식 지정
  - name이라는 field에 이름(TEXT) 정보가 저장

- 행(row) : 실제 데이터가 저장되는 형태(특정 id에 ,name,age...이 한세트로)
  - 레코드 = 1개의 행<ljm0850의 모든 데이터들,ljm의 모든 데이터들은 각각 한개의 레코드(행)>

- 기본키(Primary Key) : 각 행의 고유 값



### RDBMS

- Relational Database Management System

- 관계형 모델을 기반으로 하는 데이터 베이스 관리 시스템
  - MySQL , SQLite, ORACLE, PostgreSQL ...
    - Django에서는 SQLite(안드로이드 기본 탑재)

#### SQLite Data Type

- NULL
- INTEGER
- REAL
- TEXT
- BLOB
  - 입력된 그대로 저장된 데이터



## 2.SQL

- RDBMS 데이터 관리를 위해 설계된 언어
- DB 스키마 생성 및 수정
- 자료의 검색 및 관리
- DB 객체 접근 조정 관리



### SQL 문법 분류

- DDL - 데이터 정의 언어
  - CREATE
  - DROP
  - ALTER
- DML - 데이터 조작 언어
  - INSERT : 데이터 추가(삽입)
  - UPDATE : 데이터 갱신
  - DELETE : 데이터 삭제
  - SELECT : 데이터 조회
- DCL - 데이터 제어 언어
  - GRANT
  - REVOKE
  - COMMIT
  - ROLLBACK



### DDL - 데이터 정의

#### DB생성

```sqlite
$ sqlite3 database
--예시
$ sqlite3 DB_name.sqlite3		-- DB열고
sqlite> .databases				-- DB목록 확인
```



#### CSV 파일 불러오기

```sqlite
sqlite> .mode csv
sqlite> .import 파일명.csv 테이블명

-- 예시
sqlite> .import ljm0850.csv table_name
```



#### 테이블 생성(CREATE)

```sql
CREATE TABLE table_name(
	id INTEGER PRIMARY KEY,
    name TEXT NOT NULL);
```

- PRIMARY KEY는 INTEGER에서만 사용 가능

- NOT NULL 적용시 필수 입력



#### 테이블 & 스키마 조회 명령어 in sqlite

```sqlite
sqlite> .tables          // 테이블 목록 조회
sqlite> .schema table_name    // 특정 테이블 스키마 조회
```



#### 테이블 제거(DROP)

```sql
sqlite> DROP TABLE table_name;
sqlite> .tables // 테이블 제거 확인
```



### DML

#### 추가(INSERT)

```sql
-- type1
INSERT INTO table_name (column1, column2, ...) VALUES(value1, value2);
-- type2
INSERT INTO table_name VALUES(모든 column에 대한 값 , 구분)
```

- PRIMARY KEY 미 지정시 rowid 형태로 SQLite가 자동 관리



#### 조회(SELECT)

```sql
-- 모든 컬럼 조회
SELECT * FROM table_name;

-- 특정 컬럼 조회
SELECT column1, column2 FROM table_name;

-- LIMIT: 원하는 개수(num)만큼 조회 
-- OFFSET: 특정 위치에서부터 가져올 때(맨 위부터 num만큼 offset을 준 값부터 가져옴)
SELECT column1, column2
FROM table_name
LIMIT num OFFSET num;

-- WHERE: 조건을 통해 값 가져오기 
SELECT column1, column2
FROM table_name
WHERE column=value;

-- DISTINCT: 중복없이 가져오기 
SELECT DISTINCT column FROM table_name;
```

- WHERE 활용

```sql
SELECT * FROM table_name WHERE age >= 20;	-- age 20이상만 조회
SELECT * FROM table_name WHERE age >= 20 and name='ljm0850' -- age 20이상,name=ljm0850 조회
```







#### 삭제(DELETE)

```sql
DELETE FROM table_name
WHERE 조건;

-- 예시
DELETE FROM table_name
WHERE name='ljm0850';
```



#### 수정(UPDATE)

```sql
UPDATE table_name
SET column1=value1, column2=value2, ...
WHERE 조건;

-- 예시 id가 1의 행의 name을 ljm0850, address를 대한민국으로 수정
UPDATE table_name
SET name='ljm0850', address='대한민국'
WHERE rowid=1;
```



### Aggregate function

- 집계 함수
- 값 집합에 대해 계산을 수행하여 값을 반환
- SELECT 구문에서 사용
- 종류
  - COUNT()
  - AVG()
  - MAX()
  - MIN()
  - SUM

```sql
SELECT COUNT(*) FROM table_name; -- 레코드 값들의 개수 조회
SELECT AVG(age) FROM table_name WHERE age >= 30; -- age 30이상의 age 평균 조회
```



### LIKE

- 패턴 일치를 기반으로 데이터를 조회

- SQLite 는 2가지 wildcards 제공

  - %
  - _

  

#### 1. %

- % 자리에 문자열이 0개 이상 있다 (없을수도 있다)

```sql
SELECT * FROM table_name WHERE name LIKE 'ljm%';
```

#### 2. _

- _ 자리에 문자열이 반드시 1개만 존재

```sql
SELECT * FROM table_name WHERE age LIKE '2_';
```

#### 혼합 사용

```sql
SELECT * FROM table_name WHERE name LIKE 'l_m08%';
```



### ORDER BY

- 조회시 결과를 정렬 할 때 사용
- ASC - 오름차순 (DEFAULT)
- DESC - 내림차순

``` sql
-- age먼저 정렬, 그 후에 name 정렬 상위 10개만 조회
SELECT * FROM table_name ORDER BY age, name ASC LIMIT 10;
```



### GROUP BY

- 행 집합에서 요약 행 집합을 만듬
- WHERE 포함시 WHERE 뒤에 작성해야함

```sql
-- 같은 name을 가진 사람이 몇 명인지 조회
SELECT name, COUNT(*) FROM table_name GROUP BY name;
SELECT name, COUNT(*) AS name_cnt FROM table_name GROUP BY name;
```

- AS 를 활용시 조회시 count()를 적용한 컬럼 이름을 바꿀수 있음
  - 미적용시 위와 같이 입력시 COUNT(*)이 컬럼 이름



### ALTER

- 테이블 이름 변경

```sql
ALTER TABLE table_name
RENAME TO new_table_name;
```

- 새로운 컬럼 추가

```sql
ALTER TABLE table_name ADD COLUMN column_name datatype;
-- 예시
ALTER TABLE table_name ADD COLUMN address TEXT

-- NOT NULL 적용시
ALTER TABLE table_name ADD COLUMN address TEXT NOT NULL DEFAULT 'default_value'
-- 기존 DB에는 address 값이 없으므로 기존 db의 address 값을 default_value로 할당
```

- column 이름 수정 (3.25ver)

```sql
ALTER TABLE table_name RENAME COLUMN oldname TO newname.
```

- drop column (3.35ver) <더 알아보자>

```sql
ALTER TABLE table_name DROP COLUMN column_name
```

