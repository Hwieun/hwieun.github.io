---
layout  : wiki
title   : 
summary : 
date    : 2023-02-26 23:09:10 +0900
updated : 2023-02-27 00:45:56
tag     : 
toc     : true
public  : true
parent  : 
latex   : false
resource: 33CE2A94-308A-478D-80C9-C9BF4084DB2D
---
* TOC
{:toc}

* 함수형 인터페이스란?
 - Java 8에 생긴 개념으로, 1개의 추상 메소드를 갖고 있는 인터페이스를 말한다.
```
@FunctionalInterface
public interface FunctionalInterface {
	public abstract void doSomething(String text);
}
```
  
java.util.function 에 다양한 용도의 표준 함수형 인터페이스가 있다. 즉, 직접 구현하지 말고 있는 거 쓰라는 말이다.
총 43개의 인터페이스가 있다. 기본 인터페이스 6개만 살펴보자.
  
- Operator : 인수가 1개인 UnaryOperator와 2개인 BinaryOperator로 나뉘며, 반환값과 인수의 타입이 같은 함수를 뜻한다.
  - ex) String::toLowerCase, BigInteger::add
- Predicate : 인수 하나를 받아 boolean을 반환하는 함수. ex) Collection::isEmpty
- Function : 인수와 반환 타입이 다른 함수. ex) Arrays::asList
- Supplier : 인수를 받지 않고 값을 반환 혹은 제공하는 함수. 이름 그대로 공급자.

```
@FunctionalInterface
public interface LongSupplier {
	long getAsLong();
}
```

- Consumer : 인수를 하나 받고 반환값은 없는 함수. 이름 그대로 소비자. System.out::println

```
@FunctionalInterface
public interface LongConsumer {
	// 인수로 해당 연산을 수행한다.
	void accept(long value);

	default LongConsumer andThen(LongConsumer after) {
	  Objects.requireNonNull(after);
        return (long t) -> { accept(t); after.accept(t); };
	}
}
```

### Function 인터페이스의 변형

 * 입력과 결과 타입이 모두 기본 타입이면 접두어로 _Src_To_Result_ 를 사용한다.
 * 나머지는 접두어로 To_Result_ 를 사용한다.

### 주의사항

    **기본 함수형 인터페이스에 박싱된 기본 타입을 넣어 사용하지 말자.** (Item 61)

### 예외

Comparator<T>는 독자적인 야인터페이스로 살아남아야 한다.
그 이유는 세 가지가 있다.

1. API에서 굉장히 자주 사용되는데, 이름 자체가 그 용도를 명확히 설명해준다.
2. 구현하는 쪽에서 반드시 지켜야 할 규약을 담고 있다.
3. 비교자들을 변환하고 조합해주는 유용한 디폴트 메서드들을 듬뿍 담고 있다.

이 중 하나 이상을 만족한다면 전용 함수형 인터페이스를 구현해야 할 지 진중히 고민해야 한다. 단, 직접 작성한다면 그것이 '인터페이스'임을 명심하자 (Item 21)

### @FunctionalInterface

이 annotation을 사용하는 이유는 프로그래머의 의도를 명시하는 것으로, 크게 세 가지 목적이 있다.
1. 해당 클래스의 코드나 설명 문서를 읽을 이에게 그 인터페이스가 람다용으로 설계된 것임을 알려준다.
2. 해당 인터페이스가 추상 메서드를 오직 하나만 가지고 있어야 컴파일되게 해준다.
3. 유지보수 과정에서 누군가 실수로 메서드 추가를 하지 못하게 한다.

따라서 **직접 만든 함수형 인터페이스에는 항상 @FunctionalInterface 를 사용하라.**

### 마지막으로, 함수형 인터페이스를 API에서 사용할 때의 주의점

서로 다른 함수형 인터페이스를 같은 위치의 인수로 사용하는 overloading를 피하라.
이는 클라이언트에게 불필요한 모호함만 안겨주며 실제로 문제가 발생하기도 한다.
ExecutorService의 submit 메서드는 Callable<T>를 받는 것과 Runnable을 받는 것을 overloading했다.

```
public interface ExecutorService extends Executor, AutoCloseable {
	<T> Future<T> submit(Callable<T> task);
	
	<T> Future<T> submit(Runnable task, T result);
}
```
