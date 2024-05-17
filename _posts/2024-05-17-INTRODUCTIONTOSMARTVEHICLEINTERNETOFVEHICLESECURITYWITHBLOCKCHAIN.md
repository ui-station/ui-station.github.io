---
title: "블록체인을 활용한 스마트 차량 IoV 보안 소개"
description: ""
coverImage: "/assets/img/2024-05-17-INTRODUCTIONTOSMARTVEHICLEINTERNETOFVEHICLESECURITYWITHBLOCKCHAIN_0.png"
date: 2024-05-17 19:16
ogImage: 
  url: /assets/img/2024-05-17-INTRODUCTIONTOSMARTVEHICLEINTERNETOFVEHICLESECURITYWITHBLOCKCHAIN_0.png
tag: Tech
originalTitle: "INTRODUCTION TO SMART VEHICLE (INTERNET OF VEHICLE) SECURITY WITH BLOCKCHAIN"
link: "https://medium.com/@muskanyadavnewdelhi/introduction-to-smart-vehicle-internet-of-vehicle-security-with-blockchain-a48a9c1c24c3"
---


<img src="/assets/img/2024-05-17-INTRODUCTIONTOSMARTVEHICLEINTERNETOFVEHICLESECURITYWITHBLOCKCHAIN_0.png" />

요약 - 연결된 디바이스로부터 데이터를 수집하여 센서, 액추에이터, RFID 등을 사용하여 강력한 네트워크를 통해 자동차를 지능적으로 만들기 위한 (경험을 통해 학습) 블록체인은 이러한 안전하지만 무결하지 않을 수 있는 네트워크를 만드는 단일 솔루션으로, 보안을 높이는데 그치지 않고 동시에 블록체인은 탈중앙화되어 사용자에게 원활한 경험을 제공할 것임을 기반으로 한다. 또한, 실생활 문제들이 많이 있어야 한다(인지 이동성, 낮은 지연 시간, 큰 대역폭의 필요). 따라서 우리는 데이터 마이닝, apriori 알고리즘, 의사 결정, 컴퓨터 비전, 데이터 수집 및 경로 프로토콜을 사용하여 차량 간 통신을 간편하게 관리한다. 사물 인터넷의 인지 아이디어는 교통 관리를 모니터링, 저장 및 분석하고 충돌 경고와 더 나은 내비게이션을 제공하는데 극히 중요하다. 또한 격차 해소를 위한 양방향 통신의 간단한 구현과 자율 주행에 대한 미래 범위가 제안된다.

## 연구 목적

전기 자동차의 필요성

<div class="content-ad"></div>

1. 우리제품은 배기가스를 배출하지 않아요 = 천식, 폐암, 폐포획증

2. 자연자원 저감 없어요

3. 연소되지 않은 화석연료는 지구온난화 야기

4. 가격이 더 저렴해요

<div class="content-ad"></div>

5. 엔진 없음 소음 없음

![image](/assets/img/2024-05-17-INTRODUCTIONTOSMARTVEHICLEINTERNETOFVEHICLESECURITYWITHBLOCKCHAIN_1.png)

# 소개

인구 증가 속도가 급격히 증가함에 따라 한정된 자원을 최대로 활용해야 하는 필요성이 매우 중요해졌습니다. 미래의 고갈을 줄이기 위해 충분한 유용한 공급품을 갖춘 좋은 연결 환경, 그리고 실시간 데이터를 모니터링하여 삶의 질을 향상시키는 것을 스마트 도시의 개념이라고 합니다. 나중에 발생할 문제를 이해하는 데 많은 도움이 될 스마트 도시의 기능을 강조하기 전에 나아가기 전에 좋은 아이디어가 될 것입니다.

<div class="content-ad"></div>

요즘 EV가 블록체인 보안 없이 얼마나 안전한지에 대해 고민해 봅시다.

도로에서 무단자가 제동을 제어하려고 하거나 의도적으로 사고를 일으키려 할 때 어떤 가능성이 발생할 수 있는지 상상해 보세요. 또한, 운전자, 승객, 차량 모델 및 동시에 그 e-차량의 구동 부품 상태에 대한 모든 기록을 저장하고 있는 데이터베이스가 있다면, 네트워크 상태가 약하다면 모든 데이터가 잘못된 손에 빠질 수 있습니다. 위장자는 클라우드에 악성 소프트웨어를 주입하고 남용할 수 있습니다. 이러한 경우 해커를 감지하고 식별하는 것은 거의 불가능한 일이 됩니다. 해커가 미리 시스템에 저장된 데이터를 변경하여 데이터 침해 활동에서 보이지 않도록 만든 경우 마찬가지입니다. 51% 공격은 중요한 데이터를 삭제하거나 변경하여 이중 소비를 유발할 수 있습니다.

자동차와 관련된 스마트 시티의 특징

