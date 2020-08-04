---
layout: post
title:  "SQL 전문가되기 #1"
date:   2020-03-03 05:01:00 +0800
categories: SQL
tags: WorkSpace
--- 

본 내용은 **SQL 전문가 가이드 2013 Edition의 과목2** 내용을 참고로하여 정리한 글 입니다.

# **DDL(DATA DEFINITION LANGUAGE) - 데이터 정의어**

CREATE / ALTER, DROP, RENAME

## **기본키 설정**

```sql
CREATE TABLE TABLE2_NAME(
	TABLE2_ID
	CONSTRAINT TABLE2_PK PRIMARY KEY (TABLE2_ID)
);
```

## **외래키 설정**

```sql
CEATE TABLE TABLE_NAME(
	TABLE_ID
	TABLE2_ID
	CONSTRAINT TABLE_NAME_PK PRIMARY KEY (TABLE_ID),
	CONSTRAINT TABLE_NAME_FK FOREIGN KEY (TABLE2_ID) REFERENCES TABLE2_NAME(TABLE2_ID)
);
```

## **테이블 복사**

```sql
**CREATE TABLE TABLE_NAME
AS SELECT*FROM TABLE2_NAME;**
```

## **생성된 테이블 구조 확인**

```sql
DESCRIBE TABLE_NAME;
=DESC TABLE_NAME;
```

## **ALTER TABLE**

- ADD 칼럼

    ```sql
    ALTER TABLE TABLE_NAME
    ADD COLUMN_NAME TYPE;
    ```

- DROP 칼럼

    ```sql
    ALTER TABLE TABLE_NAME
    DROP COLUMN COLUMN_NAME;
    ```

- DEFAULT 설정

    ```sql
    ALTER TABLE TABLE_NAME
    MODIFY COLUMN_NAME TYPE DEFAULT 블라블라 NOT NULL; (디폴트 설정은 자유)
    ```

- RENAME COLUMN

    ```sql
    ALTER TABLE TABLE_NAME
    RENAME COLUMN COLUMN_NAME TO NEW_COLUMN_NAME;
    ```

- DROP CONSTRAINT

    ```sql
    /*기본키*/
    ALTER TABLE TABLE_NAME
    DROP CONSTRAINT TABLE_NAME_PK;
    ```

    ```sql
    /*외래키*/
    ALTER TABLE TABLE_NAME
    DROP CONSTRAINT TABLE_NAME_FK;
    ```

- ADD CONSTRAINT

    ```sql
    /*기본키*/
    ALTER TABLE TABLE_NAME
    ADD CONSTRAINT TABLE_NAME_PK
    PRIMARY KEY (TABLE_ID);
    ```

    ```sql
    /*외래키*/
    ALTER TABLE TABLE_NAME
    ADD CONSTRAINT TABLE_NAME_FK
    FOREIGN KEY (TABLE2_ID) REFERENCE TABLE2_NAME(TABLE2_ID);
    ```

## **RENAME TABLE**

```sql
RENAME TABLE TABLE_NAME TO NEW_TABLE_NAME
```

## **DROP TABLE**

CASECADE CONSTRAINT는 참조키가 있어도 삭제한다는 뜻

```sql
DROP TABLE TABLE_NAME [CASCADE CONSTRAINT];
```

## **TRUNCATE TABLE**

자료에 있는 모든 행을 삭제. 칼럼은 삭제 안함

```sql
TRUNCATET TABLE TABLE_NAME
```

# **DML(DATE MANIPULATION LANGUAGE) - 데이터 조작어**

SELECT / INSERT, UPDATE, DELETE

DDL과 DML의 차이는 DDL은 직접적으로 테이블에 영향을 미치기 때문에 COMMIT이 바로 처리되지만 DML은 그렇지 않다.

## INSERT

```sql
INSERT INTO TABLE_NAME (COLUMN_LIST)
VALUES (COLUMN_LIST에 넣을 VALUE_LIST);

INSERT INTO TABLE_NAME
VALUES (전체 COLUMN에 넣을 VALUE_LIST);
```

## UPDATE

