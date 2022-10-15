## Nginx란?
- Nginx는 경량 웹 서버이다.
- 클라이언트로부터 요청을 받았을때 요청에 맞는 정적파일을 응답해주는 HTTP Web Server로 활용된다.
- Reverse Proxy Server로 활용하여 WAS 서버의 부하를 줄일 수 있는 로드 밸런서로 활용되기도 한다.

## Nginx 흐름
- Nginx는 Event-Driven 구조로 동작하기 때문에 한 개 또는 고정된 프로세스만 생성하여 사용하고 비동기 방식으로 요청들을 Concurrency 하게 처리할 수 있다.
- 프로세스와 쓰레드 생성비용이 존재하지 않고 적은 자원으로도 효율적인 운용이 가능하다.

## Nginx의 구조
- Nginx는 하나의 Master Process와 다수의 Worker Process로 구성되어 실행된다.
-  Master Process는 설정파일을 읽고 유효성 검사 및 Worker Process를 관리한다.
- 모든 요청은 Worker Process에서 처리한다.
- Nginx는 이벤트 기반 모델을 사용하고, Worker Process 사이에 요청을 효율적으로 분배하기 위해 OS에 의존적인 메커니즘을 사용한다.

#[[docker]] #[[dockerfile -nginx]] #[[Nginx conf]]