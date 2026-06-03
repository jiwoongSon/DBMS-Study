#  0603 특정 세대의 대장균 찾기

## 풀이
- 트리 구조의 계층
- 재귀적으로 서브쿼리 작성? 
    - 재귀적으로 서브쿼리 작성하여, 3단 중첩까지 표현

## 생각해볼 점

- SELF JOIN 사용 
```sql
SELECT GEN3.ID
FROM ECOLI_DATA GEN1
JOIN ECOLI_DATA GEN2 ON GEN1.ID = GEN2.PARENT_ID
JOIN ECOLI_DATA GEN3 ON GEN2.ID = GEN3.PARENT_ID
WHERE GEN1.PARENT_ID IS NULL
ORDER BY GEN3.ID ASC;
```

---

# Recursive Query
- SQL의 확장 기능
- CTE를 사용하여 구현
    - CTE (Common Table Expression) : Memory에 잠깐 띄워놓는 1회용 가상 테이블

## WITH 키워드

```sql
WITH 임시테이블 AS (
    SELECT '사과' AS 과일명
)
SELECT * FROM 임시테이블;
```

- 쿼리가 실행되는 동안에만 생성, 쿼리가 종료되면 없어짐. 
- *서브쿼리의 가독성*을 위하여 사용

## RECURSIVE 키워드

```sql
WITH RECURSIVE 임시테이블 AS (
    -- [구역 1] Anchor
    SELECT 1 AS NUM
    
    UNION ALL
    
    -- [구역 2] Recursive
    SELECT NUM + 1 
    FROM 임시테이블 
    WHERE NUM < 5
)
-- [구역 3] 최종 출력
SELECT * FROM 임시테이블;
```

- 첫번째 SELECT문 "SELECT 1 AS NUM"
    - NUM (컬럼명) / 1 (Data) : 아무것도 없는 백지상태에 NUM이라는 이름표를 붙이고, 그 밑에 1이라는 값을 넣었음. 
- 이후 합집합 연산 
- 다음 SELECT 문은 이전의 임시테이블에서 NUM 값을 읽어와서 +1, 한 후 데이터 삽입
    - NUM (컬럼명) / 1 / 2 ...
- 언제까지? NUM이 5보다 작을 때 까지

```sql
WITH RECURSIVE CTE AS (
    -- [Base Case] 1세대 대장균 (부모가 없는 데이터)
    SELECT ID, PARENT_ID, 1 AS GEN
    FROM ECOLI_DATA
    WHERE PARENT_ID IS NULL
    
    UNION ALL
    
    -- [Recursive Case] 2세대 이상 대장균 (부모가 CTE에 존재하는 데이터)
    SELECT E.ID, E.PARENT_ID, CTE.GEN + 1 AS GEN
    FROM ECOLI_DATA E
    JOIN CTE ON E.PARENT_ID = CTE.ID
)
-- 위에서 만든 가상 테이블(CTE)에서 3세대만 필터링
SELECT ID
FROM CTE
WHERE GEN = 3
ORDER BY ID ASC;
```