I. 최신 및 시간을 절약하는 교통 시스템입니다.

<div class="content-ad"></div>

II. 신뢰할 수 있는 분산 원장.

III. 자동 모니터링 시스템.

이제 궁금증이 생깁니다. 어떻게 우리가 필요한 모든 세부 정보를 포함한 P2P 네트워크를 구축할 수 있을까요? 다행히도 블록체인이 우리를 구해줍니다.

## 블록 + 체인 기술

<div class="content-ad"></div>

중요한 점은 최종 사용자에게 충실성을 제공하는 제삼자 액세스를 제거하는 것입니다. 그 이름에서 알 수 있듯이, 이는 통신을 위해 연결된 개인 정보로 포함된 다양한 수의 블록으로 이루어진 데이터베이스입니다. 이 연결은 미토콘드리아에 있는 ATP(아데노신 삼인산)와 마찬가지로 저장 목적으로 사용됩니다.

이러한 블록들이 서로 어떻게 연결되어 있는지, 어떻게 통신하고 있으며, 특정 블록에 액세스하기 전에 고려해야 할 속성 및 규칙에 대해 알아보겠습니다.

저장된 모든 데이터와 정보는 디지턈 형태로 되어 있으며(이제 시스템의 관리자는 중요한 세부 정보에 변경을 할 수 없는) 불변입니다.

특징들:

<div class="content-ad"></div>

- 변경할 수 없음
- 되돌릴 수 없음
- 타임 스탬프가 찍힘
- 합의 과정

<div class="content-ad"></div>

## 작업 증명

해커들을 위해 만들어진 해시 함수와 해시 코드로 완전히 보호된 공급망으로 데이터 침범이 불가능합니다.

## 해시 함수 사용의 장점

해시 함수는 충돌 없이 올바른 매핑을 통해 해시 테이블을 통해 새로운 길이가 더 작은 문자(버킷)을 생성하는 데 도움을 줍니다. 키-값 쌍은 파이썬의 딕셔너리 개념과 마찬가지로 사용됩니다.

<div class="content-ad"></div>

## 유형

- **나눗셈 방식** h(K) = k mod M

- **중간 제곱 방식** h(K) = h (k x k)

- **접기 방식** h(K) = s

<div class="content-ad"></div>

이제 우리는 다양한 유형의 해시 함수에 대해 배웠으니 DTL'S라고 알려진 하이테크 보안 원장 시스템의 요구를 충족하는 일부 알고리즘을 살펴 보겠습니다.

먼저, SHA-256 알고리즘

SHA-1의 정제된 형태(중요한 속성을 상속함). 특별한 알고리즘이 필요한 이유와 왜 우리가 접근을 제한하여 이 보안 문제를 회피하지 않는지 궁금할 수 있습니다. 답은 질문 그 자체에 있습니다. 블록체인을 사용하여 데이터베이스를 유지하면 해커에게 혼란과 지연을 초래하는 결과로 코드가 변경됩니다.

누군가가 디지털 화폐 형태(예: 비트코인, 리트코인, 이더리움)로 자금을 이체하고 싶을 때, 그는 애플리케이션을 믿으며 해당 애플리케이션의 소유자가 변경되지 않은 서비스를 제공하는 책임이 있습니다.

<div class="content-ad"></div>

두 가지 다른 당사자 간의 목적을 위한 계약을 체결하는 것과 유사한 상황입니다.

![이미지1](/assets/img/2024-05-17-INTRODUCTIONTOSMARTVEHICLEINTERNETOFVEHICLESECURITYWITHBLOCKCHAIN_2.png)

참고용 Merkle 트리

![이미지2](/assets/img/2024-05-17-INTRODUCTIONTOSMARTVEHICLEINTERNETOFVEHICLESECURITYWITHBLOCKCHAIN_3.png)

<div class="content-ad"></div>

## 거래 구조

XYZ Private Limited 회사에 자금을 이체하려고 한다고 가정해봅시다. 블록체인 기술은 전체 프로세스 동안 모든 측면의 기록을 유지하여 어떤 수단으로도 변경할 수 없게 합니다. 이 기록은 관리자가 'n'번의 요청에 따른 남용을 방지합니다.

영구적인 기록은 시스템이 데이터 침해를 만날 때 빠른 시간 내에 생성됩니다. 그렇다면 해당 사용자는 이전에 존재하는 데이터에 수정을 가하면서 해당 블록에 대해 10분의 계산을 수행해야 합니다. 그 블록에 대해 새로운 해시 코드가 생성될 것입니다.

여기서 합의 프로세스의 사용이 필요합니다.

<div class="content-ad"></div>