```sql
UPDATE TABLE_NAME
SET COLUMN_NAME = NEW_VALUE;
```

## DELETE

선수 테이블의 테이터 모두 삭제. TRUNCATE TABLE과 비슷

```sql
DELETE FROM TABLE_NAME;
```

## SELECT

- ALL : Default 옵션이므로 별도로 표시하지 않아도 됨.
- DISTINCT : 중복된 데이터가 있는 경우 1건으로 처리해서 출력한다.

```sql
SELECT [ALL/DISTINCT] COLUMN_NAME1, COLUMN_NAME2....
FROM TABLE_NAME;
```

TABLE_NAME에 있는 데이터 모두 조회

- 좌측 정렬 : 문자 및 날짜 데이터
- 우측 정렬 : 숫자 데이터

```sql
SELECT * FROM TABLE_NAME
```

ALIAS 부여하기

```sql
SELECT COLUMN_NAME AS NEW_COLUMN_NAME
FROM TABLE_NAME
```

## 산술 연산자와 합성 연산자

**산술 연산자**

() : 우선순위 부여

* 곱하기, / 나누기, + 더하기, - 빼기

**합성 연산자**

CONCAT( COLUMN_NAME, COLUMN_NAME )을 사용해도 가능

```sql
SELECT COLUMN_NAME || ‘블라블라’ || COLUMN_NAME
FROM PLAYER;
```

# **TCL(TRANSACTION CONTROL LANGUAGE)**

COMMIT , ROLLBACK , SAVEPOINT

**Summary**

- TRANSACTION은 데이터베이스의 논리적 연산단위이다.
- TRANSACTION은 ALL OR NOTHING의 개념일 것이다.
- 올바르게 반영된 데이터를 데이터베이스에 반영시키는 것이 COMMIT
- RANSACTION 시작 이전의 상태로 콘트롤하는 것을 ROLLBACK
- SAVEPOINT

**TRANSACTION의 특성**

- **원자성(atomicity)** : 트랜잭션에서 정의된 연산들은 모두 성공적으로 실행되던지 아니면 전혀 실행되지 않은 상태로 남아 있어야 한다.( all or nothing )
- **일관성(consistency)** : 트랜잭션이 실행되기 전의 데이터베이스 내용이 잘못 되어 있지 않다면 트랜잭션이 실행된 이후에도 데이터베이스의 내용에 잘못이 있으면 안 된다.
- **고립성(isolation)** : 트랜잭션이 실행되는 도중에 다른 트랜잭션의 영향을 받아 잘못된 결과를 만들어서는 안된다.
- **지속성(durability)** : 트랜잭션이 성공적으로 수행되면 그 트랜잭션이 갱신한 데이터베이스의 내용은 영구적으로 저장된다.

## COMMIT

- COMMIT과 ROLLBACK 이전의 데이터 상태는 다음과 같다.
    - 단지 메모리 BUFFER에만 영향을 받았기 때문에 데이터의 변경 이전 상태로 복구 가능하다.
    - 현재 사용자는 SELECT 문장으로 결과를 확인 가능하다.
    - 다른 사용자는 현재 사용자가 수행한 명령의 결과를 볼 수 없다.
    - 변경된 행은 잠금(LOCKING)이 설정되어서 다른 사용자가 변경할 수 없다.
- COMMIT 이후의 데이터 상태는 다음과 같다.
    - 데이터에 대한 변경 사항이 데이터베이스에 반영된다.
    - 이전 데이터는영원히 잃어버리게 된다.
    - 모든 사용자는 결과를 볼 수 있다.
    - 관련된 행에 대한 잠금(LOCKING)이 풀리고, 다른 사용자들이 행을 조작할 수 있게 된다.
- SQL Server에서의 트랜잭션은 기본적으로 3가지 방식으로 이루어진다.
    - AUTO COMMIT
    - 암시적 트랜잭션
    - 명시적 트랜잭션

## ROLLBACK

- ROLLBACK 후의 데이터 상태는 다음과 같다.
    - 데이터에 대한 변경 사항은 취소된다.
    - 이전 데이터는 다시 재저장된다.
    - 관련된 행에 대한 잠금(LOCKING)이 풀리고, 다른사용자들이 행을 조작할 수 있게 된다.
