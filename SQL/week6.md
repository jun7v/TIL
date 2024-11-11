# 1. 틀린 코드 이유 분석
**틀린코드**
```sql
SELECT *
FROM (SELECT FOOD_TYPE, REST_ID, REST_NAME, MAX(FAVORITES) AS FAVORITES
FROM REST_INFO
GROUP BY FOOD_TYPE
ORDER BY FOOD_TYPE DESC)
```
- 일단 select * from은 왜 필요한지 모르곘다.
- sql에서 group by를 사용했으면, select 절에는 group by된 칼럼과 나머지 칼럼의 집계만 적을 수 있다.
- 따라서 이대로 실행하면 Favorites은 max값이 나오지만 나머지 칼럼은 아마 첫 값이 나올 것이다.

**정답코드**
```sql
SELECT FOOD_TYPE, REST_ID, REST_NAME, FAVORITES
FROM REST_INFO
WHERE (FOOD_TYPE, FAVORITES) IN (
    SELECT FOOD_TYPE, MAX(FAVORITES)
    FROM REST_INFO
    GROUP BY FOOD_TYPE
)
ORDER BY FOOD_TYPE DESC;
```
- 따라서 서브쿼리로 그룹별 favorites max를 추출해두고, 후에 where 절로 비교하며 찾아야 한다.
- 그런데, 저번 week5에도 적었지만 이런식으로 구문을 작성하면 모든 row에 대해 서브쿼리를 실행하는 꼴이 되어 성능 이슈가 있다.


# 2. 개선된 쿼리 학습
```sql
WITH RankedRest AS ( # 임시 테이블 생성
    SELECT 
        FOOD_TYPE, REST_ID, REST_NAME, FAVORITES,
        # 전체 칼럼 호출
        ROW_NUMBER() OVER (PARTITION BY FOOD_TYPE ORDER BY FAVORITES DESC, REST_ID) AS rnk
        # Food type으로 구분(파티션)한 다음, favorites 내림차순으로 행번호=순위 부여
    FROM REST_INFO
)
SELECT 
    FOOD_TYPE, REST_ID, REST_NAME, FAVORITES
FROM RankedRest
WHERE rnk = 1 # 순위가 1인 것만 출력
ORDER BY FOOD_TYPE DESC;
```
- 처음에 with as로 임시 테이블을 만들고 시작하면서 where 절 연속 서브쿼리 연산보다 필요 연산이 줄었다.



# 3. [조건에 맞는 사원 정보 조회하기](https://school.programmers.co.kr/learn/courses/30/lessons/284527)

**기본코드(Rank)**
```sql
SELECT 
    EMP_NO, 
    EMP_NAME, 
    SAL,
    RANK() OVER (ORDER BY SAL DESC) AS rnk
FROM 
    HR_EMPLOYEES;
```
| EMP_NO | EMP_NAME | SAL | rnk |
|--------|----------|-----|-----|
| 2017002 | 정호식 | 65000000 | 1 |
| 2018001 | 김민석 | 60000000 | 2 |
| 2019001 | 김솜이 | 60000000 | 2 |
| 2020002 | 김연주 | 53000000 | 4 |
| 2020005 | 양성태 | 53000000 | 4 |
- Rank()
  - 동일한 값에 대해 같은 순위
  - 동일한 값이 있을 경우 다음 순위를 건너뜀  
  

**Dense Rank**
```sql
SELECT
    EMP_NO,
    EMP_NAME,
    SAL,
    Dense_RANK() OVER (ORDER BY SAL DESC) AS rnk
FROM
    HR_EMPLOYEES;
```
| EMP_NO | EMP_NAME | SAL | rnk |
|--------|----------|-----|-----|
| 2017002 | 정호식 | 65000000 | 1 |
| 2018001 | 김민석 | 60000000 | 2 |
| 2019001 | 김솜이 | 60000000 | 2 |
| 2020002 | 김연주 | 53000000 | 3 |
| 2020005 | 양성태 | 53000000 | 3 |
- Dense_Rank()
  - 동일한 값에 대해 같은 순위
  - 동일한 값이 있어도 다음 순위를 건너뛰지 않음  
  
**Row number**
```sql
SELECT 
    EMP_NO, 
    EMP_NAME, 
    SAL,
    row_number() OVER (ORDER BY SAL DESC) AS rnk
FROM 
    HR_EMPLOYEES;
```
| EMP_NO | EMP_NAME | SAL | rnk |
|--------|----------|-----|-----|
| 2017002 | 정호식 | 65000000 | 1 |
| 2018001 | 김민석 | 60000000 | 2 |
| 2019001 | 김솜이 | 60000000 | 3 |
| 2020002 | 김연주 | 53000000 | 4 |
| 2020005 | 양성태 | 53000000 | 5 |
- Row_number()
  - 각 행에 고유한 순차적 번호를 할당
  - 동일한 값이라도 서로 다른 번호를 부여
  - 중복을 허용하지 않음