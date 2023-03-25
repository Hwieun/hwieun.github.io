---
layout  : wiki
title   : 따닥 issue
summary : 
date    : 2023-03-16 08:25:14 +0900
updated : 2023-03-24 19:12:53
tag     : 
toc     : true
public  : true
parent  : [[_wiki]]
latex   : false
resource: 5E2C0FA9-1E13-47D4-9091-98CCBC780073
---
* TOC
{:toc}

# 개요

커머스 개발을 하다가 결제 부분에서 따닥 이슈를 만나서 해결하는 과정을 적어본다.

# 😱 문제

멤버십 구독을 할 때 유저가 n 번 누르면 요청이 n 번 들어와서 결제가 n번 된다.

# 🤔 해결 방안


## 1. 요청 시 클라이언트 헤더에 멱등 키를 받아서 여러 요청이 하나의 요청인지 아닌지 구분한다. (execute at most once)

- 멱등 키는 서버에서 해당 구독 건에 대해 특정할 수 있는 key를 조합하여 사용하는 것으로 한다.
- 멱등 키는 헤더에 설정. ([IETF 표준](https://datatracker.ietf.org/doc/html/draft-idempotency-header-01))
- 결제 요청 전에 DB에서 멱등 키를 확인하여 이미 저장이 되어 있으면 중복 요청으로 `208 Already Reported` 응답
- 멱등 키와 요청한 endpoint 로 구별

### 어디에 저장하지

1. DB
멱등 키 확인/생성하는 로직을 트랜잭션을 분리시켜서 구현한 다음, 해당 클래스를 결제 로직 첫 부분에서 사용하게 한다.
2. redis
**새로 도입**해야 함.
DB보다 빠른 접근과 만료 시간을 지정할 수 있어서 데이터가 적재되는 것에 대해 고려하지 않아도 된다.

### Data Model

```
`id` int(11)
`request_method` varchar(256)
`request_endpoint` varchar(256)
`idempotent_key` int(11)
`created_at` datetime
```

## 2. 결제 요청이 들어오면 MQ 를 사용해서 queueing 해서 순서를 보장한다.

MQ 도입해야함. 한 번에 한 번의 요청만 처리하기 때문에 동시성에 대한 고민을 하지 않아도 된다.

## 3. 로직 앞에 `@Lock(LockModeType.PESSIMISTIC_WRITE)` 트랜잭션 비관적 락으로 막아두기

for update가 붙는 쿼리가 발생한다. 업데이트를 하기 위해 select 하는 것이 목적이고 해당 업데이트가 되는 트랜잭션이 완료되기 전까지 다른 트랜잭션에서 락을 걸 수 없다.

1) 첫 번째 request가 1 번 thread로 처리되는 중이다. select … for update로 비관적 락을 잡았다.

<img width="1621" alt="1" src="https://user-images.githubusercontent.com/104121915/226528112-0b5eb7b4-3f97-4521-b212-cdff6f63dabe.png">

1번 스레드가 락을 잡고 다음 로직을 실행 중이다.

<img width="1621" alt="2" src="https://user-images.githubusercontent.com/104121915/226528152-14d01776-fbf2-447e-a2d3-be97dc870484.png">

2) 2 번째 request가 2 번 thread로 처리되는 중이다. select … for update로 비관적 락을 잡으려고 시도중이다. 
대기를 하며 다음 요청을 처리하기 위해 3번 스레드가 시작된다.

<img width="1608" alt="3" src="https://user-images.githubusercontent.com/104121915/226528170-a22d6e31-7ac9-41c8-83ae-82fdaf7cd93d.png">

3) 1 번 스레드의 결과를 작성한다. `status=200 OK`

2번과 3번 스레드는 1번 스레드가 락을 놓은 후 해당 row에 대한 select를 실행하여 로직을 시작하기에 적합하지 않은 값임을 확인하였다. `status=403 FORBIDDEN`

<img width="1615" alt="4" src="https://user-images.githubusercontent.com/104121915/226528192-9bc0480b-d475-4c28-bd25-2c258041087b.png">

첫 가입 시 membership_subscriptions에 row가 없는 경우가 있을 수 있는데 이러면 상황에 따라 존재하지 않는 같은 record를 insert하는 것은 deadlock 혹은 duplicate key error를 유발할 수 있다. 즉 다른 세션이 해당 record를 insert하는 것은 막을 수 있지만, 지금 세션도 insert하지 못할 수 있다.

