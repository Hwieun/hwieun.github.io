---
layout  : wiki
title   : index
summary : index 사용 시 유의할 점 
date    : 2023-03-23 18:13:22 +0900
updated : 2023-03-23 18:15:33
tag     : 
toc     : true
public  : true
parent  : [[_wiki/db]]
latex   : false
resource: 4D032968-FFA3-4713-A6C6-8A1506DB1C45
---
* TOC
{:toc}


# index가 있어도 사용할 수 없는 경우
- where에 부정이 있으면 index를 사용할 수 없다.
    - 그래도 limit 절이 있으면 full join을 해도 문제가 없지만, 그게 아니라면 성능에 문제가 있을 수 있다

# sort

- group by
    - group by 를 하면 자동으로 group by 한 컬럼으로 정렬된다. 이로 인한 overhead를 막으려 한다면 order by null
- `ORDER BY` 에서 인덱스를 사용하지 못하는 경우
    - 서로 다른 키를 `ORDER BY`에 사용하는 경우
    - 키의 일부를 사용할 때
    - 오름차순과 내림차순을 섞는 경우
    - `ORDER BY` 절에 사용된 키와 열을 가져오기 위해 사용된 키가 다른 경우
    - `ORDER BY` 에 키의 컬럼이 아닌 다른 표현을 사용했을 경우 (함수 사용)
    - `ORDER BY`나 `GROUP BY`에 명시된 칼럼이 조인의 순서상 첫 번째 테이블이 아닌 쿼리
    - `ORDER BY` 절과 `GROUP BY` 절이 다를 경우
    - `ORDER BY` 절에 사용된 컬럼의 일부분만 인덱스로 만든 경우
- sort 성능
    - 요건: 1) (세로) row 수 2) (가로) 데이터 사이즈
    - 여기서 데이터 사이즈는 단순히 order by에 걸린 컬럼의 사이즈를 의미하지 않고,
    select 할 때 가져오는 컬럼의 사이즈도 포함이 된다
    - [https://scidb.tistory.com/entry/Sort-부하를-좌우하는-두-가지-원리](https://scidb.tistory.com/entry/Sort-%EB%B6%80%ED%95%98%EB%A5%BC-%EC%A2%8C%EC%9A%B0%ED%95%98%EB%8A%94-%EB%91%90-%EA%B0%80%EC%A7%80-%EC%9B%90%EB%A6%AC)