- COMMIT과 ROLLBACK을 사용함으로써 다음과 같은 효과를 볼 수 있다.
    - 데이터 무결성 보장
    - 영구적인 변경을 하기 전에 데이터의 변경 사항 확인 가능
    - 논리적으로 연관된 작업을 그룹핑하여 처리 가능

## SAVEPOINT

```sql
SAVEPOINT SAVEPOINT_NAME;
ROLLBACK TO SAVEPOINT NAME;
```

## ETC

- 다음의 경우에는 COMMIT과 ROLLBACK을 실행하지 않아도 자동으로 트랜잭션이 종료된다.
- CREATE, ALTER, DROP, RENAME, TRUNCATE TABLE 등 DDL 문장을 실행하면 그 전후 시점에 자동으로 커밋된다.
- 부연하면, DML 문장 이후에 커밋 없이 DDL 문장이 실행되면 DDL 수행 전에 자동으로 커밋된다.
- 데이터베이스를 정상적으로 접속을 종료하면 자동으로 트랜잭션이 커밋된다.
- 애플리케이션의 이상 종료로 데이터베이스와의 접속이 단절되었을 때는 트랜잭션이 자동으로 롤백된다.

# WHERE 절

```sql
SELECT [DISTINCT/ALL] COLUMN_NAME [ALIAS_NAME]
FROM TABLE_NAME
WHERE CONDITION;
```

## 연산자의 종류

- **비교 연산자**
    - = 같다 / > 보다 크다 / >= 보다 크거나 같다 / < 보다 작다 / <= 보다 작거나 같다.
- **SQL 연산자**
    - BETWEEN a AND b : a와 b의 값 사이에 있으면 된다.(a와 b 값이 포함됨)
    - IN (list) : 리스트에 있는 값 중에서 어느 하나라도 일치하면 된다.
    - LIKE ‘비교문자열’ : 비교문자열과 형태가 일치하면 된다. (%, _사용)
    - IS NULL : NULL 값인 경우
- **논리 연산자**
    - AND / OR / NOT
- **부정비교 연산자**
    - != / ^= 같지 않다 / <> (ISO 표준, 모든 운영체제에서 사용 가능) / NOT COLUMN_NAME = / NOT COLUMN_NAME > ~보다 크지 않다.
- **부정 SQL 연산자**
    - NOT BETWEEN a AND b / NOT IN (list) / IS NOT NULL
- **연산자의 우선순위**
    1. 괄호 ()
    2. NOT 연산자
    3. 비교 연산자, SQL 비교 연산자
    4. AND
    5. OR
- **ROWNUM, TOP 사용**

    **ROWNUM**

    한 건의 행만 가져오고 싶을 때

    ```sql
    SELECT COLUMN_NAME FROM TABLE_NAME WHERE ROWNUM = 1;

    SELECT COLUMN_NAME FROM TABLE_NAME WHERE ROWNUM <= 1;

    SELECT COLUMN_NAME FROM TABLE_NAME WHERE ROWNUM < 2;
    ```

    두 건 이상의 N 행을 가져오고 싶을 때는 ROWNUM = N;처럼 사용할 수 없으며

    ```sql
    SELECT COLUMN_NAME FROM TABLE_NAME WHERE ROWNUM <= N;

    SELECT COLUMN_NAME FROM TABLE_NAME WHERE ROWNUM < N+1 
    ```

    **TOP 절**

    - SQL server에서 행의 수를 제한할 때 사용

# **함수(FUNCTION)**

cf ) DUAL 테이블의 특성은 다음과 같다.

- 사용자 SYS가 소유하며 모든 사용자가 엑세스 가능한 테이블이다.

- SELECT ~ FROM ~ 의 형식을 갖추기 위한 일종의 DUMMY 테이블이다.

- DUMMY라는 문자열 유형의 칼럼에 ‘X’라는 값이 들어 있는 행을 1건 포함하고 있다.

## 내장형 함수

