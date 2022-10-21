# getsockname
## name
> getsockname - 소켓 이름 가져오기

## synopsis
``` c
include <sys/socket.h>

int getsockname(int sockfd, struct sockaddr *restrick addr,
				socklen_t *restrict addrlen);
```

## description
- getsockname()은 소켓 sockfd가 바인딩된 addr가 가리키는 버퍼의 현재 주소를 리턴합니다.
- addrlen 인자는 addr가 가리키는 공간의 크기를 나타내기 위해 초기화 되어야 합니다.
- 리턴시 소켓 주소의 실제 크기를 포함합니다.
- 만약 버퍼가 충분하지 않으면 리턴된 주소가 잘립니다.
- 이 경우에 addrlen 은 인자로 넣은 크기보다 더 큰값을 리턴할 수 있습니다.

## return value
- 성공시 0을 리턴합니다.
- 실패시 -1을 리턴하고 오류를 나타내기 위해 errno가 설정됩니다.

## errors
- EBADF : sockfd 인자가 유효한 파일 디스크립터가 아닙니다.
- EFAULT : 잘못된 프로세스 메모리 주소에 접근하였습니다.
- EINCAL : addrlen이 유효하지 않습니다.(음수)
- ENOBUFS : 리소스가 충분하지 않습니다.
- ENOTSOCK : 파일 디스크립터 sockfd는 소켓을 참조하지 않습니다.

#webserv 