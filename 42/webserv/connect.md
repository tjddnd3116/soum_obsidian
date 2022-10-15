# connect
## name
> connect - 소켓의 연결을 시도합니다.

## synopsis
``` c
#include <sys/socket.h>

int connect(int sockfd, const struct sockaddr *addr,
		   socklen_t addrlen);
```

## description
-  connect() 시스템콜은 addr에 지정된 주소로 파일 디스크립터 sockfd 소켓을 연결합니다.
- addrlen 인자는 addr의 크기를 지정합니다.
- addr의 주소 형식은 소켓 sockfd의 주소 공간에 의해 지정됩니다.

- 만약 소켓 sockfd가 SOCK_DGRAM 타입이면 addr는 기본적으로 데이터그램이 전송되는 주소이고 유일하게 데이터그램이 수신되는 주소입니다.
- 만약 소켓이 SOCK_STREAM 이나 SOCK_SEQPACKET의 유형이라면 addr로 지정된 주소에 바인딩된 소켓에 연결을 시도합니다.

- 일부 프로토콜 소켓(unix domain stream sockets)은 한번으로 성공적으로 connect() 할수 있습니다.
- 일부 프로토콜 소켓(datagram sockets internet domains)은 그들의 구성을 변경하기위해 connect()를 여러번 사용할 수있습니다.
- 일부 프로토콜 소켓(tcp)는 AF_UNSPEC으로 설정된 sockaddr의 sa_family 맴버 주소와의 연결에 의해 구성을 파기할수 있습니다.
- 그후 소켓은 다른 주소로 연결됩니다.

## return value
- 연결이나 바인딩이 성공하면 0을 반환합니다.
- 실패시 -1을 리턴하고 오류를 나타내기 위한 errno이 설정됩니다.