- 문자형 함수 : 문자를 입력하면 문자나 숫자 값을 반환한다.

    ```sql
    LOWER, UPPER, SUBSTR/SUBSTRING, LENGTH/LEN, LTRIM, RTRIM, ASCII
    ```

- 숫자형 함수 : 숫자를 입력하면 숫자 값을 반환한다.

    ```sql
    ABS, MOD, ROUND, TRUNC, SIGN, CHR/CHAR, CEIL/CEILING, FLOOR, EXP, LOG, LN, POWER, SIN, COS, TAN
    ```

- 날짜형 함수 : DATE 타입의 값을 연산한다.

    ```sql
    SYSDATE/GETDATE, EXTRACT/DATEPART, TO_NUMBER(TO_CHAR(d,‘YYYY’|’MM’|’DD’))/YEAR|MONTH|DAY
    ```

- 변환형 함수 : 문자, 숫자, 날짜형 값의 데이터 타입을 변환한다.

    ```sql
    TO_NUMBER, TO_CHAR, TO_DATE/CAST, CONVERT
    ```

- NULL 관련 함수 : NULL을 처리하기 위한 함수

    ```sql
    NVL/ISNULL, NULLIF, COALESCE
    ```

## 단일행 함수의 중요한 특징

- SELECT, WHERE, ORDER BY 절에 사용 가능하다.
- 각 행(Row)들에 대해 개별적으로 작용하여 데ㅔ이터 값들을 조작하고, 각각의 행에 대한 조작 결과를 리턴한다.
- 여러 인자(Argument)를 입력해도 단 하나의 결과만 리턴한다.
- 함수의 인자(Arguments)를 상수, 변수, 표현식이 사용 가능하고, 하나의 인수를 가지는 경우도 있지만 여러 개의 인수를 가질 수도 있다.
- 특별한 경우가 아니면 함수의 인자(Arguments)로 함수를 사용하는함수의 중첩이 가능하다.

## CASE 표현

```sql
SELECT COLUMN_NAME,
			 CASE WHEN CONDITION1
						THEN CONDITION2
						ELSE CONDITION3
			 END [ALIAS_NAME]
FROM TABLE_NAME;
```

```sql
CASE
	SIMPLE_CASE_EXPRESSION 조건 or SEARCHED_CASE_EXPRESSION 조건
	ELSE 표현절
END
```

첫 번째 SIMPLE_CASE_EXPRESSION ( EQUI(=) 조건만 사용, DECODE 함수와 기능면에서 동일함 )

```sql
CASE
	EXPR WHEN COMPARISON_EXPR THEN RETURN_EXPR
	ELSE 표현절
END
```

**Example**

```sql
SELECT LOC,
			 CASE LOC
						WHEN ‘NEW YORK’ THEN ‘EAST’
						WHEN ‘BOSTON’ THEN ‘EAST’
			    	ELSE ‘ETC’
			 END as AREA
FROM DEPT;
```

-두 번째 SEARCHED_CASE_EXPRESSION ( EQUI(=) 조건 포함 여러 조건(>, >=, <, <=)을 이용한 조건절 사용 가능)

```sql
CASE
		WHEN CONDITION THEN RETURN_EXPR
		ELSE 표현절
END
```

**Example**

```sql
SELECT ENAME,
			 CASE WHEN SAL >= 3000 THEN ‘HIGH’
						WHEN SAL >= 1000 THEN ‘MID’
						ELSE ‘LOW’
			 END AS SALARY_GRADE
FROM EMP;
```

## NULL 관련 함수

NVL/ISNULL 함수

**NULL에 대한 특성**

- NULL 값은 아직 정의되지 않은 값으로 0 또는 공백과 다르다. 0은 숫자이고, 공백은 하나의 문자이다.
- NULL 값을 포함하는 연산의 경우 결과 값도 널 값이다. 모르는 데이터에 숫자를 더하거나 빼도 결과는 마찬가지 로 모르는 데이터인 것과 같다.
- 결과값을 NULL이 아닌 다른 값을 얻고자 할 때 NVL/ISNULL 함수를 사용한다. NULL 값의 대상이 숫자 유형 데이터인 경우는 주로 0(Zero)으로, 문자 유형 데이터인 경우는 블랭크보다는 ‘x’ 같이 해당 시스템에서 의미 없 는 문자로 바꾸는 경우가 많다.

