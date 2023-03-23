---
layout  : wiki
title   : webclient
summary : 
date    : 2023-03-23 18:58:10 +0900
updated : 2023-03-23 18:58:47
tag     : 
toc     : true
public  : true
parent  : /Users/hwieun/hwieun.github.io/_wiki/spring
latex   : false
resource: 9866B05C-7C14-4E94-A04F-6973628792CE
---
* TOC
{:toc}


## webclient retrieve vs exchange
- retrieve : 바로 response body를 처리 가능.
- exchange
  - 상태 코드, 헤더 등 client response를 바로 전달해주어 세밀한 처리 가능.
  - 4xx, 5xx 응답코드에 대한 처리가 없어 개발자의 실수로 메모리 릭 발생 가능. 따라서 retrieve 를 권장

## webclient 로 nested object request body를 만드는 방법
- `extra[second_field_name]`
- 대괄호 안에 넣으면 됨
