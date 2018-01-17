---
layout: post
title: Axon framework reference guide 번역 중.(on going)
date: 2017-12-30 09:36:55
tags: [Java, Axon, Axon-framework, CQRS]
categories: [Java, Axon, CQRS]
---
* TOC
{:toc}


# Axon framework
## Introduction
Axon은 규모 가변성과 확장성에 대한 문제를 아키텍쳐적으로 직접 해결하여 규모 가변적이고 확장 가능한 애플리케이션을 구축을 도와주는 가벼운 프레임워크입니다. 이 문서는 Axon이 무엇인지와 어떻게 사용하는지 그리고 Axon이 어떻게 개발을 도와 줄 수 있는지를 설명할 것입니다.

Axon과 Axon이 개발되게 된 이야기를 알고 싶으시다면, [Axon Framework Background](Axon Framework Background)를 읽어 보시면 됩니다. 지금 당장 Axon을 사용해서 자신만의 애플리케이션을 만들어 보고 싶으시다면, [시작하기](Getting Started)를 바로 읽어 보시면 됩니다. Axon Framework를 만들어 가는데 도움을 주시고 싶으시다면, [기부하기](Contributing)에서 필요한 정보를 얻으 실 수 있을 겁니다. 마지막으로 이번 장은 몇가지 법적인 문제를 [라이센스](License)부분에서 다루게 됩니다.

## Axon Framework Background, Axon Framework을 개발하게 된 이야기

### A brief history, Axon Framework의 간략한 역사
시간이 흐르면서, 소프트웨어 프로젝트에 대한 요구는 빠르게 증가하였습니다. 회사들은 그들의 사업의 성장과 함께 (웹 기반의)애플리케이션 또한 발전 시키길 원합니다. 이것이 의미하는 바는, 프로젝트와 코드 베이스들만 복잡해지는 것이 아니라 애플리케이션의 기능 또한 끊임 없이 추가되고 수정되며 삭제 됩니다.  겉으로 보기에 구현하기 쉬운 기능을 개발하는데 개발팀이 전체 애플리케이션을 분해해야 한다면 이는 짜증이 나는 상황이 될 수 있습니다. 게다가, 오늘날의 웹 기반의 애플리케이션들은 수백만의 잠재적인 고객들을 대상으로 하기때문에 애플리케이션을 규모 가변적으로 만드는 것은 반드시 필요한 사항입니다.

규모가변적인 이슈들을 해결해 주는 GigaSpaces 그리고 Terra-cotta와 같은 애플리케이션과 프레임워크는 이미 많이 있습니다. 하지만 그들은 한가지 근본적인 결함을 공통적으로 가지고 있습니다. 이들 기술들은 그들이 예전부터 사용해 왔던 계층 구조의 아키텍쳐를 사용한 애플리케이션을 개발자들에게 개발하도록 하여 규모가변성의 문제를 해결하려고 한다는 것입니다. 몇몇 경우에서, 그들은 모든 도메인 로직을 서비스 객체들에게 넣으라고 강요하면서, 실(real) 도메인 모델의 사용을 엄격하게 제한하거나 심지어 막기도 합니다. 비록 그런 방법이 애플리케이션을 만드는 빠른 시작점이더라도, 결국에는 복잡성을 증가 시키고 개발 자체를 느리게 만들어 버립니다.

명령과 조회의 책임 분리(Command Query Responsibility Segregation, 줄여서 CQRS) 패턴은 애플리케이션을 설계하는 방식 자체를 바꾸어 위에서 언급되었던 문제들을 해결합니다. 로직을 분리된 계층으로 분리하는 대신, 로직을 애플리케이션의 상태를 변경하거나 조회를 하는지에 따라서 분리합니다. 다시 말하면, 명령(command)-잠재적으로 애플리케이션의 상태를 변경 시킬 수 있는 행동(action)-들은 애플리케이션의 상태를 조회하는 콤포넌트(component)와는 다른 콤포넌트에서 실행이 됩니다. 이렇게 명령과 조회를 분리하는 가장 큰 이유는 명령과 조회 각각에 대한 기술적 및 비 기술적인 요구사항들이 존재 한다는 것입니다. 명령들이 실행될때, 조회 콤포넌트는 이벤트를 사용하여 (비)동기적으로 갱신이 됩니다. 이렇게 이벤트를 통해 갱신하는 구조는 애플리케이션의 아키텍쳐를 확장성 있고, 규모 가변적이며 궁극적으로 보다 더 유지 보수성을 높여 줍니다.

> **노트**

> CQRS에 대한 상세 설명은 이 문서의 범위에 벗어 납니다. CQRS에 대해 보다 많은 내용을 알고 싶으신 경우, Axon Framework의 웹 싸이트 [www.axonframework.org](www.axonframework.org)를 방문하여 주세요. 해당 웹 싸이트에 CQRS에 대한 정보를 얻을 수 있는 링크들이 있습니다.

CQRS는 오늘날의 소프트웨어 환경을 지배하는 계층화 된 아키텍처와 근본적으로 다릅니다. 개발자들이 자신들의 애플리케이션에 적용할 이 아키텍쳐를 둘러보면서 몇가지 함정에 빠지는 것은 흔한 일입니다.
 이것이 Axon Framework가 생각한 이유입니다. 개발자가 비즈니스 로직에 중점을 두면서 CQRS 응용 프로그램을 구현할 수 있도록 도와줍니다.


### What is Axon?, Axon 이란?
Axon Framework은 CQRS 아키텍쳐 패턴을 개발자가 적용할 수 있도록 지원하여 규모 가변적이고 확장성이 있으며 유지 보수가 가능한 애플리케이션의 구축을 도와 줍니다.

Axon Framework은 aggregate, repository 그리고 이벤트 버스(이벤트를 전파하는 구조)등과 같은 핵심이 되는 빌딩 블럭(building block)들을의 구현체를 제공합니다. 게다가 Axon의 특정 로직에 묶여야 하는 코드 작성 없이 aggregate들과 이벤트 리스너를 개발할 수 있는 애노테이션(annotation)들을 Axon에서 제공합니다. 따라서 개발자는 비지니스 로직에 보다 초점을 맞춰서 개발할 수 있고, 독립된 환경에서 테스트를 쉽게 수행할 수 있습니다.

Axon은 CQRS 아키텍쳐 혹은 관련 콤포넌트들을 숨기려 하거나 하지 않습니다. 따라서 CQRS에 대하 세세히 알고 있는 개발자가 팀안에 한명 혹은 그 이상 있는 것이 좋습니다.

Axon을 사용하면, 올바른 이벤트 리스너에게 이벤트를 전달하고 전달된 이벤트들을 정확한 순서로 동시에 처리하도록 할 수 있습니다. 다중 쓰레드와 관련된 고려 사항들은 일반적으로 다루기 어렵고 버그가 발생해도 추적하기 어려습니다. 심지어 때때로 애플리케이션의 오작동을 일으 킬 수 도 있습니다.  그렇지만 Axon framework 내의 코드들은 다중 쓰레드와 관련된 유형의 버그들이 발생하지 않도록 꼼꼼히 검증이 되어 있습니다.

Axon Framework은 다수의 모듈(jar)로 구성이 되어 있습니다. 각 모듈은 규모 가변적 기반을 구축하기 위한 콤포넌트와 도구들을 제공합니다. Axon Core 모듈은 각기 다른 콤포넌트들을 위한 기본적인 API들과 단일 JVM 애플리케이션에 적합한 솔루션을 제공하는 간단한 구현체들을 제공합니다. 다른 모듈들은 규모 가변성과 고 성능 관련 이슈들을 다루기 위해 특화된 빌딩 블럭들을 제공합니다.


### When to use Axon?, 언제 Axon을 사용해야 할까?
모든 애플리케이션 개발에 Axon이 적합한 것은 아닙니다. 단순히 생성,조회, 수정 및 삭제 기능(CRUD*)이 주를 이루며 규모를 가변적으로 늘이고 줄이지 않는 애플리케이션 개발에는 CQRS 혹은 Axon을 사용함으로써 얻는 이득이 없습니다. 그렇지만 다양항 범위의 애플리케이션들의 개발에 Axon을 사용할 수 있습니다.

> CRUD는 특정 데이터에 대한 생성(Create), 조회(Read), 수정(Update) 그리고 삭제(Delete) 작업의 줄임말 입니다.


CQRS와 Axon을 사용하기 좋은 애플리케이션들은 다음의 특징들 중 하나 혹은 그 이상의 특징을 가집니다.

* 오랜 기간동안에 걸쳐 새로운 기능을 추가하여 확장될 가능성이 있는 애플리케이션. 예를 들어, 온라인 스토어는 주문 상태를 추적하는 시스템으로 시작할 수 있습니다. 그런 후에, 제품이 판매될때 마다 제품의 재고량을 갱신하기 위해 재고 정보를 관리하는 시스템을 추가하여 확장할 수 있습니다. 또한 제품 판매에 대한 재무적 통계치를 기록하는 회계 시스템이 필요할 수 있습니다. 소프트웨어 프로젝트가 앞으로 어떻게 발전해 나갈 것인지를 예측하는 것은 힘들지만, 위와 같은 형태로 발전되어 가는 애플리케이션이 분명히 대다수 존재하고 있습니다.

* 쓰기(생성, 수정 그리고 삭제) 대비 조회의 비율이 높은 애플리케이션. 데이터의 쓰기 작업은 많이 이루어 지지 않지만, 그에 비해 조회 작업이 많은 이루어 지는 애플리케이션들이 해당이 됩니다. 조회 대상이 되는 데이터를 포함한 데이터 저장소는 쓰기 명령에 필요한 데이터 저장소와는 다르기 때문이며, 쓰기에 필요한 데이터 저장소에 영향을 주지 않고 조회 데이터 저장소에 조회를 위한 최적화 작업을 수행할 수 있습니다. 이벤트가 발생될때마다 데이터는 갱신되기 때문에, 중복된 데이터는 더이상 문제가 되지 않습니다.

* 데이터를 다양항 형태로 제공하는 애플리케이션. 요즘에 많은 애플리케이션들은 단순히 웹 페이지에 정보를 표시하는데 그치지 않습니다. 예를 들어, 일부 애플리케이션들은 사용자와 관련된 변경 사항들을 사용자에게 알려주기 위해 월별로 사용자에게 이메일을 보냅니다. 검색 엔진이 또 다른 예가 될 수 있습니다. 검색엔진은 동일한 데이터를 사용할 수 있지만, 빠른 검색을 위해 최적화된 방법을 사용합니다. 보고서 도구들은 특정 기간에 걸친 데이터의 변화 추이를 보여주기 위해 정보들을 집계하여 보고서를 생성합니다. 이렇듯 동일한 데이터를 사용하지만 다른 형태로 표시하는 애플리케이션들이 존재하게 됩니다. Axon을 사용하여, 각각의 데이터 저장소들을 실시간 혹은 계획에 맞춰서 독립적으로 갱신할 수 있습니다.

* 다른 목적을 가진 대상별로 분리된 콤포넌트를 가지는 애플리케이션. 온라인 스토어로 예를 들어 보면, 고객들은 제품을 주문하고 주문 상태를 주로 조회하는 반면, 직원들은 제품의 정보와 사용 가능한 제고량을 주로 갱신 할 것입니다. Axon을 사용하면, 직원과 고객의 요구에 맞는 기능을 제공하는 콤포넌트들을 각자의 분리된 서버로 배포할 수 있고 각기 다른 규모 가변 정책(scale policy)을 설정할 수 있습니다. 또한 배포된 서버에 상관없이 각각의 콤포넌트가 원하는 이벤트가 발생하게 되면 Axon은 해당 이벤트를 전달하여 각 콤포넌트들은 최신의 데이터를 유지할 수 있습니다.

* 기존의 애플리케이션과 다른 애플리케이션을 통합하는 것은 까다로운 작업입니다. 이런 경우에도 명령과 이벤트를 사용하여 엄격히 정의된 애플리케이션의 API가 있다면, 다른 외부 애플리케이션과의 통합을 보다 쉽게 할 수 있습니다. 어떤 애플리케이션이라도 명령을 보내고 기존의 애플리케이션에서 생성된 이벤트를 수신할 수 있기 때문입니다.


## Getting started, 시작하기
Axon framework을 가지고 시작하기 위해선, Axon 라이브러리 파일들을 Axon 웹 사이트에서 직접 내려받거나 Maven, Gradle과 같은 빌드 시스템 설정을 통해 Axon 라이브러리 파일들을 받을 수 있습니다.

