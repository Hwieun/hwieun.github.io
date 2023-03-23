---
layout  : wiki
title   : inner class로-인해-메모리-릭-발생
summary : 
date    : 2023-03-23 18:59:29 +0900
updated : 2023-03-23 18:59:40
tag     : 
toc     : true
public  : true
parent  : /Users/hwieun/hwieun.github.io/_wiki/java
latex   : false
resource: 9817C8C4-3166-4EF4-A44C-333471560068
---
* TOC
{:toc}

# inner class 로 인해 메모리 릭 발생
- outer class의 작업이 끝났을 때 inner class의 outer class 객체 참조로 인해 메모리가 해제되지 않는다. → static을 붙이지 않은 inner class의 경우 outer class를 참조한다.

⇒ static inner class 를 선언