**단일행 NULL 관련 함수의 종류**

NVL(표현식1, 표현식2) / ISNUL(표현식1, 표현식2)

- 표현식1의 결과값이 NULL이면 표현식2의 값을 출력한다. 단, 표현식1과 표현식2의 결과 데이터 타입이 같아야 한다. NULL 관련 가장 많이사용되는 함수이므로 상당히 중요하다.

NULLIF(표현식1, 표현식2)

- 표현식1이 표현식2와 같으면 NULL을, 같지 않으면 표현식1을 리턴한다.

COALESCE(표현식1, 표현식2, .....)

- 임의의 개수 표현식에서 NULL이 아닌 최초의 표현식을 나타낸다. 모든 표현식이 NULL이라면 NULL을 리턴한다.

**공집합의 NVL/ISNULL 함수 사용**

```sql
SELECT MGR FROM EMP WHERE ENAME = ‘JSC’;
/*-> 데이터를 찾을 수 없음*/
```

```sql
SELECT NVL(MGR,9999) MGR FROM EMP WHERE ENAME = ‘JSC’;
/*-> 역시 데이터를 찾을 수 없음***/**
```

```sql
SELECT MAX(MGR) MGR FROM EMP WHERE ENMAE = ‘JSC’;
/*-> NULL 값이 나옴 ( NVL/ISNULL로 하면 안나오고 적절한 집계 함수를 찾아서 NVL 대신 적용한다.)*/
```

```sql
SELECT NVL(MAX(MGR),9999) MGR FROM EMP WHERE ENAME = ‘JSC’;
/*-> 9999 나옴.*/
```

**NULLIF**

```sql
SELECT ENAME, EMPNO, MGR, NULLIF(MGR,7698) NULF
FROM EMP;
```

위의 SQL문은 아래와 같은 SQL문이다.

```sql
SELECT ENAME, EMPNO, MGR,
		   CASE WHEN MGR = 7698
						THEN NULL
						ELSE MGR
			 END NUIF
FROM EMP;
```

**기타 NULL 관련 함수 (COALESCE)**

```sql
SELECT ENAME, COMM, SAL, COALESCE(COMM, SAL) COAL
FROM EMP;
```

위의 SQL문은 아래와 같은 SQL문이다.

```sql
SELECT ENAME, COMM, SAL,
			 CASE WHEN COMM IS NOT NULL
						THEN COMM
						ELSE ( CASE WHEN SAL IS NOT NULL
												THEN SAL
												ELSE NULL
								   END)
			 END COAL
FROM EMP;
```

# **GROUP BY, HAVING 절**

**집계함수(Aggregate Function)**

- 여러 행들의 그룹이 모여서 그룹당 단 하나의 결과를 돌려주는 함수이다.
- GROUP BY 절은 행들을 소그룹화 한다.
- SELECT 절, HAVING 절 , ORDER BY 절에 사용할 수 있다.

**집계 함수의 종류**

```sql
COUNT(*)

COUNT(표현식)

SUM([DISTINCT | ALL] 표현식)

AVG([DISTINCT | ALL] 표현식)

MAX([DISTINCT | ALL] 표현식)

MIN([DISTINCT | ALL] 표현식)

STDDEV([DISTINCT | ALL] 표현식)

VARIAN([DISTINCT | ALL] 표현식)

기타 통계 함수
```

**GROUP BY 절**

- HAVING 절과 GROUP BY 절의 순서는 바뀌어도 결과는 동일하지만 논리적인 순서를 지킬 것을 권장함.

```sql
SELECT [DISTINCT] COLUMN_NAME [ALIAS_NAME]
FROM TABLE_NAME
[WHERE CONDITION]
[GROUP BY COLUMN_NAME OR 표현식]
[HAVING GROUP_CONDITION];
```

**GROUP BY 절과 HAVING 절은 다음과 같은 특성을 가진다.**

