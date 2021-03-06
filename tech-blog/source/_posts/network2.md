---
title: Network Engine 2부
date: 2019-10-12 22:47:01
thumbnail : /images/network2.png
tags: [Network,IoT,RTLS,LBS,VertX,Node.js]
category : [IT Tech, 1. IoT]
---
### IoT 에서의 네트워크 엔진
> 작성자 : 플랫폼 개발실 실장 김완철(David Kim)
#### 3. IoT 에서의 데이터 수집시 문제점
##### 3.1. 대량의 데이터
- 현재 IoT 디바이스 수는 보통 우리가 생각하는 수준을 휠씬 뛰어넘고 있다. 디바이스수는 데이터량과 정비례한다. 의미있는 데이터든, 단순한 로그성 데이터든 디바이스에서 중앙저장소(서버)로 데이터를 전송하는 액션은 IoT디바이스가 가지고 있는 가장 기본적인 성향이자, 비즈니스를 하는 입장에서 추구하는 지향점이라고 할수 있다. 보통 웹브라우저에서 웹사이트에 접속하면 서버에서 로컬 클라이언트로 데이터가 전송되어 화면에 나타나게 된다. IoT디바이스의 경우 데이터의 방향이 보통의 웹사이트와 반대 방향이기 때문에 중앙저장소(서버)의 역할은 매우 크다고 할수 있다. 대량의 데이터를 서버에 처리하는 프로세스는 일반 웹서비스를 개발하는 과정과는 근본적으로 차이가 있다. 컴퓨팅 리소스는 기본이며, 애플리케이션의 견고성, 퍼포먼스는 늘 고민할 수 밖에 없다.

##### 3.2. 초고속 데이터
- 일반적으로 데이터의 양이 문제가 되지만, 그와 함께 데이터 전송 주기는 데이터의 양만큼 기본적으로 고민할 수 밖에 없는 문제점이다. 주기 또는 속도는 같은 데이터의 양이라고 하더라도 서버의 리소스와 애플리케이션의 성능을 좌지우지하는 중요한 팩터(factor)이다. 규칙적으로 빠르게, 규칙적으로 느리게, 불규칙적으로 빠르게, 불규칙적으로 느리게, 이렇게 다양한 방식으로 데이터는 전송된다. 그중 가장 큰 위험요소는 불규칙적으로 빠르게 전송되는 데이터이다. 불규칙적이고 빠른 주먹은 방어하기가 쉽지 않듯이, 사전 준비나 예측이 불가능한 경우는 컴퓨팅 리소스와 애플리케이션의 성능 측면에 있어서는 치명적인 독과 같다.  

##### 3.3. 네트워크 레이턴시
- 인터넷 속도는 근 20년 사이에 엄청나게 빨라졌다. 스마트폰에서도 실시간으로 동영상이나 MMORPG 게임이 가능한 수준이기에 일반적으로 사용하는 유선인터넷의 속도는 가히 짐작할 수 있다. 특히 기업의 경우는 가정에서 사용하는 네트워크의 전송속도나 대역폭(bandwidth)보다 수십배는 좋기 때문에 데이터 전송에 매우 최적화되어 있다. 그렇다고 해서 모든 기업의 네트워크의 성능이 최대치라고 생각하는것은 무리가 있다. 비용문제와 보안문제 등 여러가지 이유로 전송속도와 대역폭, 그리고 특정 기간동안의 제한된 전송량은 IoT디바이스의 데이터 전송 측면에서 보면 치명적인 문제점으로 다가온다. 또한, 인위적인 제한이 아니라고 하더라도, 너무 많은 디바이스로 인해 네트워크 전송속도가 느려지고, 레이턴시(지연시간)이 늘어나게 되는 경우는 매우 자주 발생하는 현상으로 볼수 있다. 단편적으로 사무실에서 누군가 대량의 데이터를 다운로드 받거나, 업로드할때 사무실 전체 네트워크가 느려지는것이 이러한 경우라 할수 있다.  

##### 3.4. 데이터 수집 서버 성능
- 데이터는 중앙저장소 즉 콜렉터(collector)라고 불리우는 서버로 전송된다. 웹서비스와 다른 대량의 데이터를 초고속으로 받아서 로직을 실행하거나, 분석하거나, 또는 단순히 저장을 하는 일련의 과정은 하드웨어 서버가 아닌 서버에 설치되어 있는 애플리케이션(웹서버, 데이터 처리 애플리케이션 등등)가 수행하게 된다. 서버를 아무리 하이엔드로 구축하였다 하더라도 데이터를 처리하는 애플리케이션의 성능은 얼마나 적절한 애플리케이션을 구축했는지, 또는 적절한 아키텍처로 구성되었는지에 따라 많은 차이를 보인다.



