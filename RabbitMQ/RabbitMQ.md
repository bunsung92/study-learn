# 메세지 브로커란?

- `메세지 브로커`는 `애플리케이션`, `시스템` 및 `서비스`가
- 서로 간에 통신하고 정보를 교환할 수 있도록 해주는 소프트웨어입니다.
- `메세지 브로커`는 메시지를 `검증`, `저장`, `라우팅`하고 이를 적절한 대상에게 전달할 수 있습니다.
- 신뢰할 수 있는 `메시지 스토리지`와 `보장된 딜리버리`를 제공하기 위해, 메시지 브로커는
- 이용하는 `애플리케이션`이 처리할 수 있을때까지 메세지를 `저장`하고 `순서화`하는
- `메시지 큐`라고 하는 하위 구조 또는 구성요소에 종종 의존합니다.
    - `메시지 큐` 에서 메시지는 전송된 정확한 순서대로 저장되며, 인수되었는지 확인될 때까지 큐에 남아있습니다.
- `비동기 메시징` 은 메시지 브로커를 통해 가능한 `애플리케이션`간 통신의 유형을 나타냅니다.
    - 이는 데이터의 유실을 방지하며 공용 네트워크에서 흔하게 나타나는 단속적인 연결 혹은
    - 지연문제가 발생해도 시스템이 계속해서 작동될 수 있도록 보장합니다.
    - `비동기 메시징`은 메시지가 다른 메시지와 상대적인 올바른 순서로 단 한번만 전달되도록 보장합니다.

### by - IBM

