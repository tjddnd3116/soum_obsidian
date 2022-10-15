# send
## name
> send - 소켓으로 데이터를 전송합니다.

## synopsis
``` c
#include <sys/socket.h>

ssize_t send(int sockfd, const void *buf, size_t len, int flags)
```

## description
- sockfd : 열린 소켓 파일디스크립터
- buffer : 읽은 데이터를 저장하기 위한 버퍼
- length : 소켓 버퍼로 부터 읽어올 크기
- flags : 옵션 플래그

- send 함수는 buffer에 있는 데이터를 lenth 바이트 길이만큼, 소켓 파일 디스크립터 sockfd를 이용해서 전송한다.
- 이 함수는 연결 지향 소켓에 사용할 수 있다
- 만약 데이터 그램 지향 소켓을 이용해서 데이터를 전송하길 원한다면 sendto 함수를 사용해야 한다.

- flags로 사용할 수 있는 값
	- MSG_OOB : OOB(out-of-band)데이터를 전송한다.
	- MSG_WAITALL : 요청한 데이터의 크기가 모두 차야지 함수를 반환한다. 하지만 이식성의 문제로 이 플래그를 사용하는 일은 드물다. 그냥 반복해서 데이터를 읽는 방법을 권장한다.
	- MSG_DONTWAIT : non-block으로 작동하도록 한다. non-block으로 작동하게 하려면 fcntl(2) 함수로 O_NONBLOCK 설정을 해주어야 한다. non-block 으로 작동하면 recv함수는 바로 리턴된다. 이때 EAGAIN 혹은 EWOULDBLOCK을 errno으로 설정한다. 프로그램은 errno 값을 읽어서 소켓에 에러가 발생해서 반환한 것인지 아니면 non-block 특성으로 반환한 것인지 확인할 수 있다.