# 1. [성분으로 구분한 아이스크림 총 주문량](https://school.programmers.co.kr/learn/courses/30/lessons/133026)
```sql
SELECT ingredient_type, sum(total_order) as total_order 
from first_half as ff, icecream_info as ii 
where ff.flavor=ii.flavor 
group by ingredient_type 
order by total_order;
```
- 저번 학기에 했었고, 기본 조인 그룹바이여서 무난

# 2. [즐겨찾기가 가장 많은 식당 정보 출력하기](https://school.programmers.co.kr/learn/courses/30/lessons/131123)
```sql
select r.food_type, r.rest_id, r.rest_name, r.favorites
from (
select food_type, max(favorites) as max_fav
from rest_info
group by food_type
) as s
inner join rest_info as r
where s.food_type = r.food_type and s.max_fav = r.favorites
order by food_type desc;
```
- 처음에 서브쿼리로 하려고 했지만, 찾아보니까 이런 류의 문제에서 서브쿼리를 where절에 넣으면
- 모든 행에 대해 계산이 발생해 성능 저하 이슈가 있다고 한다.
- 그래서 from 절에 서브쿼리를 넣어서 한 번만 계산한 뒤 조인으로 구했다.

# 3. [조건에 맞는 사원 정보 조회하기](https://school.programmers.co.kr/learn/courses/30/lessons/284527)
```sql
with df as (
    select emp_no, sum(score) as total,
        rank() over (order by sum(score) desc) as ranking
    from hr_grade
    where year=2022
    group by emp_no
)
select r.total as score, r.emp_no, h.emp_name, h.position, h.email
from (
    select emp_no, total, ranking
    from df
    where ranking=1
) as r
join hr_employees as h
on r.emp_no = h.emp_no

# limit를 쓴다면
SELECT SUM(SCORE) AS SCORE,
       E.EMP_NO, EMP_NAME, POSITION, EMAIL
FROM HR_GRADE G
         JOIN HR_EMPLOYEES E
              ON E.EMP_NO = G.EMP_NO
WHERE G.YEAR=2022
GROUP BY E.EMP_NO
ORDER BY 1 DESC
Limit 1;
```
- with as 절: 변수처럼 임시 테이블 만들기
- rank() over: 순위를 매기는 함수

- 밑에 limit 식으로 풀면 훨씬 간단하고 직관적인데,
- 만약에 점수가 제일 높은 사원이 여럿이면 이 방법으로는 풀 수가 없다.
- 따라서 rank를 사용하기 위해 점수 계산 테이블 → 랭크 1등 → 조인으로 해결했다.
- 다시보니까 랭킹=1을 서브쿼리에서 안 걸고 with에서 걸어도 될 것 같다.
