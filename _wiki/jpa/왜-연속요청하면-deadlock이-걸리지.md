---
layout  : wiki
title   : 왜 연속요청하면-deadlock이-걸리지?
summary : 
date    : 2023-03-24 18:56:19 +0900
updated : 2023-03-24 19:00:03
tag     : 
toc     : true
public  : true
parent  : /Users/hwieun/hwieun.github.io/_wiki/jpa
latex   : false
resource: 3925E1DD-1D82-4331-835A-EFFC22A7A563
---
* TOC
{:toc}

- [Could there be a deadlock when using optimistic locking?](https://stackoverflow.com/questions/38946812/could-there-be-a-deadlock-when-using-optimistic-locking) 
  it can be when update or insert command. 

> Imagine that threads A and B both want to update a particular row in a parent table and in a child table. Thread A updates the parent row first. Thread B updates the child row first. Now thread A tries to update the child row and finds itself blocked by B. Meanwhile, thread B tries to update the parent and finds itself blocked by A. You have a deadlock.
> 

내 생각에는 audit 테이블과 관련이 있지 않나 싶다.

![12](https://user-images.githubusercontent.com/29860102/227490058-df6273dd-84d1-481e-95b1-7a340a8af285.png)


isolation에 의해 deadlock 발생.

- 유니크 인덱스는 완벽한 row 단위 락이 걸림
- 일반 인덱스는 참조 했던 row가 모두 락이 걸림
- 인덱스가 없어 테이블 전체를 읽으면 모든 row가 락이 걸림

![123](https://user-images.githubusercontent.com/29860102/227490487-397430eb-750c-4765-9331-3dc05eec1643.png)

- 참고
  - https://gywn.net/2012/05/mysql-transaction-isolation-level/
  - https://happyer16.tistory.com/entry/JPA-에러-Deadlock-found-when-trying-to-get-lock-try-restarting-transaction
