# accept
## name
> accept - 소켓에서 연결을  받아들인다.
## synopsis
``` c
#include <sys/socket.h>

int accept(int sockfd, struct sockaddr *restrict addr,
		  socklent_t *restrict addrlen);
```

## description
accept 함수는 연결-기반 소켓 타입(SOCK_STREAM, SOCK_SEQPACKET, SOCK_RDM)에 사용된다.
이것은 미결인 연결들을 위한 큐에서 처음 연결을 뽑아 sockfd와 거의 같은 특성을 갖는 새로 연결된 소켓을 만든다.
그리고 소켓에 새 파일 기술자를 할당하며 이것을 반환한다.
새로 생성된 소켓은 더이상 기다리는(listen) 상태가 아니다.
원래의 소켓 s는 이 시스템 콜에 의해 영향을 받지 않는다.
파일 기술자 플래그중 어떤 것(non-blocking이나 async 상태 같이 F_SETFL fcntl을 가지고 설정할수 있는 모든것)은 accept를 통해 상속되지 않는다.

인자 sock_fd는 [[socket]](2)로 만들어진 소켓이며, [[bind]](2)로 로컬 주소를 묶고 [[listen]](2) 후에 연결을 기다린다.

인자 addr는 sockaddr 구조체에 대한 포인터이다.
이 구조체는 통신 층(communication layer)으로 알려진 접속 실체의 주소가 채워진다.
addr 인자에게 전달되는 주소의 정확한 형식은 socket 가족([[socket]](2)과 프로토콜 맨 페이지 참고)에 의해 결정된다.
addrlen 인자는 값-결과 파라미터이다. 그것은 addr가 가리키는 구조체의 크기를 포함해야 한다.
리턴시 그것은 반환된 주소의 실제 길이(바이트)를 포함한다.
addr가 null 이라면 어떤것도 채워지지 않는다.

만약 미결인 연결들이 큐에 존재한다면 그리고 소켓이 non-blocking 으로 표시되지 않았다면 accept는 연결이 존재할때까지 호출자를 봉쇄한다.
만일 소켓이 non-blocking 이고 미결인 연결들이 큐에 존재한다면, accept는 EAGAIN을 반환한다.

소켓에 들어오는 연결들을 인식하기 위해, [[select]](2)나 [[poll]](2)을 사용할 수 있다.
새로운 연결이 시도 되었을때 읽기 가능한 이벤트가 전달되며 그 연결을 위한 소켓을 가지기 위해 accept를 호출한다.
선택적으로, 활동이 소켓에서 일어났을때 SIGIO를 전달하기 위해 소켓을 설정할 수 있다.
세부사항을 위해서는 [[socket]](7)을 참조하라

DECNet 같이 명쾌한 승인을 요구하는 프로토콜에서, accept는 단지 다음 연결 요구를 큐에서 빼내고 승인을 이야기하지는 않는것으로써 생각될수 있다.
확인은 새 파일 기술자에 정상적인 읽기나 쓰기에 의해 알수 있으며 거절은 새 소켓을 닫음으로써 알 수있다.
현재 단지 DECNet만이 리눅스에서 이러한 의미론을 가진다.

SIGIO가 전달된 후나 또는 [[select]](2)나 [[poll]](2)가 읽기 가능하다는 이벤트를 반환한후 항상 연결을 기다리는 것은 아니다.
왜냐하면 accept가 호출된 후에 연결이 비동기적인 네트워크 에러나 다른 쓰레드에 의해 제거될수도 있기 때문이다.
이런일이 발생한다면 호출은 도착하는 다음 연결을 기다리며 blocking이 될것이다.
accept가 결코 봉쇄되지 않는다는 것을 보장하기 위해, 전달된 소켓 sockfd는 O_NONBLOCK 플래그 설정을 가져야 한다.([[socket]](7)을 참조)


- accept() 시스템콜은 연결 기반 소켓 타입(SOCK_STREAM, SOCK_SEQPACKET)과 함께 사용됩니다.
- listening 소켓, sockfd는 새 연결된 소켓을 생성하고 해당 소켓을 참조하는 새로운 파일 디스크립터를 리턴하고  연결 대기중인 대기열의 첫 연결 요청을 추출합니다.
- 새롭게 생성된 소켓은 리스링 상태가 아닙니다.
- 원래의 소켓 sockfd는 이 함수 호출에 영향을 받지 않습니다.

