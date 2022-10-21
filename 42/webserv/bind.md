# bind
## name
> bind - 소켓에 이름을 지정한다.

## synopsis
``` c
#include <sys/socket.h>

int bind(int sockfd, const struct sockaddr *addr,
		socklen_t addrlent);
```

## description
- socket(2)로 소켓을 생성하면 네임 스페이스(address family)는 존재하지만 할당된 주소는 없습니다.
- bind()는 sockfd 파일 디스크립터에 의해 참조된 소켓 addr에 의해 지정된 주소를 할당합니다.
- addrlen은 addr가 가리키는 주소 구조체의 크기를 바이트로 지정합니다.
- 전통적으로 이러한 작업을 "소켓에 이름을 할당한다." 라고 합니다.

- 일반적으로 SOCK_STREAM 소켓이 연결을 수신하기전에 bind()를 사용하여 로컬 주소를 할당해야 합니다.

- 이름 바인딩에 사용되는 규칙은 address family마다 다릅니다.

- addr 인자로 넣는 실제 구조체는 address family에 따라 달라집니다.
- sockaddr 구조체는 다음과 같이 정의되어 있습니다.
``` c
struct sockaddr{
	sa_family_t sa_family;
	char.       sa_data[14];
}
```
- 이 구조체의 유일한 목적은 컴파일러 경고를 피하기위해 addr 인자에 넣는 구조체 포인터를 캐스팅 하는 것입니다.

## return value
- 성공시 0을 리턴합니다.
- 실패시 -1을 리턴하고 오류를 나타내기위해 errno가 설정됩니다.

## errors
- EBADF : sockfd가 유효한 파일디스크립터가 아닙니다.
- EACCES : 경로 접두사의 구성에 대한 검색 권한이 거부되었습니다.
- ENOTSOCK : 파일 디스크립터 sockfd가 소켓을 참조하지 않습니다.
- EROFS : 소켓 inode가 읽기전용 파일 시스템에 있습니다.
- ENOENT : 소켓 경로의 디렉토리가 존재하지 않습니다.

#webserv