### Network

---

- [컴퓨터 네트워크](#컴퓨터-네트워크)
- [교환 네트워크](#교환-네트워크)
- [OSI 7계층과 TCP/IP 5계층](#osi-7계층과-tcp/ip-5계층)
- [LAN-MAC](#lan-mac)
- [Ethernet](#ethernet)
- [ATM](#atm)
- [TCP와 UDP](#tcp-udp)
- [RIP 프로토콜과 Count-to-infinity 문제](#intra-as-라우팅-프로토콜-rip)
- [IPv4 vs IPv6](#ipv4-vs-ipv6)
- [HTTP 1.0 vs HTTP 1.1 vs HTTP 2.0](#http-1.0-vs-http-1.1-vs-http-2.0)
- [HTTP vs HTTPs](#http-vs-https)
- [RESTful](#restful)
- [DNS round robin](#dns-round-robin)

---

### 컴퓨터 네트워크

---

**구성요소**

`End System` `Network devices(router, bridge, Hub)` `Communication Link` `Protocol`

- protocol: Syntax(구문) + Semantic(의미) + Timing(타이밍)



**인터넷**

TCP/IP 기반의 전 세계 모든 네트워크 간 연결

- 인터넷 주소 = Host ID(32bits) + Process ID + Physical ID(48bits, NIC) + Address Resolution Protocol



### 교환 네트워크

---

**정의**

송수신 사이에 경로를 설정하여 데이터를 주고받는데 적절한 중간 위치에 교환기(Switch)를 설치하여 효율적인 통신이 가능하도록 해주는 네트워크 기술 



**Circuit Switching Network**

- 연결지향적
- 전송 데이터 양이 많고 오래 걸리는 실시간, 긴 메시지 전송
- 다중화 - 주파수분할 다중화(FDM), 시분할 다중화(TDM), 코드 분할 다중화(CDM), 파장분할 다중화(WDM)



**Packet Switching Network**

- 네트워크 자원 공유
- 지연, 손실, 오버헤드
- Statical TDM 방식
- 가상회선 교환방식: 연결지향적 서비스이지만 논리적 경로이기 때문에 해당 노드가 링크를 사용하지 않으면 다른 노드가 사용할 수 있다. 각 패킷은 헤더에 VCI라는 다음 홉을 결정하는 식별자를 가진다. 각 교환기는 per-call 상태를 유지한다.
- 데이터그램 방식: 각 패킷이 독립적으로 전송되고 비연결지향적 서비스를 지원한다. 오버헤드가 크고 순서가 바뀌어 전달될 수 있다.



**Message Switching Network**

- 저장 후 전송방식
- 자름과 재조립이 없어 시간 절약, 오버헤드가 작음
- 파이프라인이 없어 효율이 낮고 오류 시 전체 메시지 재전송

*cf) 저장 후 전송방식 vs 컷스루 방식*

*저장 후 전송 방식을 사용하면 다음 링크로 전송되기 위해 전체 패킷이 라우터에 도착해야 하는 반면, 컷스루 방식은 다음 홉을 결정하는 헤더만 도착하면 다음 링크로 전송될 수 있다.*





### OSI 7계층과 TCP/IP 5계층

---



**OSI 7계층**

`Physical Layer`

- 허브, 리피터, 케이블
- 데이터 전달(비트흐름)

`Data Link Layer`

- 브리지, 스위치
- 신뢰성 있는 정보 전송(Point to Point)
- 프레임(주소, 제어정보)
- 에러제어, 흐름제어

`Network Layer`

- 라우터
- 패킷 전송, 라우팅 

`Transport Layer`

- 종단 간 신뢰성있고 정확한 데이터 전송
- 세그먼트 단위
- 주서 설정, 에러제어, 흐름제어, 다중화
- TCP, UDP

`Session Layer`

- 통신장치 간 상호작용 및 동기화
- 데이터 교환, 에러 복구 관리
- 메시지 단위

`Presentation Layer`

- 인코딩, 디코딩, 암호화, 코드변환
- 메시지

`Appication Layer`

- 메시지
- HTTP, FTP, DNS, SMTP





**TCP/IP 5계층**

`Physical Layer`

- 시스템 파라미터, 비트 동기화, 데이터 전송률, 물리적 부착 종류

`Datalink Layer`

- 물리주소 할당, 신뢰성 있는 전송, 프레이밍, 에러제어, 흐름제어, 매체접근제어

`Network Layer`

- Routing, 패킷 순방향 전송(데이터그램), 에러 검출

`Transport Layer`

- 다중화와 역다중화, Segment & Reassembly, 연결지향적 서비스, 에러제어, 흐름제어, 혼잡제어

`Application Layer`

- HTTP, SMTP, FT, SNMP, DNS





### LAN-MAC

---

**LAN**

- 빠른 전송, 낮은 패킷 지연, 네트워크 자원의 공유, 확장 및 재배치에 유연
- Unicast, Multicast, Broadcast
- Ethernet, 토큰 링, 토큰 버스, FDDI, ATM



**LAN Segment를 연결하는 장치**

- 리피터: 물리계층 장비, 파형 재생
- 허브: 물리계층 장비, 다중포트 리피터

*리피터와 허브를 사용하여 LAN을 확장해도 효율성은 향상되지 않음*

- 브리지: 링크계층 장비, 이더넷 프레임 처리, 충돌영역 분리, CSMA/CD, 전송 효율 증가



**MAC**

- 여러 사용자가 같은 자원을 사용하려고 할 때 누가 자원을 이용할지 결정 내려주는 프로토콜

  `Channelization Protocols` : FDMA, CDMA, TDMA

  `Controlled-access Protocols` : Polling MAC, Token Passing MAC, 예약기반 MAC

  `Random access Protocols` : ALOHA, CSMA, CSMA/CD, CSMA/CA

- ALOHA: 여러 스테이션들이 하나의 무선 전송 매체를 공유하는 것이다. Pure ALOHA는 스테이션이 데이터를 보낸 후 RTT 동안 ACK를 기다리고, 받지 못하면 임의의 시간 후 재전송하고 실패가 계속되면 전송을 포기한다. Slotted ALOHA는 데이터 전송 시간을 슬롯으로 나누고 각 슬롯의 시작에만 데이터를 전송할 수 있도록 한다.

- CSMA(Carrier-Sense Multiple Access): 전송하기 전에 매체의 이용 여부를 감지한다. 계속 감지하다 idle이 되면 바로 전송하는 1-persistent CSMA, busy일 때 임의의 시간 후 다시 listen하는 Non-persistent CSMA, idle이어도 p 확률로 전송하는 p-persistent CSMA 모드가 있다. CSMA는 충돌이 일어날 경우 재전송 시간은 노드가 데이터를 전송하고 다시 돌아와 Ack가 없는 사실을 확인한 이후라는 한계가 있다.-

- CSMA/CD: 충돌이 검출되면 즉시 전송을 멈추고 48bits의 jamming signal을 보내 링크의 모든 노드에 충돌 사실을 알리고, 임의의 시간 후 재전송한다. Ethernet의 기본 동작 방식이다.

- CSMA/CA: 무선 환경에서 약한 수신호로 인해 충돌 검출이 힘들기 때문에, 전송 전에 감지하여 충돌을 회피하는 방법이다. 숨은 터미널문제와 신호 감쇄 문제를 해결하기 위해, 전송 전에 수신 스테이션에 RTS(Request To Send) 신호를 보낸다. 이 신호를 받은 수신 스테이션은 수신이 가능하면 CTS(Clear To Send) 신호를 보내는데, 이 신호는 전송 스테이션 뿐 아니라 주변 스테이션에 전송되고 이를 통해 충돌을 회피할 수 있다. 전송 원리는 전송 스테이션이 매체가 idle 때까지 감지하다가, idle 일 때, IFS 만큼 기다리는데 이는 우선순위와 관련되어있고 각 노드마다 다른 값을 가진다. IFS 시간 후, 랜덤 크기의 window를 부여받고, window의 크기가 0이되면 전송함으로써 충돌을 회피한다.  



### Ethernet

---

- CSMA/CD MAC Protocols
- 48bits jamming signal
- Binary Back-Off 알고리즘: 재전송 시 재충돌의 확률을 낮추기 위해 사용하는 알고리즘. 
- 최소프레임의 크기 = 대역폭 * 왕복전파지연
- 이더넷의 효율 = 1/1+5*(전파지연/전송지연)



### ATM

---

**ATM(Asynchronous Transfer Mode)이란?**

음성, 비디오, 데이터를 통합하는 종단간 서비스를 지원하는 전송모드 

- 가상회선 교환방식 - VCI, QoS
- 헤더: VCI, CLP(혼잡시 폐기되는 우선순위에 관한 비트), HEC(Header Error Checksum)



**ATM 3계층**

`adaptation layer`

네트워크의 노드에만 존재하고 TCP/IP 5계층의 전송계층과 유사

`ATM layer`

네트워크 계층과 유사

`physical layer`

물리 계층과 유사



### TCP UDP

---

**TCP**

- 신뢰적, 연결지향적 서비스  -> 신뢰성 없는 인터넷을 통해 신뢰성 있는 바이트 스트림 전송을 가능하게 함
- 전이중 방식, 파이프라인 동작, 점대점 
- 신뢰성 지원 매커니즘: Ack, Timer, 재전송, 흐름제어, 혼잡제어
- 소켓 통신
- 3-way-handshake: 연결 수립(SYN -  SYN + ACK - ACK)
- 4-way-handshake: 연결 해제(FIN - ACK - (timewait) - FIN - ACK)
- 멀티태스킹, 브로드캐스팅 지원하지 않음
- SMPT, Telnet, HTTP, FTP

**UDP**

- 비연결성 서비스(no handshaking), 최선형 서비스
- 다중화, 역다중화, 에러검출
- 오버헤드 작음
- 손실에 관용적인 멀티미디어 응용 전송, DNS, SNMP에 활용







**TCP의 흐름제어**

TCP 흐름제어는 송신자가 너무 많은 데이터를 너무 빨리 보냄으로써 수신자 버퍼가 감당할 수 없을 정도로 전송하는 것을 제어하는 것을 말한다. 수신자 호스트는 송신자에게 버퍼의 여유 공간을 **RcvWindow** 필드에 넣어서 보내고, 버퍼가 꽉차면 그만 보내라는 의미로 RcvWindow 필드에 0 값을 넣어 보냄으로써 제어한다.



**TCP의 혼잡제어**

`original TCP` 연결이 시작되면 congwind=1부터 첫 번째 손실이 발생할 때까지 **지수함수적**으로 window수를 증가시킨다(**slowstart**). 임계치에 도달하게 되면 선형으로 증가하고, Timeout이 발생하면 다시 congwind=1로 돌아가서(**Congestion-avoidance**) slow start부터 수행한다. 이 때, 임계치는 timeout 시점 window의 절반으로 변경된다. 

`TCP reno` 연결이 시작되면 congwind=1부터 첫 번째 손실이 발생할 때까지 지수함수 적으로 window 수를 증가시킨다. 임계치에 도달하게 되면 선형으로 증가하고, **3 duplicate Ack**를 수신하면 임계치를 그 시점 window 크기의 절반으로 바꾸고, 임계치부터 선형으로 증가한다. TCP reno는 duplicate ACK로 **빠른 재전송**을 지원하고, 임계치부터 선형으로 증가함으로써 **빠른 회복**을 지원한다. 



**실리윈도우 신드롬**

실리윈도우 신드롬이란 송신 호스트는 최대 rcvwnd 크기의 데이터만 보낼 수 있는데, 수신 호스트가 데이터를 천천히 처리하거나 송신 호스트가 한번에 작은 데이터만을 보낼 때 발생하는 대역폭 낭비 문제를 말한다. **송신 호스트 문제일 때의 해결책은, 먼저 1byte 데이터를 즉각 송신한 후, MSS 사이즈가 되거나 수신 측에서 Ack가 올 때까지 전송을 지연하는 것(Nagle's 알고리즘)**이다. 수신 호스트 문제일 때의 해결책은, 버퍼의 50%가 되거나 MSS에 도달할 때까지 새로운 rcvwnd를 보내는 것을 지연하거나, Ack 보내는 것을 지연시키는 것이다. 



**Karn's 법칙**

TCP 재전송 타이머를 위해 예측 RTT를 계산할 때, 이전에 전송된 세그먼트의 값을 참고하게 되는데 만일 재전송된 세그먼트라면 그 값을 참고하지 않는 다는 법칙이다. 즉, **정규 RTT를 수신하기전까지 새로운 RTT를 업데이트 하지 않는 다는 것**이다.


​    



### Intra AS 라우팅 프로토콜 RIP

---

**RIP는 경로 비용을 홉 수로 판단**하는 프로토콜이다. 최대 홉 수는 15이고, 16일 시 연결이 끊어졌다고 판단한다. RIP에서 각 라우터는 advertisement라고 불리는 응답 메시지를 통해 매 **30**초마다 이웃과 DV를 교환하는데, 이 메시지는 최대 25개 목적지 서브넷의 목록을 포함한다. RIP 패킷은 UDP에 의해 전송되므로 손실될 수 있다는 특징을 가진다. 만일 **180초 이내에 advertisement를 받지 못하면 이웃이나 링크가 dead되었다고 판단**하고 그 루트는 무효화된다. 이때, failure 정보는 새로운 advertising을 통해 빠르게 전체 네트워크로 전파된다.

RIP는 세 종류의 타이머를 사용한다. 정규 업데이트 메시지 advertising을 제어하며 25~35초 사이를 임의로 동작하는 **주기 타이머**, 루트의 유효성을 판단하며 180초로 세팅되고, 만기 시 cost=16이 되는 **만기 타이머**, 루트에 관한 정보가 invalid되면, 라우터에서 바로 루트를 제거하지 않고 timer 120초 동안 cost=16을 advertising한 후 라우터에서 제거하는 **Garbage Collection Timer**가 있다.



**count-to-infinity**

 RIP에서 네트워크 연결이 끊어졌을 때, 해당 내용을 advertising하기 전에 이웃 노드에서 자신의 DV를 advertising함으로써, cost=16의 정보가 3으로 바뀌고 이러한 동작이 계속되는 문제를 말한다. 정보를 받은 쪽에서 정보를 줌으로써 발생하는 문제이다. 



**해결방안**

- split-horison: 정보를 받은 쪽에서 정보를 주지 못하도록 금지
- Poisoned Reverse: 수신한 인터페이스로 cost=16을 전송하게 하는 것
- Hold Down: unreachable 메시지 수신 호, 일정 기간 동안 새로운 업데이트 정보 무시하는 것



*cf)  **intra-AS 라우팅 프로토콜: OSPF(Open Shortest Path First)***

*OSPF는 **링크상태 알고리즘**을 사용하며 advertisement는 이웃 라우터 당 하나의 엔트리만 운반하고, 링크 상태가 변하면 broadcast되거나 정규적으로 적어도 30분 간격으로 전송된다. 이 메시지는 **IP위에서 직접 전파**된다. OSPF는 RIP에 비해 **보안**의 측면이 개선되었고, 서비스 타입 별로 **다중 경로**를 지원한다는 특징이 있다. 통합된 uni-and-multicast를 지원하며 커다란 영역에서 **계층화** 되어있다.*

***Inter-AS 라우팅 프로토콜: BGP(Boarder Gateway Protocal)***

*이웃 AS로부터 reachability 정보를 얻고 내부 라우터들에게 그 정보를 전파시킨다. 그리고 그 정보와 정책을 기반으로 good route를 결정한다. 이 때, **이웃 AS와 통신하는 경계 라우터에 설치된 것을 eBGP세션**이라고 하고, **내부 라우터에 설치된 것을 iBGP 세션**이라고 한다. BFP 메시지는 **TCP**를 사용하여 교환되기 때문여 연결하고 인증하는 메시지를 포함한다.*



### IPv4 vs IPv6

---

**IPv4**

- 비연결성 서비스, 비신뢰성 서비스
- 최선형 서비스, 비보안적 서비스
- 32bits의 ip주소



**IPv6**

- 확장된 헤더 형식
- 빠른 처리, 순방향 전송, QoS
- 재조립 시간 감소, check sum이 없기 때문에 계산 시간 절약
- 128bits의 주소 공간



**Network Mask**



**Subnet Mask**



**Gateway**



**Network Address**



### HTTP 1.0 VS HTTP 1.1 VS HTTP 2.0

---

**HTTP Method**

- OPTIONS: 요청한 URL에 어떠한 메소드 요청이 가능한지 묻는다.
- GET: 다른 작업없이 데이터의 검색에 이용.
- HEAD: 데이터의 검색에 이용하나 GET과는 다르게 응답 HEADER만 받는다.
- POST: URL에 새로운 데이터를 보낼때 사용.
- PUT: URL에 저장될 정보를 보낸다.
- DELETE: URL의 리소스를 삭제한다.
- TRACE: 보낸 메세지를 다시 돌려받는다.
- CONNECT: 프록시에서 사용되는 예약 메소드.



**HTTP 1.0**

TCP connection에서 한번에 하나의 요청만 가능

**HTTP 1.1**

- request pipelining 사용(개선), 요청과 응답이 순차적 
- HOL Blocking: 파이프라이닝 요청으로 인한 문제점으로, 먼저 처리가 시작된 하나의 요청이 진행되지 않으면 뒤이어 들어오던 요청들도 처리되지 못하는 현상을 말한다. 
- RTT 증가: 하나의 연결에 하나의 요청을 처리하기 때문에 3-way-handshaking의 반복 
- 무거운 header: 매 요청마다 중복된 헤더 값 전송, 특히 쿠키 

**HTTP 2.0**

- HTTP 헤더 압축
- Server Push
- HOL Blocking 개선
- 싱글 TCP connection에서 병렬 페이지 로딩 구현 
- 높은 호환성 



### HTTP vs HTTPs

---

**HTTP의 문제점**

- 평문 통신이기 때문에 도청이 가능함: SSL 또는 TSL이라는 다른 프로토콜과 조합함으로써 통신 내용을 함호화 할 수 있다. SSL과 조합한 HTTP를 HTTPs라고 한다.
- 통신 상대를 확인하지 않기 때문에 위장이 가능함: SSl에서 제공하는 증명서를 통해 상대를 인증할 수 있다.
- 완전성을 증명할 수 없기 때문에 변조가 가능함



**HTTPs**

SSL과 조합한 HTTP로 공통키 암호화 방식과 공개키 암호화 방식을 혼합하여 사용한다. 평문 통신에 비해 암호화 통신은 리소스가 많이 필요하기 때문에, 민감한 정보를 다룰 때만 HTTPS를 사용하는 방식을 취한다.

- SSL/ TSL: 전송계층 프로토콜

***cf) Http status code***

- 200:
- 301:
- 403:
- 404:
- 429:



### RESTful

---

**REST의 특징**

- Uniform: URI로 지정한 리소스에 대한 조작을 통일되고 한정적인 인터페이스로 수행하는 아키텍처 스타일 
- Stateless: 서버에 클라이언트의 컨텍스트를 유지하지 않음
- Cacheable: HTTP 웹표준을 그대로 사용. 캐싱 기능 적용 가능
- Self-descriptiveness: REST API 메시지만 보고도 쉽게 이해할 수 있음
- Client-Server Architecture: REST 서버는 API를 제공하고 클라이언트는 사용자 인증, 컨텍스트를 직접 관리하고 책임짐. 서로간의 의존성이 줄어들음
- 계층형 구조



**REST 구성**

`Resource - URI` `Verb - HTTP Method: GET, PUT, POST, DELETE` `Representations`



*cf) Restful 하지 않다?*





### DNS Round Robin

---

**정의**

DNS를 이용해서 하나의 서비스에 여러 대의 서버를 분산시키는 방법. 동일한 이름으로 여러 레코드를 등록시키면 질의할 때마다 다른 결과를 반환하여 여러 대의 서버에 처리를 분산시킬 수 있음

**문제점**

- 서버의 수 만큼 공인 IP 주소가 필요함 
- 균등하게 분산되지 않음
- 서버가 다운되어도 확인 불가



**DNS 스케줄링 알고리즘**

`weighted round-robin`

각각의 웹 서버에 가중치를 가미해서 분산 비율을 변경한다. 물론 가중치가 큰 서버일수록 빈번하게 선택되므로 처리능력이 높은 서버는 가중치를 높게 설정하는 것이 좋다.

`least-connection`

접속 클라이언트 수가 가장 적은 서버를 선택한다. 로드밸런서에서 실시간으로 connection 수를 관리하거나 각 서버에서 주기적으로 알려주는 것이 필요하다.



### 쿠키 vs 세션

---





### Connection Timeout vs ReadTimeout



### Soket이란

