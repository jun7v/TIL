
# 1. [우리 플랫폼에 정착한 판매자](https://solvesql.com/problems/settled-sellers-1/)

```sql
select
    seller_id,
    count(distinct order_id) as orders
from
    olist_order_items_dataset
group by
    seller_id
having
    orders >= 100
order by
    orders DESC
```

- Group by -> having
- 이 문제에서는 order_id가 PK가 아니므로 `distinct` 사용

# 2. [몇 분이서 오셨어요?](https://solvesql.com/problems/size-of-table/)

```sql
select
    *
from
    tips
where
    mod(size, 2) = = 1
```

- 찾아보니 SQLite는 `mod()`랑 `%` 둘 다 된다고 한다. 오라클은 %가 안 됐는데...

# 3. [최고의 근무일을 찾아라](https://solvesql.com/problems/best-working-day/)
```sql
select
    day,
    sum(tip) as tip_daily
from
    tips
group by
    day
order by
    tip_daily desc
    limit 1
```
- 정말 다행히도 SQLite도 `limit`가 있다