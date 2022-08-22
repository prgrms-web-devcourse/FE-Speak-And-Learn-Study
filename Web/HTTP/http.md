# http

## http란?

- http란, hypertext transfer protocol의 약자로 월드 와이드 웹에서 문서를 전송하기 위한 프로토콜입니다.
- http는 클라이언트 - 서버 기반 프로토콜로, TCP/IP 연결로 동작하며 포트는 well-known 포트인 80번 포트를 사용합니다.

## http의 버전과 역사

### http 0.9

- 가장 큰 특징은 http header가 없었고, 요청 method 역시 GET 한 종류 말고 없었습니다.

### http 1.0

- http 헤더의 개념이 처음 생겼고, 따라서 다양한 타입의 데이터 형식을 지원합니다.
- method 역시 GET, POST, PUT이 생겨 이전보다 더 다양한 요청 형식을 지원합니다.

### http 1.1 (표준 프로토콜)

- `{ Connection: 'keep-alive' }` 옵션을 지원하여 일정 시간 또는 일정 횟수 이내에서 서버와 클라이언트의 연결을 유지할 수 있습니다.
- **데이터 파이프라이닝**을 지원합니다.

#### 데이터 파이프라이닝이란 무엇인가요?

- 데이터 파이프라이닝이란, 어떤 요청을 보낼 때, 해당 요청에 대한 응답이 오기를 기다리지 않고 다음 요청을 보낼 수 있는 형태를 뜻합니다.
- 다만 데이터 파이프라이닝은 `HOL(Head Of Line) Blocking`이 발생하는 문제가 있습니다.

#### HOL Blocking 문제

- 요청에 대한 응답을 뒤로 미룰 수 있지만, 요청에 대한 응답이 도착할 때 까지 해당 요청 다음 요청에 대한 응답이 먼저 도착했더라도 첫 번째 응답이 도착할 때까지 기다리는 현상을 뜻합니다.

### http 2.0

- data 파이프라이닝 문제를 해결하기 위하여 `Multiflexing` 데이터 전송 개념을 도입하였습니다.
- `request`, `response`를 각각 독립적으로 처리하여 해당 요청에 대한 응답이 도착하지 않아도, 요청을 보내거나 다른 요청에 대한 응답을 받을 수 있게 됩니다.

### http 3.0

- 기존 TCP/IP 프로토콜의 문제점을 해결하기 위하여 등장했습니다.
  - TCP/IP 연결 과정은 `3-way handshaking`으로 연결을 시도하고, 종료할 때 `4-way handshaking`으로 연결을 종료합니다.
  - 이 방식은 많은 오버헤드 비용이 발생하며 네트워크 레이턴시를 발생시키는 원인이 됩니다.
- 이를 해결하기 위하여 http3.0 부터는 TCP/IP가 아닌 UDP 통신을 기반하여 동작하게 됩니다.

## htttp의 특징은 무엇인가요?

### 비연결성(connectionless)

http 프로토콜은 서버와 클라이언트가 연결 된 후, 서버와 클라이언트간 통신이 종료되면 연결을 끊어버리는 특성이 있습니다.  
http는 불특정 다수와의 통신을 목적으로 만들어졌기 때문에 클라이언트와 서버간 연결이 유지된다면 리소스가 많이 소모되고, 더 많은 클라이언트가 서버에 접근할 수 없게 됩니다. 따라서 http는 서버와 클라이언트간 통신이 종료되면 연결을 끊습니다. 다만, 연결을 끊고 다시 연결하는 과정은 많은 오버헤드 비용이 발생하게 됩니다.

### 어떤 비용?

연결을 시도하고 종료할 때 handshake 과정을 거치므로, 시간적인 소모가 많이 이루어집니다.

### 연결을 유지하는 방법은 뭐가 있나요?

http헤더에 `{ Connection: Keep-alive }` 옵션을 부여하면, 일정 시간 동안 연결이 유지되고, 3-way handshake 과정이 생략됩니다. 다만 이는 http1.1 이상부터 기본 옵션으로 지정되어 있습니다.

### 무상태성(stateless)

각각 요청이 독립적으로 이루어지기 때문에 서버는 클라이언트의 상태를 기억하지 않습니다. 따라서 클라이언트에 따른 정보를 달리 보여주게 하기 위해서는 세션, 쿠키, JWT 등을 함께 헤더에 담아서 요청합니다.