db에 결제 transaction이 저장되기 전에는 멱등키와 글로벌 캐시로 요청을 무효화할 수 있지만, 그 후에는 db에 저장된 transaction을 확인하여 중복을 방지하는 과정도 필요할 것 같다.

# 🧐 Test

1. JUnit Test

```java
@Test
    @DisplayName("row가 존재하지 않는 경우, 멤버십 가입(멀티 스레드) 테스트")
    void nonExistsMembershipSubscriptionsRowForMultiThreadTest() throws InterruptedException {
        long userId = 4;

        MembershipJoin membershipJoin = new MembershipJoin();
        membershipJoin.setMembershipUuid("7265812d-0dfd-493f-ad6f-a21cfa2a6bfd");
        membershipJoin.setPaymentId(3L);
        membershipJoin.setPaymentType(PaymentType.CARD);

        AtomicInteger successCount = new AtomicInteger();
        int numberOfExcute = 10;
        ExecutorService service = Executors.newFixedThreadPool(10);
        CountDownLatch latch = new CountDownLatch(numberOfExcute);

        for (int i = 0; i < numberOfExcute; i++) {
            service.execute(() -> {
                try {
                    joinMembershipService.createSubscription(membershipJoin, userId);
                    successCount.getAndIncrement();
                    System.out.println("성공");
                } catch (Exception e) {
                    System.out.println("[DATA ERROR] " + e.getMessage());
                }
                latch.countDown();
            });
        }
        latch.await();

        assertThat(successCount.get()).isEqualTo(1);
        assertThat(subscriptionRepository.findByUserIdAndCreatorId(userId, 3L).isPresent()).isTrue();
        assertThat(subscriptionRepository.findByUserIdAndCreatorId(userId, 3L).get().getStatus()).isEqualTo(MembershipSubscriptionStatus.SUBSCRIBED);
    }

    @Test
    @DisplayName("row가 존재하는 경우, 멤버십 가입(멀티 스레드) 테스트")
    void existsMembershipSubscriptionsRowForMultiThreadTest() throws InterruptedException {
      // 위와 같음
    }
```
기존에 row가 존재하는 경우 한 번만 실행되는 것이 보장되고, 존재하지 않는 경우 동시에 insert 를 실행한다. 
unique index가 있으면 하나만 생성되는 것을 보장한다.

2. postman으로 연속 요청을 재현

다음은 test script 이다.
```js
const echoPostRequest = {
  url: 'http://localhost:8080/api/v1/user/membership/join',
  method: 'POST',
  header: {
    "Authorization": 'Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJyaXZlckB0dW1ibGJ1Zy5jb20iLCJpYXQiOjE2NjQzMzIzNzYsImV4cCI6MTY5NTg2ODM3NiwiZWlkIjoiYjE3YjVkZDNjMDE4NjljMjBiMGJmMjM5MjY3NDk0MDAiLCJtYWNoaW5lX2lkIjoiOTA0YTYxZWUzOWEwMDE2ZSJ9.QbWLspwdv5BwXTtMv0eDqeVRy1SxaunxvHziUvr9nk0',
    "Content-Type": "application/json"
  },
  body: {
    mode: 'raw',
    raw: JSON.stringify({
    "membershipUuid": "03e36383-40f4-43cd-bac9-cdfb3da26bbe",
    "noticeAgreement": true,
    "paymentId": 332,
    "paymentType": "CARD",
    "recurringPaymentAgreement": true,
    "thirdPartyPrivacyForCreatorAgreement": true
    })
  }
};

function sendRequest() {
    return new Promise((resolve, reject) => {
        pm.sendRequest(echoPostRequest, (err, res) => {
            if (err) {
                return reject(err);
            }

            return resolve(res);
        })
    });
}

pm.test('', (async function main() {
    const result1 = sendRequest();
    const result2 = sendRequest();
    const result3 = sendRequest();

    console.log('result1:', result1.code);
    console.log('result2:', result2.code);
    console.log('result3:', result3.code);
})());
```

<img width="467" alt="1" src="https://user-images.githubusercontent.com/29860102/227491892-342862ac-ba51-471c-a45d-4e8298cb7efd.png">
