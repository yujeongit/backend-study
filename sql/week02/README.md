**『SQL 첫걸음』 (한빛미디어)**

## 3. 정렬과 연산

### 1. `ORDER BY`를 이용한 정렬

`ORDER BY`는 `SELECT`로 조회한 결과의 **출력 순서**를 지정할 때 사용한다.

```sql
SELECT 열명
FROM 테이블명
WHERE 조건식
ORDER BY 열명;
```

* `ASC` : 오름차순
* `DESC` : 내림차순
* `ASC`는 기본값이므로 생략 가능

```sql
-- 오름차순
SELECT *
FROM sample31
ORDER BY age ASC;

-- 내림차순
SELECT *
FROM sample31
ORDER BY age DESC;
```

데이터 유형에 따라 정렬 기준이 달라진다.

* 숫자형 → 숫자의 크기
* 날짜/시간형 → 날짜와 시간의 순서
* 문자열형 → 사전식 순서

> `ORDER BY`는 테이블에 저장된 데이터 자체의 순서를 변경하는 것이 아니라, **조회 결과가 반환되는 순서만 변경**

---

### 2. 여러 열을 기준으로 정렬하기

`ORDER BY`에는 여러 개의 열을 지정할 수 있다.

```sql
SELECT *
FROM sample32
ORDER BY a, b;
```

정렬은 **앞에 작성한 열부터 우선적으로 적용**

```text
ORDER BY a, b
→ 먼저 a로 정렬
→ a의 값이 같은 행끼리 b로 정렬
```

각 열마다 정렬 방향을 다르게 지정하는 것도 가능하다.

```sql
SELECT *
FROM sample32
ORDER BY a ASC, b DESC;
```

* `a` → 오름차순
* `b` → 내림차순

`ORDER BY`에서 `ASC`, `DESC`를 생략하면 기본적으로 `ASC`가 적용

#### `NULL`의 정렬

`NULL`은 일반적인 값처럼 크기를 비교할 수 없다.

MySQL에서는 `NULL`을 가장 작은 값으로 취급한다.

* `ASC` → `NULL`이 앞쪽
* `DESC` → `NULL`이 뒤쪽

---

### 3. `LIMIT`으로 결과 행 제한하기

`LIMIT`은 조회 결과에서 **반환할 행의 최대 개수**를 제한한다.

```sql
SELECT *
FROM 테이블명
LIMIT 행수;
```

예를 들어:

```sql
SELECT *
FROM sample33
LIMIT 3;
```

→ 최대 3개의 행만 조회한다.

`ORDER BY`와 함께 사용하면 원하는 순위의 데이터만 가져올 수 있다.

```sql
SELECT *
FROM sample33
ORDER BY no DESC
LIMIT 3;
```

→ `no`를 내림차순으로 정렬한 후 상위 3개를 반환한다.

#### `OFFSET`

`OFFSET`을 사용하면 결과에서 몇 개의 행을 건너뛸지 지정할 수 있다.

```sql
SELECT *
FROM 테이블명
LIMIT 행수 OFFSET 시작위치;
```

```sql
SELECT *
FROM sample33
LIMIT 3 OFFSET 3;
```

→ 앞의 3개 행을 건너뛰고 그다음부터 최대 3개를 가져온다.

페이지 단위로 데이터를 보여주는 **페이지네이션**에 활용할 수 있다.

---

### 4. 수치 데이터 연산

SQL에서는 조회 과정에서 수치 연산을 수행할 수 있다.

| 연산자 | 의미  |
| :-- | :-- |
| `+` | 덧셈  |
| `-` | 뺄셈  |
| `*` | 곱셈  |
| `/` | 나눗셈 |
| `%` | 나머지 |

```sql
SELECT price * quantity
FROM sample34;
```

상품의 가격과 수량을 곱해 총 금액을 계산하는 것처럼 사용할 수 있다.

#### `AS`로 별칭 지정

계산 결과에 이름을 붙이려면 `AS`를 사용한다.

