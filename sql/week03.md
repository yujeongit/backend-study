**『SQL 첫걸음』 (한빛미디어)**
### 5장. 집계와 서브쿼리
### 5-1. 집계함수와 COUNT
SQL은 데이터 집합을 다루는 언어로, 집합의 개수나 합계 등을 구하기 위한 집계함수를 제공합니다.

대표적인 집계함수: COUNT, SUM, AVG, MIN, MAX

```
-- 테이블 전체 행 개수 구하기
SELECT COUNT(*) FROM locations;

-- WHERE 조건절 추가
SELECT COUNT(*) FROM locations WHERE name LIKE '%역%';
```

- COUNT(열명): 지정한 열에서 NULL을 제외한 행 개수를 반환
- COUNT(*): NULL 포함 여부와 관계없이 전체 행 개수를 카운트

### 5-2. DISTINCT로 중복 제거

```
-- 중복 제거 후 데이터 조회
SELECT DISTINCT name FROM locations;

-- 중복과 NULL을 모두 제거한 개수 구하기
SELECT COUNT(ALL phone_number), COUNT(DISTINCT phone_number) FROM users;
```
- COUNT(ALL ...): NULL만 제거
- COUNT(DISTINCT ...): NULL 제거 및 중복값을 제거

### 5-3. SUM과 AVG
```
-- 합계 구하기 (수치형 열만 가능)
SELECT SUM(age) FROM owners;

-- 평균 구하기
SELECT AVG(age), SUM(age)/COUNT(age) FROM owners;
```
- SUM, AVG 모두 NULL 값을 무시하고 처리
- NULL을 0으로 간주해 평균을 내고 싶다면 CASE 문으로 NULL을 0으로 변환 후 AVG를 적용해야 한다.

### 5-4. MIN과 MAX
MIN, MAX는 수치형뿐만 아니라 문자열형과 날짜시간형에서도 사용 가능함.

```
SELECT MIN(running_time), MAX(running_time), MIN(release_date), MAX(release_date) FROM movies;
```
### 5-5. GROUP BY와 HAVING
- 특정 열의 값이 같은 행들을 하나의 그룹으로 묶어 집계

```
SELECT seat_remain, COUNT(*) FROM tickets GROUP BY seat_remain;
```
- 집계함수는 WHERE 구의 조건식에서 사용할 수 없습니다. 내부 처리 순서상 WHERE가 GROUP BY보다 먼저 실행되기 때문
- 쿼리 내부 처리 순서: WHERE -> GROUP BY -> HAVING -> SELECT -> ORDER BY

```
-- 집계 결과에 조건을 걸 때는 HAVING 절 사용
SELECT seat_remain, COUNT(*) FROM tickets GROUP BY seat_remain HAVING COUNT(*) > 1;

-- 정렬까지 함께 적용하는 예시
SELECT seat_remain, SUM(price) FROM tickets GROUP BY seat_remain ORDER BY SUM(price) ASC;
```
- 주의: GROUP BY에 지정하지 않은 열은 집계함수를 감싸지 않은 채 SELECT 구에 작성하면 안 됩니다.

### 5-6. 서브쿼리
- 서브쿼리는 SQL 명령문 안에 지정하는 하부 SELECT 명령으로, 괄호 ()로 묶어 작성
- 단 하나의 행과 하나의 열(1x1 데이터)을 반환하는 서브쿼리를 '스칼라 서브쿼리'라고 부르며, WHERE 절이나 SELECT 절에 사용하기 용이하다.

```
-- WHERE 절에서 스칼라 서브쿼리 활용
DELETE FROM owners WHERE age = (SELECT MIN(age) FROM owners);

-- SELECT 절에서 스칼라 서브쿼리 활용
SELECT 
    (SELECT COUNT(*) FROM movies) AS movie_cnt,
    (SELECT COUNT(*) FROM actors) AS actor_cnt;

-- FROM 절에서 서브쿼리 활용 (인라인 뷰)
SELECT * FROM (SELECT * FROM movies) sq;
```
### 5-7. 상관 서브쿼리와 EXISTS
- EXISTS 술어는 서브쿼리가 반환하는 결과 데이터가 존재하는지 여부(참/거짓)를 판별할 때 사용합니다.

