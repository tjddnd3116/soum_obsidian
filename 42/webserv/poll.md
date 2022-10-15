# poll
## name
> poll - 파일 디스크립터에서 어떤 이벤트를 기다립니다.

## synopsis
``` c
#include <poll.h>

int poll(struct pollfd *fds, nfds_t nfds, int timeout);
```

## description
- poll() 함수는 select() 함수와 유사한 작업을 수행합니다.
- 파일 디스크립터중 하나가 I/O를 수행할 준비가 될때까지 기다립니다.
- 리눅스 전용 epoll() 함수는 유사한 작업을 수행하지만 poll() 이상의 기능을 제공합니다.
- 모니터링될 파일 디스크립터는 fds 인자로 제공됩니다.
- fds인자는 아래의 구조체로 이루어진 배열이다.
- form
	``` c
	struct pollfd {
		int fd;        // 파일 디스크립터
		short events;  // 요청된 이벤트
		short revents; // 반환된 이벤트
	}
	```
	- nfds 인자는 fds의 배열의 크기를 지정해야 합니다.
	- fd 필드는 열려있는 파일디스크립터 입니다.
	- fd가 음수면 events필드는 무시되고 revents필드는 0이 됩니다. (이것은 poll() 호출에 대한 해당 파일디스크립터를 무시하는 쉬운 방법입니다. 하지만 fd가 0일때는 사용할수 없습니다.)
	- events 필드는 해당 파일디스크립터를 어떠한 event를 발생할 것인지에 대한 비트 마스크이며 입력 매개변수 입니다.
	- 이 필드는 0으로 지정할수 있으며 이 경우 revent가 POLLHUP, POLLERR, POLLNVAL으로 리턴됩니다.
	- revents 필드는 이벤트가 발생하면 커널에의해 초기화되는 출력 매개변수 입니다.
	- revents의 값은 events의 값이나 POLLERR, POLLHUP, POLLNVAL중 하나의 값입니다. 
	- 어떠한 파일 디스크립터에서 이벤트가 발생하지 않고 오류가 없으면 poll() 함수는 파일 디스크립터에서 이벤트가 발생할 때까지 block을 합니다.

- timeout 인자는 파일 디스크립터가 준비 상태가 될때까지 대기를 block 하는 시간입니다.
	- 아래의 호출이 발생할때까지 block 합니다.
		- 파일 디스크립터가 준비상태가 됨
		- signal handler에 의해 interrupt가 발생함
		- timeout이 만료됨

- events 필드를 설정하거나 revents가 리턴하는 비트들의 정의
	- POLLIN : 읽을 데이터가 있습니다.
	- POLLPRI :
		- 해당 파일디스크립터에 아래의 예외적인 조건이 있습니다.
			- TCP 소켓에 out-of-band 데이터가 있습니다.
			- 패킷 모드의 pseudoterminal master 가  slave에서 상태 변화를 파악함
			- cgroup.event 파일이 변경됨
	- POLLOUT : 쓰기는 가능하지만 가능한 공간보다 더 큰 값을 쓰거나 파이프가 여전히 block 상태입니다. (O_NONBLOCK 이 설정되지 않은 경우)
	- POLLRDHUP : stream 소켓 peer 연결이 닫히거나 쓰는중에 연결이 종료됨
	- POLLERR :
		- 에러 상태 (events에는 무시되고 revents에서만 리턴됨)
		- 이 비트는 파일 디스크립터가 읽기가 닫혔을때 파이프의 끝에 쓰기를 참조할때도 발생합니다.
	- POLLNVAL : 유효하지 않은 요청 :fd 가 열리지 않음 (events에서는 무시되고 revents에서만 반환됨)
	_XOPEN_SOURCE로 컴파일 할때 아래의 비트들이 추가됨
	-  POLLRDNORM : POLLIN과 동일함
	- POLLRDBAND : Priority band data 를 읽을 수 있습니다. (일반적으로 리눅스에서는 사용하지 않음)
	- POLLWRNORM : POLLOUT과 동일함
	- POLLWRBAND : Priority data를 쓸수 있습니다.
	POLLMSG는 리눅스또한 알고 있지만 사용하지는 않습니다.

## return value
- 성공시 revents 필드(이벤트나 에러를 나타내는)가 0이 아닌 pollfds 요소의 개수를 리턴합니다.
- 어떠한 파일 디스크립터가 읽기 상태가 되기전에 시스템콜 시간이 만료되면 0을 리턴합니다.
- 실패시 -1이 리턴되고 오류를 나타내기 위한 errno이 설정됩니다.

## errors
- EFAULT : fds가 접근 가능한 메모리 밖을 가리킵니다.
- EINTR : 이벤트가 요청되기 전에 signal이 발생했습니다.
- EINVAL : nfds값이 RLIMIT_NOFILE 값을 초과 했습니다.
- ENOMEM : 메모리를 할당할 수 없습니다.

## notes
- poll()은 O_NONBLOCK 플래그의 영향을 받지 않습니다.
- 일부 유닉스 시스템에서는 리눅스의 ENOMEM 에러 처럼 할당에 실패할때 EAGAIN에러를 발생합니다.