#### 4. IoT 에서의 데이터 수집에 새로운 접근법
##### 4.1. 새로운 아키텍처에 대한 고민
- 데이터 수집시 네트워크 속도나 대역폭, 그리고 전송량은 데이터를 수집해야 하는 해당 지역 또는 회사의 네트워크 인프라에 의존(Dependency)할수 밖에 없다. 기본적으로 네트워크에 문제가 발생하거나, 전송이 불규칙하거나, 데이터가 한꺼번에 몰리는 경우가 있다는 전제을 깔고, 아키텍처를 구성해야 한다. 일반적인 아키텍처는 수집 서버(또는 웹서버) 한대로 모두 처리하는 경우로 데이터베이스 역시 함께 구성한다. 또는 데이터베이스를 분리하는 경우가 그 다음으로 구성하는 단계이다. 데이터 처리가 서버 한대로 불가능한 수준까지 오게 되면, 1차적으로 스케일업(scale-up)을 하게 된다. 즉 CPU, 메모리, 스토리지 용량을 높이는 단계이다. 정통적인 서버의 경우는 스케일업조차 쉽지 않았다. 왜냐하면 직접 하드웨어를 열어서 업그레이드를 해야 했기 때문이다. 클라우드가 보편화된 요즘은 한번의 설정 변경으로 스케일업이 이루어지기 때문에 크게 문제가 되지 않는다. 만약, 스케일업 수준을 넘게되면 2차로 횡적으로 확장하는 스케일아웃(scale-out)을 시도하게 된다. 하지만, 이 마저도 어느 시점에 가게 되면 한계에 도달하게 된다. 즉, 새로운 애플리케이션, 새로운 아키텍처에 대한 고민이 시작되며, 기존의 정통적인 방식의 단순한 아키텍처로는 해결이 어렵다는것을 알게 된다. 빅데이터 아키텍처가 발전함에 따라 대용량 데이터 처리를 위한 새로운 애플리케이션, 예를 들어 스톰, 하둡, 카프카 등등 매우 다양한 애플리케이션이 나타났으면, 클라우드에서 제공하는 매니지먼트 서비스 (AWS 키네시스 등등)도 함께 발전하게 되었다.

##### 4.2. 클라우드 컴퓨팅과 엣지 컴퓨팅
- 클라우드 컴퓨팅이 보편화된것이 그리 오래 되지 않았다. 기존에는 서비스를 오픈하기 위해서는 하드웨어 스펙과 대수를 산정하고 견적을 받는 업무가 필수적이였다. 인프라 지식이 없는 엔지니어나 개발자의 경우는 하드웨어 스펙을 산정하는 일이 그리 쉬운일만은 아니였다. 또한 규모가 있는 회사에서는 구매팀이 따로 있어서 서버 구매금액에 대한 네고라고 불리우는 네고시에이션(negotiation)을 수행했지만, 작은 회사, 보통 스타트업의 경우 개발리더 또는 CTO가 견적 및 네고까지 하는 경우가 비일비재했다. 특히 서버 수량의 산정은 구매 금액의 할인률과도 연관이 있고, 서비스를 어느정도까지 커버할지, 어느 정도까지 확장할지에 대한 미래 계획까지도 포함을 해서 결정해야 하는 무척 어려운 일이였다. 하지만, 클라우드가 보편화되고, 저렴(솔직히 엄청 저렴하진 않다.)해진 지금 시점에서는 서버스펙 및 서버대수에 대한 고민은 시간낭비가 되어 버렸다. 그렇다고 해도, 적절할 인스턴스 타입을 선택하고, 어떻게 아키텍처를 구성하느냐에 따라서 비용이 천차만별이기 때문에 클라우드 비용절감에 대한 고민은 지속적으로 해야 한다. 또한 좀더 저렴함 새로운 타입의 인스턴스가 지속적으로 출시되기 때문에 클라우드의 새로운 소식도 놓치지 말고 찾아보아야 한다. 클라우드 덕에 편하게 서버를 증설할수 있지만, 서버 하나하나가 모두 비용이기 때문에 데이터를 좀더 효율적으로 수집하고 처리할수 있는 방법에 대한 고민은 계속되고 있으며, 그에 대한 대안이 엣지 컴퓨팅이라는 개념이라고 생각한다. 엣지 컴퓨팅이란 쉽게 얘기해서 IoT디바이스 자체에서 클라이언트에 제공할수 있는 데이터는 자체적으로 처리하고, 꼭 필요한 데이터만 중앙서버(클라우드)에 전송하는 개념이다. 이 개념은 필요한 데이터만 전송하기 때문에 중앙 저장소에 부담이 적을뿐만 아니라 네트워크를 효율적으로 사용할수 있고, 클라이언트 입장에서는 엣지 컴퓨팅에서 바로 데이터 처리 내용을 받아 볼수 있어, 기존보다 빠르게 응답 받을수 있다. 단순히 데이터만 전송하는 IoT디바이스의 경우는 데이터를 서버로 전송할때 불필요한 데이터를 필터링할수 있는 기능만 추가하더라고, 좀더 빠르게 데이터를 수집하고, 클라우드 컴퓨팅 리소스 또한 절약할수 있다.    

