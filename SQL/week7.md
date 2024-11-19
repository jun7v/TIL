# [NULL처리하기 (SQL 고득점kit)](https://school.programmers.co.kr/learn/courses/30/lessons/59410)
## 문제1. IFNULL()으로 해결
```sql
SELECT animal_type,
       ifnull(name,"No name"),
       sex_upon_intake
from animal_ins
order by animal_id asc;
```

## 문제2. CASE WHEN으로 해결
```sql
SELECT animal_type,
       case when name IS NULL 
           then "No name" 
           else name 
           end,
       sex_upon_intake
from animal_ins
order by animal_id asc;
```


# [중성화 여부 파악하기](https://school.programmers.co.kr/learn/courses/30/lessons/59409#qna)
## 문제 3. 문제를 풀어주세요 (힌트: IF, LIKE를 사용할 수 있습니다)
```sql
SELECT animal_id,
       name,
       if(
           sex_upon_intake like '%Neutered%' or sex_upon_intake like '%Spayed%',
           'O',
           'X'
       ) AS 중성화
from animal_ins
order by animal_id asc;
```


## [문제 4. 아래는 QnA에 올라온 질문입니다. 왜 풀이가 틀렸는지 답해주세요.](https://school.programmers.co.kr/questions/80270)
> or 연산자에서 양쪽은 각각 논리 연산이어야 하는데,  
> A like '%Neutered%' OR '%Spayed%'라고 하면  
> '%Spayed%' 텍스트가 그냥 하나의 논리 연산으로 인식되어서  
> (이 경우엔 0이 아니니까 True값)  
> 틀린 풀이이다.