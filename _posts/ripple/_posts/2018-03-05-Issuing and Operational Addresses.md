---
layout: post
title: Issuing and Operational Addresses
date: 2018-03-05 09:36:55
tags: [ripple, 리플, 번역]
categories: [Ripple, 번역]
---
* TOC
{:toc}


# Issuing and Operational Addresses
(source: [https://ripple.com/build/issuing-operational-addresses/](https://ripple.com/build/issuing-operational-addresses/))

XRP 원장 시스템 상에서, 금융 기관들은 일반적으로 다수의 XRP 원장 주소를 사용한다. 그 이유는 손상된 비밀키(compromised secret key)와 연관된 위험을 최소화하려고 하기 때문이다. Ripple은 다음과 같이 발행 주소, 거래 주소등으로 역활을 분리하는 것을 강력히 권장한다.

* "cold wallet"이라고도 불리는 발행주소(issuing address)는 금융 기관의 원장 기반의 회계적 관계상의 중심역활(허브)을 하지만, 최소한의 거래만을 다루어야 한다.
* "hot wallet"으로 불리는 하나 혹은 그 이상의 거래 주소(operational address)들이 있다. 자동화되어 있고 인터넷에 연결된 시스템은 secret key와  거래 주소들을 사용하여 고객들과 파트너간에 발생하는 매일 매일의 거래를 수행한다.
* 선택적으로, 스탠바이 주소(standby address)는 "warm wallet"으로도 불리며, 신뢰할 수 있는 작업자들이 거래주소들로 금전을 송금하기 위해 이 주소들을 사용한다.

## 자본의 생애주기(흐름), Funds Lifecycle
XRP 원장 기반의 XRP가 아닌 모든 통화(발행 통화)들은 두개의 XRP 원장 주소들과 회계적 관계를 가진다. 금융 기관이 Ripple에서 권고하는 분리된 역활 주소 체계를 사용하는 경우,  해당 기관과 관련된 자금은 일정한 흐름을 가지는 경향이 있다.

![](https://cdn.ripple.com/wp-content/uploads/2016/03/funds_flow_diagram.png)

발행 주소를 통해 지불을 할 경우, 발행 주소는 XRP 원장상에 회계적 관계로 표현되는 잔액(대차(수지) 계산)을 생성한다. (즉, 누구한테 돈을 얼마를 주었는지를 장부에 기록한다는 의미) XRP 원장내에서, 사용자는 다른 회계적 관계로 잔액(자본)을 교환할 수 있기 때문에  XRP가 아닌 자본을 *발행 통화* 라는 용어를 사용한다. 발행은 채무를 나타내기때문에, 발행자산은 발행자의 관점에서 보면 음수 값을 가질 수 있다. 발생자의 계약 대상자의 입장에선 동일한 발행 자산은 양수의 값을 가진다. 발행자의 주소로 지불을 하게 되면, 채무는 줄어 들게 되고 발행된 발행 자산을 상쇄한다.

발행 주소는 발행 자산을 스탠바이 주소로 보내거나 거래주소(operational address)로 직접 보낸다. 스탠바이 주소는 받은 발행 자산을 거래주소(operational address)로 보낸다. 거래주소(operational address)는 유동성 공급자, 파트너 그리고 다른 고객들과 같은 다른 계약 대상자에게 지불을 하게 된다. 그 이유는 모든 발행 자산은 발행 주소와 회계적 관계로 묶여 있으며, 지불과 발행 자산의 교환은 발행 주소를 거쳐 ripple("ripple through")되기 때문이다.  지불은 송금자의 잔액을 회계적 관점에서 차감하는 차변에 기입 하고, 수령인의 잔액을 발행자와의 회계 관계상에서 대변 처리(수익 발생)를 하게 된다. XRP 원장 시스템은 주문서들을 통한 다수의 발행자들과 [자신의 자산을 ripple로 교환해 주는 유동성 공급자들](https://ripple.com/build/understanding-the-noripple-flag/)을 연결하는 보다 복잡한 [관계(경로)](https://ripple.com/build/paths/)들을 지원한다.

## 발행 주소, Issuing Address
발행 주소는 마치 금고와도 같다. 파트너, 고객 그리고 운영 주소들은 발행 주소와 회계 관계(trustlines)를 맺지만, 발행 주소는 가능한 최소한의 트랜잭션만을 처리한다. 주기적으로, 운영자(human operator)는 스탠바이 혹은 운영 주소의 잔액을 채우기 위해 발행 주소로부터 발생된 트랜 잭션을 생성 및 싸인한다. 이상적으로, 이런 트랜잭션을 싸인하는데 사용되는 비밀키(secret key) 인터넷 연결을 통해 다른 컴퓨터에서 절대 접근할 수 없어야 한다.

금고와는 달리, 발행 주소들은 고객과 파트너로부터 직접 지불을 받을 수 있다. XRP 원자 시스템 상의 모든 트랜잭션은 공개되기때문에, 자동화된 시스템은 비밀키 없이도 발행 주소로 지급되는 지불을 모니터링 할 수 있다.

### 발행 주소 노출, Issuing Address Compromise
만약 악의적인 공격자가 특정 기관의 발행 주소 뒤에 숨겨진 비밀키를 탈취하게 되면, 공격자는 새로운 발행 자산을 제한없이 생성할 수 있고 탈 중앙화된 교환소를 통해 해당 자산을 거래할 수 있다. 이렇게 되면, 금융 기관은 합법적으로 획득한 발행 자산을 구별하고 해당 자산을 공정하게 환급하는 것을 어렵게 만든다. 만약 금융기관이 발행 자산에 대한 통제권을 잃는 다면, 해당 기관은 반드시 새로운 발행 주소를 생성해야 하며, 이전 발생 주소와 회계적 관계를 맺고 있는 모든 사용자들은 반드시 새로운 발생 주소와 새로운 회계 관계를 생성해야 한다.

### 다수의 발행 주소, Multiple Issuing Address
금융 기관은 하나의 발행 주소를 사용하여 하나 이상의 통화를 XRP원상 시스템 상에서 발행 할 수 있다. 그렇지만, 송금 수수료 및 글로벌 동결 상태를 포함한 주소에서 발행 된 모든 통화에 동등하게 적용되는 일부 설정이 있다. 만약 금융 기관이 개별 통화에 대해 설정을 다르게 관리하기를 원한다면, 해당 기관은 개별 통화에 대해 다른 발생 주소를 사용해야 한다.


## 운영 주소, Operational Addresses
운영 주소는 금전 출납원과 같다. 발행 자산을 고객들과 파트너들에게 전송하여 기관을 대신하여 지불을 한다. 자동으로 트랜잭션을 싸인하기 위해, 운영 주소의 비밀키는 반드시 인터넷에 연결된 서버에 저장이 되어야 한다.(비밀키는 암호화되어 저장하고 트랜잭션을 싸인하기 위해 복화화 될 수 있어야 한다.) 고객들과 파트너들은 운영 주소와는 회계 관계를 맺을 수도 맺어서도 안된다.

각각의 운영주소는 발행 자산의 보유 금액에 대한 제한을 가진다. 운영 주소의 보유 금액이 낮아지게 되면, 금융 기관은 발행 주소 혹은 스탠바이 주소로부터 지급된 금액을 운영 주소로 보내 운영 주소의 보유 금액을 채워야 한다.

## 운영 주소 노출, Operational Addresses Compromise
만약 악의적인 공격자가 운영 주소 뒤에 숨겨진 비밀키를 탈취한다면, 해당 금융 기관은 운영 주소가 가진 금액만큼만을 잃게 된다. 해당 기관은 고객과 파트너들의 대응 없이도 새로운 운영 주소로 이전 운영 주소를 바꿀 수 있다.

## 스탠바이 주소, Standby Addresses
특정 기관은 선택적으로 발행 주소와 운영 주소간 중간자적 역활을 할 수 있는 "스탠바이 주소(standby address)"를 사용하여 위험과 편의의 균형을 취할 수 있다. 해당 기관은 XRP 원장 시스템상의 추가적인 주소를 스탠바이 주소로 사용할 수 있으며, 비밀 키는 온라인에 저장되지 않는다. 하지만 비밀키는 다른 신뢰할 수 있는 사용자에게 맡긴다.

운영 주소의 보유 자금이 낮아 지는 경우에, 신뢰할 수 있는 사용자는 스탠바이 주소를 사용하여 운영 주소의 보유 자금을 다시 채울 수 있다. 스탠바이 주소의 보유 금액이 낮은 경우, 해당 기관은 발행 주소를 통해 단일 트랜잭션으로 스탠바이 주소로 추가적인 통화를 보낼 수 있다. 그리고 스탠바이 주소들은 필요에 따라 통화를 자신들간에 분배할 수 있다. 이렇게하면 발급 주소의 보안이 향상되어 단일 자동화 시스템을 제어하는 데 너무 많은 돈을 낭비하지 않고 총 거래량을 줄일 수 있다.

운영 주소와 마찬가지로 대기 주소는 고객 또는 파트너가 아닌 발급 주소와 회계 관계가 있어야한다. 운영 주소에 적용되는 모든주의 사항은 대기 주소에도 적용된다.

### 스탠바이 주소 노출, Standby Address Compromise
대기 주소가 유출 된 경우 작동중인 주소가 손상된 것과 같은 결과가 발생한다. 악의적인 행위자는 스탠바이 주소가 보유한 잔고를 훔칠 수 있으며 금융 기관은 고객 및 파트너의 조치없이 새로운 대기 주소로 변경할 수 있다.