## http 메서드의 종류

- GET (Read): 서버에 리소스를 요청
- POST(Create): 서버의 body 생성할 데이터를 포함하여 함께 전송
- PUT(Update): 서버의 body에 수정할 데이터를 포함하여 함께 전송
- DELETE(Delete): 서버에 삭제할 리소스를 요청
- PATCH: PUT과 비슷하지만, 데이터의 일부만 수정하도록 할 수 있음
- HEAD : GET과 같이 작용하지만, 메세지의 바디는 받지 않습니다.(URI 유효성확인, 리소스 갱신 시간 확인 용도 사용)
- OPTIONS: 서버에서 사용가능한 메소드를 확인하기 위한 메서드
- TRACE: 통신 경로를 받기 위해 사용할 수 있지만, 실제 사용빈도가 매우 낮으며, 보안상 이슈도 존재한다.
- CONNECT: 프록시에 접속 확립을 요구함으로써, TCP 통신의 터널링을 위해 사용

## HTTP status code

HTTP 요청이 성공했는지 실패했는지를 서버에서 알려주는 코드이다.

## http Status

- 2XX (Success):
  - 200 (OK): 서버가 클라이언트 요청을 성공적으로 처리했다는 의미이다.
  - 204 (No Content): 요청은 성공했는데, 돌려줄 resourse는 없을 때
- 3XX (redirect): 요청 완료를 위해 추가 작업 조치가 필요하다는 의미이다.
  - 301: 검색엔진에 해당 컨텐츠의 URL이 영구적으로 변경되었음을 알림
  - 302: 검색엔진에 해당 컨텐츠의 URL이 임시적으로 변경되었을을 알림
- 4XX (Client Error): 클라이언트의 요청이 유효하지 않아 서버가 해당 요청을 수행하지 않았다는 의미이다.
  - 400 : 클라이언트 요청이 유효하지 않아 더 이상 작업을 진행하지 않는 경우
  - 401 (Unauthorized): 클라이언트가 권한이 없기 때문에 작업을 진행할 수 없는 경우
  - 403 (Forbidden): 접근이 거부됨을 알림
  - 404 (Not Found): 해당 요청에 대한 resourse가 없음을 알림
- 500 (Server Error)
  - 503 (Unavailavle):
    현재 과부화 혹은 점검 중으로 사용불가함을 알림

## http의 통신 과정에 대하여 설명해주세요.

http 통신 과정은 다음 7개 과정으로 간단히 나타낼 수 있습니다.

> 1. 주소창에 URL을 입력후 엔터를 치면 URL을 해석
> 2. DNS를 조회하여 IP 탐색
> 3. IP주소를 이용하여 ARP 프로토콜을 통해 MAC주소 검색
> 4. 웹서버와 TCP 연결 시도
> 5. 서버에 요청을 하고 응답을 반환
> 6. 연결 종료

## 1. 주소창에 URL을 입력

주소를 치고 엔터를 누름과 동시에 통신이 시작됩니다. 먼저, URL을 해석하는 과정을 거치게 됩니다.

## 2. DNS를 조회하여 IP를 탐색

주소창에 URL을 입력하면, 가장 먼저 URL과 연결된 DNS서버로 이동하여 URL에 할당된 IP 주소를 찾습니다. `DNS`는 `Domain name system`의 약자로 호스트의 도메인을 IP로 변환하거나 그 반대의 변환을 수행할 수 있도록 도와줍니다.  
다만 브라우저에 캐시가 남아있다면, DNS 서버에 접근하지 않아 시간을 절약할 수 있습니다.

## 3. IP주소를 이용하여 ARP 프로토콜을 통해 MAC주소 검색

IP 주소는 논리 주소이므로 실제 서버의 위치를 알 수 없습니다. 따라서 **ARP`(Address Resolution protocol)`**을 이용하여 해당 서버의 MAC주소를 찾고, 서버와 통신을 시도합니다.

### ARP란 무엇인가요?

논리 주소인 IP주소를 물리 주소인 MAC 주소로 변환하는 프로토콜이다.

### 왜 IP주소가 아닌 MAC 주소를 통신을 위해 사용할까요?