### Download Axon, Axon 다운로드하기
[axonframework.org/download](http://www.axonframework.org/download/)을 통해 직접 Axon framework을 다운 받을 수 있습니다.

현재 버전별로 다운로드를 제공하고 있지만, 최신의 안정된 버전을 다운로드 합니다. 만약 가장 최신을 기능들을 사용해 보고 싶다면, 스냅샵 배포버전을 사용할 수 있습니다. Axon framework 파일과 Axon framework이 필요로 하는 라이브러리 그리고 소스와 문서들을 포함한 전체(full) 패키지를 다운로드 합니다.

현재 개발 진행 중인 Axon을 받고 싶다면, Axon framework의 Git 저장소를 복제(clone)하실 수 있습니다. 다음은 Axon framework의 Git 저장소 주소 입니다.

* git://github.com/AxonFramework/AxonFramework.git
* https://github.com/AxonFramework/AxonFramework

### Maven 설정
빌드 도구로 메이븐(Maven)을 사용하고 있다면, 아래와 같이 의존관계를 추가하여 Axon을 사용할 수 있습니다.

```
<dependency>
    <groupId>org.axonframework</groupId>
    <artifactId>axon-core</artifactId>
    <version>${axon.version}</version>
</dependency>
```

Axon framework이 제공하는 대부분의 기능은 선택 가능하며 추가적인 의존관계(다른 라이브러리)를 필요로 합니다. 불필요한 라이브러리로인해 프로젝트가 복잡해 지는 것을 방지하기 위해 추가적인 의존관계를 자동으로 추가하지 않도록 하였습니다

### Infrastructure requirements, 인프라 요구 사항
Axon framework으로 개발하는데 특별히 요구되는 인프라는 없습니다. Java8을 대상으로 만들어지고 테스트하였기 때문에 Java8은 반드시 필요합니다.

Axon 자체적으로 외부 시스템(예, DBMS)에 대한 연결 혹은 쓰레드를 생성하지 않기 때문에, 보통의 애플리케이션 서버에서 Axon을 실행할 수 있습니다. Axon은 모든 비동기 관련 작업을 ```Executor```를 사용하여 추상화하였습니다. 따라서 컨테이너에 의해서 관리되는 쓰레드 풀(Thread pool)을 쉽게 전달하여 사용할 수 있습니다. Tomcat, Jetty 혹은 오프라인으로도 동작 가능한 stand-alone 애플리케이션과 같은 애플리케이션 서버를 사용하지 않는다면, 쓰레드 풀을 생성하고 설정하기 위해 ```Executors``` 혹은 Spring framework을 사용할 수 있습니다.

### When you're stuck, 도움이 필요할때.
애플리케이션을 개발하면서, 왜 발생 했는지 잘 모르는 문제에 부딪치거나 스스로 답을 찾기 어려운 궁금증이 생길 수 있습니다. Axon 사용자 메일링 리스트를 통해 도움을 받을 수 있습니다. <a href="mailto:axonframework@googlegroups.com">axonframework@googlegroups.com</a>으로 메일을 보내 주시면 다른 사용자들이나 Axon framework을 만들어 가는 분들이 문제 해결에 도움을 드릴 수 있습니다.

Axon framework에 대한 버그를 발견하셨다면, [issues.axonframework.org.](issues.axonframework.org)으로 발견하신 버그를 알려주세요. 그리고 버그를 알려주실 때, 발견한 버그에 대해 실제 결과와 기대 했던 결과와 함께 충분한 설명을 달아 주셔야 합니다. 가능하다면, 버그가 발생하는 것을 확인할 수 있는 간단한 단위 테스트 코드를 함께 보내주세요. 위와 같이 해주시다면 문제 해결에 많은 도움이 될 수 있습니다.

## Contributing to Axon Framework, Axon Framework에 기여하기
Axon Framework은 현재 개발 중에 있습니다. 규모 가변적이고 확장 가능한 애플리케이션을 보다 쉽게 만들 수 있도록 하기 위해 Axon Framework에 새로운 기능들을 추가할 것입니다. 그렇기때문에 여러분의 도움을 기다리고 있습니다.

다음과 같은 방법을 통해 도움을 주실 수 있습니다.
* [issues.axonframework.org.](issues.axonframework.org)를 통해 발견한 버그 혹은 새로운 기능 요청 그리고 개선점등을 보내 주세요. 어떤 내용이라도 보내 주시면 됩니다. 단 버그를 발견하여 알려 주실 때는 최대한 자세한 내용을 알려 주셔야, 저희가 빨리 동일한 버그를 재현하고 처리하는데 많은 도움이 됩니다.
* 애플리케이션 개발 중, Axon framework에 도움이 될 것 같은 콤포넌트를 개발하셨다면, 저희에게 소스 파일을 포함한 패치 혹은 압축 파일을 보내 주세요. 보내 주신 코드 내용을 검토하여 Axon framework에 반영하도록 노력하겠습니다. 소스 코드를 보내 주실땐, javadoc을 사용하여 코드에 대한 문서를 만들어 주세요. 저희가 소스 코드를 이해하는데 많은 도움이 됩니다.
* 위의 방법이외에도 저희에게 도움이 될 다른 방법이 있다면 주저하지 마시고 <a href="mailto:axonframework@googlegroups.com">Axon Framework mailing list.</a>으로 메세지를 보내 주세요.

## Commercial Support, 유료 지원
Axon Framework은 오픈 소스이며 누구든지 사용 가능합니다. 단 특정 목적이나 문제가 발생하여 즉각적인 도움을 원하신다면, AxonIQ를 통해 유료 지원을 받을 수 있습니다. 해당 서비스는 Axon에 대해 상당한 경험과 지식을 가지고 있는 분들로 부터 교육, 자문 긜고 운영 지원을 받을 수 있습니다.

AxonIQ를에 대한 상세 내용을 원하시면, [axoniq.io](https://axoniq.io/) 혹은  [axoniq.io/services](https://axoniq.io/services)를 방문해 주세요.

## License information, 라이센스 정보
Axon Framework과 관련 문서들은 "**Apache License, Version 2.0.**"로 배포되고 있습니다. 라이센스에 대한 복사본은 [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)을 통해 확인 할 수 있습니다.

관련법에 의해 요구되거나 서면으로 동의하지 않는 한, 라이센스에 따라 배포 된 소프트웨어는 명시 적 또는 묵시적으로 어떠한 종류의 보증이나 조건없이 "있는 그대로"의 상태로 배포됩니다. 라이선스에 따른 사용 권한 및 제한 사항을 설명하는 특정 언어에 대한 [라이센스](http://www.apache.org/licenses/LICENSE-2.0)를 참조하십시오.

## Architectural Overview,
CQRS는 자체만 보면 정말 간단한 패턴입니다. CQRS는 명령을 처리하기 위한 애플리케이션 콤포넌트와 조회를 위한 콤포넌트를 분리하도록 규정합니다. 명령 및 조회 콤포넌트를 분리하는 것은 그 자체로만 보면 단순해 보이지만, 다른 패턴들과 결합하게 되면 여러가지 강력한 기능을 제공합니다. Axon은 CQRS와 조합하여 사용할 수 있는 다른 패턴들을 쉽게 구현할 수 있는 빌딩 블럭(building block)들을 제공합니다.

아래의 다이어그램은 CQRS 기반의 이벤트 기반 아키텍쳐(event driven architecture)의 확장된 레이아웃입니다. 왼쪽에 표시된 UI(User Interface) 콤포넌트는 두 방향으로 애플리케이션의 다른 부분과 연동합니다. UI 콤포넌트는 애플리케이션으로 명령을 요청하고 다른 방향으로는 특정 정보를 얻기 위해 애플리케이션으로 조회 요청을 합니다. (조회 요청은 다이어그램의 하단을 보면 됩니다.)

![](/assets/axon/extended_layout_of_cqrs_eda.png)

명령들은 일반적으로 간단명료한 객체들로 표현이 되며, 명령을 표현하는 객체들은 명령 처리자(command handler)가 명령을 처리하기 위해 필요한 모든 데이터들을 포함합니다. 명령의 이름만으로도 그 의도를 파악 할 수 있어야 합니다. 즉, 명령 객체를 표현하기 위한 클래스의 이름과 명령 객체의 속성(field)명을 통해 의도와 명령을 처리하기 위해 필요한 데이터가 무엇 인지를 파악할 수 있어야 합니다.

커맨드 버스(Command Bus)는 명령들을 수신하고 명령을 처리할 수 있는 명령 처리자(command handler)에게 명령을 분배 합니다. 개별 명령 처리자는 자신이 처리할 수 있는 특정 유형의 명령에만 응답하고 명령이 포함하고 있는 데이터를 가지고 선언된 로직을 실행합니다. 하지만 몇몇 경우에, 명령의 실제 유형에 상관없이 실행해야 하는 유효성 검사, 로깅(logging) 혹은 권한 확인과 같은 로직들이 있을 수 있습니다.

명령 처리자는 도메인 객체들(Aggregates)을 저장소에서 가져와 객체의 상태를 변경하기 위한 메서드를 실행(호출)합니다. 이런 집계 객체(aggregates)들은 일반적으로 실제 비지니스 로직을 포함하고 있으므로 자신들의 불변성을 보호하는 책임집니다. 즉, 특정 객체의 상태를 변경은 명령 처리자를 통해 수행되는 것처럼 보이지만 실제로는 객체 자신이 자신의 상태에 대한 관리 책임을 가지고 있습니다. 집계 객체의 상태 변경의 결과로 도메인 이벤트들이 발생이 됩니다. 도메인 이벤트와 집계 객체 둘 모두 도메인 모델을 구성하는 구성 요소들입니다.

저장소(repository)를 통해 집계 객체에 접근할 수 있으며, 일반적으로 저장소는 오로지 식별자만을 통해 집계 객체를 찾아 반환하도록 최적화 되어 있습니다.

일부 저장소는 예를 들어 객체 관계형 매핑을 사용하여 집계 객체 자체의 상태를 저장하는 반면, 다른 저장소들은 집계 객체가 이벤트 스토어(event store)를 통해 적용한 상태의 변경을 저장합니다. 또한 저장소는 집계 객체의 변경들을 백업 스토리지(storage)에 저장해야 하는 책임을 집니다.

Axon은 예를 들어 객체 관계형 매핑을 사용해 집계 객체를 직접 저장하는 방법과 이벤트 소싱(sourcing)에 대한 지원 제공합니다.

이벤트 버스(bus)는 시스템에 등록되어 특정 이벤트 수신을 원하는 이벤트 리스너(listener)로 이벤트를 전달합니다. 이벤트 전달은 동기 혹은 비동기 방식으로 이루어 집니다. 비 동기 이벤트 전달은 백그라운드(background)에서 이벤트가 전달되고 처리되는 도중에, 명령의 실행 결과를 반환하고 사용자가 다른 제어를 할 수 있도록 합니다. 이벤트 처리가 완료되기를 기다리지 않기때문에 애플리케이션의 반응성은 더 좋아지게 됩니다. 반면, 동기적인 이벤트 처리는 비교적 단순하고 기본적으로 설정되어 있는 처리 방법입니다. 기본적으로 동기 처리는 명령 처리와 이벤트 리스너를 처리를 동일한 트랜잭션(transaction)내에서 처리합니다.

이벤트 리스너는 이벤트를 수신하고 수신한 이벤트를 처리합니다. 일부 처리자(handler)들은 조회를 위한 데이터 소스(source)를 갱신하는 반면, 다른 처리자들은 외부 시스템으로 메세지를 전송합니다. 알고 있을 수 있겠지만, 명령 처리자들은 명령 처리자가 변경한 내용을 처리하는 콤포넌트들을 전혀 신경쓰지 않습니다. 이것이 의미하는 바는, 명령 처리자와 이벤트 리스너는 서로 의존 관계가 없는 독립적인 객체들입니다. 따라서 애플리케이션에 새로운 기능을 추가하여 확장하기 좋은 구조입니다. 단지 새로운 이벤트 리스너를 추가하면 됩니다. 이벤트는 애플리케이션의 모든 콤포넌트들을 약한 의존관계로 만들어 버립니다.

몇몇 상황에서, 이벤트를 처리하는 과정에서 새로운 명령을 보내야 하는 경우가 있습니다. 예를 들어, 새로운 주문이 들어왔을때, 고객계좌에서 고객이 구매한 제품의 가격만큼을 차감해야 하고 고객이 구매한 제품의 배송을 준비해야 합니다. 많은 애플리케이션에서 이보다 더 복잡한 로직을 구현해야 하는 경우들이 있습니다. 만약 고객이 제 시간에 결제를 하지 않는 다면, 배송을 해야 하는지 아니면 결제되기를 기다려야 하는지등을 고려해야 합니다. 이런 복잡한 비지니스 트랜잭션을 처리하기 위해 사가(sage)라는 CQRS의 개념이 있습니다.

Axon 3.1 이후로, 조회 쿼리를 처리하기 위한 콤포넌트를 framework에서 제공합니다. 쿼리 버스(query bus)는 조회요청을 받아 해당 조회(쿼리)를 처리할 쿼리 처리자(handler)에게 분배합니다. 쿼리 처리자를 사용하기 위해선 쿼리 처리자를 자신이 처리할 쿼리 유형과 쿼리 실행 후 반환값의 유형과 함께 쿼리 버스에 등록해야 합니다. 쿼리와 결과 유형 모두 읽기 전용의 DTO 객체를 일반적으로 사용합니다. 개별 DTO 객체의 내용(속성)들은 일반적으로 사용자 인터페이스가 필요롤 하는 데이터들에 의해 결정이 됩니다. 대부분의 경우, UI의 특정 뷰(view)에 직접 매핑됩니다 (또한 뷰-테이블(table per view)로 불려지기도 합니다.). 동일한 쿼리 그리고 반환 유형에 대한 다수의 쿼리 처리자를 등록할 수 있습니다. 쿼리를 전달할때, 클라이언트는 단일 쿼리자의 결과를 원하는지 사용 가능한 모든 쿼리 처리자의 결과를 원하는지를 지정할 수 있습니다.

### Axon Module Structure, Axon의 모듈 구조
Axon Framework은 CQRS의 특정 문제 영역에 해당하는 모듈들로 구성이 되어 있습니다. 개발하고자 하는 프로젝트의 필요에 따라 하나 혹은 그 이상의 모듈을 사용하시면 됩니다.

Axon 2.1의 경우, 모든 모듈들은 OSGI에 호환 가능한 번들로 되어 있습니다. 각 모듈은 manifest 파일에 필요한 header들을 포함하고 있고 필요한 모듈과 외부에서 사용 가능한 모듈을 선언해 놓았습니다. 현 시점에선, Slf4j 번들(1.7.0 <= version < 2.0.0)만이 필요합니다. 개발에 필요한 모듈이라 하더라도, 다른 모든 필요한 외부 모듈은 선택적 사항으로 표기 되어 있습니다.

#### Main Modules, 주(main) 모듈들
Axon의 주(main) 모듈들은 철저한 테스트를 거쳤으며 운영 환경에서도 충분히 사용 가능합니다.  모든 모듈의 메이븐(maven) groupId는 **axon.framework** 입니다.

핵심 모듈에는 이름에서 알 수 있듯이 Axon의 핵심 콤포넌트들이 포함되어 있습니다. 만약 단일 노드로 사용할 경우, 필요한 모둘 콤포넌트들을 핵심모듈에서 찾을 수 있습니다. 다른 모든 Axon 모듈들은 핵심 모듈을 필요로 하기때문에, 핵심 모듈은 반드시 클래스 패스(classpath)* 에 있어야 합니다.

> 클래스 패스(classpath): JRE(Java Runtime Environment)이 애플리케이션 실행 시(실행환경), 필요한 클래스들(혹은 필요한 리소스)을 찾는 위치를 의미합니다.

테스트 모듈은 명령 처리자(command handler), 집계 객체(aggregate) 그리고 사가(saga)들과 같은 Axon 기반의 콤포넌트를 테스트 하기위한 테스트 픽스쳐(fixture)를 포함하고 있습니다. 보통 애플리케이션을 실행할때는 테스트 모듈이 필요하지 않고 테스트를 실행하는 경우에만 필요합니다. 따라서 메이븐을 사용할 경우, scope을 test로 설정하여 사용하면 됩니다.

분산 명령버스(Distributed CommandBus) 모듈은 다중 노드 환경* 에서 명령을 분산하기 위한 구현체들을 포함하고 있습니다. 분산 명령버스(Distributed CommandBus) 모듈에는 다중 노드들을 연결하기 위한 JGroups와 Spring Cloud Connectors가 함께 제공됩니다.

> 다중 노드 환경: 각 노드는 애플리케이션을 실행 할 수 있는 환경이 마련되어 있어야 하며, 동일 레벨의 다른 노드에 종속적이지 않은 상태에서 애플리케이션을 실행 할 수 있어야 합니다. 이런 노드들이 하나 이상 존재하는 환경을 의미 한니다.

AMQP 모듈은 AMQP 기반의 메세지 브로커(broker)를 분산 메커니즘으로 사용하여 이벤트 버스(EventBus)를 구축할 수 있는 콤포넌트들을 제공합니다. AMQP 모듈을 사용하여 이벤트 버스를 구축하면, 이벤트 처리자(handler)가 일시적으로 사용할 수 없더라도, 나중에 이벤트 처리자가 사용 가능한 상태로 복구되면 메세지가 잘 전달될 수 있도록 할 수 있습니다.

스프핑(Spring) 모듈을 사용해서 Axon 콤포넌트들을 스프링 애플리케이션 컨텍스트에 빈(Bean)으로 설정할 수 있습니다. 또한 스프링 메세지 채널을 통해 Axon 이벤트들을 주고 받을 수 있도록 하는 어댑터(adaptor)와 같은 스프링 프레임워크에서 사용 가능한 구현체들을 제공합니다.

몽고(Mongo)DB는 NoSQL 데이터베이스를 기반으로한 도큐먼트로, 몽고 모듈에서 제공하는 이벤트 스트림(stream)과 사가들을 저장하기 위한 이벤트 스토어와 사가 스토어의 구현체들을 사용하여 몽고DB와 연동할 수 있습니다.

여러 Axon Framework 콤포넌트들을 모니터링 정보를 제공합니다. 매트릭스(metrics) 모듈은 모니터링 정보를 수집하는 CodeHale에 기반한 구현체들을 제공합니다.

### Working with Axon APIs, Axon API 사용하기
CQRS는 아키텍쳐적인 패턴으로, 모든 프로젝트에 적용 가능할 수 없습니다. Axon Framework은 하나의 해결책을 제공하는 것이 아닌, 모범 사례를 따르는 구현체와 구현해야 하는 요구 사항에 맞는 구현체들을 상황에 맞도록 조정하는 방법을 제공합니다.

거의 대부분의 인프라로 제공되는 빌딩 블럭들은 인터셉터, 리졸버와 같은 후크 포인트(hook point)\* 를 제공합니다. 후크 포인트를 사용하여 애플리케이션의 특정 행위를 추가할 수 있습니다. 대부분의 경우, Axon이 제공하는 후크 포인트에 대한 구현체들을 사용하여 애플리케이션의 특정 행위를 구현할 수 있습니다. 필요에 따라, 직접 구현하여 사용할 수도 있습니다.

> 용어 후킹은 운영 체제, 응용 프로그램 또는 다른 소프트웨어 구성 요소의 동작을 소프트웨어 구성 요소간에 전달되는 함수 호출이나 메시지 또는 이벤트를 가로 채어 변경하거나 보강하는 데 사용되는 다양한 기술을 망라합니다. 이러한 인터셉트 된 함수 호출, 이벤트 또는 메시지를 처리하는 코드를 후크 (hook)라고합니다. ( \*출처: [위키피디아](https://en.wikipedia.org/wiki/Hooking))

메세지와 같은 비-인프라 객체들은 일반적으로 불변객체입니다. 불변성을 통해 다중 쓰레드 환경에서 부작용없이 안전하게 해당 객체들을 사용할 수 있습니다.

최대한 사용자의 확장성을 보장하기 위해, Axon의 모든 콤포넌트들은 인터페이스들을 사용하여 정의되어 있습니다. 추상 클래스와 일반 구현체 클래스들은 개발을 돕기 위해 제공되지만, Axon Framework는 구체적인 구현체에 종속적이지 않습니다. 따라서 Axon에서 제공하는 인터페이스를 사용하여 직접 구현한 빌딩 블럭들을 언제든지 사용할 수 있습니다.

### Spring Support, 스프링 지원
Axon Framework은 스프링을 광범위하게 지원하지만 Axon을 사용하기 위해 스프링을 사용할 필요는 없습니다. 모든 콤포넌트들은 스프링없이도 프로그램 코드로 설정이 가능합니다. 하지만 스프링을 사용한다면, 스프링에서 제공하는 애노테이션(annotation)을 사용해서 보다 쉽게 많은 부분의 설정을 할 수 있습니다.

## Messaging concepts
메세징(Messaging)은 Axon의 핵심 개념 중 하나로, 콤포넌트간의 모든 연동은 메세지 객체를 통해 이루어 집니다. 메세지를 통해 연동하기때문에, 각 콤포넌트들은 위치 투명성(location transparency)을 가지게 되어 필요에 따라 규모를 가변적으로 만들 수 있고 분산 시킬 수 있습니다.

모든 메세지 객체들은 ```Message``` 인터페이스를 구현하지만, 메세지들의 다양한 유형과 처리 방법은 명확하게 구별이 됩니다.

모든 메세지들은 페이로드(payload), 메타 데이터 그리고 유일한 식별자값을 포함합니다. 메세지의 페이로드는 메세지가 의미하는 것에 대한 기능적 설명입니다. 객체의 클래스명과 객체가 포함한 데이터의 조합은 메세지가 애플리케이션내에서 가지는 의미를 설명합니다. 메타 데이터를 통해 전송된 메세지의 컨텍스트를 알 수 있습니다. 예를 들어, 메세지의 발생지 혹은 원인등을 추적하여 저장할 수 있습니다. 명령(command)이 실행되는 환경의 보안 컨텍스트를 저장할 수 있습니다.

> Note
> 모든 메세지는 불변 객체이기때문에, 메세지에 데이터를 저장하는 것은 기존 메세지를 기반으로 새로운 메세지를 생성하는 것을 말합니다. 메세지의 불변성으로인해 다중 쓰레드 및 분산 환경하에서 메세지를 안전하게 사용할 수 있습니다.

### Commands (명령 객체)
명령(command)는 애플리케이션의 상태를 변경하기 위한 의도를 나타냅니다. 명령 객체는 **CommandMessage** 구현체 중의 하나로 랩핑(wrapping)된 POJO 객체로 구현할 수 있습니다 (보통 읽기 전용으로 구현이 선호 됩니다.).

명령 객체들은 항상 단 하나의 목적지를 가져야 합니다. 명령을 전송하는 측은 어떤 콤포넌트가 해당 명령을 처리지 혹은 명령을 처리할 콤포넌트가 어디에 위치하는지를 상관하지 않는 반면, 명령을 처리한 후의 결과를 필요로 할 수 는 있습니다. 이런 이유로 명령 처리 결과를 반환할 수 있는 커맨드 버스(CommandBus)를 통해 명령 메세지가 전달 되는 것입니다.

### Events (이벤트)
이벤트는 애플리케이션 내에서 발생한 사건을 나타냅니다. 보통 이벤트는 Aggregate으로부터 발생이 됩니다. Aggregate으로부터 중요한 사건이 발생하였다면, 그 발생한 사건은 이벤트를 발생 시킵니다. Axon Framework에선, 모든 객체를 이벤트로 만들 수 있습니다. 단, 모든 이벤트들은 반드시 직력화 가능(serializable) 객체이어야 합니다.

이벤트가 전파될때, Axon은 해당 이벤트를 **Event Message** 로 감싸줍니다. 사용되는 메세지의 실제 유형(타입) 이벤트의 발생지에 따라 다르며, Aggregate으로부터 발생된 경우에는 **Event Message** 의 하위 객체인 **DomainEventMessage** 로 감싸게(wrapped)됩니다. Aggregate으로부터 발생한 이벤트가 아닌 다른 모든 이벤트들은 **Event Message** 로 감싸게 됩니다. 고유 식별자와 같은 일반적인 속성외에 **Event Message** 는 타임스탬프(timestamp) 또한 포함합니다. **DomainEventMessage** 는 추가적으로 이벤트를 발생시킨 aggregate의 식별자와 유형(타입)을 포함하고 있으며, aggregate의 이벤트 스트림(stream)의 범위 내에서의 이벤트의 순차적인 번호 또한 포함하고 있습니다. 이벤트의 순차적인 번호는 이벤트를 발생한 순서대로 재현하기 위해 사용할 수 있습니다.

> Note
> **DomainEventMessage** 가 Aggregate의 식별자에 대한 참조를 가지고 있다 하더라도, 실제 발생한 이벤트의 식별자 또한 포함해야 합니다.
> 이벤트 스토어(EventStore)가 이벤트를 저장하기 우해 **DomainEventMessage** 의 식별자를 사용하며, 다른 용도로 사용하기에는 적절하지 않을 수 있습니다.

원본 이벤트 객체는 이벤트 메세지의 페이로드(payload)로 저장이 됩니다. 페이로드 다음으로, 감사용 정보 등의 목적으로 사용할 수 있는 비지니스 로직 용도가 아닌 데이터를 저장하기 위해 이벤트 메세지의 메타 데이터를 활용할 수 있습니다. 메타 데이터를 활용하면, 이벤트 프로세싱에 사용된 계정 혹은 장비명등의 이벤트가 발생된 당시의 환경에 대한 정보를 파악할 수 있습니다.

> Note
> 일반적으로, 이벤트 메세지에 포함된 메타 데이터를 가지고 비지니스 결정을 해서는 안됩니다. 비지니스 용도로 특정 데이터를 활용하기 위해선, 메타 데이터가 아닌 이벤트 자체에 해당 데이터를 추가하여 사용해야 합니다. 전형적으로 보고서, 감사 그리고 추적용으로 메타 데이터를 사용하기 때문입니다.

필수 사항은 아니지만, 도메인 이벤트를 불변 객체로 만들어 사용하는 것이 좋습니다. 보통 모든 속성(멤버 변수)를 final로 선언하고 생성자를 통해 속성들을 초기화 하는 방법이 선호됩니다. 이벤트를 생성하는 것이 생각보다 까다롭다면, 빌더 패턴(builder pattern)을 사용하는 것도 고려할 수 있수 있습니다.

> Note
> 원칙적으로 도메인 이벤트들은 상태의 변경을 나타내지만, 변경의 의도 또한 표현해야 합니다. 특정 상태의 변경에 대한 이벤트를 추상 구현체로 만들고 변경의 의도를 나타내기 위한 상세한 하위 구현체를 사용하는 것이 좋은 방법입니다.
> 예를 들어 상태의 변경을 나태내기 위해 추상 구현체로 **AddressChangedEvent** 를 사용하고, 상세 의도를 표현하기 위한 두개의 하위 구현체로 **ContactMovedEvent** 와 **AddressCorrectedEvent** 를 사용할 수 있습니다.
> 몇몇 이벤트 리스터(listener)들은 이벤트의 의도에 별 개의치 않습니다. (예, 데이터 베이스에 값을 갱신하는 이벤트 리스트) 이런 리스너들은 추상 타압(상위 타입)의 객체를 받아 처리하지만, 이벤트의 변경 의도에 맞는 하위 타입의 겍체를 받아 처리하는 리스너들을 사용하여 주소 변경에 대한 이메일 알림을 을 고객에게 발송하도록 할 수 있습니다.

![](/assets//axon/state_change_and_intent_types.png)

이벤트 버스로 이벤트를 전달할때, 해당 이벤트를 이벤트 메세지로 감싸줘야 합니다. **GenericEventMessage** 를 사용하여 해당 이벤트를 이벤트 메세지안에 포함 시킬 수 있으며, 이때 **GenericEventMessage** 의 생성자 혹은 클래스 메서드인 **asEventMessage()** 메서드를 사용할 수 있습니다. **asEventMessage** 메서드는 주어진 이벤트가 이미 **EventMessage** 이면, 바로 이벤트를 반환하고 Message의 하위 객체라면 주어진 이벤트를 **GenericEventMessage** 로 반환 하는데 주어진 이벤트(메세지 하위 객체)의 페이로드와 메타 테이터를 사용 그대로 사용하게 됩니다. 특정 이벤트가 Aggregate으로부터 발생되었다면, Axon은 해당 이벤트를 **DomainEventMessage** 로 감싸주게 되며, **DomainEventMessage** 는 Aggregate의 식별자, 유형(타입) 그리고 일련 번호를 포함하게 됩니다.

### Unit of Work, 작업 단위
Axon Framework에서 **작업 단위(Unit of Work)** 는 중요한 개념이지만, 대부분의 경우 이를 직접적으로 다루는 일은 거의 없을 것입니다. 메세지의 처리는 단일 단위로 처리되는 것으로 보여집니다. 작업 단위의 목적은 명령 혹은 이벤트와 같은 메세지를 처리하는 동안 수행된 작업들을 조정하는 것입니다. 콤포넌트들은 onPrepareCommit 이나 onCleanup과 같은 작업 단위의 개별 단계에서 실행할 작업들을 등록할 수 있습니다.

작업 단위에 직접 접근하여 작업할 일은 없을 것입니다. 작업 단위는 Axon에서 제공하는 빌딩 블록들이 주로 사용을 합니다. 만일 작업 단위를 사용해야 한다면, 몇가지 방법이 있습니다. 처리자(handler)의 handle 메서드에 작업 단위를 추가로 선언하여 처리자가 이를 받아 볼 수 있도록 할 수 있습니다. 애노테이션을 사용한다면, 애노테이션이 사용된 메서드에 ```UnitOfWork``` 타입의 매개변수를 추가할 수 있습니다. 처리자가 아닌 다른 곳에서 작업 단위를 사용하기 위해선, 현재의 쓰레드에 묶여있는(bound) 작업 단위를 ```CurrentUnitOfWork.get()``` 메서드를 통해 받아 올 수 있습니다. ```CurrentUnitOfWork.get()```메서드 사용시 유의점은 현재 쓰레드에 묶여 있는 작업 단위가 없다면 예외가 발생 할 수 있습니다. 따라서 ```CurrentUnitOfWork.get()```메서드 사용전에 ```CurrentUnitOfWork.isStarted()```메서드를 통해 현재 사용 가능한 작업 단위가 있는지를 확인해야 합니다.

메세지의 처리를 수행하는 동안 여러번 재사용할 수 있는 자원을 작업 단위에 첨부하거나, 작업 단위가 완료되었을때, 첨부된 자원을 해제하기 위해 작업 단위에 접근할 필요가 있습니다. 이런 경우 ```unitOfWork.getOrComputeResource()```와 ```onRollback()```, ```afterCommit()``` 그리고 ```onCleanup()```같은 생애 주기 이벤트에 따라 호출되는 메서들을 통해 작업 단위에 자원을 등록하고 작업 단위를 처리하는 동안 실행되어야 할 작업들을 선언할 수 있습니다.

> Note
> 작업 단위(Unit of Work)는 단지 변경을 임시 저장하는 버퍼일뿐입니다. 트랜잭션을 대체하는 것이 아닙니다. 모든 단계적 변경 사항들은 작업 단위가 커밋될때만 커밋이 되지만, 원자적이지는 않습니다. 다시 말하면, 커밋이 실패했을때, 일부 변경은 영속화가 되었을 수 있지만 다른 나머지는 그렇지 않을 수 있습니다. 따라서 하나의 명령에 하나 이상의 작업을 포함 시키지 않는 것을 권장합니다. 이런 권장 사항을 지킨다면, 작업 단위는 단일 작업만을 포함하게 되므로 있는 그대로 안전하게 사용할 수 있습니다. 그렇지만 만일 작업 단위에 하나 이상의 작업을 포함 시켜야 한다면, 작업 단위의 커밋을 하나의 트랜잭션으로 처리하도록 해야 합니다. 작업 단위가 커밋될때, 수행되어야 할 작업은 ```unitOfWork.onCommit(..)```을 사용하여 등록하면 됩니다.

작성한 처리자는 메세지의 처리 결과로 예외를 발생 시킬 수 있습니다. 기본적으로, unchecked 예외들이 발생하면 작업 단위는 모든 변경 사항을 롤백(rollback)처리 합니다. 결과적으로 발생할 수 있는 부작용(side effect)는 취소되어 발생하지 않게 됩니다.

Axon은 아래와 같은 몇가지 롤백 전략을 제공합니다.
* ```RollbackConfigurationType.NEVER```는 항상 작업 단위를 커밋합니다. 롤백을 절대 수행하지 않는 것이죠.
* ```RollbackConfigurationType.ANY_THROWABLE```은 예외의 종류에 상관없이 예외가 발생되면 항상 롤백합니다.
* ```RollbackConfigurationType.UNCHECKED_EXCEPTIONS```은 **Errors**와 **RuntimeException** 의 예외가 발생했을 때, 롤백합니다.
* ```RollbackConfigurationType.RUNTIME_EXCEPTION```은 **RuntimeException** 의 예외가 발생했을 때만 롤백 합니다.

메세지 처리를 위해 Axon 콤포넌트를 사용할때, 작업 단위의 생애주기는 자동으로 관리됩니다. Axon 콤포넌트를 사용하지 않고 직접 구현한 콤포넌트를 사용한다면, 작업 단위의 시작 및 커밋으 직접 코드로 작성해 주어야 합니다.

대부분의 경우, 필요한 기능들은 ```DefaultUnitOfWork```를 통해 사용할 수 있습니다. 단일 쓰레드에서 처리가 이루어 질 것으로 생각됩니다. 작업 단위의 컨텍스트내에서 작업을 수행하려면, ```UnitOfWork.execute(Runnable)```이나 ```UnitOfWork.executeWithResult(Callable)```을 새로운 ```DefaultUnitOfWork```에서 호출하면 됩니다. 해당 작업이 종료될때 작업 단위는 시작되고 종료됩니다. 단 작업이 실패할 경우, 작업 단위는 롤백됩니다. 좀 더 세세한 제어가 필요할 경우, 작업 단위를 수동으로, 직접 시작, 커밋 혹은 롤밸할 수 있습니다.

작업 단위의 일반적인 사용법은 아래와 같습니다.

```
UnitOfWork uow = DefaultUnitOfWork.startAndGet(message);
//아래와 같이 자동 커밋을 사용할 수 있습니다.
uow.executeWithResult(() -> ...처리할 비지니스 로직을 이곳에 작성합니다.);

//혹은 아래와 같이 직접 커밋 혹은 롤백 처리를 할 수 있습니다.
try {
  //처리할 비지니스 로직을 이곳에 작성합니다.
  uow.commit();
} catch(Exception e) {
  uow.rollback(e);
  // 발생한 예외를 다시 던질 수 도 있습니다.
}
```

작업 단위는 활성 단계(active phase), 커밋 단계(commit phase) 그리고 종료 단계(cleanup phase)등과 같은 여러 단계들로 처리되며, 각 단계들이 처리될때마다 작업 단위 리스너(UnitOfWork Listener)에게 통보 합니다.

* 활성 단계(Active phase): 작업 단위가 시작하는 단계로 일반적으로 해당 작업 단위를 ```CurrentUnitOfWork.set(UnitOfWork)```메서드를 통해 현재의 쓰레드에 등록합니다. 그 다음에, 메세지 처리자를 통해 메세지를 처리하도록 합니다.
* 커밋 단계 (Commit phase): 메세지 처리를 완료한 후, 하지만 작업 단위가 커밋되기 전에 ```onPrepareCommit``` 리스너들을 호출 합니다. 만약 작업 단위가 트랜잭션에 물려 있다면, 지원 가능한 트랜잭션을 커밋하기 위해 ```onCommit``` 리스너들을 호출합니다. 성공적으로 커밋을 완료하면, ```afterCommit``` 리스너들을 호출하고 만일 커밋 혹은 이전 단계들이 실패할 경우, ```onRollback``` 리스터들을 호출합니다. 가능하다면, 메세지 처리자가 반환한 결과는 작업 단위의 ```ExecutionResult```에 포함됩니다.
* 종료 단계(Cleanup phase): 작업 단위가 사용했던 락(lock)과 같은 자원들을 해제하기 위한 단계입니다. 만일 여러 작업 단위가 중첩되어 있다면, 가장 바깥의 작업 단위가 종료 준비될때 까지 종료 단계는 지연이 됩니다.

메세지 처리과정은 원자적으로 생각할 수 있습니다. 왜냐면 전체가 처리 완료되거나 그 어떤 것도 처리되지 않기 때문입니다. Axon Framework은 메세지 처리자가 실행하는 작업들을 추적하기 위해 작업 단위(Unit of Work)를 사용합니다.  메세지 처리자가 메세지 처리를 완료한 후, Axon은 작업 단위에 등록되어 있는 작업들을 커밋합니다.

작업 단위에 트랜잭션을 묶을 수 있습니다. 커맨드 버스 구현체들과 비동기 처리를 위한 이벤트 프로세서등과 같은 많은 콤포넌트들을 통해 트랜잭션 매니져를 설정할 수 있습니다. 트랜잭션 매니져를 사용하여 메세지 처리를 위한 작업단위에 묶을 트랜잭션을 생성할 수 있습니다.

애플리케이션 콤포넌트들은 메세지 처리의 각 단계마다 데이터베이스 연결 혹은 엔티티 매니져(EntityManager)와 같은 자원을 필요로 하는데, 이런 자원들을 작업 단위에 첨부하여 콤포넌트들이 사용할 수 있도록 할 수 있습니다. ```unitOfWork.getResources()```메서드를 통해 현재 작업 단위에 첨부되어 있는 자원들을 사용할 수 있습니다. 해당 자원들을 보다 쉽게 사용하기 위한 작업 단위의 여러 헬퍼(Helper) 메서드들을 직접 사용할 수 있습니다.

중첩된 작업 단위에서 자원을 사용하도록 하기 위해선, 최상위 작업 단위에 해당 자원을 첨부하고 중첩 작업 단위에선 ```unitOfWork.root()``` 메서드를 통해 최상위 작업 단위에 접근하여 해당 자원을 사용하도록 하는 것이 좋습니다. 만약 최상위 작업 단위에서 ```unitOfWork.root()``` 메서드를 사용하게 되면, 해당 메서드는 자기 자신을 반환합니다.

## Configuration API, 설정 API
Axon을 통해 비지니스 로직과 인프라관련 설정을 엄격히 분리하여 설정할 수 있습니다. 메세지 처리 시 발생할 수 있는 트랜잭션 처리를 위한 트랜잭션 매니지먼트(management)와 같은 인프라 관련 고려 사항들이 있을 수 있습니다. 이런 고려 사항들은 Axon에서 제공하는 빌딩 블럭을 통해 처리할 수 있습니다. 실제 메세지의 페이로드와 처리자(핸들러)의 내용들은 Axon에 독립적인 Java 클래스로 구현할 수 있습니다.

이런 인프라 관련 콤포넌트들에 대한 설정을 보다 쉽게 만들고 각 기능 콤포넌트간의 관계 정의를 하기 위한 구성 API를 제공합니다.

### Setting up a configuration, 설정 구성하기
아래와 같이 기본 설정 객체를 받아오는 것은 굉장히 쉽습니다.

```
Configuration config = DefaultConfigurer.defaultConfiguration().buildConfiguration();
```
위와 같은 구성을 통해 메세지를 처리하는 구현체를 사용하여 메세지를 전달하는 빌딩 블럭을 사용할 수 있습니다.

위의 구성은 너무 단순하여 그다지 유용하지 못합니다. 이 구성을 유용하게 사용하기 위해선, 명령 모델들과 이벤트 처리자(핸들러)들을 이 구성에 등록해야 합니다.

명령 모델들과 이벤트 처리자(핸들러)들을 이 구성에 등록하기 위해선, ```.defaultConfiguration()```을 통해 반환 받은 ```Configurer``` 인스턴스를 사용해야 합니다.

```
Configurer configurer = DefaultConfigurer.defaultConfiguration();
```
컨피규어러(configurer)객체는 명령 모델 혹은 이벤트 처리자를 등록하기 위한 많은 메서드들을 제공합니다. 보다 더 상세한 등록과 설정 방법은 각 구성 요소를 설명하는 장(chapter)에서 다룹니다.

콤포넌트를 등록하는 일반적인 방법은 아래와 같습니다.

```
Configurer configurer = DefaultConfiguration.defaultConfiguration();
configurer.registerCommandHandler(c -> doCreateComponent());
```

위의 예제 코드의 ```registerCommandHandler``` 호출 부분의 람다 표현식을 주목해서 보세요. 해당 표현식의 매겨변수인 ```c```는 전체 구성을 설명하는 구성 객체입니다. 만일 직접 구현한 콤포넌트에서 기능상의 필요로 다른 콤포넌트들이 필요할 경우, 이 설정 객체를 통해 필요한 객체를 받아 볼 수 있습니다.

예를 들어, 시리얼라이져(serializer)를 필요로하는 명령 처리자(핸들러)를 등록하기 우한 코드는 다음과 같습니다.

```
configurer.registerCommandHandler(c -> new MyCommandHandler(c.serializer));
```

모든 콤포넌트들은 명시적인 접근 메세드를 가지고 있음을 주목해서 보세요. 다음의 예제 코드는 특정 콤포넌트를 설정 객체로부터 받아오는 내용을 보여 줍니다.

```
cofigurer.registerCommandHandler(c -> new MyCommandHandler(c.getComponent(MyOtherComponent.class)));
```
위와 같이 ```MyOtherComponent```를 설정 객체로부터 받아 오기 위해선, ```MyOtherComponent``` 콤포넌트를 설정 객체에 등록할 때 다음과 같이 등록해야 합니다.

```
configurer.registerComponent(componentType, builderFunction)
```
위 코드의 ```builderFunction```은 ```Configuration```객체를 매개변수로 받게 됩니다.

### Setting up a configuration using Spring, 스프링을 사용하여 설정 구성하기
스프링 프레임워크(이하 스프링)를 사용하면, 명시적으로 ```Configurer```를 사용할 필요가 없습니다. 대신, 단지 스프링 ```@Configuration``` 클래스 중 하나에 ```@EnableAxon``` 애노테이션을 붙여주면 됩니다.

Axon은 빌딩 블럭의 특정 구현체들을 위치 시키기 위해 스프링 애플리케이션 컨텍스트를 사용하고 특정 구현체들이 없을 경우 기본 구현 객체들을 제공합니다. 그래서 ```Configurer```를 사용해 빌딩 블럭을 등록하는 대신, 스프링 애플리케이션 컨텍스트에 등록되는 ```@Bean```객체들로 선언하여 사용하면 됩니다.

## Command Model, 명령 모델
CQRS 기반의 애플리케이션에서, 도메인 모델(에릭 에반스와 마틴 파울러에 의해 정의된)은 통해 상태 변경의 검증과 실행에 관련된 복잡성을 극복하기 위한 강력한 메카니즘을 제공합니다. 비록 일반적인 도메인 모델은 많은 수의 빌딩 블럭들을 가지고 있지만, 빌딩 블럭 중 하나인 Aggregate는 굉장히 중요한 역활을 담당합니다.

애플리케이션내에서 상태의 변경은 명령으부터 시작됩니다. 모든 명령은 표현 의도와 그 의도에 따라 행동을 취하기 위해 필요한 정보의 조합입니다. 명령 모델은 들어오는 명령을 처리하기 위해 사용되며, 수신한 명령을 검증하고 반환 값을 정의 합니다. 이 모델을 사용하면, 명령 처리자는 특정 유형의 명령을 처리해야할 책임을 가지고 명령에 포함된 정보들을 가지고 조치를 취합니다.

### Aggregate,
Aggregate는 항상 일정한 상태로 유지되는 하나의 엔티티이거나 엔티티들의 그룹입니다. 최상위 Aggregate(Aggregate Root)는 aggregate 트리상의 최상위 객체이며, 일정한 상태를 유지하는 책임을 가지고 있습니다. 이런 점들이 Aggregate를 CQRS 기반의 애플리케이션에서 명령 모델을 구현하기 위한 가장 중요한 빌딩 블록으로 만듭니다.

> Note  // TODO - 다듬어야 함.
> "**Aggregate**"라는 용어는 도메인 주도 개발에서 다음과 같이 에릭 에반스가 정의한 aggregate을 말합니다.
> "데이터 변경 목적을 위한 하나의 단위로 취급되는 연관된 객체들의 묶음으로, 외부의 참조는 최상위로 지정된 Aggregate내의 하나의 멤버로 제한된다. 일관성 규칙들의 집합은 Aggregate의 경계내에서 적용된다."

예를 들어, 하나의 "연락처" aggregate은 "연락처"와 "주소"라는 두개의 엔티티를 포함할 수 있습니다. 전체 aggregate을 일관된 상태로 유지하기 위해선, 하나의 연락처로써 주소를 추가하는 행위는 연락처 엔티티를 통해서 이루어 져야 합니다. 이 경우, 연락처 엔티티를 최상위 aggregate로 지정 합니다.

Axon에선, aggregate는 Aggregate 식별자에 의해 식별이 됩니다. 식별자의 유형에 제한은 없지만, 어떤 객체든 식별자로 사용할 있습니다. 하지만 좋은 식별자를 구현하기 위한 아래와 같은 몇가지 가이드 라인은 있습니다.
* 다른 인스턴스와의 비교에서 정확안 동치성을 보장하기 위한 ```equals```와 ```hasCode```메서드를 구현해야 합니다.
* 일관된 결과를 제공하는 ```toString()``` 메서드를 구현해야 합니다. 동치성 비교에 사용되는 항목들의 내용은 항상 일관되어야 하며, ```toString``` 메서드를 통해서도 일관된 내용이 표시되어야 합니다.
* ```Serializable``` 인터페이스를 구현하도록 권장합니다.

테스트 픽스쳐([테스팅 참조](// TODO link))는 이런 조건들을 검증하고 Aggregate가 호환되지 않는 식별자를 사용할 경우 테스트는 실패하게 됩니다. ```String```, ```UUID``` 그리고 숫자형태의 식별자를 사용한다면 아무런 문제가 되지 않습니다. 원시 타입의 변수는 늦은 초기화(lazy initialization)을 허용하지 않기때문에, 원시(primitive)타입의 값을 식별자로는 사용이 불가합니다. 일부 환경에서, Axon은 원시 타입 변수의 기본값을 식별자의 값으로 잘못 가정할 수 있습니다.

> Note
> 순차적인 값을 사용하는 것보다 무작위로 생성된 값을 식별자로 사용하는 것이 모범 사례로 생각됩니다. 순차적인 값을 식별자로 사용하게 되면 애플리케이션의 규모 가변성을 크게 해치게 됩니다. 왜냐면 애플리케이션 인스턴스간 마지막으로 사용된 순차적인 값을 최신으로 유지를 해야 하기 때문입니다. 8.2 × 10^11^ UUID를 사용한다면, UUID의 값이 중복될 확률은 매우 작습니다. (중복될 확률: 10^-15^)
> 기능적 식별자는 변경될 가능성이 있어, 적절하게 애플리케이션에 적용하는 것이 쉽지 않습니다. 따라서 기능적 식별자를 사용할 경우에는 조심해서 사용해야 합니다.

### Aggregate implementations, Aggregate 구현체들
특정 Aggregate은 Aggregate 루트(최상위 Aggregate)인 단일 엔티티를 통해서만 접근이 가능합니다. 보통 해당 엔티티의 이름은 Aggregate의 이름과 완전히 같습니다. 예를 들어, 주문 Aggregate은 주문 엔티티와 주문 상품 항목 엔티티로 구성될 수 있으며, 주문과 주문 상품 함께 Aggregate을 구성하게 됩니다.

Aggregate은 일반 객체이며, 상태와 상태를 변경할 수 있는 메서드를 포함합니다. 비록 CQRS 원칙을 완벽히 따르는 것은 아닐지라도, setter(accesor) 메서드를 통해 aggregate의 상태를 변경하는 것은 가능합니다.

Aggregate 루트 객체는 Aggregate 식별자를 포함하는 항목을 반드시 선언해야 합니다. 이 식별자는 최소한 첫번째 이번트가 발생되었을때 초기화 되어야 합니다. ```@AggregateIdentifier``` 애노테이션을 반드시 해당 식별자에 사용해야 합니다. JPA와 JPA 애노테이션들을 aggregate에 사용한다면, ```@Id```애노테이션을 사용하여 식별자를 나타낼 수 있습니다.

Aggregate은 게시(publication)할 이벤트를 등록하기 위해 ```AggregateLifeCycle.apply()```메서드를 사용할 수 도 있습니다. EventMessage로 메세지들을 감싸는  ```EventBus```와는 다르게, ```AggregateLifeCycle.apply()``` 메서드는 페이로드(payload) 객체를 바로 사용할 수 있습니다. 다음의 예제코드는 aggregate에 JPA, JPA 애노테이션의 사용과 ```AggregateLifeCycle.apply()```메서드의 사용 예 입니다.

```
@Entity // JPA 엔티티
public class MyAggregate {

    @Id // JPA @Id 애노테이션을 사용할 경우, @AggregateIdentifier 애노테이션은 필요하지 않습니다.
    private String id;

    // 상태를 나타내는 멤버 필드...

    @CommandHandler
    public MyAggregate(CreateMyAggregateCommand command) {
        // ... 상태 변경
        apply(new MyAggregateCreatedEvent(...));
    }

    // JPA가 필요롤 하는 생성자
    protected MyAggregate() {
    }
}
```
Aggregate내의 엔티티는 ```@EventHandler``` 애노테이션이 사용된 메서드를 통해 Aggregate가 게시하는 이벤트들을 수신할 수 있습니다. 외부 핸들러로 전파되기 전에, 해당 메서드들은 이벤트가 게시되었을때 호출이 됩니다.


// TODO - 제목 다음어야함.
### Event sourced aggregates, 이벤트로 기반 aggregates
Aggregate의 현재 상태를 저장하는 것 외에, 이미 게기된 지난 이벤트들을 가지고 Aggregate의 상태를 복원한는 것 또한 가능합니다. 이것을 가능하게 하기 위해선, 모든 상태변경은 이벤트로 표현이 되어야 합니다.

가장 중요한 부분은, 다른 aggregate들과 마찮가지로 식별자를 가져야 하고 ```apply```메서드를 통해 이벤트를 게시하는 점에서 이벤트로 기반 Aggregate들은 '일반' aggregate들과 유사합니다. 그렇지만 이벤트 기반 Aggregate들의 상태 변경(예를 들어 필드 값의 변경)과 식별값의 설정은 전적으로 ```@EventSourcingHandler``` 애노테이션이 사용된 메서드를 통해서만 적용이 되어야 합니다.

Aggregate가 게시하는 가장 첫번째 이벤트는 보통 생성 이벤트이며, Aggregate가 게시한 가장 첫번째 이벤트를 처리할 ```@EventSourcingHandler``` 메서드를 통해 반드시 Aggregate의 식별값을 설정해야 합니다.

이벤트 기반 Aggregate의 Aggregate 루트는 인자를 가지지 않는 기본 생성자를 반드시 포함해야 합니다. Axon Framework은 지난 이벤트를 사용하여 Aggregate를 초기화하기 전에, 기본 생성자를 사용하여 기본적인 Aggregate 인스턴스를 생성합니다. Aggregate에 기본 생성자가 없는 경우, Aggregate 로딩 시 예외가 발생하게 됩니다.

```
public class MyAggregateRoot {

    @AggregateIdentifier
    private String aggregateIdentifier;

    // 상태를 나타내는 멤버 필드...

    @CommandHandler
    public MyAggregateRoot(CreateMyAggregate cmd) {
        apply(new MyAggregateCreatedEvent(cmd.getId()));
    }

    // 복원을 위해 필요한 기본 생성자
    protected MyAggregateRoot() {
    }

    @EventSourcingHandler
    private void handleMyAggregateCreatedEvent(MyAggregateCreatedEvent event) {
        // 식별자 값은 항상 올바르게 초기화 되어야 합니다.
        this.aggregateIdentifier = event.getMyAggregateIdentifier();

        // ... 상태 변경
    }
}
```

```@EventSourcingHandler``` 애노테이션이 달린 메서드들은 특정 규칙에 따라 호출됩니다. 이 규칙들은 [애노테이션 기반 이벤트 처리자](// TODO link)에서 상세히 설명할 ```@EventHandler``` 애노테이션이 달린 메서드들에 적용되는 규칙과 동일합니다.

> Note
>  JVM의 보안 설정을 Axon Framework가 메서드의 접근 제한을 변경할 수 있도록 하는 한
 이벤트 처리 메서드를 private으로 선언할 수 도 있습니다. 이벤트 처리 메서드를 private으로 선언하면 aggregate의 이벤트를 생성하는 메서드를 노출시키는 공용 API와 이벤트를 처리하는 내부 로직을 분리할 수 있습니다.

 대부분의 IDE들은 "사용하지 않는 private 메서드"에 대한 경고를 무시할 수 있는 옵션을 가지고 있기 때문에, 이벤트 처리 메서드를 private으로 변경하여도 경고를 표시하지 않도록 할 수 있습니다. 아니면 ```@SuppressWarnings("Unused-Declaration")``` 애노테이션을 사용하면, 실수로 이벤트 처리 메서드를 삭제하지 않도록 할 수 있습니다.

 몇몇 경우에, 특히 aggregate의 구조가 한 두개의 엔티티를 넘어 커질 경우,  동일한 aggregate내의 다른 엔티티로 게시되는 이벤트들을 처리하는 것이 보다 더 깔끔한 코드를 작성할 수 있는 방법입니다. 그러나 이벤트 처리 메서드는 aggregate의 상태를 재구성할때도 호출이 되기때문에, 반드시 특별한 주의가 필요합니다.

 이벤트 처리 메서드내에서 ```apply```메서드를 통해 새로운 이벤트를 발생 시킬 수 있습니다. 이렇게 하면, 엔티티 B가 특정 이벤트를 받았을 때, 엔티티 A로 하여금 무언가를 하도록 하기 위해 다른 이벤트를 발생 시킬 수 있습니다. 단 이미 기록된 이벤트들을 재현할 때는 ```apply()``` 메서드 호출은 무시됩니다.  ```apply()``` 메서드를 통해 발생되는 이벤트는 모든 엔티티들이 첫번째 이벤트를 수신한 이후에 해당 엔티티들에게만 전달이 되는 점에 유의해야 합니다.  ```apply()``` 메서드를 통해 발생된 이벤트가 적용된 이후의 엔티티 상태에 기반하여 더 많은 이벤트를 발생 시켜야 한다면, ```apply(...).andThenApply(...)``` 형태로 코드를 작성해야 합니다.

 ```AggregateLifecycle.isLive()``` 메서드를 사용하여 현재 aggregate가 활성 상테인지를 확인할 수 있습니다. 기본적으로 지난 이벤트들의 재현을 모두 마쳤다면 aggregate는 활성 상태로 간주 됩니다. 지난 이벤트들을 재현하는 동안에는 ```isLive()```메서드는 false를 반환합니다. ```isLive``` 메서드를 사용하면 새로 생성 된 이벤트를 처리 할 때만 수행해야하는 작업을 수행 할 수 있습니다.

 ### Complex Aggregate structures
 복잡한 비지니스 로직을 처리하기 위해서는 때때로 단 하나의 aggregate 루트가 제공하는 aggregate, 그 이상이 필요합니다. 이런 경우, 복잡성은 aggregate내의 다수의 엔티티에 걸쳐 있다는 것이 중요합니다. 이벤트 소싱을 사용할때, aggregate 뿐만 아니라 aggregate내의 엔티티들도 이벤트를 통한 상태 변경을 할 필요가 있습니다.

 > Note
 > "Aggregate는 상태를 노출해서는 안된다."라는 것을 보통 '어떤 엔티티도 상태에 대한 접근 메서드를 가져선 안된다'라고 오해합니다. 그렇지 않으며 사실은 aggregate 내의 엔티티들이 동일한 aggregate내의 다른 엔티티들에게 상태를 노출하는 것은 Aggregate를 작성하는데 많은 도움이 됩니다.  그렇지만 Aggregate외부의 엔티티들에게 상태를 노출하는 것은 권장하지 않습니다.

 Axon을 통해 복잡한 aggregate 구조내에서의 이벤트 소싱을 처리할 수 있습니다. Aggregate 루트와 같은 엔티티들은 단지 객체일 뿐입니다. 하위 엔티티를 참조하는 필드(field)에 반드시 ```@AggregateMember``` 애노테이션을 달아 주어야 합니다. Axon은 ```@AggregateMember```을 통해 명령 및 이벤트 처리자에게 해당 필드의 타입 클래스를 검사하도록 합니다.

Aggregate 루트를 포함한 엔티티가 이벤트를 적용할때, Aggregate 루트가 먼저 해당 이벤트를 처리하고 난 다음에, 모든 ```@AggregateMember``` 애노테이션이 달린 항목들과 해당 항목의 하위 항목들로 차례로 이동하게 됩니다.

하위 항목을 포함하는 필드에는 반드시 ```@AggregateMember``` 애노테이션을 달아 주어야 합니다. ```@AggregateMember``` 애노테이션은 다음과 같은 유형의 필드에 사용합니다.

* 필드에 직접적으로 사용된 엔티티 타입 (예 ```private CustomAggregateMember customAggregateMember```)
* ```Iterable``` 타입의 필드, ```Set```, ```List```와 같은 모든 컬렉션을 포함합니다.
* ```java.util.Map``` 타입의 항목의 값(value)


### Handling commands in Aggregate, Aggregate내에서 명령 처리
명령 처리가가 해당 작업을 수행하는데 Aggregate의 상태는 필요 하지 않지만, 명령 처리를 위한 상태를 가지고 있는 Aggregate내에 직접 명령 처리자를 정의하는 것을 권장합니다.

Aggregate에 명령 처리자를 정의하기 위해선, 단지 명령 처리를 위한 메서드에 ```@CommandHandler``` 애노테이션을 달아 주기만 하면 됩니다. 다른 일반적인 핸들러 메서드에 적용되는 규칙을 동일하게 ```@CommandHandler``` 애노테이션 대상 메서드에도 적용하면 됩니다. 그렇지만 명령은 명령 객체가 포함하고 있는 페이로드에 의해서면 분배되지는 않습니다. 명령 메세지들은 명령 객체의 정규화된 클래스명을 기본값으로 하는 이름을 가지고 있습니다.

기본적으로,  ```@CommandHandler``` 애노테이션 메서드는 다음과 같은 매개변수 타입을 받습니다.

* 첫번째 매개변수는 명령 메세지의 페이로드입니다. ```@CommandHandler``` 애노테이션에 처리자가 처리할 명령 객체의 이름을 명시적으로 선언하였다면, 첫번째 매개변수는 ```Message``` 혹은 ```CommandMessage```가 될 수 도 있습니다. 기본적으로, 명령 이름은 명령 메세지의 페이로드 객체의 정규화된 클래스의 이름이 됩니다.
* ```@MetaDataValue``` 애노테이션과 애노테이션에 명시된 키에 해당하는 메타 데이터 값을 인자로 받을 수 있습니다. (예 ```@MetaDataValue("userId") String id")```. ```@MetaDataValue``` 애노테이션에 ```required``` 속성을 기본값인 ```false```로 주게 되면,  없는 메타 데이터를 ```null```로 처리하여 넘겨줍니다. 반면 ```required```가 ```true```이고 해당 메타 데이터가 존재하지 않으면, 해당 메서드는 호출되지 않습니다.
* ```MetaData``` 타입의 매개변수는 주입된 ```CommandMessage```의 전체 ```MetaData```를 포함하게 됩니다.
* ```UnitOfWork``` 타입의 매개변수는 현재의 작업 단위(Unit of Work) 객체를 가지게 됩니다. 넘겨받은 ```UnitOfWork``` 매개변수를 통해, 명령 처리자는 작업 단위 객체의 특정 단계에서 실행되어야 할 작업을 등록할 수 있거나 이미 등록된 자원을 사용할 수 있습니다.
* ```Message``` 혹은 ```CommandMessage``` 타입의 매개변수는 페이로드와 메타 데이터를 포함한 완전한 메세지를 가지게 됩니다. 이를 통해 메타 데이터의 필드 혹은 래핑 메세지의 다른 속성들을 사용할 수 있습니다.

Aggregate 타입의 명령 메세지를 처리해야하는 인스턴스를 알기 위해서, 아래의 예제 코드처럼 명령 객체내의 Aggregate 식별자를 포함하는 속성에 반드시 ```@TargetAggregateIdentifier``` 애노테이션을 붙여야 합니다. ```@TargetAggregateIdentifier``` 애노테이셔은 필드나 속성에 접근 하는 메서드(예, getter 메서드)에 사용해야 합니다.

```
public class UpdateAccountCommand<ID> {
    @TargetAggregateIdentifier
    private final ID id;

    UpdateAccountCommand(ID id) {
        this.id = id;
    }

    public ID identifier() {
        return id;
    }
}
```
Aggregate 인스턴스를 생성하는 명령어 객체들은 대상 aggregate 식별자를 식별할 필요는 없지만, 그래도 해당 명령어 객체들에도 ```@TargetAggregateIdentifier```을 사용하는 것을 권장합니다.

명령을 분배하기 위한 또 다른 메커니즘을 사용할려면, ```CommandTargetResolver```인터페이스의 구현체를 직접 작성하면 됩니다. 직접 구현한 구현체는 주어진 명령 객체에 기반한 Aggregate 식별자와 기대 버전을 반환해야 합니다.

> Note
> Aggregate 생성자에 ```@CommandHandler``` 애노테이션을 사용하면, 해당 명령은 aggregate의 새로운 인스턴스를 생성하고 저장소에 생성된 aggregate를 저장합니다. 이러한 명령들은 특정 aggregate 인스턴스를 대상으로 할 필요가 없습니다. 그러므로, 이러한 명령들은 ```@TargetAggregateIdentifier``` 혹은  ```@TargetAggregateVersion``` 애노테이션을 필요로 하지 않으며, 또한 ```CommandTargetResolver```가 이런 명령들을 처리할 필요도 없습니다.  
>
> 명령으로부터 aggregate 인스턴스를 생성할때, 해당 명령 처리가 정상적으로 수행되면 명령에 대한 콜백에 aggregate 식별자를 전달합니다.

```
“public class MyAggregate {

    @AggregateIdentifier
    private String id;

    @CommandHandler
    public MyAggregate(CreateMyAggregateCommand command) {
        apply(new MyAggregateCreatedEvent(IdentifierFactory.getInstance().generateIdentifier()));
    }

    // Axon에서의 처리를 위한 기본 생성자.
    MyAggregate() {
    }

    @CommandHandler
    public void doSomething(DoSomethingCommand command) {
        // 해당 명령에 대한 처리 수행
    }

    // 간단한 예제코드이기 때문에, MyAggregateCreatedEvent를 수신하여 id값을 설정하는 코드는 생략합니다.
}

public class DoSomethingCommand {

    @TargetAggregateIdentifier
    private String aggregateId;

    // 간단한 예제코드이므로, 이하 코드는 생략합니다.
}

```

Aggregate 설정을 위한 Axon Configuration API 사용에 대한 예제 코드입니다.

```
Configurer configurer = ...
// 기본 사항 사용을 위함.
//configurer.configureAggregate(MyAggregate.class);

// 사용자 정의 허용
configurer.configureAggregate(
	AggregateConfigurer
	.defaultConfiguration(MyAggregate.class)
	.configurerCommandTargetResolver(c -> new CustomCommandTargetResolver())
);
```

```@CommandHandler``` 애노테이션은 Aggregate 루트에만 사용할 수 있는 것은 아닙니다. 모든 명령 처리자들을 Aggregate 루트에 정의하게 되면 너무 많은 메서드들이 생길 수 있으며, 그 중 많은 수의 메서드는 단순히 하위 엔티티들 중 하나로 호출을 전달합니다. 이런 것이 문제가 된다면, ```@CommandHandler``` 애노테이션을 하위의 엔티티들 중 하나의 엔티티의 메서드에 선언하여 사용할 수 있습니다. Axon이 ```@CommandHandler``` 애노테이션이 사용된 메서드를 찾도록 하기 위해서 해당 하위 엔티티를 aggregate 루트의 속성 필드에 반드시 ```@AggregateMember``` 애노테이션을 사용해야 합니다. ```@AggregateMember``` 애노테이션이 사용된 필드만이 명령 처리자를 위한 검사 대상이 되기 때문입니다. 해당 엔티티에 대한 명령을 수신했을때, 만약 해당 엔티티 필드의 값이 ```null```이라면 예외가 발생하게 됩니다.

```
public class MyAggregate {
	@AggregateIdentifier
	private String id;

	@AggregateMember
	private MyEntity entity;

	@CommandHandler
	public MyAggregate(CreateMyAggregateCommand command) {
		apply(new MyAggregateCreatedEvent(...);
	}

	// Axon에서의 처리를 위한 기본 생성자.
	MyAggregate() {}

	@CommandHandler
	public void doSomething(DoSomethingCommand command) {
		// 해당 명령에 대한 처리 수행
	}


    // 간단한 예제코드이기 때문에, MyAggregateCreatedEvent를 수신하여 id값을 설정하는 코드는 생략합니다.
    // 그리고 생애주기 상의 특정 지점에서, DoSomethingInEntityCommand commands를 처리하기 위해 "entity" 필드에 대한 값을 설정을 반드시 해주어야 합니다.
}

public class MyEntity {
	@CommandHandler
	public void handleSomeCommand(DoSomethingInEntityCommand command) {
		// entity에 대한 DoSomethingInEntityCommand 처리 수행
	}
}
```

>  Note
>  Aggregate내에 개별 명령에 대한 처리자는 오로지 하나만 존재해야 합니다. 즉, 동일한 타입의 명령을 처리할 수 있는 다수의 엔티티(루트, 루트가 아닌 엔티티 모두 포함)에 ```@CommandHandler```를 사용할 수 없습니다. 만약 조건에 따라서 명령을 특정 엔티티로 분배해야 한다면, 이런 엔티티들의 부모 객체가 해당 명령을 처리해야 하고 조건에 따라 해당 명령을 전달해야 합니다.
>
> 실행 시점의 필드 타입과 선언 시점의 필드 타입이 반드시 일치해야 하는 것은 아니지만, ```@AggregateMember```로 선언된 필드의 타입으로 ```@CommandHandler```를 적용합니다.

엔티티들을 포함하는 컬렉션(Collection)과 맵(Map) 타입의 필드에도 ```@AggregateMember``` 애노테이션을 사용할 수 있습니다. 맵을 경우 값들에는 엔티티들을 포함하도록 해야 하고 키는 값에 대한 참조로 사용될 값을 가지면 됩니다.

명령을 정확한 인스턴스로 분배해야 하기때문에, 이런 인스턴스들은 반드시 정확하게 식별이 되어야 합니다. ```@EntityId``` 애노테이션을 인스턴스들의 "id" 필드에 반드시 사용해야 합니다.  메세지를 받아야할 엔티티를 찾기 위한 용도로 사용되는 명령의 속성은 기본적으로 ```@EntityId``` 애노테이션이 사용된 필드의 이름으로 지정이 됩니다. 예를 들어, "myEntityId"필드에 ```@EntityId``` 애노테이션을 사용했을 때, 해당 명령은 반드시 동일한 이름의 속성을 가지고 있어야 합니다. 다시 말하면, 명령 객체는 ```getMyEntityId``` 혹은 ```myEntityId()``` 메서드들 중 하나를 반드시 포함해야 합니다. 만약 필드 명과 분배 기준이 되는 속성이 다르다면, ```@EntityId(routingKey = "customRoutingProperty" )```와 같이 ```routingKey```를 사용하여 명시적으로 분배 기준이 되는 항목명을 지정해야 합니다.

```@AggregateMember``` 애노테이션이 사용된 컬렉션이나 맵에서 엔티티가 발견되지 않는다면, aggregate이 해당 시점에 수신된 명령을 처리할 수 없기 때문에  ```IllegalStateException```이 발생하게 됩니다.

> Note
> ```@AggregateMember``` 애노테이션이 사용된 컬렉션이나 맵 타입의 필드를 선언할 때, 내부 요소의 타입을 식별할 수 있게 해주는 제너릭을 사용해야 합니다. 만약 제너릭을 사용할 수 없다면, ```@AggregateMember``` 애노테이션의 속성인 ```entityType```을 사용하여 엔티티의 타입을 명시적으로 선언해 주어야 합니다.

### External Command Handlers, 외부 명령 처리자
명령을 Aggregate 인스턴스로 직접 분배할 수 없는 경우가 있을 수 있는데, 이런 경우 명령 처리자를 등록해야 합니다.

명령 처리자 객체는 ```@CommandHandler``` 애노테이션이 붙은 메서드를 가지는 일반 객체입니다. Aggregate의 경우와는 다르게, 오로지 단 하나의 명령 처리자 객체가 생성되어 해당 명령 처리자 객체에 선언된 모든 명령을 처리할 수 있습니다.

```
public class MyAnnotatedHandler {
	@CommandHandler
	public void handleSomeCommand(SomeCommand command, @MetaDataValue("userId") String userId ) {
		// 명령을 받아 처리할 로직 수행
	}

	@CommandHandler( commandName = "myCustomCommand")
	public void handleCustomCommand(SomeCommand command) {
		// 명령을 받아 처리할 로직 수행
	}
}

// 위의 명령 처리자를 커맨드 버스(command bus)에 등록
Configurer configurer = ...
configurer.registerCommandHandler(c -> new MyAnnotatedHandler());

```

## Event Handling, 이벤트 처리
이벤트 리스너는 애플리케이션 내에서 발생한 이벤트를 받아 처리하는 콤포넌트입니다. 이벤트 리스너들은 일반적으로 명령 모델에 의해 결정된 사항을 기반으로 보통 뷰 모델(View model)을 갱신하거나 변경 내용을 써드파티 서비스 등의 다른 콤포넌트로 전달하는 등의 로직을 수행합니다.  몇몇의 경우에, 이벤트 처리자 스스로 수신한 이벤트를 기반으로 이벤트를 발생 시키거나 추가 변경의 필요로 명령을 전송 할 수 있습니다.

### Defining Event Handlers, 이벤트 처리자 정의하기
```@EventHandler``` 애노테이션을 사용하여 하나의 객체에 다수의 이벤트 처리 메서드를 선언할 수 있습니다. 이벤트 처리자가 처리할 이벤트는 이벤트 처리 메서드의 매개변수로 선언합니다.

이벤트 처리 메서드에 선언 가능한 매개변수 타입은 아래와 같습니다.

* 첫번째 매개변수는 항상 이벤트 메세지의 페이로드이어야 합니다.  메세지의 페이로드에 접근할 필요가 없는 경우,  ```@EventHandler``` 애노테이션의 ```payloadType```에 원하는 페이로드 타입을 명시해주어야 합니다. ```payloadType```을 명시적으로 선언한 경우에는 첫번째 매개변수는 아래의 설명된 규칙에 따라 결정이 됩니다. 메세지의 페이로드가 매개변수로 전달되어야 한다면, ```@EventHandler``` 애노테이션의 ```payloadType```를 선언해선 안됩니다.
* ```@MetaDataValue``` 애노테이션이 붙은 매개변수는 애노테이션에 명시된 키에 해당하는 메타 데이터 값으로 해석되어 메서드로 전달됩니다. ```@MetaDataValue``` 애노테이션에 ```required``` 속성을 기본값인 ```false```로 주게 되면, 없는 메타 데이터를 ```null```로 처리하여 넘겨줍니다. 반면 ```required```가 ```true```이고 해당 메타 데이터가 존재하지 않으면, 해당 메서드는 호출되지 않습니다.
* ```MetaData``` 타입의 매개변수는 주입된 ```CommandMessage```의 전체 ```MetaData```를 포함하게 됩니다.
* ```@Timestamp``` 애노테이션이 붙고 타입이 ```java.time.Instant``` (혹은 java.time.temporal.Temporal) 인 매개변수는 ```EventMessage```의 타임 스탬프값으로 전달 됩니다. 해당 타임 스탬프 값은 이벤트가 생성된 시간 값을 가집니다.
* ```@SequenceNumber``` 애노테이션이 붙고 타입이 ```java.lang.Long``` 혹은 ```long``` 타입의 매개변수는 ```DomainEventMessage```의 ```sequenceNumber``` 값으로 전달이 됩니다. 해당 ```sequenceNumber``` 값은 이벤트가 발생한 Aggregate의 범위 내에서의 순서를 의미합니다.
* 매개변수가 [Message](http://www.axonframework.org/apidocs/3.0/org/axonframework/messaging/Message.html) 하위 구현체인 경우, 해당 메서드는 전체 ```EventMessage```를 주입 받습니다. (메세지가 매개변수에 할당 가능한 경우에만 가능합니다.) 첫번째 매개변수가 [Message](http://www.axonframework.org/apidocs/3.0/org/axonframework/messaging/Message.html) 인 경우, Java의 타입 이레이져(type erasure)가 컴파일 후에 제너릭 타입을 지우기 때문에 제너릭 타입을 명시하더라도 매개변수의 타입을 알 수 없습니다. 이런 경우, 페이로드 타입의 매개변수를 먼저 선언하고, 그 다음에 [Message](http://www.axonframework.org/apidocs/3.0/org/axonframework/messaging/Message.html)의 매개변수를 선언해야 합니다.
* 스프링을 사용하고 Axon Spring Boot Starter 모듈에 대한 의존성을 선언하거나 ```@EnableAxon``` 애노테이션을 ```@Configuration``` 클래스에 사용하여 Axon Configuration이 활성화 되면, 다른 모든 매개변수들은 주입 가능한 빈(bean)이 애플리케이션 컨텍스트내에 있다면 자동으로 주입이 됩니다. 이를 활용하여 ```@EventHandler``` 애노테이션이 붙은 메서드에 자동으로 필요한 자원을 주입할 수 있습니다.

```ParameterResolverFactory``` 인터페이스를 직접 구현하고  ```/META-INF/service/org.axonframework.common.annotation.ParameterResolverFactory``` 파일을 생성한후 구현체 클래스를 설정하여  ```ParameterResolver```들을 추가적으로 설정할 수 있습니다. 보다 상세한 내용은 [고급 사용자 지정](/avanced/customization)을 참고하세요.  // TODO: 고급 사용자 지정 링크 수정.

모든 상황에서, 리스너 인스턴스마다 하나의 이벤트 처리 메서드가 호출이 됩니다. Axon은 다음과 같은 규칙을 적용하여 가장 적합한 호출 대상 메서드를 찾아 냅니다.

1. 실제 인스턴스의 클래스(this.getClass()로 반환되는 클래스) 계층 구조상에서, ```@EventHandler``` 애노테이션이 붙은 메서드들을 평가합니다.
2. 모든 매개변수들을 값으로 치환할 수 있는 하나 이상의 메서드가 발견되었을 경우, 가장 구체적인 타입을 선언한 메서드를 선택합니다.
3. 현재 클래스 구조상에서 메서드를 발견할 수 없는 경우, 상위 타입의 클래스 구조를 동일한 방법으로 탐색합니다.
4. 클래스 구조상의 최상위에서도 메서드를 발견할 수 없는 경우, 해당 이벤트는 무시됩니다.

```
// EventB는 EventA를 상속한다고 가정합니다.
// EventC는 EventB를 상속한다고 가정합니다.
// SubListener의 단일 인스턴스가 등록되었다고 가정합니다.

public class TopListener {

    @EventHandler
    public void handle(EventA event) {
    }

    @EventHandler
    public void handle(EventC event) {
    }
}

public class SubListener extends TopListener {

    @EventHandler
    public void handle(EventB event) {
    }
}
```
위의 예제를 살펴보면, ```SubListener```의 이벤트 처리 메서드는 ```EventB```와 ```EventC``` 인스턴스에 대해 호출이 됩니다. 다시 말하면, ```TopListener```의 이벤트 처리 메서드는 ``EventC``` 에 대해 전혀 호출이 되지 않습니다.  그 이유는 3번 규칙때문인데 현재의 클래스 레밸인  ```SubListener```에서 ```EventC```의 상위 타입인 ```EventB```를 처리 할 수 있도록 정의 되어 있기 때문입니다. ```EventA``` 인스턴스는 ```EventB```로 다운 캐스팅할 수 없기때문에, ```EventA``` 타입의 이벤트들은  ```TopListener```의 이벤트 처리 메서드로 처리됩니다.

### Registering Event Handlers, 이벤트 처리자 등록하기
이벤트를 처리할 수 있는 콤포넌트들은 ```EventHandlingConfiguration```을 사용하여 정의 할 수 있습니다. ```EventHandlingConfiguration```은 Axon의 전역 설정 객체인 ```Configurer```에 모듈로 등록되는 객체입니다. 일반적으로 하나의 애플리케이션은 정의된 하나의 ```EventHandlingConfiguration``` 객체를 가지지만, 모듈화되어 있는 보다 규모가 큰 애플리케이션은 모듈마다 ```EventHandlingConfiguration``` 객체를 가질 수도 있습니다.

```@EventHandler``` 메서드를 가진 객체를 등록하기 위해선, ```EventHandlingConfiguration```의 ```registerEventHandler``` 메서드를 사용합니다.

```
// EventHandlingConfiguration 정의
EventHandlingConfiguration ehConfiguration = new EventHandlingConfiguration()
    .registerEventHandler(conf -> new MyEventHandlerClass());

//  Axon Configuration에 정의된 EventHandlingConfiguration 등록
Configurer axonConfigurer = DefaultConfigurer.defaultConfiguration()
    .registerModule(ehConfiguration);
```
스프링의 자동 설정(AutoConfiguration)을 사용하여 이벤트 처리자들을 등록하는 상세한 내용은 [이벤트 처리 설정](/eventhandling/configuration)를 참조하시면 됩니다. // TODO 이벤트 처리 설정 링크 수정

## Query Handling, 조회 요청 처리
쿼리(query)처리를 위한 콤포넌트는 애플리케이션으로 들어오는 조회 요청 메세지를 처리합니다. 이벤트 리스터가 생성한 일반적으로 뷰(view)모델로부터 데이터를 읽어 옵니다. 쿼리 처리 콤포넌트는 일반적으로 이벤트나 명령을 발생 시키지 않습니다.

### Defining Query Handlers, 쿼리 처리자 정의
Axon내에서, 쿼리 처리를 위한 다수의 메서드를 선언할 수 도 있습니다. 해당 메서드에는 ```@QueryHandler``` 애노테이션을 붙여 사용합니다. 메서드가 처리할 메세지는 매개변수를 통해 선언합니다.

기본적으로, ```@QueryHandler``` 애노테이션을 붙인 메서드들은 다음과 같음 매개변수들을 선언할 수 있습니다.

* 첫번째 매개변수로 쿼리 메세지의 페이로드를 선언할 수 있습니다. 만약 ```@QueryHandler``` 애노테이션에 핸들러가 처리할 쿼리명을 명시적으로 선언한다면, 첫번째 매개변수를 ```Message``` 혹은 ```QueryMessage``` 타입으로 선언할 수 도 있습니다. 기본적으로 쿼리 메세지의 페이로드 클래스 명을 쿼리명으로 사용합니다.
* ```@MetaDataValue``` 애노테이션이 붙은 매개변수는 애노테이션에 명시된 키에 해당하는 메타 데이터 값으로 해석되어 메서드로 전달됩니다. ```@MetaDataValue``` 애노테이션에 ```required``` 속성을 기본값인 ```false```로 주게 되면, 없는 메타 데이터를 ```null```로 처리하여 넘겨줍니다. 반면 ```required```가 ```true```이고 해당 메타 데이터가 존재하지 않으면, 해당 메서드는 호출되지 않습니다.
* ```MetaData``` 타입의 매개변수는 주입된 ```CommandMessage```의 전체 ```MetaData```를 포함하게 됩니다.
* ```UnitOfWork``` 타입의 매개변수를 통해 현재의 작업 단위 객체를 주입 받을 수 있습니다. 이를 통해, 쿼리 처리자는 작업 단위의 특정 단계에서 실행해야할 작업을 등록할 수 있습니다. 혹은 현재의 작업 단위 객체에 등록되어 있는 자원을 사용할 수 있습니다.
* ```Message``` 혹은 ```QueryMessage``` 타입의 매개변수는 페이로드와 메타 데이터를 포함한 전체 메세지를 주입 받을 수 있습니다. 여러 메타 데이터가 필요한 경우 혹은 메세지의 다른 속성이 필요할때 유용하게 사용할 수 있습니다.

```ParameterResolverFactory``` 인터페이스를 직접 구현하고  ```/META-INF/service/org.axonframework.common.annotation.ParameterResolverFactory``` 파일을 생성한후 구현체 클래스를 설정하여  ```ParameterResolver```들을 추가적으로 설정할 수 있습니다. 보다 상세한 내용은 [고급 사용자 지정](/avanced/customization)을 참고하세요.  // TODO: 고급 사용자 지정 링크 수정.

모든 상황에서, 쿼리 처리자 인스턴스마다 하나의 이벤트 처리 메서드가 호출이 됩니다. Axon은 다음과 같은 규칙을 적용하여 가장 적합한 호출 대상 메서드를 찾아 냅니다.

1. 실제 인스턴스의 클래스(this.getClass()로 반환되는 클래스) 계층 구조상에서, ```@QueryHandler ``` 애노테이션이 붙은 메서드들을 평가합니다.
2. 모든 매개변수들을 값으로 치환할 수 있는 하나 이상의 메서드가 발견되었을 경우, 가장 구체적인 타입을 선언한 메서드를 선택합니다.
3. 현재 클래스 구조상에서 메서드를 발견할 수 없는 경우, 상위 타입의 클래스 구조를 동일한 방법으로 탐색합니다.
4. 클래스 구조상의 최상위에서도 메서드를 발견할 수 없는 경우, 해당 이벤트는 무시됩니다.

명령 처리와 유사하지만 이벤트 처리와는 달리 쿼리 처리는 쿼리 메세지의 클래스 계층을 고려하지 않는 점에 유의 하세요.

```
// QueryB는 QueryA를 상속한다고 가정합니다.
// QueryC는 extends를 상속한다고 가정합니다.
// SubHandler의 단일 인스턴스가 등록되어 있다고 가정합니다.

public class TopHandler {

    @QueryHandler
    public MyResult handle(QueryA query) {
    }

    @QueryHandler
    public MyResult handle(QueryB query) {
    }

    @QueryHandler
    public MyResult handle(QueryC query) {
    }
}

public class SubHandler extends TopHandler {

    @QueryHandler
    public MyResult handleEx(QueryB query) {
    }
}
```

위의 예제에서, ```QueryB``` 타입의 쿼리 처리 시, SubHandler의 쿼리 처리자 메서드가 호출 되고 ```MyResult```를 반환합니다. ```QueryB``` 타입의 쿼리를 처리할 때는 ```TopHandler```의 메서드가 호출 되고 ```MyResult```를 반환합니다.

### Registering Query Handlers, 쿼리 처리자 등록하기
동일한 쿼리명과 응답 타입(유형)에 대한 다수의 쿼리 처리자를 등록할 수 있습니다. 쿼리들을 처리할때, 클라이언트는 하나 쿼리 처리자로부터 결과를 받을지 혹은 사용 가능한 모든 쿼리 처리자로부터 결과를 받을 지를 지정할 수 있습니다.


### Using Spring, 스프링 프레임워크와 연동하여 사용하기
스프링의 자동설정(AutoConfiguration)을 사용하면, ```@QueryHandler``` 애노테이션이 붙은 메서드들을 찾기 위해 모든 싱글톤(singleton) 빈(bean)들을 검색합니다. 발견된 각각의 메서드들은 새로운 쿼리 처리자로 쿼리 버스(query bus)에 등록됩니다.

### Using Configuration API, 설정 API 사용하기
설정 API를 사용하여 쿼리 처리자를 등록할 수 있습니다. 아래의 예제 코드 처럼 ```Configurer``` 클래스의 ```registerQueryHandler``` 메서드를 사용하면 됩니다.

```
// 샘플 쿼리 처리자
public class MyQueryHandler {
  @QueryHandler
  public String echo(String echo) {
    return echo;
  }

  ...

  // 쿼리 처리자 등록하기
  Configurer axonConfigurer =
    DefaultConfigurer
    .defaultConfiguration()
    .registerQueryHandler((conf) -> new MyQueryHandler());

}
```

## Sagas, 사가
### Managing complex business transactions, 복잡한 비지니스 트랜잭션 관리하기
모든 명령들이 단일의 ACID 트랜잭션으로 완전히 처리할 수 있는 것은 아닙니다. 가장 대표적인 예로 이체를 들 수 있습니다. 보통 하나의 계좌에서 다른 계좌로 돈을 이체하기 위해서는 원자적이고 일관된 트랜잭션 처리가 반드시 필요하다고 봅니다. 실은 그렇지 않습니다. 오히려 그렇게 하는 것은 불가능합니다. 은행 A의 한 계좌에서 은행 B의 계좌로 돈을 이체할 경우, 은행 A가 은행 B의 데이터베이스의 잠금(lock)을 얻을 수 있을까요? 이체가 진행 중이라면, 은행 B가 이체된 금액을 아직 입금하지 않았는데, 은행 A가 이체 금액을 계좌에서 차감 할 수 있을까요? 실제로 은행 간 이체는 "진행 중" 입니다. 한편, 은행 B에서 이체 금액을 입금하는 동안 문제가 발생하여 뭔가 잘못되었다면, 은행 A의 고객은 이체 금액을 되돌려 받을 수 있길 할 것입니다. 따라서 궁극적으로 다른 형태의 일관성을 기대합니다. (// TODO 기대한다? 찾는다?)

ACID 트랜잭션은 불필요하거나 혹은 몇몇의 경우 ACID 트랜잭션을 통한 처리는 불가능한 반면, 다른 형태의 트랜잭션 관리는 여전히 필요합니다. 일반적으로 이런 트랜잭션들은 BASE(Basic, Availability, Soft state, Eventual consistency) 트랜잭션이라고 합니다. ACID와는 반대로, BASE 트랜잭션들은 쉽게 되돌릴(롤백:rollback)할 수 없습니다. 롤백을 하기 위해선, 트랜잭션의 일부로 이미 발생한 것을 되돌릴 작업을 통해 상쇄 시켜야 합니다. 은행 간 이체 예제에서, 은행 B에서 이체 금액의 입금이 실패했을 때 은행 A로 해당 금액을 다시 되돌려 주게 됩니다.

CQRS에선, Saga들을 통해 BASE 트랜잭션들을 처리할 수 있습니다. Saga들은 이벤트들에 응답하고 명령을 보내며 외부 애플리케이션을 호출할 수 있습니다. 도메인 주도 설계(Domain Driven Design)에 따르면, 여러 바운디드 컨텍스트(bounded context)간 조정 메카니즘으로 Saga들을 사용하는 것은 흔한 일입니다.

### Saga, 사가
Saga는 비지니스 트랜잭션을 관리할 수 있는 특별한 유형의 이벤트 리스너입니다. 일반적인 트랜잭션들은 거의 즉시 처리가 완료되는 반면, 일부 트랜잭션들은 몇일 혹은 몇주에 걸쳐 진행될 수 있습니다. Axon에서 제공하는 Saga의 개별 인스턴스를 통해 단일 비지니스 트랜잭션을 처리할 수 있습니다. 다시 말해, Saga는 트랜잭션을 관리하기 위한 속성을 유지하여, 트랜잭션의 진행 혹은 이미 발생한 작업에 대한 롤백(rollback) 작업들을 처리할 수 있습니다. 일반적으로, 그리고 보통의 이벤트 리스너와는 반대로, Saga는 이벤트를 기반으로한 시적점과 종료점을 가집니다. 보통 Saga의 시적점은 분명하나, Saga의 종료점은 다수가 존재할 수 있습니다.

Axon에선, Saga 클래스는 하나 혹은 그 이상의 ```@SagaEventHandler``` 애노테이션으로 정의할 수 있습니다. 보통의 이벤트 처리자와는 달리, 특정 시점에 다수의 Saga 인스턴스가 존재할 수 있습니다. Saga 인스턴스들은 정의된 Saga 타입에 대한 이벤트들의 처리를 위한 단일 프로세서(추적 혹은 구독)에 의해 관리됩니다.

#### Life Cycle, 생애 주기
단일 Saga 인스턴스는 단일 트랜잭션에 대한 처리를 담당합니다. 다시 말해, Saga의 생애주기의 시작과 종료를 표시할 필요가 있습니다.

Saga에서, 이벤트 처리자들에 ```@SagaEventHandler``` 애노테이션을 붙여야 합니다. 특정 이벤트가 트랜잭션의 시작을 나타낸다면, ```@SagaEventHandler``` 애노테이션을 사용한 메서드에 ```@StartSaga``` 애노테이션을 추가적으로 사용해야 합니다. ```@StartSaga``` 애노테이션은 새로운 Saga를 생성하고 원하는 이벹트가 발생 했을때 해당 Saga의 이벤트 처리 메서드를 호출 하도록 합니다.

기본적으로, 새로운 Saga는 이미 생성된 Saga가 없는 경우에만 생성이 됩니다. ```@StartSaga``` 애노테이션의 ```forceNew``` 속성 값을 ```true```로 설정하여, 새로운 Saga 인스턴스를 생성하도록 강제 할 수 있습니다.

Saga의 종료는 두 가지 방법으로 처리할 수 있습니다. 특정 이벤트가 Saga 생애주기의 종료를 언제나 나타낸다면, 해당 이벤트를 처리하는 ```@SagaEventHandler``` 애노테이션을 사용한 메서드에 ```@EndSaga``` 애노테이션을 사용하면 됩니다. Saga의 생애주기는 이벤트 처리 메서드 호출 후에 종료됩니다. 다른 방법으로, Saga내에서 ```end()``` 메서드를 호출하여 Saga의 생애 주기를 종료할 수 있습니다. ```end()```메서드를 사용하면 조건에 따라 Saga의 생애 주기를 종료할 수 있습니다.

#### EventHandling, 이벤트 처리
Saga내에서의 이벤트 처리는 보통의 이벤트 처리자와 거의 동일합니다. 메서드와 매개변수 분석에 대해서도 동일한 규칙이 적용됩니다. 큰 차이점은 수신된 이벤트들을 처리하는 이벤트 처리자의 인스턴스는 하나만 존재하지만, Saga 인스턴스는 여러개가 존재할 수도 있고 각기 다른 이벤트에 관심이 있습니다. 얘를 들어, 1번 주문과 관련된 트랜잭션을 처리하는 Saga는 2번 주문에 대한 이벤트들에는 관심이 없습니다. 이는 2번 주문을 처리하는 Saga도 동일합니다.

모든 이벤트들을 모든 Saga 인스턴스에 전달하는 것 -자원을 낭비하는 것이 분명한- 대신, Axon은 Saga와 관련된 속성을 포하하고 있는 이벤트들만을 전달합니다. 이를 위해 ```AssociationValue```들을 사용하며,  ```AssociationValue```는 키와 값으로 구성이 되어 있습니다. 키값은 예를 들어 "주문 아이디" 혹은 "주문"곽 같은 식별자의 유형을 나타냅니다. 키와 연결된 값은 "1" 혹은 "2"와 같이 식별자 유형에 대한 값을 나타냅니다.

```@SagaEventHandler``` 애노테이션이 사용된 메서드의 전개 순서는 ```@EventHandler``` 애노테이션이 사용된 메서드의 전개 순서와 동일합니다. (참조: [이벤트 처리자 정의하기](#이벤트 처리자 정의하기)). 수신한 이벤트와 이벤트 처리 메서드의 매개변수들이 일치하고 이벤트 처리 메서드에 정의되어 있는 속성들이 Saga와 연결되어 있는 경우에, 해당 메서드는 호출이 됩니다.

```@SagaEventHandler``` 애노테이션은 두개의 속성을 가집니다. 가장 중요한 속성은 ```associationProperty```이며, 속성값은 이벤트와 연결된 Saga를 찾기 위해 사용되는 수신 이벤트의 속성명입니다. 연결값의 키가 속성의 이름이며, 값은 속성의 getter 메서드를 통해 반환되는 값입니다. 예를 들어, 수신 메세지 객체가 ```String getOrderId()``` 메서드를 가지며, 해당 메서드는 "123"을 반환한다고 하면, 해당 이벤트를 처리하는 메서드의 ```@SagaEventHandler``` 애노테이션은 다음과 같이 orderId를 ```associationProperty```의 값으로 가지고 있어야 합니다.  ```@SagaEventHandler(associationProperty="orderId")```
 이 이벤트는 "orderId"를 키로 가지는 ```AssociationValue```와 연결되어 있는 모든 Saga에게 전달됩니다. 이는 하나 혹은 그 이상의 Saga에 전달되거나 어떤 Saga에도 전달 되지 않음을 의미힙니다.

 때때로, 연결하고자 하는 속성의 이름이 사용하고자 하는 연결 이름이 아닐 때가 있습니다. 예를 들어, 구매 주문에 대한 판매 주문을 처리하기 위한 Saga를 정의해야 한다면, "buyOrderId"와 "sellOrderId"를 가지는 트랜잭션 객체를 정의할 수 있습니다. Saga를 "orderId"로 연결하고자 한다면, ```@SagaEventHandler(associationProperty="sellOrderId", keyName="orderId")```와 같이 ```@SagaEventHandler``` 애노테이션에 다른 키 이름을 정의 할 수 있습니다.

#### Managing associations, 연결 관리 하기
Saga를 통해 주문, 배송 그리고 송장 등과 같은 다수의 도메인 개념에 걸쳐 발생하는 트랜잭션을 관리할때, Saga를 해당 도메인 객체의 인스턴스와 연결을 시켜줘야 합니다. Saga와 도메인 객체의 인스턴스간 연결은 해당 주문 그리고 배송 등과 같은 연결 유형을 식별하기 위한 **키**와 도메인 객체의 식별자를 나타내는 **값**이 필요합니다.

위와 같은 Saga와 도메인 객체는다양한 방법으로 연결할 수 있습니다. 우선, ```@StartSaga``` 애노테이션이 사용된 이벤트 처리자를 호출하여 새로운 Saga가 생성되면, 생성된 Saga는 ```@SagaEventHandler``` 애노테이션에 명시된 속성과 자동으로 연결이 됩니다. 다른 연결은 ```SafaLifecycle.associateWith(String key, String/Number value)``` 메서드를 사용하여 생성할 수 있습니다. 생성한 특정 연결을 삭제할려면, ```SagaLifecycle.removeAssociationWith(String key, String/Number value)``` 메서드를 사용하세요.

주문과 관련된 트랜잭션 처리를 위해 생성된 Saga를 생각해보세요. 주문 생성 이벤트를 처리하는 메서드에 ```@StartSaga``` 애노테이션이 사용되었기 때문에, 해당 Saga는 자동적으로 주문과 연결이 됩니다. 해당 Saga는 주문에 대한 송장을 생성하고 주문에 대한 배송을 준비 시키도록 합니다. 배송이 완료되고 송장에 대한 지불이 완료되면, 트랜잭션과 Saga는 종료됩니다.

위에서 설명한 Saga에 대한 코드는 아래와 같습니다.

```
public class OrderManagementSaga {
	private boolean paid = false;
	private boolean delivered = false;

	@Inject
	private transient CommandGateway commandGateway;

	@StartSaga
	@SagaEventHandler(associationProperty = "orderId")
	public void handle(OrderCreatedEvent event) {
		// shipmentId와 invoiceId를 생성합니다.
		ShippingId shipmentId = createShipmentId();
		InvoiceId invoiceId = createInvoiceId();

		// 명령을 전송하기 전에 shipmentId와 invoiceId를 Saga와 연결합니다.

		associateWith("shipmentId", shipmentId);
		associateWith("invoiceId", invoiceId);

		commandGateway.send(new PrepareShippingCommand(...));
		commandGateway.send(new CreateInvoiceCommand(...));
	}

	@SagaEventHandler(associationProperty = "shipmentId")
	public void handle(ShippingArrivedEvent event) {
		delivered = true;
		if (paid) {
			end();
		}
	}

	@SagaEventHandler(associationProperty = "invoiceId")
	public void handle(InvoicePaidEvent event) {
		paid = true;
		if (delivered) {
			end();
		}
	}

	// ...

}

```

// TODO - 클라이언트가 의미하는 바를 좀 더 구체적으로
클라이언트들이 식별자를 생성하게 함으로써, 요청-응답 형태의 명령 없이도 Saga와 도메인 객체들을 쉽게 연결할 수 있습니다. 명령을 보내기 전에, 도메인 객체들과 이벤트를 연결합니다. 이렇게하면, 명령의 일부로 생성되는 이벤트들을 감지할 수 있습니다. 송장에 대한 지불과 배송이 완료되면 Saga를 종료합니다.

#### Keeping track of Deadlines, 마감 시한을 지키도록 하기
이벤트가 발생하였을때, Saga를 통해 쉽게 조치를 취할 수 있습니다. 해당 이벤트가 Saga에게 전달되기 때문입니다. 하지만 아무것도 일어나지 않았을때 Saga를 통해 조치를 취하려면 어떻게 해야 할까요? 이를 위해 마감 시한(deadline)을 사용합니다. 송장의 예에서, 신용 카드 지불은 수초안에 결제가 이루어지는 반면, 송장은 보통 수주가 걸립니다.

Axon은 ```EventScheduler```를 제공하여 이벤트를 발생 시킬 수 있도록 합니다. 송장의 예에서, 송장이 30일 이내에 지불이 완료 되기를 원한다면, Saga를 통해  ```CreateInvoiceCommand```를 보낸 이후에 ```InvoicePaymentDeadlineExpiredEvent```를 30일이 되는 시점에 발생 시킬 수 있습니다. 이벤트 스케쥴러(EventScheduler)는 특정 이벤트를 발생 시키도록 예정(schedule)한 후, ```ScheduleToken```을 반환 합니다. 송장에 대한 지불이 이루어 지면, 반환된 ```ScheduleToken```을 통해 해당 스케쥴을 취소할 수 있습니다.

Axon은 두개의 ```EventScheduler```구현체들을 제공합니다. 하나는 순수 Java로 작성된 것이고 다른 하나는 backing scheduling 메커니즘인 Quartz2를 사용한 구현체 입니다.

```EventScheduler```의 순수 자바 구현체는 이벤트 게시 일정을 세우기 위해 ```ScheduledExecutorService```를 사용합니다. 이 스케쥴러는 매우 안정적인 타이밍을 제공하지만, 메모리 기반의 구현을 제공합니다. 따라서 JVM이 종료되면, 모든 스케쥴이 사라지게 됩니다. 따라서 긴 기간에 걸친 스케쥴을 위해서는 적당하지 않습니다.

```SimpleEventScheduler```는 ```EventBus```와 ```SchedulingExecutorService```를 함께 설정해 주어야 합니다. (```ScheduledExecutorService``` 생성 시 필요한 헬퍼 메서드는 [```java.util.concurrent.Executors```](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Executors.html)를 참고하세요.)

```QuartzEventScheduler```는 보다 더 안정적이고 엔터프라이즈에 적당한 구현체입니다. Quartz를 기반 스케쥴링 메커니즘으로 사용하면, 영속화, 클러스터링 그리고 실패(misfire) 관리와 같은 보다 더 강력한 기능들을 제공합니다. 해당 기능들을 사용하여 이벤트 게시를 확실히 보장할 수 있습니다. 조금 늦을 수는 있지만 결국 이벤트를 게시할 수 있습니다.

```QuartzEventScheduler```는 Quartz ```Scheduler```와 ```EventBus```를 필요로 합니다. 선택적으로 기본적으로 "AxonFramework-Events"로 설정되어 있는 Quartz job들이 스케쥴되어 있는 그룹의 이름을 설정할 수 있습니다.

하나 혹은 그 이상의 콤포넌트들은 스케쥴되어 있는 이벤트들을 수신할 수 있습니다. 이 콤포넌트들은 콤포넌트를 호출하는 쓰레드에 묶여 있는 트랜잭션에 의존할 수 있습니다. 스케쥴된 이벤트들은 ```EventScheduler```를 관리하는 쓰레드에 의해 게시됩니다. 이런 쓰레드들 상의 트랜잭션을 관리하기 위해서, ```TransactionManager``` 혹은 작업 단위(Unit of Work)에 묶인 트랜잭션을 생성하는  ```UnitOfWorkFactory```를 설정할 수 있습니다.

> Note
> 스프링을 사용한다면, ```QuartzEventSchedulerFactoryBean``` 혹은  ```SimpleEventSchedulerFactoryBean```을 사용하여 보다 쉽게 설정을 할 수 있습니다.
> ```QuartzEventSchedulerFactoryBean``` 그리고   ```SimpleEventSchedulerFactoryBean```에 스프링의 트랜잭션 인프라의 핵심 인터페이스인  PlatformTransactionManager를 직접 설정할 수 있습니다.

#### Injecting Resources, 자원 주입하기
Saga들은 일반적으로 이벤트에 기반한 상태를 유지하는 것 그 이상의 것들을 처리합니다. Saga들은 외부의 콤포넌트들과 연동할 수 있습니다. 외부 콤포넌트와 연동하기 위해선, 해당 콤포넌트들에 접근하기 위해 필요한 자원들을 사용해야 합니다. 보통 이런 자원들은 Saga 상태의 일부는 아니며 영속화의 대상도 아닙니다. 하지만 Saga가 복원된 이후, 이런 자원들은 Saga 인스턴스에게 이벤트가 전달되기 전에 반드시 주입이 되어야 합니다.

Saga 인스턴스에게 자원을 주입하기 위한 목적으로 사용되는 ```ResourceInjector```가 있으며, ```SagaRepository```에 의해 사용됩니다. Axon은 애플리케이션 컨텍스트로부터 자원을 받아 애노테이션이 사용된 필드와 메서드에 해당 자원을 주입하는 ```SpringResourceInjector```과 등록된 자원들을 ```@Inject```애노테이션이 사용된 필드와 메서드에 주입하는 ```SimpleResourceInjector```를  제공합니다.

> Tips
> 자원들을 Saga와 함께 영속화하면 안되기 때문에, ```transient``` 키워드를 해당 자원 필드에 반드시 사용해야 합니다.
> 이렇게 하면, 해당 필드의 내용을 저장소(repository)에 쓰기 위해 필요한 직렬화 메커니즘을 방지할 수 있습니다.
> 저장소는 Saga가 역직렬화 된 이후, 자동으로 필요한 자원을 재 주입합니다.

```SimpleResourceInjector```는 주입되어야 할 자원의 묶음(collection)을 미리 정의하는 것을 허용합니다. ```SimpleResourceInjector```는 Saga의 메서드(setter)와 필드를 스캔하여 ```@Inject``` 애노테이션이 사용된 필드와 메서드를 찾게 됩니다.

설정 API를 사용할 경우, Axon은 ```ConfigurationResourceInjector```를 기본으로 사용합니다. ```ConfigurationResourceInjector```는 설정 객체(Configuration)내의 모든 사용 가능한 자원을 주입할 수 있습니다. ```EventBus```, ```EventStore```, ```CommandBus``` 그리고 ```CommandGateway```와 같은 콤포넌트들을 기본적으로 ```ConfigurationResourceInjector```을 통해 주입할 수 있으며, 주입이 필요한 콤포넌트들은 ```configurer.registerComponent()``` 메서드를 사용하여 주입할 수 있도록 등록할 수 있습니다.

```SpringResourceInejctor```는 스프링의 의존성 주입 메커니즘을 사용하여 aggregate에 필요한 자원을 주입합니다. 다시말해, 필요한 자원을 필드에 직접 주입하거나  setter 메서드를 통한 주입을 사용할 수 있습니다. 주입이 필요한 메서드 혹은 필드는 스프링이 필요한 자원을 주입할 수 있도록 ```@Autowired```와 같은 애노테이션을 사용해야 합니다.

### Saga Infrastructure, Saga 인프라 스트럭쳐(Infrastructure)
이벤트를 적절한 Saga 인스턴스에 전송해야 합니다. 이를 위해, 몇가지 인프라 스트럭쳐(infrastructure) 클래스들이 필요합니다. 가장 중요한 콤포넌트는 ```SagaManager```와 ```SagaRepository```입니다.

#### Saga Manager, Saga 매니져
이벤트를 처리하는 다른 콤포넌트들처럼, 이벤트 처리는 이벤트 프로세서(processor)에 의해 처리됩니다. 하지만 Saga들은 싱글톤(singleton) 인스턴스가 아니고 개별적인 생애 주기를 가지기 때문에, 별도로 관리될 필요가 있습니다.

Axon ```AnnotatedSagaManager```를 통해 Saga 인스턴스의 생애 주기를 관리 하도록 지원합니다. ```AnnotatedSagaManager```은 이벤트 처리를 위한 실제 인스턴스의 호출을 처리하기 위해, 이벤트 프로세서에 제공됩니다. (* EventProcessor의 구현체인 [SubscribingEventProcessor](http://www.axonframework.org/apidocs/3.0/org/axonframework/eventhandling/SubscribingEventProcessor.html) 혹은 [TrackingEventProcessor](http://www.axonframework.org/apidocs/3.0/org/axonframework/eventhandling/TrackingEventProcessor.html)의 생성자를 보면 [EventHandlerInvoker](http://www.axonframework.org/apidocs/3.0/org/axonframework/eventhandling/EventHandlerInvoker.html)를 인자로 받는 것을 볼 수 있습니다. 그리고 [AnnotatedSagaManager](http://www.axonframework.org/apidocs/3.0/org/axonframework/eventhandling/saga/AnnotatedSagaManager.html)는 [EventHandlerInvoker](http://www.axonframework.org/apidocs/3.0/org/axonframework/eventhandling/EventHandlerInvoker.html)의 하위 타입입니다.) ```AnnotatedSagaManager```는 관리되어야 할 Saga의 타입을 사용하여 초기화되며, Saga 유형을 저장하고 검색 할 수있는 Saga 저장소도 포함됩니다. 하나의 ```AnnotatedSagaManager```는 오로지 하나의 Saga 유형을 관리할 수 있습니다.

설정 API를 사용할 경우, Axon은 인지할 수 있는 대부분의 콤포넌트에 대한 기본값을 사용합니다. 하지만 ```SagaStore```의 구현체를 정의하여 사용하는 것을 권장합니다. ```SagaStore```은 Saga 인스턴스를 '물리적인' 저장소에 저장하는 메커니즘입니다. 기본으로 사용되는 ```AnnotatedSagaRepository```는 Saga 인스턴스를 저장하고 필요에 따라 저장된 Saga 인스턴스를 반환하기 위해 ```SagaStore```를 사용합니다.

```
Configurer configurer = DefaultConfigurer.defaultConfiguration();
configurer.registerModule(
	SagaConfiguration.subscribingSagaManager(MySagaType.class)
	// Axon은 메모리 기반 SagaStore를 기본으로 사용하지만, 다른 SagaStore를 사용하는 것을 권장합니다.
	.configureSagaStore(c -> new JpaSagaStore(...))
);

// 다른 방법으로, 모든 Saga 유형에 대해 단일 SagaStore를 등록하는것도 가능합니다.
configurer.registerComponent(SagaStore.class, c -> new JpaSagaStore(...));
```

#### Saga Repository and Saga Store, Saga 레퍼지토리와 Saga 스토어

```SagaManager```의 필요에 의해 ```SagaRepository```는 Saga들을 저장하고 반환하는 역활을 합니다. ```SagaRepository```는 Saga 인스턴스들의 식별자는 물폰 연결 값을 사용하여 특정 Saga 인스턴스들을 반환할 수 있습니다.

그런데 특별히 필요한 사항들이 있습니다. Saga들에 대한 동시성 처리는 매우 깨지기 쉽기 때문에, 레포지토리는 식별자의 동치로 구분되는 동일한 Saga 유형의 인스턴스는 반드시 JVM상에 단 하나의 인스턴스가 존재하는 것을 보장해야 합니다. 즉, 동일한 식별자를 가지는 Saga인스턴스는 반드시 하나만 존재해야 합니다. 이때 동일 여부는 식별자의 ```equals```메서드로 판단 합니다.

Axon은 ```AnnotatedSagaRepository``` 구현체를 제공합니다. ```AnnotatedSagaRepository```는 Saga 인스턴스를 조회하고 동일 시점에 단일 Saga 인스턴스에 접근하도록 합니다. ```AnnotatedSagaRepository```는 Saga 인스턴스의 영속화를 ```SagaStore```를 통해 수행합니다.

애플리케이션에서 사용하는 저장 엔진에 따라 구현체를 선택하여 사용합니다. Axon에서 제공하는 ```JdbcSagaStore```, ```InMemorySagaStore```, ```JpaSagaStore``` 그리고 ```MongoSagaStore```를 사용할 수 있습니다.

경우에 따라, Saga 인스턴스를 캐싱하여 사용하는 것이 더 좋을 수 있습니다. 이 경우, 캐싱 기능을 사용하기 위해 다른 구현체를 기반으로  ```CachingSagaStore```를 사용할 수 있습니다. ```CachingSagaStore```는 생성자에서 ```delegate```라는 SagaStore의 구현체를 인자로 받습니다. ```delegate```로 Axon에서 제공하는 ```JdbcSagaStore```, ```InMemorySagaStore```, ```JpaSagaStore``` 그리고 ```MongoSagaStore```과 같은 ```SagaStore```의 구현체를 사용하면 됩니다. ```CachingSagaStore```는 연속 기입 (write-through) 캐시입니다. 다시 말해, 데이터 안전성을 보장하기 위해 저장 작업이 즉시 백업 저장소로 즉시 전달됩니다.

##### JpaSagaStore, JpaSagaStore
```JpaSagaStore```는 Saga의 상태와 연결 값들을 저장하기 위해 JPA를 사용합니다. Saga 자체에 JPA 애노테이션들을 사용할 필요는 없습니다. Axon은  ```Serializer```를 사용하여 Saga들을직력화 합니다. (이벤트 직렬화와 비슷하게, ```JavaSerializer``` 혹은 ```XStreamSerializer```를 사용할 수 있습니다.)

```JpaSagaStore```는 ```EntityManager``` 인스턴스에 대한 접근을 제공하는 ```EntityManagerProvider```와 함께 설정되어야 합니다. 이런 추상화를 통해 애플리케이션에 관리되거나 컨테이너에서 관리되는 ```EntityManager```들을 사용할 수 있습니다. 선택적으로, Saga 인스턴스를 직력화 하기위한 시리얼라이져(serializer)를 정의하여 사용할 수 있으며, Axon에서는 ```XStreamSerializer```를 기본으로 사용합니다.

##### JdbcSagaStore
```JdbcSagaStore```는 Saga의 상태와 연결 값들을 저장하기 위해 일반 JDBC를 사용합니다. ```JpaSagaStore```와 비슷하게, Saga 인스턴스는 자신들이 어떻게 저장이 되는지에 대해 신경 쓸 필요는 없습니다. Saga 인스턴스들은 시리얼라이져(serializer)를 통해 직렬화 됩니다.

```JdbcSagaStore```는 ```DataSource``` 혹은 ```ConnectionProvider```과 함께 초기화 됩니다. 반드시 필요하진 않지만, ```JdbcSagaStore```를 ```ConnectionProvider```와 함께 초기화 할때, ```UnitOfWorkAwareConnectionProviderWrapper```로 ```ConnectionProvider```구현체를 한번 감싸서 ```UnitOfWorkAwareConnectionProviderWrapper```를 ```ConnectionProvider```로 사용하는 것을 권장합니다. 이렇게 하면, 현재의 작업 단위(Unit of Work)에 연결되어 있는 데이터베이스 커넥션(connection)이 있는지 확인하여 작업 단위내의 모든 작업이 단일 커넥션으로 처리되는 것을 보장할 수 있습니다.

JPA와는 달리, JdbcSagaRepository(* 3.x에서 deprecated된 것으로 보이고 SagaSqlSchema을 대신 사용하는 것으로 보임)는 Saga에 대한 정보를 저장하고 조회하기 위해 일반적인 SQL 구문을 사용합니다. 따라서 몇몇 쿼리 작업은 데이터베이스에 의존적인 SQL 문법을 사용해야 합니다.  또한 특정 데이터베이스 공급 업체가 제공하는 비 표준 기능을 사용하고자 하는 경우가 있을 수 있습니다. 이런 경우에 대처하기 위해, 직접 ```SagaSqlSchema``` 구현체를 작성하여 ```JdbcSagaStore```에 제공 할 수 있습니다. ```SagaSqlSchema```는 기반 데이터베이스위에서 레포지토리가 필요로 하는 모든 작업을 정의한 인터페이스로, 개별 작업에 사용되는 SQL 구문을 재정의하여 사용할 수 있도록 합니다. ```SagaSqlSchema```의 기본 구현체는 ```GenericSagaSqlSchema```이며, 그 외에 ```PostgressSagaSqlSchema```, ```Oracle11SagaSqlSchema``` 그리고 ```HsqlSagaSchema```들이 있습니다.

##### MongoSagaStore
이름에서 알 수 있듯이, ```MongoSagaStore```는 MongoDB 데이터베이스를 대상으로 Saga의 상태와 연결 값들을 저장합니다. ```MongoSagaStore```는 모든 saga들을 MongoDB 데이터베이스의 하나의 컬렉션(collection)에 저장하며 Saga인스턴스 당 하나의 도큐먼트(document)가 생성이 됩니다.

```MongoSagaStore```는 또한 고유한 식별자를 가지는 단일 Saga 인스턴스가 JVM상에 존재하도록 보장합니다. 따라서 동시성 문제로 인해 상태 값을 잃어버리지 않도록 보장해 줍니다.

```MongoSagaStore```는 ```MongoTemplate```과 선택적으로 ```Serializer```를 사용하여 초기화 됩니다. ```MongoTemplate```은 Saga를 저장하는 컬렉션에 대한 참조를 제공합니다. Axon은 ```MongoClient```와 데이터베이스 이름 그리고 Saga들이 저장되는 컬렉션 이름을 필요로 하는 ```DefaultMongoTemplate```을 제공합니다. 데이터베이스 이름과 컬렉션 이름은 생략할 경우, 데이터베이스 이름으로 "axonframework"가 사용되며 컬렉션 이름으로 "sagas"를 사용합니다.

##### Caching, 캐슁
Saga 스토리지(storage)로 데이터베이스를 사용할 경우, Saga 인스턴스를 저장하고 조회하는 것은 상대적으로 비용이 많이 드는 작업입니다. 특히 짧은 시간 동안 동일한 Saga 인스턴스를 조회하는 상황에선, 캐쉬를 사용하는 것이 애플리케이션의 성능 향상에 많은 도움이 될 수 있습니다.

Axon은 ```CachingSagaStore```의 구현체를 제공하며, ```CachingSagaStore```는 실제 Saga 저장소 역활을 하는 다른 ```SagaStore``` 구현체의 래퍼(wrapper)입니다. Saga 혹은 연결 값을 로딩할 때, ```CachingSagaStore```는 먼저 자신 내부의 캐쉬를 검색하고 캐쉬가 해당 객체를 가지고 있지 않으면 감싸고 있는 레퍼지토리를 통해 필요한 객체를 반환 받습니다. Saga 인스턴스를 저장할때, 감싸고 있는 레퍼지토리가 일관된 Saga의 상태를 가질 수 있도록 해당 레퍼지토리에게 저장 작업을 위임합니다.

캐쉬를 설정하기 위해선, 간단히 ```SagaStore``` 구현체를 ```CachingSagaStore```에 전달해주면 됩니다. ```CachingSagaStore```의 생성자는 세개의 매개변수를 받습니다. 첫번째 매개변수는 레퍼지토리이며, 두번째 매개변수는 연결 값에 대한 캐쉬 그리고 마지막 매개변수는 Saga에 대한 캐쉬입니다. 애플리케이션에 따라서 두번째와 세번째 인자들은 동일한 캐쉬 객체 혹은 다른 캐쉬 객체를 참조할 수도 있습니다.

## Testing, 테스팅
CQRS의 가장 큰 이점 중 하나이면서 특히 이벤트 소싱의 이점 중 하나는 이벤트와 명령으로 테스트를 순수하게 표현할 수 있다는 것입니다. 기능적 콤포넌트, 이벤트 그리고 명령들은 도메인 전문가 혹은 비지니스 소유자(owner)가 알기 쉽도록 되어 있습니다. 테스트가 이벤트와 명령으로 명확한 기능적 의미를 표현하는 것 뿐만 아니라, 구현 선택에 거의 의존하지 않는 다는 것을 의미합니다.

이번 장에서 설명할 기능들은 ```axon-test``` 모듈을 필요로 합니다. ```axon-test``` 모듈은 메이븐 의존성 설정을 아래와 같이 설정하여 사용할 수 있습니다. 혹은 전체 패키지를 다운로드 하여 사용할 수 있습니다.

```
<dependency>
	<groupId>org.axonframework</groupId>
   <artifactId>axon-test</actifactId>
   <scope>test</scope>
</dependency>
```

이번 장에서 설명할 픽스쳐(fixture)는 JUnit과 TestNG와 같은 테스트 프레임워크와 연동이 가능합니다.

### Command Component Testing, 명령 콤포넌트 테스팅
명령 처리 콤포넌트는 가장 큰 복잡성을 가지는 CQRS 기반 아키텍쳐의 전형적인 콤포넌트입니다. 다른 콤포넌트 보다 더 복잡하다는 것은, 명령 콤포넌트를 테스트 하기 위한 추가적인 필요 사항들이 있다는 것을 말합니다.

보다 더 복잡하긴 하지만, 명령 처리자 콤포넌트의 API는 상당히 쉽습니다. 명령 처리자 콤포넌트는 처리할 명령을 받고, 이벤트를 발생시키는 기능을 수행합니다. 몇몇 경우에, 명령 실행의 일부로써 쿼리를 수행 할 수도 있습니다. 그 외에는, 명령들과 이벤트들가 API의 유일한 부분입니다. 따라서 명령과 이벤트들로 완전한 테스트할 시나리오를  작성할 수 있습니다. 일반적으로 테스트는 다음과 같은 형태로 작성 할 수 있습니다.

* given: 주어진 이미 발생한 특정 이벤트 들이 있다,
* when: 해당 명령을 실행할 때,
* expect: 이런 이벤트들이 게시되고 혹은 저장된다.

Axon Framework은 위에서 설명한 형태의 테스트를 작성할 수 있도록 테스트 픽스쳐를 제공합니다. ```AggregateTestFixture```는 특정 인프라 스트럭쳐의 설정, 필요한 명령 처리자와 레퍼지토리의 조합 그리고 테스트 시나리오를 given-when-then 형태의 이벤트와 명령들로 작성할 수 있도록 해줍니다.

다음의 예제 코드를 통해 JUnit4 기반으로 테스트 픽스쳐를 사용하여 given-when-then 형태의 테스트 코드를 작성하는 방법을 볼 수 있습니다.

```
public class MyCommandComponentTest {
	private FixtureConfiguration fixture; // 테스트 픽스쳐

	@Before
	public void setUp() {
		fixture = new AggregateTestFixture(MyAggregate.class);
	}

	@Test
	public void testFirstFixture() {
		fixture.given(new MyEvent(1))
				.when(new TestCommand())
				.expectSuccessfulHandlerExecution()
				.expectEvents(new MyEvent(2));

		/*
		위 4줄의 코드가 실제 테스트 시나리오와 시나리오에 대한 기대값을 정의한 것입니다.
		첫번째 줄의 코드는 이미 발생한 이벤트를 정의한 것이며, 이 이벤트들은 테스트 상에서 aggregate의 상태를 정의합니다.
		좀 더 구체적으로 말하면, aggregate가 로딩되었을때, 이벤트 저장소가 반환하는 이벤트들을 말합니다.
		두번째 줄의 코드는 시스템 상에서 처리해야 할 명령을 정의 합니다.

		마지막 두 줄의 코드는 기대 행위와 기대 값을 정의한 것으로, 이 중 첫번째 코드는 명령 처리가자 성공적으로 명령을 처리했는지를 확인하는 코드이며, 마지막 줄의 코드는 명령의 실행 결과로 발생한 단일 이벤트가 기대한 이벤트와 맞는지를 검증하는 코드입니다.
		*/				
	}
}

```
given-when-then 테스트 픽스쳐는 설정, 실행 그리고 검증의 세 단계로 정의 됩니다. 개별 단계들은 각기 다른 인터페이스(interface)로 표현되며, 각각의 인터페이스는 다음과 같습니다.

* 설정: FixtureConfiguration
* 실행: TestExecutor
* 검증: ResultValidator

```Fixtures``` 클래스의 정적 메서드인 ```newGivenWhenThenFixture()``` 메서드는 설정에 대한 FixtureConfiguration의 참조를 제공합니다. (* ```Fixtures``` 클래스는 2.x 버전 이하에서만 제공됩니다.)

> Note
> 위의 세 단계간에 이동을 최적화 하기 위해선, 위의 예제 코드와 같이 이 메서드들이 반환하는 인터페이스를 사용하는 것이 제일 좋습니다.

설정 단계(예, 첫번째 "given" 이전)동안, 테스트 실행에 필요한 빌딩 블록들을 제공할 수 있습니다. 테스트를 위해 특별히 사용될 이벤트 버스, 커맨드 버스 그리고 이벤트 저장소를 픽스쳐의 일부분으로 제공됩니다. ```FixtureConfiguration```를 통해 이벤트 버스, 커맨드 버스 그리고 이벤트 저장소에 대한 참조를 얻을 수 있습니다. (이벤트 버스, 커맨드 버스 그리고 이벤트 저장소에대한 getter 메서드를 제공합니다.) Aggregate에 직접 등록되지 않은 명령 처리자들은 ```FixtureConfiguration```의 ```registerAnnotatedCommandHandler``` 메서드를 통해 명시적으로 설정이 되어야 합니다. 애노테이션 기반의 명령 처리자 외에도 테스트 관련 인프라 설정 방법을 정의하는 다양한 구성 요소와 설정을 구성 할 수 있습니다.

픽스쳐가 설정이 되면, 이미 발생한("given") 이벤트를 정의 할 수 있습니다. 테스트 픽스쳐는 이런 이벤트들을 ```DomainEventMessage```로 감싸서 처리합니다. 주어진("given") 이벤트들이 Message의 구현체라면, 메세지의 페이로드와 메타 데이터는 ```DomainEventMessage```에 포함됩니다. 그렇지 않은 경우는, 주어진 이벤트는 페이로드로 사용이 됩니다. 0에서 부터 시작하는 일련의 ```DomainEventMessage```의 일련번호는 순차적으로 증가합니다.

혹은 주어진("given")명령어를 제공할 수 도 있습니다. 이 경우, 테스트 대상이 되는 실제 명령이 실행될때, 주어진 명령어에 의해 발생되는 이벤트들이 Aggregate의 이벤트 소스로 사용됩니다. ```givenCommands(...)``` 메서드를 사용하여 명령 객체들을 설정합니다.

실행 단계에서는 명령 처리자가 처리할 명령을 제공해야 합니다. 해당 명령 처리를 위해 호출되는 처리자(Aggregate 상의 처리자 혹은 외부 처리자)의 작동은 감시되고 검증 단계에 등록된 기대 사항들과 비교 됩니다.

> Note
> 테스트의 실행 단계 동안, Axon을 통해 테스트 하에서의 Aggregate의 비 정상적인 상태 변경을 발견할 수 있습니다.
> Aggregate가 "주어진(given)" 이벤트들과 저장된 이벤트들로부터 복원(sourced)된 상태에서 Aggregate의 상태를 변경 시킬 명령을 실행 한 후에 Aggregate의 상태를 비교하여 비 정상적인 상태 변경을 발견합니다.
> 만약 상태가 기대한 상태와 동일하지 않다면, Aggregate의 이벤트 처리자 메서드가 아닌 다른 곳에서 Aggregate의 상태가 변경된 것 입니다.
> 정적이고 transient한 필드들은 비교 대상에서 제외 됩니다.
> 비 정상적 상태 변경의 감지 기능은 ```setReportIllegalStateChange(boolean reportIllegalStateChange)``` 메서드를 통해 키거나 끌 수 있습니다.

마지막 단계인 검증 단계에선, 반환 값들과 이벤트들을 통해 명령 처리 콤포넌트의 작업들을 확인할 수 있습니다.

테스트 픽스쳐를 통해 명령 처리자의 반환값을 검증 할 수 있습니다. 명시적으로 기대 되는 반환 값을 정의하거나 해당 메서드가 성공적으로 결과를 반환 했는지를 확인 할 수 있습니다. 또한 명령 처리자가 던질 수 있는 예외를 기대값으로 설정하여 확인 할 수 있습니다.

게시된 이벤트를 검증할 수 있으며, 이벤트를 확인할 수 있는 방법은 두가지 방법이 있습니다.

첫번째 방법은 실제 이벤트와 문자 그대로 비교되어야하는 이벤트 인스턴스를 전달하는 것입니다.기대 이벤트의 모든 속성들을 실제 발생한 이벤트의 동일 속성들과 비교(```equals()``` 메서드 사용)합니다. 만약 한 속성이라도 동일하지 않다면, 해당 테스트는 실패할 것이고 상세한 결과를 담음 에러 결과를 생성합니다.

다른 검증 방법은 Hamcrest 라이브러리의 Matcher들을 사용하여 기대값들을 표현하는 것입니다. ```Matcher```는 ```matches(Object)```와 ```describe(Description)``` 메서드를 포함하고 있는 인터페이스입니다. ```matches(Object)``` 메서드는 주어진 객체가 비교 대상과 일치 하는지를 검증하여 참 혹은 거짓을 반환합니다. ```describe(Description)``` 메서드는 기대 사항을 표현할 수 있도록 해줍니다. 예를 들어, "GreaterThanTwoMatcher"에 "다른 두 이벤트보다 큰 값을 가진 이벤트"라는 설명을 추가할 수 있습니다. 설명(Description)은 테스트가 실패 했을 때 실패한 이유에 대한 에러 메세지를 표현하는데 사용할 수 있습니다.

이벤트들에 대한 ```matcher```들을 생성하는 것은 장황하고 에러를 발생 시킬 수 있습니다. 간단히 코드를 작성하기 위해, Axon에서 제공하는 이벤트 검증을 위한 ```matcher```들을 사용하여 발생한 이벤트들을 검증할 수 있습니다.

아래의 예를 통해 이벤트 목록에 대한 ```matcher```들과 목적을 확인 할 수 있습니다.

* List with all of: ```Matchers.listWithAllOf(event matchers...)```

    해당 matcher는 주어진 이벤트 matcher가 실제 발생한 이벤트들 중 적어도 하나 이상의 이벤트와 일치하면 성공 결과를 반환합니다. 여러개의 matchers가 같은 이벤트와 맞는지,리스트의 이벤트가 어떤 matchers와도 일치하지 않든 상관 없습니다. // TODO 두번째 "리스트의 이벤트가 어떤 matchers와도 일치하지 않든 상관 없습니다." 재 확인 필요.
*  List with any of: ```Matchers.listWithAnyOf(event matchers...)```

    이 matcher는 주어진 이벤트 matcher들 중 하나 혹은 그 이상이 실제 발생한 이벤트들 중 하나 혹은 그 이상의 이벤트와 일치하면 성공 결과를 반환합니다. 몇개의 matcher들은 다수의 이벤트에 일치하는 반면, 일부 matcher들은 전혀 일치 하지 않을 수 있습니다.

*  Sequence of Events: ```Matchers.sequenceOf(event matchers...)```

    실제 발생한 이벤트와 발생 순서가 주어진 이벤트 matcher와 동일한 순서 인지를 검증하기 위해 이 matcher를 사용합니다. 개별 matcher는 이전 matcher가 일치 여부를 검증한 이벤트 다음의 이벤트를 검증하여 검증 결과를 반환합니다. 즉, 주어진 이벤트 matcher가 모두 검증에 성공을 하면 결과는 성공이 됩니다. 따라서 matcher와 일치하지 않는 이벤트가 나타날 수 있는 가능성이 있습니다.
    만약 모든 이벤트를 검증 한 후에도 matcher가 남아 있다면, 남아 있는 matcher들은 ```null```과 일치 여부를 검증하게 됩니다. 따라서 남아 있는 matcher들이 ```null```을 허용하는지 아닌지에 따라 성공 여부가 결정됩니다.

* Exact sequence of Events: ```Matchers.exactSequenceOf(event matchers...)```

    "Sequence of Events"의 변형된 matcher로 "Sequence of Events"에서는 matcher와 일치하지 않는 이벤트가 발생할 수 있었으나, 이 matcher는 일치하지 않는 이벤트를 허용하지 않습니다. 따라서 정확한 순서로 이벤트가 발생했는지를 검증 할 수 있습니다.

편의상, 자주 사용되는 이벤트 matcher들만 살펴보았습니다. 다음은 단일 이벤트에 대한 matcher 메서드를 살펴보겠습니다.

* Equal Event: ```Matchers.equalTo(instance...)```

    해당 matcher는 주어진 객체와 발생한 이벤트가 의미상 동일한지를 검증합니다. 두 객체간 모든 속성들을 비교하며 ```null```비교를 허용하여 문제없이 ```null```비교를 할 수 있습니다. 다시 말하면 ```equals```메서드를 구현하지 않은 이벤트들도 비교할 수 있습니다. 주어진 매개변수 객체의 속성들에 저장되어있는 객체들은 ```equals```메서드를 통해 비교하므로, 속성 객체는 ```equals```를 정확히 구현해야 합니다.

* No More Events: ```Matchers.andNoMore()``` 혹은 ```Matchers.nothing()```

    위 matcher는 ```null```값을 비교하는 matcher로 "Exact sequence of Events"의 가장 마지막에 사용되어 일치하지 않는 이벤트들이 남아 있지 않도록 합니다.

위의 matcher들은 이벤트의 목록에 대해 검증을 하는데, 때로는 메세지의 페이로드 부분만 검증해야 할 수 있습니다. 이런 경우 사용할 수 있는 matcher들은 아래와 같습니다.

* Payload Matching: ```Matchers.messageWithPayload(payload matcher)```

    실제 메세지의 페이로드와 주어진 페이로드 matcher가 일치하는지를 검증합니다.

* Payloads Matching: ```Matchers.payloadsMatching(list matcher)```

    실제 메세지들의 페이로드들이 주어진 페이로드들과 일치하는지를 검증합니다. 인자로 주어진 matcher는 메세지들의 페이로드를 포함하는 목록과 반드시 일치해야 합니다. "Payload Matching" matcher는 일반적으로 페이로드 matcher들의 반복을 방지하기 위한 페이로드 matcher들을 감싸는 외부 matcher로 사용이 됩니다.

아래의 예제 코드를 통해 위에서 살펴본 matcher들의 사용법에 대해 간단히 살펴보겠습니다. 아래의 예제를 통해 두개의 이벤트가 게시될 것으로 예상하며, 첫번째 이벤트는 반드시 "ThirdEvent"이어야 하고 두번째 이벤트는 "aFourthEventWithSomethingSpecialThings"이어야 합니다. 세번째로 발생하는 이벤트는 없고 이를 검증하기 위해 "andNoMore" matcher를 사용하였습니다.

```
fixture.given(new FirstEvent(), new SecondEvent())
		.when(new DoSomethingCommand("aggregateId"))
		expectEventsMatching(
			exactSequenceOf(
				// 페이로드 부분만 검증합니다.
				mesageWithPayload(equalTo(new ThirdEvent())),

				// 메세지에 대한 검증을 합니다.
				aFourthEventWithSomethingSpecialThings(),

				//더 이상의 이벤트가 없음을 검증합니다.
				andNoMore()
			)
		);

		//혹은 페이로드 부분만을 검증할 수 있습니다.
		.expectEventMatching(
			payloadsMatching(
				exactSequenceOf(
					// 페이로드만 검증하기때문에, equalTo를 바로 사용합니다.
					equalTo(new ThirdEvent()),					
					//아래의 코드 또한 페이로드 부분만 검증합니다.
					aFourthEventWithSomeSpecialThings(),

					// 더 이상의 이벤트가 없음을 검증합니다.
					andNoMore()
				)
			)
		);

```

### Testing Annotated Sagas, 애노테이션 기반의 Saga 테스트 하기
명령 처리 콤포넌트와 비슷하게, Saga들도 이벤트에 응답하는 명확하게 정의된 인터페이스들을 가지고 있습니다. 반면에, Saga는 종종 시간의 개념을 가지고 있으며 이벤트 처리 과정의 일부로 다른 콤포넌트들과 연동할 수 있습니다. Axon Framwork의 테스트 지원 모듈을 통해 Saga에 대한 테스트를 작성할 수 있습니다.

각각의 테스트 픽스쳐는 이전 장에서 설명한 명령 처리 콤포넌트와 유사한 세단계를 포함하고 있습니다.

* given: 주어진 이미 발생한 특정 이벤트 들이 있다,(특정 aggregate로 부터 발생한 이벤트)
* when: 이벤트가 도착했거나 시간이 경과하였을때,
* expect: 특정 기대 행위 혹은 상태가 되어야 한다.

"given"과 "when" 단계들 모두 연동의 일부분으로 이벤트를 받습니다. "given" 단계 동안, 가능한 경우 생성된 명령들과 같은 모든 부작용(side-effect)들은 무시됩니다. 반면 "when" 단계 동안, Saga로부터 발생된 이벤트들과 명령들은 기록되고 확인 할 수 있습니다.

다음의 코드 예제를 통해 송장에 대한 결제가 30일 이내에 이루어 지지 않았을때 통지를 보내는 saga를 테스트 하기 위한 테스트 픽스쳐의 사용법을 확인 할 수 있습니다.

```
FixtureConfiguration fixture = new SagaTestFixture(InvoicingSaga.class);
fixture.givenAggregate(invoiceId).published(new InvoiceCreatedEvent())
		.whenTimeElapses(Duration.ofDays(31))
		.expectDispatchedCommandsMatching(
			Matchers.listWithAllOf(
				aMarkAsOverdueCommand()
			)
		);

		// 혹은 명령 메세지의 페이로드만 일치하는지 검증할 수 있습니다.
		.expectDispatchedCommandsMatching(
			Matchers.payloadsMatching(
				Matchers.listWithAllOf(
					aMarkAsOverdueCommand()
				)
			)
		);
```

Saga들은 명령 처리 결과을 받으면 실행되는 콜백(callback)을 사용하여 명령을 전송할 수 있습니다.  테스트 상에서 실제로 완료되는 명령 처리는 없기 때문에, 명령을 보내는 행위는 ```CallbackBehavior``` 객체를 사용하여 정의합니다. ```CallbackBehavior``` 객체는 ```setCallbackBehavior()``` 메서드를 통해 픽스쳐에 등록하고 명령이 전달될때 콜백을 호출해야 하는지 여부 및 그 방법에 대해 정의 합니다. ```CallbackBehavior```는 커맨드 버스가 콜백 메서드와 함께 전송된 명령을 받았을 때 호출이 되며 ```CallbackBehavior```의  ```handle``` 메서드의 반환 값은 해당 콜백의 ```onSuccess``` 메서드를 호출할때 사용이 됩니다.

```CommandBus```를 직접 사용하지 않고, ```CommandGateWay```를 사용할 수 있습니다. 사용법은 아래를 참조 하면 됩니다.

종종, Saga들은 자원들을 필요로 합니다. 해당 자원들은 Saga 상태의 일부분이 아니지만, Saga가 생성되거나 로딩된 후에 주입됩니다. 테스트 픽스쳐를 통해  Saga가 필요로 하는 자원을 등록할 수 있습니다. 자원을 등록하기 위해서는, 등록할 자원을 인자로 주어 ```fixture.registerResource(Object)``` 메서드를 호출하면 테스트 픽스쳐는 적당한 setter 메서드 혹은 ```@Inject``` 애노테이션 필드를 찾아 호출하여 해당 자원을 주입합니다.

> Tip
> Mockito 혹은 Easymock과 같은 Mock 객체들을 Saga의 자원으로 주입하여 사용하면 외부 자원과 Saga간의 연동이 제대로 이루어 지는지를 확인 할 수 있습니다.

```CommandGateWay```는 Saga로 하여금 명령을 보다 더 쉽게 전송할 수 있도록 해줍니다. 재정의한 커맨드 게이트웨이를 사용하면, 테스트 상에서 사용할 mock 혹은 stub 객체를 보다 쉽게 만들어 사용할 수 있습니다. 하지만 mock 혹은 stub를 제공할때, 실제 명령이 전달되지 않아 테스트 픽스쳐를 통해 전송된 명령을 확인할 수 없게 됩니다. 그러므로 테스트 픽스쳐가 제공하는 ```registerCommandGateway(Class)```와 ```registerCommandGateway(Class, stubImplementation)``` 메서드를 통해 커맨드 게이트웨이 객체를 등록하고 선택적으로 커맨드 게이트웨이의 행위를 정의하는 mock(혹은 stub)을 허용하는 방법을 사용해야 합니다. 두 메서드 모두 사용할 게이트웨이를 나타내는 주어진 클래스의 인스턴스를 반환합니다. 이 반환된 인스턴스를 또한 자원으로 등록할 수 있고 등록된 인스턴스는 주입하여 사용할 수 있습니다.

게이트웨이를 등록하기 위해 ```registerCommandGateway(Class)```메서드를 사용하면, 픽스쳐가 관리하는 커맨드 버스에 명령을 전달합니다. 게이트웨이의 행위는 대부분 픽스쳐상에 정의된 ```CallbackBehavior```에 의해 정의됩니다. 만약 명시적인 ```CallbackBehavior```가 주어지지 않는다면, 콜백들은 호출되지 않으므로 게이트웨이에 대한 어떤 반환 값도 제공되지 않습니다.

게이트웨이를 등록하기 위해 ```registerCommandGateway(Class, stubImplementation)```메서드를 사용할때, 두번째 매개변수는 게이터웨이의 동작을 정의하는데 사용됩니다.

테스트 픽스쳐는 가능하면 시스템 시간이 오래 걸리는 것을 제거하려고 합니다. 다시 말해, ```whenTimeElapses() ``` 메서드를 사용하여 명시적으로 선언하지 않는 한, 테스트 실행 동안 시간이 경과하지 않는 것처럼 보입니다. 모든 이벤트들은 테스트 픽스쳐가 생성된 시점의 타임 스탬프를 가지고 있습니다.

테스트 동안 시간을 멈춰 놓으면, 예정에 맞춰 언제 이벤트가 게시될지 더 쉽게 예측할 수 있습니다. 만약 30초 이내에 게시될 예정인 이벤트를 검증하는 테스트가 있다면, 실제 예정과 테스트 실행간에 걸리는 시간에 상관없이 30초가 걸릴 것입니다.

> Note
> 픽스쳐는 이벤트를 계획하고 시간을 앞당기는 등의 시간에 기반한 활동을 위해 ```StubScheduler```를 사용합니다. 픽스쳐는 Saga 인스턴스에 보내진 모든 이벤트의 타임 스탬프를 ```StubScheduler```의 시간으로 설정 할 것입니다.
> 즉, 시간은 픽스쳐가 시작하게 되면 '멈춰진' 상태가 되고, ```whenTimeAdvanceTo```와 ```whenTimeElapses``` 메서드를 사용하여 결정적으로 시간을 앞당겨 집니다.

특정 시점에 발생될 이벤트를 테스트 해야 한다면, 테스트 픽스쳐에 독립적인 ```StubEventScheduler```를 사용할 수 있습니다. ```EventScheduler```의 구현체인 ```StubEventScheduler```를 통해 어떤 이벤트가 어느 시간으로 예정이 되어 있는지를 검증할 수 있고 시간의 경과를 조작할 수 있는 옵션들을 사용할 수 있습니다. 특정 ```기간(Duration)```만큼 시간을 앞당겨 특정 날짜와 시간으로 조정하거나 다음에 예정된 이벤트로 시간을 앞당길 수 있습니다. 이 모든 작업은 진행된 일정내의 예정된 이벤트들을 반환합니다.

## Command Dispatching
명시적인 명령 전달 메카니즘을 사용하면 좋은 점들이 있습니다. 그 중 첫번째는 클라이언트의 의도를 명확히 묘사하는 단일 객체가 있다는 것입니다. 명령을 로깅하여, 클라이언트의 의도와 관련 데이터들을 나중에 사용할 목적으로 저장할 수 있습니다. 명령 처리를 통해 예를 들어 웹 서비스등을 통해 원격의 클라이언트에게 명령 처리 콤포넌트를 쉽게 노출 시킬 수 있습니다. 시작 상황(given), 실행할 명령(when) 그리고 기대 결과(then)로 이벤트들과 명령들을 열거 하는 형식으로 테스트 스크립트를 정의 할 수 있기 때문에 테스트를 보다 쉽게 수행할 수 있습니다. 마지막 주요 이점은 동기와 비동기간 변경뿐만 아니라 로컬 기반 명령 처리를 분산 환경의 명령 처리로의 변경을 매우 쉽게 처리할 수 있다는 것입니다.

명시적인 명령 객체를 사용한 명령 전달 처리만이 위와 같은 처리를 하는데 유일한 방법은 아닙니다. Axon의 목적은 특정 방법을 규정하는 것이 아니라 기본적 구현으로서 모범 사례를 제공하면서 사용자 나름의 방법을 지원하는 것입니다. 따라서 여전히 명령 실행을 위한 서비스 계층(layer)를 사용할 수 있으며 서비스 계층의 메서드는 작업 단위를 시작하고 메서드의 종료에 따라서 커밋 혹은 롤백을 수행합니다.

다음장에서는, Axon Framework을 사용하여 명령 전달 인프라 스트럭쳐의 구성과 관련된 작업의 개요를 살펴 볼 것입니다.

### Command Gateway, 커멘드 게이트웨이
Command Gateway는 명령 전달 메카니즘에 대한 인터페이스입니다. 명령을 전달하기 위해 게이트웨이를 사용할 필요는 없지만 일반적으로 게이트웨이를 사용하는 것이 가장 쉬운 방법입니다.

Command Gateway를 사용하기 위한 두가지 방법 중, 첫번째 방법은 Axon에서 제공하는 ```CommandGateway``` 인터페이스와 ```DefaultCommandGateway``` 구현체를 사용하는 것입니다. Command Gateway를 통해 명령을 전송하고 결과를 동기적으로 기다리기는 처리를 할 수 있습니다. 또한 시간 제한 및 비 동기적인 처리를 할 수 있습니다.

다른 방법은 가장 유연한 방법으로, ```CommandGateWayFactory```를 사용하여 거의 모든 인터페이스를 Command Gateway로 변경할 수 있는 방법입니다. 정확한 타입 정보와 비지니스 관련 예외를 사용하여 정의된 애플리케이션 인터페이스를 정의하여, Axon을 통해 해당 인터페이스를 런타임(실행 시점)에 자등으로 해당 인터페이스에 대한 구현체를 생성할 수 있습니다.

### Configuring and Command Gateway
사용자 정의 게이트웨이와 Axon에서 제공되는 게이트웨이 모두 커맨드 버스(command bus)에 접근 할 수 있도록 설정이 필요합니다. 또한, 커맨드 게이트웨이는 ```RetryScheduler```, ```CommandDispatchInterceptor``` 그리고 ```CommandCallback```을 통한 설정을 해야 합니다.

```RetryScheduler```을 통해 명령 실행이 실패했을 경우, 명령 처리에 대한 재시도에 대한 스케쥴링을 할 수 있습니다. ```RetryScheduler```의 구현체인 ```IntervalRetryScheduler```를 통해, 명령 처리가 성공할때까지 일정한 간격으로 주어진 명령 처리를 재시도 하거나, 최대 재시도 횟수 만큼 명령을 처리를 재차 시도 할 수 있습니다. 하지만 일시적인 예외가 아닌 예외 사항으로 명령 처리에 실해한 경우에는 재시도는 이루어 지지 않습니다. ```RetryScheduler```는 명령 처리가 ```RuntimeException```으로 실패한 경우에만 호출됩니다. 확인된 예외(Checked exceptions)는 "비지니스 예외"로 간주되어 절대 재시도를 하지 않습니다. 분산 환경에서 커맨드 버스를 사용하여 명령 처리를 할때 주도 ```RetryScheduler```를 사용합니다. 한 노드가 실패했을때, ```RetryScheduler```를 사용하여 다른 사용 가능한 노드로 해당 명령을 분산하여 명령이 처리될 수 있도록 조치합니다. (참조: [분산 커맨드 버스](// TODO - link update))

```CommandDispatchInterceptor```를 통해, 커맨드 버스로 명령이 전달되기 전에 명령 메세지(```CommandMessage```)를 변경할 수 있습니다. ```CommandDispatchInterceptor```는 ```CommandBus```에 설정이 되지만, 메세지들이 게이트웨이를 통해서 전달이 되는 경우에만 설정된 인터셉터들은 호출됩니다. 예를 둘어, 인터셉터들을 사용하여 메타 데이터를 추가하거나 검증을 수행할 수 있습니다.

```CommandCallback```은 각각의 전송된 명령에 대해 호출이 됩니다. 명령의 타입에 상관없이, 게이트 웨이를 통해 전송되는 모든 명령에 대해 일반적인 기능을 수행할 수 있습니다.

### Creating a Custom Command Gateway, Command Gateway 재정의 하기
Command Gateway를 재정의한 인터페이스를 사용할 수 있습니다. 인터페이스에 정의된 각각의 메서드들은 메서드의 매개변수, 반환 타입 그리고 선언된 예외 사항으로 어떤 기능을 하는지 알 수 있습니다. 인터페이스로 선언된 게이트웨이를 사용하면 사용의 간편성뿐만 아니라 mock을 사용하여 테스트를 보다 쉽게 진행할 수 있도록 해줍니다.

다음을 통해 매개변수들이 Command Gateway의 기능(행위)에 어떤 영향을 미치는지 살펴보겠습니다.

* 첫번째 매개변수는 전달될 실제 명령 객체이어야 합니다.
* ```@MetadataValue``` 애노테이션이 사용된 매개변수의 값은 애노테이션 매개변수로 전달된 식벽자를 통해 메타데이터 필드에 할당이 됩니다.
* ```MetaData``` 타입의 매개변수는 커멘드 메세지(CommandMessage)의 ```MetaData```와 합쳐지게 되며, 동일한 키를 가지는 항목은 이후에 정의된 메타 데이터 항목으로 덮어 쓰게 됩니다.
* ```CommandCallback``` 타입의 매개변수는 명령 처리 이후 호출되는 ```onSuccess``` 혹은 ```onFailure``` 메서드를 가집니다. 하나 이상의 콜백을 전달할 수 있으며, 반환 값과 함께 결합되어 처리 됩니다. 이 경우, 콜백의 호출은 항상 반환값(혹은 예외)과 일치하게 됩니다. (//TODO - 호출에 필요한 매개변수로 반환값을 사용하는 것으로 생각됨)
* 마지막 두개의 매개변수들은 각각 ```long```(혹은 ```int```) 그리고 ```TimeUnit``` 타입일 것입니다. 이 경우, 매개변수들이 나타내는 시간동안 해당 메서드는 블럭킹되어 메서드가 완료될때까지 대기합니다. 타임아웃 발생 시에 대한 반응 및 처리는 메서드에 선언된 예외에 따라 달라지게 됩니다. 메서드의 다른 속성들이 블럭킹을 방지하게 되면, 타임아웃은 절대 발생하지 않습니다.

메서드에 선언된 반환 타입 또한 아래와 같이 메서드의 행위에 영향을 미칩니다.

* ```Void```반환 타입은 해당 메서드에 타임아웃 혹은 선언된 예외 사항같은 대기해야 한다는 지시가 없다면, 해당 메서드가 즉시 반환하도록 합니다.
* ```Future```, ```CompletionStage``` 그리고 ```CompletableFuture```의 반환 타입은 메서드가 즉시 반환 되도록 합니다. 메서드가 반환하는 ```CompletableFuture``` 인스턴스를 통해 명령 처리자의 결과에 접근할 수 있습니다. 메서드에 선언된 예외나 타임아웃은 무시됩니다.
* 다른 반환 타입들을 사용하면, 해다 메서드는 사용 가능한 결과가 나올때까지 블럭킹 되어 결과를 기다리게 됩니다. 결과값은 반환 타입으로 캐스팅되며, 타입이 맞지 않으면 ```ClassCastException```이 발생하게 됩니다.

메서드에 선언된 예외 사항들은 아래와 같은 영향을 미칩니다.

* 명령 처리자(혹은 인터셉터)에서 확인된 예외(checked exception)을 던진다면, 동일한 예외가 해당 메서드에서도 발생 될 것입니다. 만약 해당 예외를 선언하지 않는다면 발생한 예외는 ```RuntimeException```인 ```CommandExecutionException```로 감싸져서 발생하게 됩니다.
* 타임아웃이 발생하면, 해당 메서드는 ```null```을 기본으로 반환합니다. ```null```을 반환하는 대신, ```TimeoutException```을 선언하여 ```TimeoutException```을 대신 던지도록 할 수 있습니다.
* 결과를 기다리는 동안 대기 중인 쓰레드가 중단(interrupted)된다면, 해당 메서드는 기본적으로 ```null```을 반환합니다. 이 경우, 쓰레드가 중단된 것을 나타내는 값(interrupted flag)을 해당 쓰레드에 설정합니다. 해당 메서드에 ```InterruptedException```을 선언하여, interrupted flag가 설정되는 것 대신 ```InterruptedException``` 예외를 발생 시킬 수 있습니다. 예외가 발생하였을 경우, interrupted flag 값은 Java specification에 따라 삭제됩니다.
* 다른 런 타임 예외들를 메서드에 선언하면, API를 사용하는 사용자에게 설명을 주는 것 외에 다른 효과는 없습니다.

마지막으로, 애노테이션들을 사용할 수 있습니다.

* ```@MetaDataValue``` 애노테이션을 사용하여 메타 데이터 값들을 메서드의 인자로 받아 볼 수 있습니다. 메타 데이터의 키값은 애노테이션의 매개변수로 선언하여 사용합니다.
* ```@Timeout``` 애노테이션을 메서드에 사용하면, 최대 애노테이션에 지정된 시간만큼 메서드는 블럭킹되어 결과를 기다리게 됩니다. 단 메서드에 타임아웃 매개변수를 선언하면 이 애노테이션은 무시됩니다.
* ```@Timeout```을 클래스에 사용하면,  해당 클래스에 선언된 모든 메서드들에 해당 애노테이션이 적용됩니다. 따라서 클래스의 모든 메서드들은 최대 애노테이션에 지정된 시간만큼 메서드는 블럭킹되어 결과를 기다리게 됩니다. 단 메서드에 선언된 ```@Timeout``` 애노테이션이 우선 적용됩니다.

```
public interface MyGateway {
  // 전송하고 잊어버리는 전략입니다.
  void sendCommand(MyPayloadType command);

  // "userId"라는 메터 데이터를 가지고 10초의 타임아웃을 가지는 메서드입니다.
  @Timeout(value = 10, unit = TimeUnit.SECONDS)
  ReturnValue sendCommandAndWaitForAResult(MyPayloadType command,  @MetaDataValue("userId") String userId);

  // 타임아웃이 발생했을때, 예외를 던지는 메서드입니다.
  @Timeout(value = 10, unit = TimeUnit.SECONDS)
  ReturnValue sendCommandAndWaitForAResult(MyPayloadType command) throws TimeoutException,  InterruptedException;

  // 아래의 메서드를 호출하는 client가 timeout 값을 지정합니다.
  void sendCommandAndWaitForAResult(MyPayloadType command, long timeout, TimeUnit unit) throws TimeoutException, InterruptedException;
}

// 게이트웨이 설정
CommandGatewayFactory factory = new CommandGatewayFactory(commandBus);

// commandBus는 'configurer.buildConfiguration()' 메서드가 반환하는 Configuration객체를 통해 얻을 수 있습니다.
MyGateway myGateway = factory.createGateway(MyGateway.class);
```

## The Command Bus, 커맨드 버스
커맨드 버스(Command Bus)는 각각의 명령 처리자에게 명령들을 전달하기 위한 메커니즘을 말합니다. 각각의 명령은 정확히 하나의 명령 처리자에게 전달됩니다. 만약 명령을 처리할 처리자가 없다면, ```NoHandlerForCommandException```이 발생합니다. 동일한 명령 타입에 대해 다수의 명령 처리자를 등록하게 되면, 마지막에 등록한 명령 처리자가 등록되어 해당 명령을 처리하게 됩니다.

### Dispatching commands
```CommandBus```는 각각의 처리자에게 명령을 전달하기 위한 두개의 메서드를 제공합니다. 해당 메서드들은 ```dispatch(commandMessage, callback)```과 ```dispatch(commandMessage)```이며, 두 메서드의 공통된 매개변수이면서 첫번째 매개변수는 전달될 실제 명령을 포함하고 있습니다. 선택적인 두번째 매개변수로 전달되는 콜백 객체는 명령 처리가 완료되면 명령을 전송한 콤포넌트에 완료 여부를 알리는 용도로 사용할 수 있습니다. 해당 콜백은 명령 처리가 정상적으로 처리되었을 때 호출되는 ```onSuccess```와 예외 발생 등의 상황에서 호출되는 ```onFailure``` 두개의 메서드를 가집니다.

명령이 전달되는 쓰레드와 콜백이 호출되는 쓰레드는 다를 수 있기 때문에, 호출하는 쓰레드가 다음 로직을 진행하기 전에 결과를 원한다면, ```FutureCallback```을 사용하여 처리할 수 있습니다. ```FutureCallback```은 Java의 ```java.concurrent``` 패키지에 정의되어 있는 ```Future```와 Axon에서 제공하는 ```CommandCallback```의 조합입니다. 다른 대안으로는 커맨드 게이트웨이의 사용을 생각해 볼 수 있습니다. 만약 명령 처리의 결과에 관심이 없다면, ```dispatch(commandMessage)``` 메서드를 사용하면 됩니다.

### SimpleCommandBus
```SimpleCommandBus```는 이름에서 알 수 있듯이, 가장 간단한 ```CommandBus```의 구현체입니다. 명령들을 전송하는 쓰레드에서 곧장 명령들을 처리합니다. 명령이 처리되고 난후에, 변경된 aggregate(들)은 저장되고 발생된 이벤트들은 동일한 쓰레드에서 게시 됩니다. 웹 애플리케이션과 같은 대부분의 경우에 ```SimpleCommandBus```만으로도 충분하며, 설정 API에서 기본적으로 사용되는 구현체이기도 합니다.

다른 대부분의 ```CommandBus``` 구현체들처럼, ```SimpleCommandBus```에도 인터셉터(interceptor)들을 설저할 수 있습니다. ```CommandDispatchInterceptor```는 커맨드 버스에서 명령이 전송될때 호출이 됩니다. 실제 명령 처리자 메서드를 호출하기전에 ```CommandDispatchInterceptor```를 호출합니다. 따라서 해당 명령을 변경하거나 전송되지 않도록 처리할 수 있습니다. 상세 내용은 [Command Interceptors](#Command Interceptors)를 참조하세요.

모든 명령 처리가 동일한 쓰레드내에서 이루어 지기 때문에, JVM의 한도내에서 작동하게 됩니다. ```SimpleCommandBus```의 성능은 좋지만, 뛰어난 정도는 아닙니다. JVM의 한도를 넘어서거나 CPU 싸이클을 최대한 활용하려면, 다른 ```CommandBus```의 구현체를 확인해 보세요.

### AsynchronousCommandBus
  이름에서 알수 있듯이, ```AsynchronousCommandBus```는 ```CommandBus``` 의 또 다른 구현체로, 명령을 발송하는 쓰레드로부터 비동기적으로 명령을 처리합니다. ```AsynchronousCommandBus```는 ```Executor```를 사용하여 실제 명령 처리 로직을 다른 쓰레드 상에서 처리합니다.

  기본적으로, ```AsynchronousCommandBus```는 개수 제한이 없는 캐쉬 기반의 쓰레드 풀(pool)을 사용합니다. 즉, 한 명령이 발송될때 하나의 쓰레드가 생성이 되며, 명령 처리를 완료한 쓰레드는 새로운 명령들을 처리하기위해 다시 재 사용됩니다. 그리고 60초 동안 아무런 명령을 처리하지 않았다면 쓰레드들은 멈추게 됩니다.

  다른 방법으로, 다른 쓰레드 전략을 가지는 ```Executor``` 인스턴스를 사용할 수 있습니다.

  주의 할 것은, 명령을 기다리는 모든 쓰레드들을 정상적으로 종료시키기 위해 ```AsynchronousCommandBus```는 애플리케이션이 종료될때 함께 종료되어야 합니다. ```shutdown()``` 메서드를 호출하여 ```AsynchronousCommandBus```를 종료하면 되고 주어진 ```Executor```가 ```ExecutorService```를 구현한 구현체라면, 함께 종료가 됩니다.

### 디스럽터 커맨드버스 DisruptorCommandBus
```SimpleCommandBus```는 수긍할만한 성능 특성을 가지고 있고, 특히 [Performance Tuning](Performance Tuning)의 성능 관련 사항을 살펴보게될때 알 수 있을 것입니다. ```SimpleCommandBus```는 동일한 aggregate에 다수의 쓰레드들이 동시에 접근하는 것을 방지하지 위한 락킹(locking)을 필요로 합니다. 이로 인해 처리 과부하 및 락(lock)을 얻기 위한 경쟁이 발생합니다.

```DisruptorCommandBus```는 다른 다중 쓰레드를 처리 방법을 사용합니다. 다수의 쓰레드들이 동일한 프로세스를 처리하도록 하는 대신, 각각의 쓰레드들이 프로세스의 부분 부분을 처리하도록 합니다. ```DisruptorCommandBus```는 상당히 향상된 성능을 내는 동시성 프로그래밍을 위한 작은 프레임워크인 [Disruptor](http://lmax-exchange.github.io/disruptor/)를 사용합니다. 호출자의 쓰레드에서 프로세스를 처리하는 방법 대신, 프로세스의 각 부분을 담당하는 두 그룹의 쓰레드에 작업을 전달합니다. 첫번째 그룹의 쓰레드는 명령 처리자를 호출하고 aggregate의 상태를 변경합니다. 두번째 그룹은 이벤트들을 저장하고 이벤트 스토어에 이벤트를 게시합니다.

```SimpleCommandBus```에 비해 ```DisruptorCommandBus```는 4!배 만큼 월등한 성능을 내지만, 몇가지 제약 사항들이 있습니다.

* ```DisruptorCommandBus```는 이벤트 소스 기반의 Aggregate만을 지원합니다. 이 커멘트 버스는 디스럽터(disruptor)가 처리하는 aggregate의 저장소와 같은 역활을 합니다. 저장소에 대한 참조 값은 ```createRepository(AggregateFactory)``` 메서드를 사용하면 됩니다.
* 하나의 명령은 단일 aggregate 인스턴스에 대해서만 상태변경을 할 수 있습니다.
* 캐쉬를 사용할 경우, 동일한 식별값을 가지는 두개의 다른 유형의 aggregate를 사용할 수 없습니다. 특정 식별자에 대해 오로지 단일의 aggregate만을 허용하기 때문입니다.
* 커맨드 버스를 통해 처리되는 명령은 일반적으로 작업단위의 롤백이 이루어져야 하는 실패를 발생 시켜서는 안됩니다. 롤백이 이루어질 경우, ```DisruptorCommandBus```는 명령이 전송된 순서와 동일한 순서로 처리되는 것을 보장하지 않습니다. 게다가 불필요한 계산을 필요로 하는 다른 명령들을 다시 시도 해야 합니다.
* 새로운 Aggregate 인스턴스를 생성할때, 생성된 인스턴스를 갱신하는 명령이 주어진 순서대로 수행되지 않을 수 있습니다. aggregate가 생성되면, 모든 명령은 전달 된 순서대로 정확하게 실행됩니다. 순서대로 처리되도록 보장하려면, aggregate가 생성되기를 기다리도록 하는 콜백을 해당 명령에 사용해야 합니다. 하지만 aggregate는 몇 밀리 초안에 생성되므로 콜백의 처리 또한 짧은 시간안에 이루어 집니다.

```DisruptorCommandBus``` 인스턴스를 생성하기 위해선, ```EventStore```가 필요합니다. ```EventStore```는 [Repositories and Event Store](Repositories and Event Store)에 설명이 되어 있습니다.

특정 환경하에서 성능에 최적화된 설정을 위해 ```DisruptorConfiguration``` 인스턴스를 선택적으로 생성하여 ```DisruptorCommandBus```에 설정할 수 있습니다. 버퍼 크기, 생산자 유형 및 대기 전략 등의 설정 가능한 항목들은 아래와 같습니다.

* 버퍼 크기(Buffer size): 수신 명령을 저장하는 링 버퍼(ring buffer)의 슬랏의 개수를 나타냅니다. 값을 높게 설정하면 출력을 높일 수 있지만, 지연(latency)이 길어질 수 있습니다. 해당 값은 반드시 2의 거듭 제곱근이 되어야 합니다. 기본값은 4096입니다.
* 생산자 유형(ProducerType): 단일 쓰레드 혹은 다중 쓰레드로 엔트리들을 생성할 지를 결정하는 값입니다. 기본값은 다중 쓰레드로 되어 있습니다.
* 대기 전략(WaitStrategy): 처리 프로세서 쓰레드들간(세개의 쓰레드가 실제 처리를 담당합니다.) 다른 쓰레드의 작업 처리 동안 어떤 방법으로 대기할지에 대한 전략을 설정할 수 있습니다. 가장 좋은 대기 전략은 사용 가능한 코어의 개수와 진행 중인 다른 프로세스들의 개수에 따라 결정이 됩니다. 낮은 지연 시간이 가장 중요하고 ```DisruptorCommandBus```가 자신을 위한 코어를 요청해야 한다면, ```BusySpinWaitStrategy```를 사용할 수 있습니다. ```DisruptorCommandBus```가 적게 CPU를 사용하고 다른 쓰레드들이 처리할 수 있도록 허용하기 위해선, ```YieldingWaitStrategy```를 사용하세요. 마지막으로 다른 쓰레드들과 공평하게 CPU 사용을 하기 위해서 ```SleepingWaitStrategy```와 ```BlockingWaitStrategy```를 사용 할 수 있습니다. ```BlockingWaitStrategy```는 커맨드 버스가 프로세스 처리를 위해 CPU를 독점적으로 사용하지 않도록 합니다. 기본값은 ```BlockingWaitStrategy```입니다.
* 익스큐터(Executor): ```DisruptorCommandBus```가 사용할 쓰레드를 제공해주는 ```Executor```를 설정합니다. ```Executor```는 최소한 4개의 쓰레드를 제공해야 하는데, 그중 세개의 쓰레드는 ```DisruptorCommandBus```의 프로세싱 콤포넌트에 할당이 됩니다. 나머지 쓰레드들은 콜백을 호출하고 Aggregate의 상태 변경에 문제가 발생하였을때 재시도를 스케쥴링하기 위한 용도로 사용이 됩니다. "DisruptorCommandBus"의 이름을 가진 쓰레드 그룹으로부터 쓰레드를 제공하는  ```CachedThreadPool```이 기본적으로 사용이 됩니다.
* 트랜잭션 매니져(TransactionManager): 이벤트의 저장과 게시를 트랜잭션내에서 처리될 수 있도록 하기 위한 트랜잭션 매니져를 정의합니다.
* 인보커-인터셉터스(InvokerInterceptors): 명령 처리자의 호출 과정에서 사용되는 ```CommandHandlerInterceptor```들을 정의 합니다. 실제 명령 처리자 메서드를 호출하는 과정입니다.
* 퍼블리셔-인터셉터스(PublisherInterceptors): 발생된 이벤트를 저장하고 게시하는 과정에서 사용되는 ```CommandHandlerInterceptor```들을 정의합니다.
* 롤백 설정(RollbackConfiguration): 작업 단위를 롤백해야하는 예외들을 정의합니다. 기본적으로 메서드에 정의되지 않은 예외(unchecked exception)에 대해 롤백 처리를 하도록 설정되어 있습니다.
* 비정상 상태변경 처리를 위한 명령의 리스케쥴링(RescheduleCommandsOnCorruptState): 예를 들어 작업 단위(Unit of Work)가 롤백된 경우와 같이 Aggregate의 상태가 정상적으로 변경되지 않았을때, 이미 처리된 명령을 다시 처리하도록 스케쥴링을 해야 하는지를 결정합니다. ```false```로 값을 설정하면, 콜백의 ```onFailure()``` 메서드가 실행됩니다. 반대로 기본값인 ```true```일 경우, 해당 명령은 다시 처리되도록 스케쥴링 됩니다.
* 쿨링다운 시간(CoolingDownPeriod): 명령들이 모두 처리될때까지 기다리는 초단위의 시간 값을 설정합니다. 이 시간 동안, 새로운 명령을 받지 않지만, 이미 수신한 명령들은 처리되고 필요에 따라 재처리 되도록 스케쥴링 됩니다. 설정된 시간 동안, 명령에 재처리 스케쥴링과 콜백 호출을 위한 쓰레드들의 사용을 보장합니다. 기본값은 1000(1초)입니다.
* 캐쉬(Cache): 이벤트 저장소를 통해 복원된 aggregate 인스턴스들을 저장해놓는 캐쉬를 설정합니다. 설정된 캐쉬는 디스럽터(disruptor)가 사용 중이 아닌 aggregate 인스턴스들을 저장합니다.
* 명령 처리자 호출 쓰레드 개수(InvokerThreadCount) : 명령 처리자를 호출하는데 사용되는 쓰레드의 개수를 말합니다. 해당 장비 CPU의 코어 개수의 반이 적절한 시작 값입니다.
* 이벤트 게시 쓰레드 개수(PublisherThreadCount): 이벤트를 게시하는데 사용되는 쓰레드의 개수를 설정 할 수 있습니다. 해당 장비 CPU의 코어 개수의 반이 적절한 시작 값이며, IO 비용이 많이 든다면 해당 값을 증가 시킬 수 있습니다.
* 직렬화 쓰레드 개수(SerializerThreadCount): 이벤트를 사전-직렬화하는데 사용되는 쓰레드의 개수를 의미하며, 기본값은 1입니다. 만약 serializer가 설정이 되어 있지 않다면 해당 값은 무시됩니다.
* 직렬화 객체(Serializer): 이벤트를 직렬화하는 객체입니다. Serializer를 설정하면, ```DisruptorCommandBus```는 ```SerializationAware``` 메세지로 생성된 모든 이벤트를 포함시켜 버립니다. 페이로드 및 메타 데이터는 직렬화된 형식으로 게시되기 전 이벤트 저장소에 저장됩니다.

## 커멘드 인터셉터, Command Interceptors
커맨드 버스를 사용하면 명령의 유형에 상관없이 모든 수신 명령들에 대해 로깅 및 인증과 같은 작업을 처리할 수 있습니다. 인터셉터(Interceptor) 이런 작업들을 처리할 수 있습니다.

Dispatch Interceptor와 Handler Interceptor들과 같이 다른 유형의 인터셉터가 있습니다. Dispatch Interceptor는 명령 처리자로 전달되기 전의 명령들을 처리할 수 있습니다. 이때, 명령 처리자가 호출되기 전에는, 해당 명령에 대한 처리자가 있는지는 확신 할 수 없습니다.

### Message Dispatch Interceptors
메세지 게시 인터셉터(Message Dispatch Interceptor)들은 커맨드 버스로 명령이 전달될때 호출이 됩니다. 메세지 게시 인터셉터를 통해 메타 데이터의 추가와 같은 명령 메세지 변경을할 수 있고 예외를 발생 시켜 명령이 전달되는 것을 막을 수 있습니다. 메세지 게시 인터셉터들은 명령을 전달하는 쓰레드와 동일한 쓰레드에서 항상 호출이 됩니다.

#### 구조적 검증, Structural validation
필요한 모든 정보가 올바른 형식으로 되어 있지 않다면 명령은 처리되지 않습니다. 사실, 필요한 정보가 누락된 명령은 가급적 트랜잭션이 시작되기전에 처리되지 않도록 최대한 빨리 막는 것이 좋습니다. 따라서, 인터셉터를 통해 명령이 필요한 모든 정보를 포함하고 있는지 확인 해야 하며, 이를 구주적 검증이라고 합니다.

Axon Framework은 JSR 303 Bean Validation 기반 검증을 지원합니다. ```@NotEmpty``` 그리고 ```@Pattern```과 같은 애노테이션을 사용하여 명령의 필드를 검증할 수 있습니다. Hibernate-Validator와 같은 JSR 303 구현체를 클래스 패스에 추가해야 합니다. 그런 후에, ```BeanValidationInterceptor```를 커맨드 버스에 설정하면 자동으로 검증 대상을 찾고 설정하게 됩니다. 합리적인 기본값을 사용하지만, 필요에 따라 조정할 수 있습니다.

> Tip
> 부적합한 명령을 처리하는데 최소한의 자원을 사용하길 원할 것입니다. 그러므로, ```BeanValidationInterceptor```를 인터셉터 체인의 맨 앞에 위치 시켜야 합니다.
> 몇몇의 경우에, 로깅 혹은 감사(auditing) 인터셉터를 맨 앞에 위치시켜야 한다면, ```BeanValidationInterceptor```를 바로 그 다음에 위치 시켜야 합니다.

```BeanValidationInterceptor```는 ```MessageHandlerInterceptor```의 구현체이기도 합니다. 따라서 핸들러 인터셉터로도 설정이 가능합니다.

### Message Handler Interceptors
메세지 처리 인터셉터는 명령 처리 이전과 이후에 특정 작업을 수행할 수 있도록 합니다. 메세지 처리 인터셉터는 예를 들어, 보안 문제등과 같은 이유로 명령이 처리되는 것을 막을 수 있습니다.

메세지 처리 인터셉터는 ```MessageHandlerInterceptor```의 구현체이며, ```handle``` 메서드를 구현하고 있습니다. ```handle```메서드는 명령 메세지, 현재의 작업단위(UnitOfWork) 그리고 ```InterceptorChain```을 매개변수로 받습니다. ```InterceptorChain``` 매개변수는 명령 전달을 계속할지 말지를 결정하는데 사용이 됩니다.

메세지 게시 인터셉터와는 달리, 메세지 처리 인터셉터는 명령 처리 컨텍스트 내에서 호출이 됩니다. 즉, 예를 들어 작업 단위에서 처리되는 메세지와 상관있는 데이터를 첨부할 수 있습니다. 이 상관 데이터는 동일 작업 단위의 컨텍스트에서 생성되는 메세지들에 첨부 됩니다.

메세지 처리 인터셉터는 또한 명령 처리를 위한 트랜잭션을 관리하기 위해 사용이 되며, 이를 위해 ```TransactionManagingInterceptor```를 등록해야 합니다. ```TransactionManagingInterceptor```는 ```TransactionManager```와 함께 구성이 되어 실제 트랜잭션을 시작하고 커밋(혹은 롤백)을 처리하게 됩니다.

## 분산 커맨드 버스, Distributing the Command Bus
이전에 설명한 ```CommandBus``` 구현체들은 단일 JVM내에서 명령 메세지를 전달할 수 있는 구현체들입니다. 때때로, 다른 JVM상에서 작동하는 다수의 커맨드 버스들을 하나의 커맨드 버스처럼 작동하도록 해야 할 경우가 있습니다. 한 JVM상의 커맨드 버스로 전달된 명령은 다른 JVM상의 명령 처리자로 전달되어야 하며 결과는 반환되어야 합니다.

위와 같은 경우를 처리하기 위해 ```DistributedCommandBus```를 사용합니다. 다른 ```CommandBus``` 구현체들과는 달리, ```DistributedCommandBus```는 어떤 명령 처리자도 호출하지 않고, 다른 JVM상의 커맨드 버스들을 어려 이어주는(bridge) 역활을 할 뿐입니다. 각각의 JVM상에서 동작하는 ```DistributedCommandBus```의 개별 인스턴스를 "세그먼트(Segment)"라고 합니다.

![](/assets/axon/distributed_command_bus.png)

> Note
> 분산 처리가 가능한 커맨드 버스는 Axon Framework의 핵심 모듈의 일부분이긴 하지만, ```axon-distributed-commandbus-...``` 모듈 중 하나의 모듈의 콤포넌트들을 필요로 합니다.
> 메이븐을 사용할 경우, 필요한 의존성들을 설정해야 합니다. 핵심 모듈과 동일한 그룹 아이디(groupId)와 버전을 사용해야 합니다.

```DistributedCommandBus```는 두개의 콤포넌트를 필요로 합니다. 하나는 JVM간 통신 프로토콜을 구현한 ```CommandBusConnector```이고, 다른 하나는 수신된 명령을 분배하는 ```CommandRouter```입니다. 커맨드 라우터(CommandRouter)는 라우팅 전략(Routing Strategy)에 의해 계산된 라우팅 키(Routing Key)에 기반하여, 분산 처리 커맨드 버스의 어느 세그먼트로 처리할 명령을 보낼지를 결정합니다. 세그먼트의 개수와 설정이 변경되지 않는한, 동일한 라우팅 키를 가지는 두개의 명령들을 항상 동일한 세그먼트로 가게 됩니다. 일반적으로, 대상 aggregate의 식별값이 라우팅 키로 사용이 됩니다.

```RoutingStrategy```의 두 구현체로 라우팅 키를 찾기 위해 커맨드 메세지의 메타 데이터 속성을 사용하는 ```MetaDataRoutingStrategy```와 라우팅 키를 뽑아내기 위해 명령 메세지의 페이로드에 사용된 ```@TargetAggregateIdentifier```를 기반으로 작동하는 ```AnnotationRoutingStrategy```가 있습니다.

기본적으로, ```RoutingStrategy```의 구현체들은 명령 메세지로부터 라우팅 키를 찾지 못하면 예외를 발생 시킵니다. 하지만 ```MetaDataRoutingStrategy``` 혹은 ```AnnotationRoutingStrategy```의 생성자에 ```UnresolvedRoutingKeyPolicy```를 전달하여 예외를 던지는 기본 행위를 변경할 수 있습니다. **ERROR**, **RANDOM_KEY**, **STATIC_KEY**와 같이 세개의 가능한 정책을 제공합니다.

* ERROR: 기본값이며, 사용 가능한 라우팅 키가 없는 경우 예외를 발생 시킵니다.
* RANDOM_KEY: 명령 메세지에서 라우팅 키를 찾지 못한 경우, 무작위로 생성된 값을 사용합니다. 따라서 해당 명령들은 무작위로 커맨드 버시의 세그먼트로 분배 됩니다.
* STATIC_KEY: "unresolved"의 값을 가지는 정적 키를 사용하게 되며, 세그먼트의 설정이 변경되지 않는 한 동일한 세그먼트로 해당 명령들은 전달됩니다.

### JGroupsConnector
```JGroupsConnector```는 이미 이름에서 알 수 있듯이, 기본 검색 및 전송 메커니즘으로 JGroups를 사용합니다. JGroups의 기능들을 살펴보는 것은 본 참조 문서의 범위를 벗어나기 때문에, 상세 내용을 알고 싶다면 [JGroups User Guide](http://www.jgroups.org/ug.html)를 참조해주세요.

JGroups는 노드(Node)의 검색과 노드간 통신을 다루기 때문에, ```JGroupsConnector```는 ```CommandBusConnector```와 ```CommandRouter```와 같은 역활을 합니다.

> Note
> ```axon-distributed-commandbus-jgroups``` 모듈에서 ```DistributedCommandBus```를 위한 JGroups 콤포넌트를 찾을 수 있습니다.

```JGroupsConnector```는 아래와 같이 반드시 필요한 네가지의 설정을 필요로 합니다.

* 첫번째는 jChannel이며, jChannel은 JGroups의 프로토콜 스택을 정의합니다. 일반적으로, jChannel은 JGroups의 설정 파일에 대한 참조로 생성됩니다. JGroups는 다수의 기본 설정값들을 제공합니다. 일반적으로 아마존과 같은 클라우드 서비스 상에서 IP Multicast는 작동하지 않습니다. 클라우드 환경에서 TCP Gossip을 사용하는 것이 좋은 시작점이 될 것입니다.
* 클러스터 이름은 각 세그먼트가 등록해야하는 클러스터의 이름을 정의하며, 동일한 클러스터 이름을 가진 세그먼트는 결국 서로를 감지하고 서로간에 명령을 전달합니다.
* "로컬 세그먼트"는 로컬 JVM을 대상으로 명령을 전달하는 커맨드 버스의 구현체입니다. 이런 명령들은 다른 JVM의 인스턴스나 로컬 JVM으로부터 전달될 수 있습니다.
* 마지막으로 명령 메세지가 전송되기 전에 명령 메세지를 직렬화하는 시리얼라이져 입니다.

> Note
> 캐쉬를 사용할때, 잠재적인 데이터 부패 현상을 피하기 위해 ```ConsistentHash```값이 변경될때 캐쉬를 비우는 것이 좋습니다. (예, 명령이 ```@TargetAggregateVersion```을 가지고 있지 않고,  aggregate가 다른 곳에 캐쉬되어 있는데 수정될때, 새로운 멤버가 JGroups에 빠르게 참여 했다가 떠나는 경우.)

JGroupsConnector는 다른 세그먼트로 메세지를 전달하기 위해 연결되어야 합니다. 이때 ```connect()``` 메서드를 호출하여 연결합니다.

```
JChannel channel = new JChannel("path/to/channel/config.xml");
CommandBus localSegment = new SimpleCommandBus();
Serializer serializer = new XStreamSerializer()p;

JGroupsConnector connector = new JGroupsConnector(
    channel,
    "myCommandBus",
    localSegment,
    serializer
);
DistributedCommandBus commandBus = new DistributedCommandBus(connector, connector);

// on one node;
commandBus.subscribe(CommandType.class.getName(), handler);
connector.connect();

//on another node, with more CPU
commandBus.subscribe(CommandType.class.getName(), handler);
commandBus.subscribe(AnotherCommandType.class.getName(), handler2);
commandBus.updateLoadFactor(150); // 기본값: 100
connector.connect();
```

> Note
> 모든 세그먼트가 동일한 타입의 명령들에 대한 명령 처리자를 가지고 있을 필요는 없습니다. 다른 명령 타입에 대해 다른 세그먼트를 함께 사용할 수 있습니다. 분산된 커맨드 버스는 특정 타입의 명령을 처리할 수 있는 세그먼트로 명령을 전달할 수 있는 노드를 항상 선택합니다.

스프링을 사용한다면, ```JGroupsConnectorFactoryBean```을 사용하는 것을 고려해 보세요. ```JGroupsConnectorFactoryBean```은 애플리케이션 컨텍스트가 시작할때 자동으로 커넥터에 연결을 하고, 애플리케이션 컨텍스트가 종료될때 자동으로 연결을 끊습니다. 또한 테스트 환경에 대한 합당한 기본값을 사용하고(운영 환경에 배포가능한 것은 아닙니다.) 구성을 위해 자동 의존성 주입을 합니다.

### Spring Cloud Connector
// TODO - continue

#### Spring Cloud Http Back Up Command Router
## Event Publishing & Processing
### Publishing Events
### Event Bus
### Event Processors
#### Assigning handlers to processors
#### Configuring processors
##### Event Handlers
##### Sagas
#### Token Store
#### Parallel Processing
##### Multi-node processing
### Distributing Events
#### Spring AMQP
##### Forwarding events to an AMQP Exchange
##### Reading Events from an AMQP Queue
### Asynchronous Event Processing

## Query Dispatching
## Query Gateway
## Query Bus
### Simple Query Bus
## Query Interceptors
### Dispatch Interceptors
#### Structural validation
### Handler Interceptors

## Repositories and Event Store
### Standard repositories
## Event Sourcing repositories
## Event Store implementations
### JPA Event Storage Engine
### JDBC Event Storage Engine
### MongoDB Event Storage Engine
## Event Store Utilities
### Combining multiple Event Stores into one
### Filtering Stored Events
### In-Memory Event Store

## Influencing the serialization process
### Serializing Events vs 'the rest'

## Event Upcasting
### Provided abstract upcaster implementations
### Writing an upcaster
### Content type conversion

## Snapshotting
### Creating a snapshot
### Storing Snapshot Events
### Initializing an Aggregate based on a Snapshot Event

## Advanced conflict detection and resolution

## Spring Boot AutoConfiguration
### Event Bus and Event Store Configuration
### Command Bus Configuration
### Query Bus Configuration
### Transaction Manager Configuration
### Serializer Configuration
### Aggregate Configuration
### Saga Configuration
### Event Handling Configuration
### Query Handling Configuration
#### Parallel processing
### Enabling AMQP
### Distributing commands
#### Using JGroups
#### Using Spring Cloud

## Advanced Customizations
### Parameter Resolvers
### Meta Annotations
### Customizing Message Handler behavior

## Performance Tuning
### Database Indexes and Column Types
#### SQL Databases
### MongoDB
### Caching
### Snapshotting
### Event Serializer tuning
#### XStream Serializer
#### Preventing duplicate serialization
#### Different serializer for Events
### Custom Identifier generation


## Glossary
### Scalable
"규모 가변성[Scalable]"이라는 용어는 단순 확장성이 있다라는 의미보다는 확장과 축소의 개념을 함께 포함하고 있다. 즉, 사용자의 증감에 따라 애플리케이션의 규모를 동적으로 변경할 수 있다는 의미로 사용할 수 있다.

많은 사용자가 애플리케이션을 사용할때도 scalable한 애플리케이션은 많은 사용자를 감당하고 잘 운영이 되어야 한다. 그리고 사용자의 수가 감소하게 되면 그만큰 애플리케이션을 실행하는 서버의 자원을 아낄 수 있어야 scalable하다고 할 수 있다.

### Extensible
"확장 가능성[extensible]"은 새로운 기능의 추가 및 기존 기능의 확장을 쉽게 할 수 있다라는 의미로 사용된다. 즉, framework을 사용할 경우, framework은 기본 테투리가 되거나 기본적인 기능을 수행하는 객체를 제공해야 하며, framework에서 제공하는 기본 객체의 확장을 쉽게 수행할 수 있어야 한다.


### DDD
DDD는 software application을 개발하기 위한 개발 방법론의 하나이며 다음과 같은 전제를 가지고 있다.
* 핵심 도메인과 도메인의 business logic을 가장 중점적으로 다룬다.
* 기술(개발) 전문가와 도메인(business)전문가의 긴밀한 협업을 통해 특정 도메인의 문제를 해결하기 위한 논리적인(개념적인) 모델을 점진적으로 발전시켜 가는 방법을 지향한다.

DDD model은 domain,
* Domain: 특정 business 영역을 의미한다. 개발하고자 하는 제품의 사업 영역으로 생각할 수 있다.
* Bounded Context: 복잡하고 거대한 시스템을 좀 더 관리 가능한 작은 부분으로 나눠야 하며, 이 과정에서 도출되는 각 부분을 의미한다. 또한 각 부분(bounded context)는 다른 부분과의 의존성을 가지긴 하지만 스스로 해당 영역에서의 기능을 가져야 한다. 다른 부분(bounded context)과의 의존성을 줄이기 위해 보통 이벤트 기반으로 서로 메세지를 주고 받는다.
* Ubiquitous language: Bounded context내의 모델 및 business logic을 설명하기 위한 공통의 언어를 의미한다. 각 Bounded context는 팀을 나누어 개발할 수 있고 팀내의 팀원들은 공통의 언어(기술적 여부를 떠나)로 서로 소통해야 한다.
* Context map: Bounded context내의 model간 혹은 bounded context간의 접점을 분명히 기술해야 한다. 기술적인 관점으로만 기술해야 하는 것이 절대 아닌 팀을 구성하는, 함께 제품을 개발하는 모든 구성원이 이해할 수 있도록 기술 되어야 한다.

### Bounded Context (바운디드 컨텍스트)
도메인 주도 설계(Domain Driven Design)의 핵심 패턴으로, 커다란 비지니스 영역을 판매, 제품, 회계 등과 같이 각 영역별로 나누는 것을 말합니다. 단 각 영역별 의존성은 최소화해야 하며, 모델 및 기능의 중복 또한 최소화 해야 합니다. 참조 - [Bounded Context by Martin Fowler](https://martinfowler.com/bliki/BoundedContext.html)

### 위치 투명성(location transparency)
네트웍크 상의 자원의 위치를 실제 위치(IP 등)를 사용하지 않고, 식별 가능한 이름을 부여 하여 해당 자원을 사용하는 것을 의미 한다. 위치 투명성이 보장이 되면, 실제의 위치에 의존하지 않기 때문에 자원이 실제 위치한 곳은 문제가 되지 않는다.  보다 상세한 내용은 [위키피디아](https://en.wikipedia.org/wiki/Location_transparency)를 참고하면 된다.



### ACID
ACID는 Atomicity, Consistency, Isolation, Durability의 첫글짜를 딴 줄임말로 데이터베이스 트랜잭션이 안정하게 수행된다는 것을 보장하기 위한 성질을 의미한다. 데이터베이스에서 데이터에 대한 논리적 실행단계를 트랜잭션이라고 한다. [자세히](https://ko.wikipedia.org/wiki/ACID)

### backing scheduling
// TODO - search
