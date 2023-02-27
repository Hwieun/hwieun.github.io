---
layout  : wiki
title   : explain-설명
summary : 
date    : 2023-02-23 10:24:10 +0900
updated : 2023-02-27 18:25:32
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
resource: 3B736AA6-F00B-4B7E-8F39-7BE89066B412
---
* TOC
{:toc}

# explain option
* format (FORMAT={value})
  * TREE
  * JSON
  * TRADITIONAL

# output

| Column        | JSON Name     | Meaning                                         |
|---------------|---------------|-------------------------------------------------|
| id            | select_id     | SELECT 식별자                                   |
| select_type   | None          | SELECT type                                     |
| table         | table_name    | output row의 table                              |
| partitions    | partitions    | 매칭되는 파티션들                               |
| key           | key           | 실제로 선택된 인덱스                            |
| key_len       | key_length    | 선택된 키의 길이                                |
| possible_keys | possible_keys | 선택될 수 있는 인덱스들                         |
| ref           | ref           | 인덱스와 비교되는 컬럼들                        |
| rows          | rows          | 실행될 행의 추정치                              |
| filtered      | filtered      | table condition으로 인해 필터링될 행의 퍼센테지 |
| extra         | None          | 추가적인 정보                                   |


# extra

* using index
  MySql이 테이블에 접근하지 않도록 covering index를 사용한다는 것을 의미
  즉, 인덱스만으로 검색 결과를 출력 
  extra column에 using index 없이도 type=index이고 key가 primary라면 사용될 수 있다.

* using index condition
  index tuple에 접근하고 full table row를 읽을지 여부를 판단하기 위해 먼저 테스트하여 테이블을 읽는다.

* using where
  where절을 사용했는데 extra 값에 `using where`이 없고 table join type에 `ALL` 혹은 `index`가 있으면 성능이 좋지 않다.

* using filesort
  mysql은 정렬된 순서로 검색하는 방법을 찾기 위해 추가적인 작업을 해야 한다. 
  조인 유형에 따라 모든 행을 살펴보고 where 절에 맞는 모든 행에 대한 정렬 키 및 포인터를 저장하여 정렬이 수행된다.
  그 후 키가 정렬되고 정렬된 순서로 행을 가져오게 된다.

# 참고
* https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain-extra-information