#### 5. IoT 에서의 네트워크 엔진
##### 5.1. 실시간 데이터 처리
- 데이터의 수집시, 대량의 데이터 또는 초속 데이터에 따라 아키텍처의 구성을 다양하게 할수 있다. 무조건 저장만하고 추후에 데이터 분석에 이용한다거나, 데이터 스트림 과정중에 필요한 데이터만 캡쳐해서 저장하고 분석한다거나 할수도 있다. 여러가지 비즈니스 니즈와 다양한 솔루션 기능에 따라 데이터 구성 방법이나 데이터 저장 방법이 달라지겠지만, 실시간(100~500ms) 데이터를 가지고, 실시간으로 데이터 분석하여 어떠한 결과를 도출하는 솔루션의 경우는 데이터 수집 및 처리에 대해서 좀더 신중하게 그리고, 깊이 고민할 필요가 있다. 데이터가 들어오는 초기 진입시부터 최종 분석이나 결과를 도출하는 과정까지 처리되는 시간이 실시간으로 클라이언트에게 응답해줘야 하는 비즈니스 니즈의 상황에서 처리 파이프라인 사이사이에 애플리케이션이 추가되면 될수록 늘어남에 따라 실시간 응답이라는 취지를 잃게 되는 문제점이 생길수 있다. 안정적인 데이터 처리가 중요한 포인트라면 적절하게 파이프라인을 구축하는 방법은 최근 많이 알려진 빅데이터 기술을 사용하면 충분 처리가 가능하다. 하지만, 대량이면서 초고속 데이터를 실시간으로 빠르게 처리하고, 빠르게 응답해 주는 일은 그리 녹녹하지 않다. 솔루션 니즈에 100% 맞는 것이 없다면, 기존에 나와 있는 빅데이터 기술이나 데이터 스트림 처리가 가능한 다양한 애플리케이션에만 집중하지 말고, 데이터 처리를 위해 개발한 애플리케이션의 아키텍처를 수정함으로써 실시간 처리에 대한 문제를 해결해야 한다.

##### 5.2. 동기, 비동기에 대한 고려
- 데이터 수집, 처리 및 분석시 데이터베이스는 기본적으로 사용하는 스토리지이다. 비용이나 속도문제를 해결하기 위해 레디스나 몽고디비와 같은 오픈소스 메모리 데이터베이스를 많이 사용한다. 하지만 메모리 데이터베이스를 사용했다고 해서 실시간 데이터 처리가 잘될거라는 생각은 오산이다. 데이터를 저장하는 과정은 같은 하드웨어가 아닌 다른 하드웨어 설치된 메모리 데이터베이스의 경우 네트워크를 타고 넘어가야 하는 문제로 성능이 떨어지거나 또는 디스크 IO 로 인한 성능이 떨어지게 된다. 데이터 저장시 동기 처리인지 비동기 처리인지에 따라서 데이터를 처리하는 애플리케이션 전체 성능이 결정된다. 당연히 비동기 처리에 대한 고민을 해야 하며, 애플리케이션 또는 데이터베이스가 비동기처리를 지원하는지 확인해 봐야 한다. 비동기 처리가 안되는 경우 비동기 처리가 되도록 리팩토링하거나, 프레임워크를 바꾸거나 최악의 상황에서는 개발언어를 변경해서 재구축해야 하는것도 고려해야 한다. 비동기 처리는 동기 처리보다 몇십배 좋은 성능을 보여준다는것은 이미 검증된 상황이기 때문이다.

> 본 내용은 작성자 개인적인 의견입니다. 다른 의견이 있으시면, 피드백 환영합니다.