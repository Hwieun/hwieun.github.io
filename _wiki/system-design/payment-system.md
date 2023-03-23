---
layout  : wiki
title   : payment system
summary : 
date    : 2023-03-19 12:53:34 +0900
updated : 2023-03-20 09:33:22
tag     : 
toc     : true
public  : true
parent  : [[_wiki/system-design]]
latex   : false
resource: 97B2E09B-01D7-446F-B592-735C02185227
---
* TOC
{:toc}

다음 글은 [System Deisgn Interview - Volume 2](https://www.amazon.com/System-Design-Interview-Insiders-Guide/dp/1736049119)의 Payment System 장을 읽고 정리한 글이다.

#  Payment System이란?

위키피디아에 따르면 " payment system이란 금전 가치를 교환하는 금융적인 트랜잭션을 해결하는 시스템이다. 이것은 이것들을 가능하게 하는 기관, 기구, 사람, 규칙, 절차, 기술을 포함한다."

# Step 1 - 문제 이해하기

어떤 사람은 Apple Pay나 Samsung Pay같은 전자 지갑을 생각할 것이고 어떤 이는 PayPal이나 Stripe와 같은 결제를 담당하는 backend 시스템을 생각할 것이다. 인터뷰 초반에 정확한 요구사항에 대해 질문하는 것이 중요하다.

<details markdown="1"><summary>질문 과정</summary>

> 지원자: 우리가 만드려는 결제 시스템은 어떤 종류인가요?

> 인터뷰어: 우리는 Amazon.com과 같은 e-commerce 애플리케이션을 위한 결제 backend를 만든다고 가정합시다. 고객이 Amazon.com에서 주문을 할 때, 결제 시스템은 돈의 움직임과 관련된 모든 것을 관리해야 합니다.

> 지원자: 어떤 결제 수단을 지원하나요? 신용 카드, PayPal, 은행 카드 등등이요

> 인터뷰어: 실 생활에서의 결제 시스템은 모든 종류를 지원해야 합니다. 하지만, 여기서는 신용카드만 사용한다고 해볼게요.

> 지원자: 신용카드 결제 프로세싱을 저희가 직접 하나요?

> 인터뷰어: 아니요, 우리는 Stripe와 같은 third-party 결제 프로세서를 사용합니다.

> 지원자: 저희 시스템에 신용 카드 데이터를 저장하나요?

> 인터뷰어: 그건 매우 높은 수준의 보안성과 규정에 맞춰야 한다는 요구사항이 필요하기 때문에 우리는 우리 시스템에 직접 카드 번호를 갖고 있지 않습니다. 우리는 민감한 신용 카드 정보를 다루는 third-party 결제 프로세서에 맡깁니다.

> 지원자: 애플리케이션은 글로벌한가요? 다른 통화와 국제 결제를 지원해야 하나요?

> 인터뷰어: 좋은 질문입니다. 네, 애플리케이션은 글로벌이지만 우리는 이 인터뷰에서 하나의 통화만 다룰게요.

> 지원자: 하루에 얼마나 결제 트랜잭션이 일어나나요?

> 인터뷰어: 1 million transactions per day

> 지원자: e-commerce 사이트가 매달 판매자에게 지급하는 pay-out flow를 지원해야 하나요?

> 인터뷰어: 네, 지원합니다.

> 지원자: 제 생각엔 요구 사항을 다 모아본 것 같은데요. 더 알아야 할 게 있을까요?

> 인터뷰어: 네. 결제 시스템은 많은 내부 서비스와 외부 서비스와 상호 작용합니다. 서비스가 실패할 때, 우리는 서비스 간에 일관되지 않는 상태가 생길 수 있습니다. 그러므로 우리는  reconciliation을 수행해야 하고 불일치를 고쳐야 합니다.

</details>

### Functioinal requirements

- pay-in flow: 판매자 대신 구매자로부터 돈을 받는 결제 시스템
- pay-out flow: 구매자에게 돈을 전송하는 결제 시스템

### Non-functional requirements

- Reliability and fault tolerance. 실패한 결제를 조심히 다뤄야 한다.
- 내부 서비스와 외부 서비스 사이의 reconciliation process. 
  
# Step 2 - Propose High-level Design