```sql
SELECT
    price * quantity AS amount
FROM sample34;
```

→ 계산 결과 열의 이름이 `amount`로 표시된다.

#### `WHERE`에서 연산하기

계산식 자체를 조건으로 사용할 수도 있다.

```sql
SELECT *
FROM sample34
WHERE price * quantity >= 2000;
```

단, `WHERE`에서는 `SELECT`에서 지정한 별칭을 사용할 수 없다.

```sql
-- 사용할 수 없음
WHERE amount >= 2000
```

이는 `WHERE`가 `SELECT`보다 먼저 처리되기 때문이다.

반면 `ORDER BY`에서는 `SELECT`에서 지정한 별칭을 사용할 수 있다.

```sql
SELECT price * quantity AS amount
FROM sample34
ORDER BY amount DESC;
```

#### `NULL`과 연산

`NULL`은 숫자 `0`과 다르다.

따라서 `NULL`이 포함된 연산의 결과는 `NULL`이 된다.

```text
NULL + 10 → NULL
NULL * 10 → NULL
```

#### `ROUND()` 함수

`ROUND()`는 숫자를 반올림할 때 사용하는 함수다.

```sql
ROUND(값, 자릿수)
```

```sql
SELECT ROUND(amount, 1)
FROM sample341;
```

두 번째 인수를 이용해 반올림할 소수점 자릿수를 지정할 수 있다.

---

### 5. 문자열 연산

SQL에서는 문자열을 연결하거나 원하는 부분을 추출하고, 공백 및 문자열 길이를 처리할 수 있다.

#### `CONCAT()`

문자열을 연결할 때 사용한다.

```sql
SELECT CONCAT(quantity, unit)
FROM sample35;
```

예:

```text
10 + 개 → 10개
24 + 통 → 24통
1 + 장 → 1장
```

데이터베이스 제품에 따라 문자열 연결 방법이 다를 수 있다.

* MySQL → `CONCAT()`
* SQL Server → `+`
* Oracle / PostgreSQL / DB2 → `||`

#### `SUBSTRING()`

문자열에서 특정 부분만 추출한다.

```sql
SUBSTRING(문자열, 시작위치, 길이)
```

```sql
SUBSTRING('20250328', 1, 4)
→ 2025

SUBSTRING('20250328', 5, 2)
→ 03
```

#### `TRIM()`

문자열의 앞뒤에 존재하는 불필요한 공백을 제거한다.

```sql
TRIM('ABC  ')
→ 'ABC'
```

문자열 중간의 공백은 제거하지 않는다.

#### 문자열 길이 함수

`CHARACTER_LENGTH()` 또는 `CHAR_LENGTH()`는 문자열의 **문자 수**를 반환한다.

```sql
CHARACTER_LENGTH('ABC')
→ 3
```

반면 `OCTET_LENGTH()`는 문자열을 **바이트 단위**로 계산한다.

---

### 6. 날짜 데이터 연산

SQL에서는 날짜와 시간 데이터에 대해서도 연산을 수행할 수 있다.

#### 현재 날짜와 시간

```sql
CURRENT_TIMESTAMP
```

→ 시스템의 현재 날짜와 시간을 반환한다.

#### 날짜에 기간 더하기

```sql
SELECT CURRENT_DATE + INTERVAL 1 DAY;
```

→ 현재 날짜에서 하루 뒤의 날짜를 계산한다.

날짜 사이의 차이를 계산할 때는 `DATEDIFF()`를 사용할 수 있다.

```sql
DATEDIFF('2025-04-01', '2025-03-28')
```

→ 두 날짜 사이의 일수 차이를 계산한다.

---

### 7. `CASE`를 이용한 데이터 변환

`CASE`는 조건에 따라 서로 다른 값을 반환할 때 사용한다.

```sql
CASE
    WHEN 조건식1 THEN 결과1
    WHEN 조건식2 THEN 결과2
    ELSE 결과3
END
```