- IP주소는 컴퓨터 네트워크에서 장치들이 서로 인식하고 통신하기 위해서 사용하는 특수 번호로, '서울특별시 중구 태평로'처럼 규칙에 의해 만들어진 값이므로, 언제든지 변할 수 있는 값입니다.
- 반면에 MAC주소는 네트워크 세그먼트 데이터 링크 계층에서 통신을 위한 인터페이스에 할당된 고유 식별자이다. MAC주소는 논리주소인 IP주소와 달리, 고유주소이기 때문에 장치의 실제 위치를 알기 위해서는 MAC주소가 필요합니다.

## 4. 웹서버와 TCP 연결 시도

> `TCP`란, Transmission Control Protocol의 약자로 컴퓨터와 데이터 통신을 위한 규약의 일종입니다.

클라이언트와 서버가 TCP 연결을 시도하여 성공하면, 통신 준비를 마쳤고 현재 통신이 연결되어 있음을 보장합니다. TCP 통신 절차는 `3-way handshake` 과정을 거칩니다.

### 3-way handshake 과정이란?

클라이언트와 서버간 신뢰성 있는 연결을 하기 위하여 3번의 통신 과정을 거치게 됩니다.

1. SYN(synchronize sequence numbers): 클라이언트가 서버로 임의의 시퀀스 번호를 전달
2. SYN-ACK: 서버는 클라이언트가 서버로 전달한 시퀀스에 1을 더하여 클라이언트로 전달
3. ACK(acknowledgement): 클라이언트는 서버에서 전달해준 시퀀스 + 1하여 다시 서버로 전달

### 4-way handshake 과정이란?

최초에는 서로 통신 상태이기 때문에 양쪽이 ESTABLISHED 상태입니다.

1. 클라이언트가 통신을 종료하자는 FIN 데이터를 보내고, 자신의 상태를 종료요청 후 ACK를 기다리고 있다는 의미로 FIN_WAIT_1 상태로 바꿉니다.
2. 서버는 알겠다는 의미로 클라이언트에 ACK 데이터를 보내면서 애플리케이션의 소켓을 닫습니다.
   1. 이 때 자원을 정리하는데 시간이 소요 되므로, 소켓을 닫는데 기다리고 있는 상태라는 의미에서 CLOSE_WAIT 상태로 바꿉니다.
   2. 그 동안 연결 종료를 요청한 클라이언트는 서버로부터 응답이 올 때까지 기다리고 있다는 FIN_WAIT_2 상태가 됩니다.
3. 애플리케이션에서 소켓을 닫으면, 서버는 클라이언트로 FIN 데이터를 보냅니다.
   1. 그리고 자신은 클라이언트로부터 마지막 ACK를 기다리고 있는 LAST_ACK 상태가 됩니다.
4. 클라이언트는 FIN 데이터를 받으면 TIME_WAIT 상태로 바꾸면서 서버에 ACK 데이터를 보냅니다.
   1. 클라이언트는 TIME_WATE 상태에서 일정 시간이 되면 CLOSED 상태가 됩니다.
5. ACK 데이터를 받은 서버 역시 CLOSED 상태가 됩니다.

## 5. 클라이언트가 요청 후, 서버는 응답

클라이언트는 GET, POST, PUT, DELETE 요청을 서버로 요청하면, 서버는 그에 맞는 데이터와 상태를 클라이언트에 응답합니다.  
**GET, POST, PUT, DELETE** 메소드를 묶어 CRUD를 구성할 수 있습니다.

## 6. 통신 종료

TCP 통신을 종료하기 위하여 `4-way handshake`절차를 거칩니다.

# 출처

[주소 결정 프로토콜](https://ko.wikipedia.org/wiki/%EC%A0%84%EC%86%A1_%EC%A0%9C%EC%96%B4_%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C#%EC%97%B0%EA%B2%B0_%EC%83%9D%EC%84%B1)
[http 요청 흐름의 이해](https://feco.tistory.com/127)
[http 프로토콜의 발전](https://velog.io/@gjrjr4545/HTTP-%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C-%EB%B0%9C%EC%A0%84)
[http 상태 코드](https://ko.wikipedia.org/wiki/HTTP_%EC%83%81%ED%83%9C_%EC%BD%94%EB%93%9C)