![image](https://user-images.githubusercontent.com/53285909/186298534-a2ba390a-dad1-4b92-87db-724b6fdcbcb0.png)

# 환경설정

1. `NHN cloud` 인스턴스를 받았음.
1. 인스턴스는 Ubuntu 서버임.
2. 해당 Ubuntu 서버에 `Docker` 를 설치함.
3. `Docker` 는 컨테이너를 만들었음.
4. 분리된 컨테이너에 `RabbitMQ` 의 이미지를 설치함.
5. 이미지가 설치 되었으면 `RabbitMQ` 를 이용 할 수있게됨.

# Rabbit MQ

- RabbitMQ 는 메시지 브로커입니다.
- 이를 우체국에 빗대어 보자면
- 우편물을 우편함에 넣으면 도대체 무슨일이??
- 편지 배달부가 당연히 우편물을 받는 사람에게 배달할 것이라고 확신 할 수 있습니다.
- 이 비유에서 RabbitMQ는 우체통, 우체국 및 편지 운반대입니다.
  [RabbitMQ 그림](https://www.rabbitmq.com/tutorials/tutorial-one-python.html)

## Exchange 는 무엇인가?

> `Producer` 들에게서 전달받은 메세지들을 어떤 Queue 들에게 발송할지를 결정하는 객체입니다.
> `Exchange` 는 네 가지 타입이 있으며, 일종의 라우터 개념입니다.
- `RabbitMQ` 에서 메시지를 적절한 목적지로 전달하기 위해 필요한 첫 번째 입력 값으로
- AMQ 모델이 정의하는 세 컴포넌트 중 하나입니다.
- `Exchange` 는 `RabbitMQ` 로 전송한 메시지를 수신하고 메시지를 보낼 위치를 결정 합니다.


## Binding 은 무엇인가?
- `Exchange` 에게 메세지를 라우팅 할 규칙을 지정하는 행위입니다.
- 특정 조건에 맞는 메세지를 특정 큐에 전송하도록 설정할 수 있는데, 이는 해당 `Exchange` 타입에 맞게 설정되어야 합니다.

### 정리
1. `Producer` 가 메시지를 생성하여 전송 합니다.
2. `Exchange` 어떤 `Queue` 에 전달할지를 `Routing` 합니다.
3. `Queue` 들이 메시지를 순차적으로 쌓습니다.
4. `Consumer` 가 `Queue` 에 대한 `Binding` 을 가지고 있다가 메시지를 `Queue` 메시지를 `Queue` 에서 수신합니다.


# AMQP 프레임 컴포넌트

- RabbitMQ 에서 AMQP 명령을 전송하거나 수신할 때 필요한 모든 인자들은 데이터 구조로
- 캡슐화된 프레임으로 인코딩돼 전송됩니다.
- 프레임은 명령과 해당 인자를 인코딩해 각 프레임이 서로 구분되도록 하는 효율적인 방법입니다.

## 저수준 AMQP 구성 요소

> `프레임 헤더`
> 1. 프레임 유형 (단일 바이트)
> 2. 채널 번호 (프레임이 속하는 채널을 지정)
> 3. 프레임 크기(프레임 본문의 크기를 바이트로 표현)
- `프레임 페이로드`
- `끝 바이트 표식(ASCII 값 206)`
### `프레임` 은 운반하는 내용을 무결성 있게 보호하도록 설계 되었습니다.

# 프레임 유형

- `프로토콜 헤더 프레임`은 RabbitMQ 에 연결할 때 한번만 사용됩니다.
- `메소드 프레임`은 RabbitMQ 와 서로 주고받는 RPC 요청이나 응답을 전달합니다.
    - RPC??
    - RPC(원격 프로시저 호출) 을 이용하면 프로그래머는 함수가 실행 프로그램에 로컬 위치에 있든 원격 위치에 있든 동일한 코드를 이용할 수 있습니다.
- `콘텐츠 헤더 프레임` 은 메시지의 크기와 속성을 포함합니다.
- `바디 프레임` 은 메시지의 내용을 포함합니다.
- `하트비트 프레임` 은 RabbitMQ 와 연결된 클라이언트와 서버가 주고받으며 서로 사용 가능한 상태인지 확인하는데 사용합니다.

> AMQP 의 하트비트 동작은 클라이언트와 서버가 서로의 요청에 응답하는지 확인하는 데 사용하며   
> AMQP 가 양방향 RPC 프로토콜임을 알 수있는 완벽한 예다.   
> RabbitMQ 가 클라이언트 애플리케이션에 하트비트를 보냈는데 응답하지 않으면 RabbitMQ 는 클라이언트와의 연결을 끊는다.   
> 보통 단일 스레드 또는 비동기 개발 환경에서는 제한 시간을 약간 큰 값으로 늘린다.   
> 하트비트가 동작하기 어려운 환경에서 애플리케이션을 구동해 통신이 차단되는 경우,    
> 클라이언트 연결을 만들 때 하트비트 간격을 0 으로 설정해 하트비트를 끌 수 있다.   
> 대신 기본값인 600초보다 더 큰 값으로 rabbitmq.config 파일의 하트비트 값을 변경해 RabbitMQ 의 최대 하트비트 간격 값을 변경할 수 있다.

# 프로토콜 사용하기

> 메세지를 큐에 발행하기 전에 몇 가지 설정 단계를 거쳐야 하는데,   
> 최소한 익스체인지와 큐를 설정한 후 둘을 연결해야 합니다.

## 익스체인지 선언
- AMQ 모델에서 익스체인지는 큐와 같이 `1급 시민` 으로 AMQP 스펙에 해당 클래스가 존재합니다.
    - `1급 시민`
    - 변수에 담을 수 있습니다.
    - 인자로 전달할 수 있습니다.
    - 반환값으로 전달할 수 있습니다.
- `Exchange.Declare` 명령에 익스체인지의 이름과 유형 그리고 메시지 처리에 사용하는 기타 메타데이터를
- 인수로 실행해서 익스체인지를 생성합니다.
- `Exchange.Declare` 명령을 전송하면 RabbitMQ 는 익스체인지를 생성한 후 `Exchange.DeclareOK` 메소드 프레임을
- 응답으로 전송합니다.
> 만일 특정 이유로 Exchange.Declare 명령이 실패하면 RabbitMQ 는 Exchange.Declare 가 실패하고
> 채널이 닫힌 이유를 나타내는 숫자 응답 코드와 텍스트 값을 Channel.Close 명령에 포함시켜 전송하고
> Exchange.Declare 명령이 전송된 채널을 닫는다.

## 큐 선언하기

- 익스체인지를 생성한 후 RabbitMQ 에 Queue.Declare 명령을 보내 큐를 생성한다. Queue.Declare 명령도 Exchange.Declare 명령과
- 유사한 통신 절차로 진행되며 Queue.Declare 명령이 실패하면 채널이 닫힌다.
- 큐를 선언할 때 동일한 Queue.Declare 명령을 두 번 이상 전송해도 문제는 발생하지 않습니다.
- RabbitMQ 는 중복된 큐 선언을 감지해 큐에 대기 중인 메시지의 수와 구독 중인 구독자의 수와 같이
- 큐에 대한 유용한 상태를 반환합니다.

> 클라이언트 애플리케이션이 서버에서 전송하는 이벤트를 수신하지 않거나 적절하게 처리하지 않으면 메시지가 손실될 수 있습니다.
> 존재하지 않거나 이미 닫힌 채널에 메시지를 발행하는 경우 RabbitMQ 는 연결을 종료합니다.
> 메시지를 소비하는 애플리케이션이 RabbitMQ 가 채널을 닫은 사실을 모르는 경우 RabbitMQ 가 메시지를 더는 전송하지 않지만,
> 클라이언트는 빈 큐를 구독하고 이다고 간주하는 문제가 발생합니다.

## 큐와 익스체인지 연결하기

- 익스체인지와 큐가 생성되면 이제는 이 둘을 연결해야 한다.
- Queue.Declare 와 유사하게 큐를 익스체인지에 연결하는 명령인 Queue.Bind 는 한 번에 하나의 큐만 지정한다.
- Exchange.Declare 및 Queue.Declare 명령과 유사하게 Queue.Bind 명령은 실행이 성공적으로 처리된 경우 클라이언트 애플리케이션에 Queue.BindOK 메소드 프레임을 전송합니다.

## RabbitMQ 에 메시지 발행하기

- 메시지를 발행할 때 여러 종류의 프레임들이 서비스로 전송하는 메시지의 데이터를 캡슐화 한다.
- 실제 메시지 본문을 RabbitMQ 에 전달하기 전에 클라이언트 애플리케이션은 Basic.Publish 메소드 프레임, 콘텐츠 헤더 프레임, 하나이상의 바디프레임을 전송합니다.
- 메시지의 모든 프레임을 수신한 후에 RabbitMQ 는 다음 단계로 진행하기 전에 메소드 프레임에 포함된 필요한 정보를 검증한다.
- Basic.Publish 메소드 프레임에는 익스체인지의 이름과 라우팅 키가 들어있는데, 이를 RabbitMQ 는 익스체인지의 이름을 저장한 데이터 베이스와 비교한다.
- RabbitMQ가 Basic.Properties 메소드 프레임의 익스체인지 이름과 일치하는 익스체인지를 발견한 후에 해당 익스체인지는 내부 바인딩들을 평가하며 라우팅 키와 일치하는 큐를 찾는다.

## 메시징 아키텍처 만들시 고려사항
- 발행자가 메시지를 디스크에 저장해야 하는가?
- 애플리케이션의 다양한 구성 요소는 발행된 메시지가 수신됐는지 보장해야 하는가?
- TCP 배압으로 애플리케이션이 차단되거나 RabbitMQ 에 메시지를 발행하는 동안 연결이 차단된 경우 어떻게 되는가?
- 메시지가 얼마나 중요한가? 메시지 처리량을 높ㅍ이기 위해 배달 보장을 어디까지 고려할것인가?

## 소비자 애플리케이션의 성능을 조정, 메시지 배달 보장 사이의 균형
- 모든 메시지를 수신했는지 또는 폐기할 수 있는지 확인해야 하는가?
- 메시지를 받은 다음 일괄적으로 수신 확인하거나 거부해야 하는가?
- 개별 작업을 자동으로 일괄 처리하고 트랜잭션을 사용해 성능을 향상시킬 수 있는가?
- 소비자 애플리케이션에서 트랜잭션 커밋 및 롤백 기능이 정말로 필요한가?
- 소비자가 구독하는 큐의 메시지를 독점적으로 접근해야 하는가?
- 소비자 애플리케이션에 오류가 발생했을 때 어떻게 처리해야 하는가?
- 메시지를 버려야 하는가? 큐에 재삽입해야 하는가? 혹은 데드 레터 익스체인지로 보내야 하는가?
-

---
![image](https://user-images.githubusercontent.com/53285909/186299116-897a0631-e4c1-49d4-a7b9-0ca814c78352.png)

![image](https://user-images.githubusercontent.com/53285909/186299244-17dc83e5-de29-47e3-acdb-0f28cc8b07fb.png)
![image](https://user-images.githubusercontent.com/53285909/186299309-4a7bf090-3d96-46f7-91a8-a40dc037cd4d.png)
![image](https://user-images.githubusercontent.com/53285909/186299376-bd828a53-73f8-4ec0-b88d-dc8702bcf1fc.png)
![image](https://user-images.githubusercontent.com/53285909/186299419-769e3c0d-5a16-4f2e-bc9d-1dde9a96bc8b.png)
![image](https://user-images.githubusercontent.com/53285909/186299735-d714d1f7-cbea-4a60-92f8-3bc0033c21e7.png)
---

# Rabbit MQ 이용시 고려사항

![image](https://user-images.githubusercontent.com/53285909/186300098-66cce9e2-d043-4008-8d0e-837c6ad47460.png)
## At most once

1. `Producer` 가 `RabbitMQ` 에 넣음.
2. `Consumer` 가 네트워크 문제 등등 으로 메시지 받기에 실패함.
3. `RabbitMQ` 는 메시지를 보냄과 동시에 메시지를 삭제함. (Queue 구조)

> 전송 중 네트워크 및 기타이슈로 컨슈머가 메시지를 받는 도중 실패한다면 메시지는 유실됩니다.

***
![image](https://user-images.githubusercontent.com/53285909/186300136-3153fa81-73f6-4441-8f93-0718938f32a0.png)
## At least once

> At least once 는 At most once 에서 메시지 손실 가능성을 없애기 위해
반대로 중복 가능성이 있는 메시지를 보내는 보장입니다.

1. `브로커` 는 `컨슈머` 의 메시지 수신 확인에 대한 응답이 오기전까지 메시지를 큐에 유지함.
2. 만약 수신에 대한 응답을 `브로커` 에게 전달할 때 오류가 발생해 연결이 끊어 진다면 큐에 남아 있는 메시지는 다른 컨슈머에게 전달되어 중복이 발생할 수 있음.

***
![image](https://user-images.githubusercontent.com/53285909/186300174-90bf36df-3429-4713-a0a9-a9a6e0b8cf40.png)
## Exactly once

> 메시지를 손실 중복없이 전달 하는 것을 보장하기 때문에 가장 안전한 방법입니다.
> 하지만 일반적으로 `Producer`, `Broker`, `Consumer` 사이 `Transaction` 처리로 인해 속도가 느립니다.

***
![image](https://user-images.githubusercontent.com/53285909/186300220-e20e8545-7db2-4561-af42-d5690589e519.png)

---

![image](https://user-images.githubusercontent.com/53285909/186300250-1e765521-c448-4cdf-bf63-e5ebd61414f6.png)

---

![image](https://user-images.githubusercontent.com/53285909/186300288-6cbd1713-0f1c-4654-b2a5-412a34b02573.png)

---

![image](https://user-images.githubusercontent.com/53285909/186300399-6dd9d433-54d5-4714-beb5-75b57219d695.png)
---

![image](https://user-images.githubusercontent.com/53285909/186300431-fc3b7a57-9a5a-4ee7-a304-005c4ef1cfd7.png)
---

# 메시지를 받지 않고 소비하기
- `RabbitMQ` 로 [RPC](https://velog.io/@jakeseo_me/RPC%EB%9E%80#rpc%EC%9D%98-%EC%9E%A5%EC%A0%90) 패턴을 구현하는 경우
- RPC 요청을 발행하는 애플리케이션이 RPC 응답을 처리하기도 한다.
- 애플리케이션이 메시지를 소비하는 방법을 결정하는 것은 균형이 필요하다.

# `메시지를 단순히 가져올것인가?` or `메시지를 소비해야 하는가?` 라는 고민을 해 볼 필요가 있다.

1. 단순히 메시지를 전달받지 않고 소비하는것이 좋은이유
2. 메시지 배달 보장과 성능 간의 균형을 유지하는 좋은 방법
3. RabbitMQ 의 큐별 설정을 사용해 큐를 자동으로 삭제하고, 메시지의 수명을 제한하는 등의 작업

# 메시지는 언제 죽은것으로 간주 되는가?

RabbitMQ 에 도달한 후 메시지를 전달할 수 없게 되는 세 가지 상황이 있음.
- 메시지가 소비자에 의해 부정적으로 확인됨.
- 메시지의 TTL 이 만료됨.
- 대기열이 용량에 도달함.

기본적으로 브로커(RabbitMQ) 는 이러한 메시지를 삭제함.   
게시에 성공했지만 RabbitMQ 소비자는 메시지를 성공적으로 처리하거나 처리하기 위한 방법이 없음.

> Exchange 에 의해 메시지가 사라진다면, 메시지들은 alternate exchange 를 사용해야함.

### `Alternate Exchange`
아무리 조심해도 실수가 생길 수 있다.   
예를 들어 클라이언트는 존재하지 않는 라우팅 키를 사용하여 실수로 또는 악의적으로 메시지를 라우팅할 수 있음.    
정보 손실로 인한 합병증을 피하기 위해 RabbitMQ `Alternate Exchange` 에서 라우팅할 수 없는 메시지를 수집하는 것은 쉽고 안전한 백업이다.

라우팅할 수 없는 메시지가 생겼음. 애플리케이션이 배달을 여러번 시도하거나 지속적으로 로깅하여   
사용 중인 경우 처리 시간이 느려짐. 또한 라우팅되지 않은 메시지를 처리하거나 삭제해야 하는 작업이 있음.
   
메시지 반환 여부에 관계없이 라우팅할 수 없는 메시지는 다음을 수행함.
- 지속적으로 재전송하는 손상된 응용 프로그램으로 돌아감.(Requeue)

# Dead Letter Exchange

> 처리할 수 없는 Message 를 전달할 Exchange 입니다.

- RabbitMQ 의 메세지가 예상치 못한 error 로 인해 처리될 수 없을 경우 발생시??
    - 다시 Queue 로 돌아가는 ReQueuing 을 수행 함.
- 수차례 동일한 에러로 인해 해당 메시지를 처리할 수 없을 경우에는??
    - 메시지는 무한히 Queue 에 담겨있게 됨.

### 해결 방안은??
- 특정 횟수 이상의 error 가 발생한 메시지는 `Dead Letter Exchange` 로 보내 적절한 error handling 과정을 거치도록 설계해야함.
    - DLX 에 메세지가 전달된 경우 admin 에게 알람을 보내도록 설계할 수 있음.
- 이때 errorHandling 같은 경우는 정책에 따라 다르게 설정 되어진다.
- 메시지의 발송이 무조건적으로 보장 되어야 한다? (e.g 결제 후 통신장애로 인한 에러 발생)
- 이때는 DLX 에 전달된 메세지의 내용을 따로 받아서 기록 하도록 하는 소비자를 만들어 기록해야한다.
- 기록이라 함은 로그로 기록하는법, DB 에 기록하는 법 등이 있다.
- 해당 기록을 본 관리자는 그에 상응 하는 처리를 한다.


### 참조자료
- [RabbitMQ](https://www.rabbitmq.com/documentation.html)
- [DLX](https://www.cloudamqp.com/blog/when-and-how-to-use-the-rabbitmq-dead-letter-exchange.html)
- [Alternate Exchange](https://www.cloudamqp.com/blog/collecting-unroutable-messages-in-a-rabbitmq-alternate-exchange.html)
- [유튜브](https://www.youtube.com/watch?v=SmE_k8lqfRQ&t=1682s&ab_channel=NHNCloud)