`WHEN` 조건을 위에서부터 확인하고, **처음으로 참이 되는 조건의 `THEN` 결과**를 반환한다.

#### `NULL`을 다른 값으로 변경

```sql
SELECT
    a,
    CASE
        WHEN a IS NULL THEN 0
        ELSE a
    END AS result
FROM sample37;
```

→ `a`가 `NULL`이면 `0`, 그렇지 않으면 기존 값을 반환한다.

#### `COALESCE()`

`NULL`을 다른 값으로 대체하는 경우 `COALESCE()`도 사용할 수 있다.

```sql
SELECT COALESCE(a, 0)
FROM sample37;
```

→ `a`가 `NULL`이면 `0`을 반환한다.

#### 단순 `CASE`

하나의 값을 기준으로 여러 값을 대응시킬 수도 있다.

```sql
CASE a
    WHEN 1 THEN '남자'
    WHEN 2 THEN '여자'
    ELSE '미지정'
END
```

예:

```text
1 → 남자
2 → 여자
그 외 → 미지정
```

#### `CASE` 사용 시 주의점

`ELSE`를 생략하면 조건에 해당하지 않는 값은 `NULL`이 된다.

또한 `NULL`은 `=` 연산자로 비교할 수 없다.

```sql
-- 올바르지 않은 방식
CASE a
    WHEN NULL THEN ...
END
```

`NULL` 여부를 판단하려면 `IS NULL`을 사용하는 검색 CASE를 활용한다.

---

## 4. 데이터의 추가, 삭제, 갱신

### 1. `INSERT` - 데이터 추가

`INSERT`는 테이블에 **새로운 행을 추가**할 때 사용한다.

```sql
INSERT INTO 테이블명
VALUES (값1, 값2, ...);
```

예:

```sql
INSERT INTO sample41
VALUES (1, 'ABC', '2025-03-29');
```

#### 특정 열만 지정해서 추가

모든 열에 값을 입력하지 않고 필요한 열만 지정할 수도 있다.

```sql
INSERT INTO 테이블명 (열1, 열2)
VALUES (값1, 값2);
```

```sql
INSERT INTO sample41(a, no)
VALUES ('XYZ', 2);
```

지정하지 않은 열에는 `NULL` 또는 해당 열의 기본값이 적용될 수 있다.

---

### 2. `NOT NULL`과 `DEFAULT`

#### `NOT NULL`

`NOT NULL`은 해당 열에 `NULL` 값을 저장하지 못하도록 하는 제약 조건이다.

즉, 테이블에 저장되는 데이터를 일정한 규칙으로 제한한다.

#### `DEFAULT`

`DEFAULT`는 데이터를 입력할 때 값을 지정하지 않은 경우 사용되는 **기본값**이다.

```sql
INSERT INTO sample411(no, d)
VALUES (2, DEFAULT);
```

또는 해당 열을 생략하여 기본값을 적용할 수도 있다.

```sql
INSERT INTO sample411(no)
VALUES (3);
```

---

### 3. `DELETE` - 데이터 삭제

`DELETE`는 테이블의 행을 삭제한다.

```sql
DELETE FROM 테이블명
WHERE 조건식;
```

예:

```sql
DELETE FROM sample41
WHERE no = 3;
```

→ `no`가 3인 행을 삭제한다.

#### `WHERE` 생략 시 주의

```sql
DELETE FROM sample41;
```

`WHERE`를 생략하면 **모든 행이 삭제 대상**이 된다.

따라서 실제 데이터를 삭제할 때는 `WHERE` 조건을 정확하게 작성해야 한다.

`DELETE`에서는 `ORDER BY`를 사용할 수 없다.

---

### 4. `UPDATE` - 데이터 수정

`UPDATE`는 기존 데이터의 값을 변경할 때 사용한다.

```sql
UPDATE 테이블명
SET 열명 = 값
WHERE 조건식;
```

예:

```sql
UPDATE sample41
SET b = '2014-09-07'
WHERE no = 2;
```

