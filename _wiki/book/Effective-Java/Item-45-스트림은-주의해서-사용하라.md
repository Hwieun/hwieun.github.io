---
layout  : wiki
title   : 
summary : 
date    : 2023-02-27 00:08:39 +0900
updated : 2023-02-27 02:19:47
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
resource: 6CA1626B-B1F4-411E-823B-56632CCC7F46
---
* TOC
{:toc}

스트림 API는 다량의 데이터 처리 작업을 위해 Java 8 에 추가되었다.
스트림이 제공하는 추상 개념 중 핵심은 두 가지다.
1. stream은 데이터 원소의 유한 혹은 무한 sequence를 의미한다.
2. stream pipeline은 이 원소들로 수행하는 연산 단계를 표현하는 개념이다.

스트림의 원소들은 어디로부터든 올 수 있다. collection, array, file, regex pattern matcher, random generator, 다른 stream이 있다.

## 스트림 파이프라인

* 스트림 파이프라인은 source stream ( -> intermediate operation) -> terminal operation으로 이루어진다.
  * 각 중간 연산은 스트림을 변환한다. 각 원소에 함수를 적용하거나 특정 조건으로 걸러낸다.
  * 종단 연산은 원소를 정렬해 컬렉션에 담거나, 특정 원소 하나를 선택하건, 출력하는 식이다.
  ```
   productStream.filter(product -> "노트북".equals(product.getName())).collect(Collectors.toList()); 
  ```
  
* lazy evaluation 
  스트림 파이프라인은 지연 평가(lazy evaluation)된다.
  <u>실제 연산은 중단 연산이 호출될 때 이루어지며, 종단 연산에 쓰이지 않는 데이터 원소는 계산에 쓰이지 않는다.</u>
* 기본적으로 순차적으로 수행된다.
* parallel 메서드를 사용하면 병렬로 실행할 수는 있으나, 효과를 볼 수 있는 상황은 많지 않다(Item 48)

> 스트림을 과용하면 읽거나 유지보수하기 어려워진다.

## 코드 블록으로만 할 수 있는 작업

* 범위 안의 지역변수를 읽고 수정할 수 있다. 하지만 람다는 final이거나 사실상 final인 변수만 읽을 수 있다.
* return 문을 사용해 메서드를 빠져나가거나, break, continue로 흐름을 바꿀 수 있다.
* 메서드 선언에 명시된 검사 예외를 던질 수 있다.

## 스트림과 맞는 작업

* 원소들의 sequence를 일관되게 변환/필터링/하나의 연산을 사용해 결합한다.
* 원소들의 sequence를 컬렉션에 모은다.
* 원소들의 sequence에서 특정 조건을 만족하는 원소를 찾는다.

> 무엇이 나은지 확신하기 어렵다면 둘 다 해보고 더 나은 쪽을 택하라