```
![Introducing Smart Vehicle Security with Blockchain](/assets/img/2024-05-17-INTRODUCTIONTOSMARTVEHICLEINTERNETOFVEHICLESECURITYWITHBLOCKCHAIN_4.png)

If client B wants to add some information into the block, then the system will generate an automatic vote poll which is completely unbiased. Client A has to agree upon the modification scheme, then only client B can make any changes in the data otherwise the request will be rejected.

Nowadays, cryptocurrencies are growing like never before.

## Why is security so crucial in the automobile industry?
```

<div class="content-ad"></div>

a) 연결성을 높이는 것

b) 데이터 취약성으로부터 안전

한 예로 ARGUS CYBER SECURITY가 있습니다

## 관련 작업

<div class="content-ad"></div>

- IOTA Tangle은 자신들의 원장이 블록체인의 탈중앙화된 원장보다 강력하고 빠르다고 주장했지만 2017년에 382만 달러의 도난 사건이 발생하여 MIOTA(IOTA)의 완전한 실패로 이어졌습니다.

- Argus 보안

- Movable type 스크립트(웹사이트)

- SPIC-C는 사설 주차장을 위해 설계된 시스템 중 하나입니다.

<div class="content-ad"></div>

- 2019년 6월 이후로, 미시건 공과대학교의 디지털 통화 이니셔티브는 40여 건 이상의 51% 공격을 감지하거나 관찰하였으며 알려졌습니다.

- 2018년 CES에서 라스베이거스에서 닛산의 브레인 투 차량 기술을 소개했습니다.

# 프로젝트는 네 가지 기본 기둥을 기반으로 합니다:

1. EEG를 통한 웨어러블 및 무선 감지 기술

<div class="content-ad"></div>

2. 운전자의 움직임을 감지하는 데이터 분석

3. 운전자와 차량 간의 공유 제어 절차

4. 시뮬레이션을 기반으로 한 통합 시스템 및 테스트

## 어떤 알고리즘을 사용해야 할까요? (해결책)

<div class="content-ad"></div>

# APRIORI 알고리즘

두 개의 매개변수인 지지도(Support)와 신뢰도(Confidence) 사이에 연관 규칙을 설정하는 데 사용됩니다. 이 관계는 임계 값이 충족되어야만 수용되며 그렇지 않으면 거부되거나 후속 연관에 고려되지 않을 수 있습니다. 마지막으로는 아래쪽 폐쇄 속성을 사용하여 규칙이 생성됩니다.

## 가상 상황

학교로 향하는 도중 길에 흩어진 쓰레기통이나 자전거 타이어를 찔 수 있는 날카로운 가장자리를 가진 유리 조각 등이 몸에 영향을 주기 전까지 보이지 않을 수 있습니다. 이러한 상황 모두에서 공통점은 다양한 위험 상황에 노출될 가능성이 매우 높다는 것입니다. 누군가는 차량의 방향을 변경해서 피하면 된다고 생각할 수 있지만 실제로 그렇지 않습니다.

<div class="content-ad"></div>

판단 능력을 지수적으로 향상시켜야 합니다. 시간 내 주변 상황을 미리 예측하는 능력은 실제로 생명을 구할 수 있는 응답 시간을 포함합니다.

알고리즘은 기계에 무엇을 입력할지와 무엇을 입력하지 말아야 하는지 결정하는 데 우리를 도와줄 것입니다. 기계 학습과의 동등한 협력을 통해 인간 뇌처럼 생각하는 방식을 훈련시킬 것입니다. 이것은 도전적인 과제일 것입니다.

뇌-컴퓨터 인터페이스(BCI) = 침범적, 부분적 침범적, 비침범적 연구는 상당히 유익한 결과를 보여줍니다.

기계의 주요 단점은 고장과 실수에 매우 취약하다는 점입니다.

<div class="content-ad"></div>

큰 양의 훈련된 데이터가 필요합니다.

## 방법론

컴퓨터 비전 사용

컴퓨터 비전이 블록체인과 손을 잡아 중요한 역할을 하는 것입니다.

<div class="content-ad"></div>

텍스타일 산업: 천연 원료를 인공적으로 가공하여 완제품으로 만드는 농업 기반 산업이에요. 천의 품질, 옷이 제공하는 안전 수준, 식품 산업 등이 중요한 요소에요.

![image](/assets/img/2024-05-17-INTRODUCTIONTOSMARTVEHICLEINTERNETOFVEHICLESECURITYWITHBLOCKCHAIN_5.png)

## 참고 자료

- [Nissan brain to vehicle technology](https://www.bitbrain.com/blog/nissan-brain-to-vehicle-technology)
- [IOTA](https://www.iota.org/)
- [Argus Security](https://argus-sec.com/)

<div class="content-ad"></div>

"내게 말해주면 나는 잊어버리고, 가르쳐주면 기억할 수도 있지만, 함께 참여시켜주면 나는 배운다." - 벤자민 프랭클린

의견이 있으면 언제든 환영합니다.