- sockfd 인자는 socket(2) 함수로 생성된 소켓 입니다.
- sockfd 인자는 bind(2) 함수로 로컬 주소에 바인딩 되며 listen(2) 함수 이후에 연결을 수신 합니다.

- addr 인자는 sockaddr 구조체의 포인터 입니다.
- 이 구조체는 통신 계층으로 알려진 peer 소켓의 주소로 채워져있습니다.
- 리턴된 addr 주소의 정확한 형식은 소켓의 address family에 의해 결정됩니다.
- addr 가 NULL 이면 아무것도 채워져 있지 않습니다.
- 이 경우 addrlen은 사용되지 않으며 이것 역시 NULL이어야 합니다.

- addrlen 인자는 결과값 인자입니다.
- 호출자는 반드시 addr이 가리키는 구조체의 크기를 포함하여 초기화합니다.
- 리턴 시 peer 주소의 실제 크기가 포함됩니다.

- 리턴된 주소는 만약 버퍼가 너무 작게 제공되면 잘립니다.
- 이러한 경우 addrlen은 함수 호출시 넣은 값보다 더 큰값을 리턴할 수 있습니다.

- 만약 대기열에 보류중인 연결이 없고 소켓이 nonblocking 으로 되어있지 않다면 accept()는 연결이 제공될때까지 block 합니다.
- 만약 소켓이 nonblocking 이지만 대기열에 보류중인 연결이 없다면 accept()는 EAGAIN 이나 EWOULDBLOCK 오류와 함께 실패하게 됩니다.

- 소켓에서 들어오는 연결에 대한 알림을 받으려면 select(2), poll(2) 또는 epoll(2)를 사용할 수 있습니다.
- 읽을수 있는 이벤트는 새로운 연결이 시도 될때 전달됩니다. 그 후에 해당 연결을 위해 소켓을 얻으려면 accept() 함수를 호출하여야 합니다.
- 아니면 소켓이 활동할때 SIGIO를 전달하기 위한 소켓을 설정할수 있습니다.

- 아래의 값은 함수의 다른 동작을 위해 flag 인자에서 or 비트 연산을 할수 있습니다.
	- SOCK_NONBLOCK 
		- 열린 파일 디스크립터에 O_NONBLOCK 플래그를 설정합니다.
		- 새로운 파일 디스크립터에 의해 참조 됩니다.
		- 동일한 결과를 얻기위해 fcntl(2)의 호출을 하지 않아도 됩니다.
	- SOCK_CLOEXEC : 새 파일 디스크립터에 FD_CLOEXEC플래그를 설정합니다.

## return value
- 성공시 해당 시스템 콜은 accept된 소켓을 위한 파일 디스크립터를 리턴합니다. (음수가 아닌 수)
- 실패시 -1이 리턴되고 오류를 나타내기 위한 errno 이 설정됩니다. addrlen은 바뀌지 않습니다.

## errors
- EAGAIN or EWOULDBLOCK : socket이 nonblocking 으로 되어있고 accept되기 위한 연결이 제공되지 않습니다.
- EBADF : sockfd 가 열린 파일 디스크립터가 아닙니다.
- ECONNABORTED : 연결이 중단 되었습니다.
- EFAULT : addr 인자가 사용자 주소 공간의 writable한 부분이 아닙니다.
- EINTR : 시스템 콜이 유효한 연결이 도착하기 전에 signal에 의해중단 되었습니다.
- EINVAL : 소켓이 연결을 수신하지 않거나 addrlen이 유효하지 않습니다. (음수)
- EMFILE : 열린 파일 디스크립터의 수가 프로세스 제한 크기에 도달하였습니다.
- ENFILE : 열린 파일 디스크립터의 수가 시스템 전체 제한 크기에 도달하였습니다.
- ENOBUFS, ENOMEM : 사용가능한 메모리가 충분하지 않습니다. 이것은 종종 시스템 메모리가 아닌 버퍼 크기 제한에 의해 메모리 할당이 되지 않음을 의미합니다.
- ENOSOCK : 파일 디스크립터 sockfd 가 소켓을 참조하지 않습니다.
- EOPNOTSUPP : 참조된 소켓이 SOCK_STREM 타입이 아닙니다.
- EPERM : 방화벽 규칙이 연결을 금지합니다.
- EPROTO : 프로토콜 에러