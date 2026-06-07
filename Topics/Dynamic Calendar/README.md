# 달력생성기 0607
- System 함수: CURRENT_DATE
    - 오늘 날짜 반환
- 상수와 수식만을 사용하여 날짜 DATA 생성하기

## AS 관련
- 가상테이블이 Alias를 달 때 두가지 방법이 있다. 
    - 괄호를 쓰지 않고, 가상테이블에서 생성한 각각의 컬럼에 AS 키워드 작성 
    - 테이블 선언 시 괄호를 달아서 Alias라고 선언하기
        - WITH 부서별_급여통계 (부서명, 총인건비, 직원수) AS ( ...
        - 임시 테이블의 컬럼명을 바로 알 수 있음. 

## SQL에서의 재귀
- SQL에서는 CallStack이라는 개념이 없다. 근데 어떻게?? 
- --> 내부적으로 2개의 Table 생성, 반복문으로 처리
    - Result Table: 최종 DATA SET
    - Working Table(Spool): 직전 단계에서만 생성된 DATA
- 조건이 < 5 , ++ 인 경우 
    - Anchor Table이 Result, Spool에 들어간다. 
    - Spool에 있는 DATA를 꺼내서 조건 확인, ++ 연산
    - 2 TABLE을 만들어서 Result Set에 넣고(UNION ALL), 1 테이블이 있던 Spool을 비우고 2 TABLE을 넣는다. 
    - Spool에 더이상 데이터가 없을 때 까지 반복

### ORACLE에서의 재귀 
- START WITH ... CONNECT BY PRIOR
    - START WITH: Anchor
    - CONNECT BY: 해당 조건으로 연결해라
    - LEVEL: spool loop 횟수 자동계산

```sql

SELECT 
    사번, 
    이름, 
    상사_사번,
    LEVEL AS 계급층
FROM 직원테이블
START WITH 상사_사번 IS NULL
CONNECT BY PRIOR 사번 = 상사_사번;

```