- GROUP BY 절을 통해 소그룹별 기준을 정한 후 , SELECT 절에 집계 함수를 사용한다.
- 집계 함수의 통계 정보는 NULL 값을 가진 행을 제외하고 수행한다.
- GROUP BY 절에서는 SELECT 절과는 달리 ALIAS 명을 사용할 수 없다.
- 집계 함수는 WHERE 절에는 올 수 없다. (집계 함수를 사용할 수 있는 GROUP BY 절보다 WHERE 절이 먼저 수행된다.)
- WHERE 절은 전체 데이터를 GROUP으로 나누기 전에 행들을 미리 제거시킨다.
- HAVING 절은 GROUP BY 절의 기준항목이나 소그룹의 집계 함수를 이용한 조건을 표시할 수 있다.
- GROUP BY 절에 의한 소그룹별로 만들어진 집계 데이터 중, HAVING 절에서 제한 조건을 두어 조건을 만족하는 내용만 출력한다.
- HAVING 절은 일반적으로 GROUP BY 절 뒤에 위치한다.

**집계 함수와 NULL 처리**

- 집계 함수는 NULL 값을 제외하고 계산한다.
- 그러나 – 나 + 같은 연산자는 NULL 값이 포함되어있을 경우 NULL 값을 나타낸다.

# ORDER BY 절

**ORDER BY 정렬**

- ORACLE에서는 NULL 값을 가장 큰 값으로 봄
- SQL server에서는 NULL 값을 가장 작은 값으로 봄

```sql
SELECT COLUMN_NAME [ALIAS_NAME]
FROM TABLE_NAME
[WHERE CONDITION]
[GROUP BY COLUMN_NAME OR EXPRESSION]
[HAVING GROUP_CONDITION]
[ORDER BY COLUMN_NAME OR EXPRESSION [ASC OR DESC]]
```

**SELECT 문장 실행 순서**

- ORDER BY 절에는 SELECT 목록에 나타나지 않은 문자형 항목이 포함될 수 있다.
- 단, SELECT DISTINCT를 지정하거나 SQL 문장에 GROUP BY 절이 있거나 또는 SELECT 문에 UNION 연산자가 있으면 열 정의가 SELECT 목록에 표시되어야 한다.

```sql
5. SELECT COLUMN_NAME [ALIAS_NAME]
1. FROM TABLE_NAME
2. WHERE CONDITION
3. GROUP BY COLUMN_NAME OR EXPRESSION
4. HAVING GROUP_CONDITION
6. ORDER BY COLUMN_NAME OR EXPRESSION
```

- **Example**

    SAL 은 GROUP으로 묶이지 않았으므로 오류가 난다.

    ```sql
    SELECT JOB, SAL
    FROM EMP
    GROUP BY JOB
    HAVING COUNT(*) > 0
    ORDER BY SAL
    ```

    이렇게 하면 실행이됨. ORDER BY 절에도 집계함수 사용 가능함.

    ```sql
    SELECT JOB
    FROM EMP
    GROUP BY JOB
    HAVING COUNT(*) > 0
    ORDER BY MAX(EMPNO), MAX(MGR), SUM(SAL), COUNT(DEPT), MAX(HIREDATE)
    ```

**Top N 쿼리**

- **ROWNUM**

    Oracle의 경우 정렬이 완료된 후 데이터의 일부가 출력되는 것이 아니라, 데이터의 일부가 먼저 추출된 후(ORDER BY 절은 결과 집합을 결정하는데 관여하지 않음) 데이터에 대한 정렬 작업이 일어나므로 주의해야 한다.

    이렇게 하면 위에서 3건을 먼저 가져온 후에 내림차순을 정렬한다.

    ```sql
    SELECT ENAME, SAL
    FROM EMP
    WHERE ROWNUM < 4
    ORDER BY SAL DESC;
    ```

    이렇게 해야 내림차순으로 조회된 데이터에서 위에서 3개의 행을 조회한다.

    ```sql
    SELECT ENAME, SAL
    FROM (SELECT ENAME, SAL
    			FROM EMP
    			ORDER BY SAL DESC)
    WHERE ROWNUM < 4;
    ```

