# 1. [조건에 맞는 사용자와 총 거래금액 조회하기](https://school.programmers.co.kr/learn/courses/30/lessons/164668)
```sql
SELECT (select user_id from used_goods_user where user_id = writer_id) as USER_ID,
       (select nickname from used_goods_user where user_id = writer_id) as NICKNAME,
       sum(price) as TOTAL_SALES
from used_goods_board
where status="done"
group by writer_id
having sum(price)>=700000
order by TOTAL_SALES asc
```
- 생각없이 풀다보니 문제에서 요구하는 바를 구하기 위해 일단 서브쿼리를 썼는데,
- 다른 사람 답안을 보니 그냥 조인을 쓰면 되는 문제였다.
- 서브쿼리 떄문에 select 절이 읽기 불편해진 문제가 있었다.

# 2. [업그레이드 할 수 없는 아이템 구하기](https://school.programmers.co.kr/learn/courses/30/lessons/273712)
```sql
select item_id, ITEM_NAME, RARITY
from item_info
where EXISTS (SELECT * FROM item_tree WHERE item_info.ITEM_ID = PARENT_ITEM_ID) = False
order by item_id desc
```
- 풀고 나서 보니 얘도 원래는 조인을 써서 풀어야 했던 문제 같다.
- 이 문제에서 '업그레이드 불가' 조건은 결국 ITEM_TREE.PARENT_ITEM_ID에 들어있지 않아야 한다는 뜻이다.
- 처음에는 Not In을 쓰려고 했는데, ITEM_TREE에 null 값이 있어서 제대로 결과가 나오지 않았다.
- 서브쿼리를 두개 쓰는건 너무 복잡해지는 것 같아서, 존재 여부를 True/False로 판단하는 EXISTS를 사용했다.
- 단순하게, item_info.ITEM_ID가 item_tree.PARENT_ITEM_ID과 같은 row를 출력하게 해서 있으면 True 반환 없으면 False 반환인 서브쿼리를 만들었다.
- where 절에서 False면 출력하게 했다.


# 3. [조건에 맞는 개발자 찾기](https://school.programmers.co.kr/learn/courses/30/lessons/276034)
```sql
select id, email, first_name, last_name
from developers
where skill_code & (select sum(code) from skillcodes where name="C#" or name="Python") != 0
order by id asc;
```

- 저번 학기에 푼 문제여서 답이 그대로 남아있었는데, 이것도 원래 join 문제인데 서브쿼리로 풀었다;;
- 코드 리뷰를 해보자면,
- 일단 핵심은 비트 연산자인 &이다.
- 스킬 코드는 이진수로 변환하면 '1000', '100000'의 형태이기에, 이들을 합해도 다시 이진수로 변환하면 '101000' 식으로 원래 무엇이 합쳐진 것인지 알 수 있다.
- 따라서 비트 연산자 &로 비교하면 두 이진수의 교집합, 즉 해당 스킬이 skill_code에 더해져 있는지 알 수 있고
- 위 코드의 경우엔 서브쿼리로 C#과 Python에 해당하는 코드를 가져와 비교해서 포함되어 있는지 여부를 구했다.
- 해당 비교값이 0이 아니라면, 즉 하나라도 포함되어있다면 where 문을 만족해 출력된다.
- 비트 연산자는 어렵다...