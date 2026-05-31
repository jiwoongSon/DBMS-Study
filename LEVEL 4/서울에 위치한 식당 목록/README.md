#  0531 서울에 위치한 식당 목록 출력하기

## 풀이
- SELECT 절에 있는 컬럼 중, 그룹함수가 적용되지 않은 모든 일반 컬럼은 반드시 GROUP BY 절에 적어주어야 한다. 

## 생각해볼 점
- JOIN을 사용하지 않고 조건으로 구현 가능

```SQL
SELECT 
    I.REST_ID, 
    I.REST_NAME, 
    I.FOOD_TYPE, 
    I.FAVORITES, 
    I.ADDRESS, 
    ROUND(AVG(R.REVIEW_SCORE), 2) AS SCORE
FROM 
    REST_INFO I, 
    REST_REVIEW R 
WHERE 
    I.REST_ID = R.REST_ID  
    AND I.ADDRESS LIKE '서울%'
GROUP BY 
    I.REST_ID, I.REST_NAME, I.FOOD_TYPE, I.FAVORITES, I.ADDRESS
ORDER BY 
    SCORE DESC, I.FAVORITES DESC;
```

- 서브쿼리 사용
    - FROM -> WHERE -> SELECT 순서로 실행된다. 
    - 따라서 가장 바깥의 SELECT ALL 절이 없다면 SCORE NOT NULL 문장을 처리할 수 없다. WHERE이 SELECT 안의 SCORE를 정의한 부분보다 빨리 실행되기 때문
    - SELECT 문을 하나의 완성된 테이블로 보기 -> FROM 절에 던져줌. 그리고 NOT NULL 조건을 처리
        - NOT NULL : 리뷰가 아예 없는 식당이 있을 수 있음. 이런 경우 AVG()의 결과도 NULL return
    - ORACLE에서는 NULL을 무한대 값으로 취급

```SQL
SELECT * FROM (
    SELECT 
        REST_ID, 
        REST_NAME, 
        FOOD_TYPE, 
        FAVORITES, 
        ADDRESS, 
        (
            SELECT ROUND(AVG(REVIEW_SCORE), 2) 
            FROM REST_REVIEW 
            WHERE REST_ID = I.REST_ID
        ) AS SCORE
    FROM REST_INFO I
    WHERE ADDRESS LIKE '서울%'
)

WHERE SCORE IS NOT NULL
ORDER BY SCORE DESC, FAVORITES DESC;
```