```
-- actors 테이블에 first_name이 '정'인 데이터가 존재하면 업데이트 실행
UPDATE movies SET title = '있음' WHERE EXISTS (
    SELECT * FROM actors WHERE actors.first_name = '정'
);
```
- 부모 쿼리와 자식 서브쿼리가 특정 조건을 주고받으며 관계를 맺는 형태를 상관 서브쿼리라 합니다. 열 이름이 중복될 수 있으므로 테이블명.열명 명시가 필요
- 집합 안에 특정 값이 존재하는지 비교할 때는 IN 연산자를 사용

```
SELECT * FROM movies WHERE id IN (3, 5, 7);
```

### 6장. 데이터베이스 객체 작성과 삭제
### 6-1. 데이터베이스 객체
데이터베이스 객체란 테이블, 뷰, 인덱스 등 데이터베이스 내에 실체를 가지고 정의되는 모든 구조물을 의미
- 명명 규칙: 기존 예약어 중복 불가, 숫자로 시작 불가, 특수문자는 언더바(_)만 허용
- 스키마: 객체가 생성되는 그릇(네임스페이스)으로, 스키마가 다르면 같은 이름의 객체도 공존 가능

### 6-2. 테이블 작성, 삭제, 변경

```
-- 테이블 생성
CREATE TABLE sample62 (
    no INTEGER NOT NULL,
    a VARCHAR(30),
    b DATE
);

-- 테이블 삭제
DROP TABLE sample62;

-- 데이터 행 빠른 삭제
TRUNCATE TABLE sample62;
```
- ALTER TABLE을 사용하여 테이블 구조를 변경할 수 있음.

```
-- 열 추가
ALTER TABLE sample62 ADD newcol INTEGER;

-- 열 속성(자료형) 변경
ALTER TABLE sample62 MODIFY newcol VARCHAR(20);

-- 열 이름 변경
ALTER TABLE sample62 CHANGE old_col new_col VARCHAR(20);

-- 열 삭제
ALTER TABLE sample62 DROP newcol;
```

### 6-3. 제약조건
NOT NULL, UNIQUE, PRIMARY KEY 등의 제약을 설정할 수 있음.

```
-- 테이블 생성 시 복수열 기본키 지정 예시
CREATE TABLE sample632 (
    no INTEGER NOT NULL,
    sub_no INTEGER NOT NULL,
    name VARCHAR(30),
    CONSTRAINT pkey_sample PRIMARY KEY (no, sub_no)
);

-- 기존 테이블에 제약 추가/삭제
ALTER TABLE sample632 ADD CONSTRAINT pkey_sample632 PRIMARY KEY(no);
ALTER TABLE sample632 DROP PRIMARY KEY;
```
- 기본키(Primary Key)는 테이블 내 행을 유일하게 식별하는 키로, 중복 값을 허용하지 않으며 NOT NULL 조건이 필수

### 6-4. 인덱스
인덱스는 검색 속도(SELECT 명령 실행 속도) 향상을 위해 테이블에 별도로 작성하는 데이터 구조

```
-- 인덱스 생성
CREATE INDEX idx_sample ON sample634(a);

-- 인덱스 삭제
DROP INDEX idx_sample ON sample634;

-- 쿼리 실행계획 확인
EXPLAIN SELECT * FROM sample634 WHERE a = 'a';
```
### 6-5. 뷰
뷰는 SELECT 문에 이름을 붙여 데이터베이스 객체화한 가상 테이블입니다. 실체 데이터 저장 공간을 차지하지 않고 복잡한 SELECT 문을 재사용할 때 사용

```
-- 뷰 생성
CREATE VIEW sample_view AS SELECT id, name FROM users WHERE status = 'ACTIVE';

-- 뷰 조회
SELECT * FROM sample_view;

-- 뷰 삭제
DROP VIEW sample_view;
```
