---
layout  : wiki
title   : Transactional을 통한 트랜잭션 분리
summary : 
date    : 2023-03-15 08:11:20 +0900
updated : 2023-03-15 08:26:28
tag     : 
toc     : true
public  : true
parent  : [[_wiki/spring]]
latex   : false
resource: 09A47362-4501-42D2-A9DA-FF297FB6A469
---
* TOC
{:toc}

 ```java
public class Parent {
  @Transactional
  public void parent() {
    parentRepository.save(Parent.builder().id(20L).build());
    child();
  }
}

public class Child {
  @Transactional
  public void child() {
    userRepository.save(User.builder().id(20L).build());
    throw new CustomException(HttpStatus.BAD_REQUEST, "child");
  }
}
```

1. 트랜잭션 전파 같게
    
    둘다 롤백 진행됨.
    
2. REQUIRE_NEW 적용
    
    ```java
    @Transactional(propagation=REQUIRE_NEW)
    public void child() {
        userRepository.save(User.builder().id(20L).build());
        throw new CustomException(HttpStatus.BAD_REQUEST, "child");
    }
    ```
    
    둘다 롤백 진행됨.
    
    트랜잭션은 분리되지만 하나의 스레드로 수행되기 때문에 예외가 전해져서 호출한 쪽도 롤백된다.
    

3. 2 + parent에 norollback 적용
    
    ```java
    @Transactional(noRollbackFor = CustomException.class)
    public void parent() {
      parentRepository.save(Parent.builder().id(20L).build());
      child();
    }
    ```
    
    parent만 저장. noRollbackFor 을 적용하면 Exception이 발생하기 전까지 commit한다
    
4. 3 + child에 norollback 적용
    
    ```java
    @Transactional(noRollbackFor = CustomException.class)
    public void parent() {
      parentRepository.save(Parent.builder().id(20L).build());
      child();
    }
    
    @Transactional(propagation=REQUIRE_NEW, noRollbackFor = CustomException.class)
    public void child() {
        userRepository.save(User.builder().id(20L).build());
        throw new CustomException(HttpStatus.BAD_REQUEST, "child");
    }
    ```
    
    둘다 저장
    

5. child에 norollback 대신 try - catch

```java
@Transactional(noRollbackFor = CustomException.class)
public void parent() {
  parentRepository.save(Parent.builder().id(20L).build());
  child();
}

@Transactional(propagation=REQUIRE_NEW)
public void child() {
  try{
    userRepository.save(User.builder().id(20L).build());
    throw new CustomException(HttpStatus.BAD_REQUEST, "child");
  }
  catch(Exception e) {  }
}
```

parent만 저장

6. child에 norollback 대신 try - finally
    
    ```java
    @Transactional(noRollbackFor = CustomException.class)
    public void parent() {
      parentRepository.save(Parent.builder().id(20L).build());
      child();
    }
    
    @Transactional(propagation=REQUIRE_NEW)
    public void child() {
      User user = new User();
      try{
        user = User.builder().id(20L).build();
        throw new CustomException(HttpStatus.BAD_REQUEST, "child");
      }
      finally {
        userRepository.save(user);
      }
    }
    ```
    
    둘다 저장
    
7. 둘다 noRollbackFor만 설정하면?
    
    ```java
    @Transactional(noRollbackFor = CustomException.class)
    public void parent() {
      parentRepository.save(Parent.builder().id(20L).build());
      child();
    }
    
    @Transactional(noRollbackFor = CustomException.class)
    public void child() {
      User user = new User();
      try{
        user = User.builder().id(20L).build();
        throw new CustomException(HttpStatus.BAD_REQUEST, "child");
      }
      finally {
        userRepository.save(user);
      }
    }
    ```
    
    둘다 저장됨. child에 try-finally 안해도 둘다 저장됨.
    
- @Transactional과 try-catch
    
    @Transactional에서 예외를 잡으면 (try-catch) 롤백이 발생하지 않는다. 단, 트랜잭션을 분리하지 않고 하나로 실행하는 상황에서 예외를 잡기 전 이미 Exception이 발생하여 rollback marking이 되면 롤백이 진행된다.
    

---

- 주의
    
    트랜잭션 분리해서 Parent, Child 둘다 같은 테이블에 저장/수정하려하면 deadlock 발생
