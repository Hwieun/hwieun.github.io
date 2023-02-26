---
layout  : wiki
title   : 
summary : 
date    : 2023-02-23 10:24:10 +0900
updated : 2023-02-23 10:27:56
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

Column	JSON Name	Meaning
id	select_id	The SELECT identifier
select_type	None	The SELECT type
table	table_name	The table for the output row
partitions	partitions	The matching partitions
type	access_type	The join type
possible_keys	possible_keys	The possible indexes to choose
key	key	The index actually chosen
key_len	key_length	The length of the chosen key
ref	ref	The columns compared to the index
rows	rows	Estimate of rows to be examined
filtered	filtered	Percentage of rows filtered by table condition
Extra	None	Additional information


# extra

* using index
  * MySql이 테이블에 접근하지 않도록 covering index를 사용한다는 것을 의미
  * 즉, 인덱스만으로 검색 결과를 출력 
  * extra column에 using index 없이도 type=index이고 key가 primary라면 사용될 수 있다.

* using index condition
  *  

* using where
  * MySql 서

* using filesort
  * 

# 참고
* https://dev.mysql.com/doc/refman/8.0/en/explain-output.html#explain-extra-information
