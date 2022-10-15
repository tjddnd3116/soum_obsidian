# select
## name
> select, pselect, FD_CLR, FD_ISSET, FD_SET, FD_ZERO - 동기 입출력 다중화

## synopsis
``` c
#include <sys/select.h>

int select(int nfds,
		   fd_set *restrict readfds,
		   fd_set *restrict writefds,
		   fd_set *restrict exceptfds,
		   struct timeval *restrict timeout);
```

## description
경고 :
- select()는 FD_SETSIZE(1024) 보다 작은 파일 디스크립터 개수만 모니터링 할수 있습니다.
- 이 제한을 겪지 않으려면 poll() 이나 epoll()을 사용해야 합니다.

- select() 를 사용하면 프로그램이 여러 파일 디스크립터를 모니터링 할 수 있습니다.
- I/O 작업의 일부 클래스를 위해 하나 이상의 파일 디스크립터가 준비 될때까지 기다립니다.
- 파일 디스크립터가 블로킹 없이 I/O 작업을 수행할 수 있는 경우 준비된 것으로 간주됩니다.

## arguments
- readfds
	- 읽기 준비 상태가 된 파일 디스크립터 입니다.
	- 읽기 작업이 블로킹되지 않은경우 해당 파일 디스크립터는 읽기 준비 상태가 됩니다.
	- EOF일때는 해당 파일디스크립터는 준비 상태가 됩니다.
	- select()함수가 리턴한 이후 읽기 준비 상태가 된 파일 디스크립터를 제외한 모든 파일디스크립터는 삭제됩니다.

- writefds
	- 쓰기 준비 상태가 된 파일 디스크립터 입니다.
	- 쓰기 작업이 블로킹되지 않은경우 해당 파일 디스크립터는 쓰기 준비 상태가 됩니다.
	- 하지만 해당 파일 디스크립터가 쓰기 준비 상태가 되더라도 큰 쓰기는 여전히 차단될수 있습니다.
	- select() 함수가 리턴한 이후 쓰기 준비 상태가 된 파일 디스크립터를 제외한 모든 파일 디스크립터는 삭제됩니다.

- exceptfds
	- 예외적 조건의 파일 디스크립터 입니다.
	- 예외적 조건의 예는 poll(2)의 POLLPRI를 참조하세요
	- select() 함수가 리턴한 이후 예외적 조건이 발생한 파일 디스크립터를 제외한 모든 파일디스크립터는 삭제됩니다.

- nfds
	- 인자로 들어온 파일 디스크립터들의 숫자보다 1 높은 숫자로 설정된 인자입니다.
	- 각각의 파일 디스크립터는 nfds 보다 높은지 확인합니다.

- timeout
	- timeout 인자는 아래의 timeval 구조체 입니다.
	- timeout은 파일 디스크립터가 준비 상태가 될때까지 select() 함수가 block 해야할 시간 간격입니다.
	- 아래의 호출이 있을때까지 block합니다.
		- 파일 디스크립터가 준비됨
		- signal handler에 의해 interrupt 당함
		- timeout 시간이 끝남
	- kernel scheduling delays, system clock granlarity에 의해 시간 간격이 약간 초과될수 있음으로 timeout은 반올림될 수 있습니다.
	- 만약 timeval 구조체의 두 filed가 0이면 select() 함수는 바로 리턴됩니다. (이것은 polling을 하기 유용합니다.)
	- 만약 timeval이 NULL포인터이면 select() 함수는 파일 디스크립터가 준비상태가 될때까지 무기한 대기를 block 합니다.
- The timeout
	- type :
	```c
	struct timeval {
	time_t      tv_sec;   /* seconds */
	suseconds_t tv_usec;  /* microseconds */
	}
	```
	- 리눅스에서 select() 함수는 sleep 하지 않은 시간을 반영하여 timeout을 수정합니다.
	- 대부분의 다른 구현에서는 이 작업을 수행하지 않습니다.
	- 따라서 리눅스이외의 운영체제에서는 문제가 발생할수 있습니다.
	- timeout은 select() 함수가 리턴한 이후 undefined가 됩니다.
	
## return value
- 성공시 select() 함수는 인자로 넣은 파일디스크립터의 수를 리턴합니다.
- 만약 파일 디스크립터가 준비상태가 되기 전에 timeout이 만료된 경우 0을 리턴합니다.
- 실패시 -1 을 리턴합니다.
- 오류를 나타내가 위해 errno이 세팅됩니다.
- 파일 디스크립터는 unmodified 가 되고 timeout은 undefined가 됩니다..

## errors
- EBADF : 셋중의 하나의 파일 디스크립터가 잘못되었습니다. (closed된 파일 디스크립터 이거나 오류가 발생함)
- EINTR : signal 이 발생함
- EINVAL : nfds가 음수이거나 RLIMIT_NOFILE 리소스 제한을 초과합니다.
- EINVAL : timeout안의 값이 잘못되었습니다.
- ENOMEM : 메모리를 할당할 수 없습니다.

## example
``` c
#include <stdio.h>
#include <stdlib.h>
#include <sys/select.h>

int main(void)
{
	fd_set rfds;
	struct timeval tv;
	int retval;

	FD_ZERO(&rfds);
	FD_SET(0, &rfds);
	tv.tv_sec = 5;
	tv.tv_usec = 0;

	retval = select(1, &rfds, NULL, NULL, &tv);
	// Don't rely on the value of tv now!

	if (retval == -1)
		perror("select()");
	else if (retval)
		printf("Data is available now.\n");
		// FD_ISSET(0, &rfds) will be true.
	else
		printf("No data within five seconds.\n");
	exit(EXIT_SUCCESS);
}
```