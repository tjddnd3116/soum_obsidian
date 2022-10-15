# listen
## name
> listen - 소켓에서 연결을 수신합니다.

## synopsis
``` c
#include <sys/socket.h>

int listen(int sockfd, int backlog);
```

## description
- 서버측 프로그램은 socket(2) 함수를 이용해서 클라이언트의 연결을 받아들일 듣기 소켓을 만들게된다.
- 클라이언트의 연결은 듣기소켓을 통해서 이루어지는데 클라이언드틑 connect(2)를 호출해서 서버에 연결을 시도하고 hand-shake가 성공하면 서버와의 완전한 연결이 만들어진다.
- 만들어진 연결은 queue에 들어가게 되고 서버측에서 accept(2)를 호출하면 비로서 서버는 연결 소켓을 만들고 만들어진 연결소켓을 이용해서 클라이언트와 통신하게된다.
- listen(2) 시스템 콜은 SOCK_STREAM과 SOCK_SEQPACKET에만 사용된다.
- sockfd는 socket(2)에 의해서 만들어진 듣기 소켓이다.
- backlog는 연결이 대기할 수 있는 큐의 갯수이다.
- 만약 backlog에 연결이 모두 찬 상태에서 새로운 연결을 시도한다면 클라이언트는 ECONNEREFUSED 에러를 받게된다.
- 만약 재전송을 지원하는 프로토콜을 사용한다면 에러를 무시하고 성공할 때까지 재시도를 하게 된다.

## return value
- 성공시 0을 리턴한다.
- 실패시 -1을 리턴하고 error 표시를 위한 errno이 설정된다.

## errors
- EADDRINUSE : 다른 소켓이 동일한 포트를 사용하고 있다.
- EBADF : sockfd가 잘못된 파일 디스크립터이다.
- ENOTSOCK : 파일디스크립터 sockfd이 소켓을 참조하지 않는다.
- EOPNOTSUPP : 소켓이 listen()을 지원하지 않는 타입이다.

## notes
- 연결을 수락하려면 다음 단계를 수행해야합니다.
	1. socket(2)으로 소켓을 생성합니다.
	2. 소켓은 bind(2)를 사용하여 로컬 주소에 바인딩 되므로 다른 소켓이 connect(2)될수 있습니다.
	3. 연결 요청의 수락과 연결 요청을 위한 큐 제한은 listen()으로 지정됩니다.
	4. accept(2)로 연결을 수락합니다.