→ `no`가 2인 행의 `b` 값을 변경한다.

#### 기존 값을 이용한 연산

기존 데이터에 연산을 적용해 수정할 수도 있다.

```sql
UPDATE sample41
SET no = no + 1;
```

→ 각 행의 `no` 값이 1씩 증가한다.

#### 여러 열 수정

쉼표를 사용하면 여러 열을 동시에 변경할 수 있다.

```sql
UPDATE 테이블명
SET 열1 = 값1,
    열2 = 값2
WHERE 조건식;
```

#### `NULL`로 수정

열의 값을 `NULL`로 변경하는 것도 가능하다.

단, `NOT NULL` 제약이 설정된 열은 `NULL`로 변경할 수 없다.

---

### 5. 물리 삭제와 논리 삭제

데이터를 삭제하는 방법은 크게 **물리 삭제**와 **논리 삭제**로 나눌 수 있다.

#### 물리 삭제

`DELETE`를 이용해 실제 행 자체를 삭제하는 방식이다.

```sql
DELETE FROM 테이블명
WHERE 조건식;
```

→ 데이터가 테이블에서 실제로 제거된다.

#### 논리 삭제

데이터를 실제로 삭제하지 않고 별도의 열을 이용해 **삭제 여부만 표시**하는 방식이다.

예를 들어:

```text
id | name | deleted
---|------|--------
1  | A    | 0
2  | B    | 1
```

`deleted = 1`인 데이터를 조회에서 제외하는 방식으로 삭제된 것처럼 처리할 수 있다.

| 구분       | 물리 삭제     | 논리 삭제       |
| :------- | :-------- | :---------- |
| 실제 행 삭제  | O         | X           |
| 데이터 보존   | 어려움       | 가능          |
| 삭제 여부 관리 | 별도 관리 불필요 | 삭제 플래그 등 필요 |

어떤 방식을 사용할지는 서비스의 특성과 데이터 관리 목적에 따라 결정한다.

---

## 5. 3·4장 핵심 SQL 문법

| 목적          | 문법                      |
| :---------- | :---------------------- |
| 결과 정렬       | `ORDER BY`              |
| 오름차순        | `ASC`                   |
| 내림차순        | `DESC`                  |
| 결과 개수 제한    | `LIMIT`                 |
| 시작 위치 지정    | `OFFSET`                |
| 계산          | `+`, `-`, `*`, `/`, `%` |
| 별칭 지정       | `AS`                    |
| 반올림         | `ROUND()`               |
| 문자열 연결      | `CONCAT()`              |
| 문자열 추출      | `SUBSTRING()`           |
| 공백 제거       | `TRIM()`                |
| 문자열 길이      | `CHARACTER_LENGTH()`    |
| 현재 날짜/시간    | `CURRENT_TIMESTAMP`     |
| 조건에 따른 값 변환 | `CASE`                  |
| NULL 대체     | `COALESCE()`            |
| 행 추가        | `INSERT`                |
| 행 삭제        | `DELETE`                |
| 데이터 수정      | `UPDATE`                |
| 실제 데이터 삭제   | 물리 삭제                   |
| 삭제 상태만 변경   | 논리 삭제                   |

## 총정리

```text
3장
→ 조회한 데이터를 정렬한다      : ORDER BY
→ 결과 개수를 제한한다          : LIMIT
→ 특정 위치부터 가져온다        : OFFSET
→ 숫자를 계산한다               : 연산자 / ROUND()
→ 문자열을 가공한다             : CONCAT / SUBSTRING / TRIM
→ 날짜를 계산한다               : 날짜 연산 / DATEDIFF()
→ 조건에 따라 값을 바꾼다       : CASE / COALESCE()

4장
→ 새로운 데이터를 넣는다        : INSERT
→ 데이터를 삭제한다             : DELETE
→ 기존 데이터를 수정한다        : UPDATE
→ 삭제 방식을 결정한다          : 물리 삭제 / 논리 삭제
```
