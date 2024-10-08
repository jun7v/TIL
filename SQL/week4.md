# 1. [ROOT 아이템 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/273710)
```sql
select item_info.item_id, item_info.item_name
from item_info
    inner join item_tree
        on item_info.item_id = item_tree.item_id
where item_tree.parent_item_id is null
order by item_info.item_id asc
```
- null은 "="이 아니라 "IS"를 써야 한다.
- 이번주에는 그냥 join을 썼다.

# 2. [노선별 평균 역 사이 거리 조회하기](https://school.programmers.co.kr/learn/courses/30/lessons/284531)
```sql
select route,
       concat(round(sum(D_BETWEEN_DIST),1),"km") as TOTAL_DISTANCE,
       concat(round(avg(D_BETWEEN_DIST),2),"km") as AVERAGE_DISTANCE
from subway_distance
group by ROUTE
order by sum(D_BETWEEN_DIST) desc;
```
- mysql에서 문자 결합은 "CONCAT"
- 정렬할 때 concat후에는 문자열 판정이라 정렬이 똑바로 안되는 것 같았다 → 정렬 기준을 sum(D_BETWEEN_DIST)로 해주었다.


# 3. [헤비 유저가 소유한 장소](https://school.programmers.co.kr/learn/courses/30/lessons/77487)
```sql
select id, name, host_id
from places
where host_id in (
    select host_id
    from places
    group by host_id
    having count(*) >= 2
    )
order by id asc;
```
- 서브쿼리를 사용하면 쉽게 해결할 수 있다
- 서브쿼리로 list를 받아왔기 떄문에 "in"으로 존재 여부를 조건으로 걸면 된다