---
layout  : wiki
title   : DB CPU 70퍼센트 이상 사용현상
summary : 
date    : 2023-03-05 11:27:34 +0900
updated : 2023-03-05 13:00:35
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
resource: 694EF168-0043-4CDA-845A-99D2CDB638AA
---
* TOC
{:toc}

# 현상

![image](https://user-images.githubusercontent.com/29860102/222938474-911c6f52-ccaf-409a-bdea-3d0dfb323e2a.png)

- 단순 select 쿼리 실행에도 실행 시간 증가


# 원인

- DELETE, UPDATE 작업을 수행하면서 데이터 파일이 조각화될 가능성이 높아짐.
- 조각 모음을 하고 사용하지 않는 공간을 회수하는 작업이 필요.

# 해결

- 조각 모음을 회수하는 작업이 MySQL에서 최적화가 필요한 테이블이 된다.
- 테이블 최적화를 하면 데이터 입력 및 출력의 성능을 향상시키는 전용 스토리지 서버의 데이터 재정렬을 지원한다.
- optimize 명령은 테이블에 lock이 걸리므로 최대한 영향이 적은 시간에 실행해야 한다.


# 결과

![image](https://user-images.githubusercontent.com/29860102/222940938-8f3495dc-6699-4942-8d50-9ff7130693be.png)