- **TOP ()**

    SQL server는 TOP 조건을 사용하게 되면 별도 처리 없이 관련 ORDER BY 절의 데이터 정렬 후 원하는 일부 데이터만 쉽게 출력할 수 있다.

    WITH TIES 옵션은 ORDER BY 절의 조건 기준으로 TOP N의 마지막 행으로 표시되는 추가 행의 데이터가 같을 경우 N+ 동일 정렬 순서 데이터를 추가 반환하도록 지정하는 옵션이다.

    ```sql
    TOP (Expression) [PERCENT] [WITH TIES]
    ```

    사원 테이블에서 급여가 높은 2명을 내림차순으로 출력하는 같은 급여를 받는 사원이 있으면 같이 출력한다.

    ```sql
    SELECT TOP(2) WITH TIES ENAME, SAL
    FROM EMP
    ORDER BY SAL DESC;
    ```

# **조인(JOIN)**

**EQUI JOIN**

WHERE 절에 JOIN 조건을 넣는다.

```sql
SELECT TABLE1_NAME.COLUMN_NAME, TABLE2_NAME.COLUMN_NAME, ....
FROM TABLE1_NAME, TABLE2_NAME
WHERE TABLE1.COLUMN_NAME = TABLE2.COLUMN_NAME;
```

아래는 위와 같은 표현 (ANSI/ISO SQL 표준 방식)

ON 절에 JOIN 조건을 넣는다.

```sql
SELECT TABLE1_NAME,COLUMN_NAME, TABLE2_NAME.COLUMN_NAME, ....
FROM TABLE1_NAME INNER JOIN TABLE2_NAME
ON TABLE1.COLUMN_NAME = TABLE2.COLUMN_NAME;
```

**ALIAS의 틀린 예**

FROM 문에서 PLAYER를 P로 ALIAS를 정의했는데 SELECT문에서 테이블명을 사용하면 오류가 난다.

```sql
SELECT PLAYER.PLAYER_NAME 선수명, P.BACK_NO 백넘버, T.REGION_NAME 연고지
FROM PLAYER P, TEAM T
WHERE P.TEAM_ID = T.TEAM_ID
			AND P.POSITION = ‘GK’
ORDER BY P.BACK_NO;
```

**Non EQUI JOIN**

- Non EQUI JOIN의 경우에는 “=” 연산자가 아닌 다른(Between, >, >=, <, <= 등) 연산자들을 사용하여 JOIN을 수행하는 것이다.
- 두 개의 테이블 간에 칼럼 값들이 서로 정확하게 일치하지 않는 경우에는 EQUI JOIN을 사용할 수 없다.
- 이런 경우에는 Non EQUI JOIN을 시도할 수 있으나 데이터 모델에 따라서 Non EQUI JOIN이 불가능한 경우도 있다.

```sql
SELECT TABLE1_NAME.COLUMN_NAME, TABLE2_NAME.COLUMN_NAME, ...
FROM TABLE1_NAME, TABLE2_NAME
WHERE TABLE1_NAME.COLUMN1_NAME1
			BETWEEN TABLE2_NAME.COLUMN1_NAME AND TABLE2_NAME.COLUMN2_NAME;
```

**3개 이상 TABLE JOIN**

**Example 1**

```sql
SELECT P.PLAYER_NAME 선수명, P.POSITION 포지션, T.REGION_NAME 연고지, T.TEAM_NAME 팀명,

S.STADIUM_NAME 구장명

FROM PLAYER P, TEAM T, STADIUM S

WHERE P.TEAM_ID = T.TEAM_ID

AND T.STADIUM_ID = S.STADIUM_ID

ORDER BY 선수명;
```

**Example 2** 

INNER JOIN을 사용한 예

```sql
SELECT P.PLAYER_NAME 선수명, P.POSITION 포지션, T.REGION_NAME 연고지, T.TEAM_NAME 팀명,
S.STADIUM_NAME 구장명
FROM PLAYER P INNER JOIN TEAM T
ON P.TEAM_ID = T.TEAM_ID
INNER JOIN STADIUM S
ON T.STADIUM_ID = S.STADIUM_ID
ORDER BY 선수명;
```