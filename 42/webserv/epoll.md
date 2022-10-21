# epoll
## name
>  I/O event 알림 기능

## synopsis
```c
#include <sys/epoll.h>
```

## description
- epoll API는 poll()와 유사한 작업을 수행합니다.
- 여러 파일 디스크립터중 하나가 I/O 을 수행가능한지 모니터링 합니다.
- epoll은 edge-triggered나 a level-triggered interface로 사용할수 있고 더 큰 수의 파일 디스크립터를 모니터링 할수 있습니다.
- epoll 의 주요한 개념은 epoll의 인스턴스입니다.
- 사용자 공간의 관점에서 볼때 커널 데이터 구조의 두 가지 목록으로 간주될 수 있습니다.
	- 관심 목록 : 
		- 모니터링의 관심에 등록된 프로세스의 파일 디스크립터 세트
	- 준비 목록 :
		- I/O를 위한 준비 상태인 파일 디스크립터의 세트
		- 준비 목록은 관심 목록에 있는 파일 디스크립터의 서브셋 입니다.
		- 준비 목록은 파일 디스크립터의 I/O 활동의 결과로 커널에의해 동적으로 채워집니다.
- 아래의 시스템콜은 epoll 인스턴스 관리와 생성을 위해 제공됩니다.
	- epoll_create() : 새로운 epoll 인스턴스를 생성하고 해당 인스턴스를 참조하는 파일 디스크립터가 리턴됩니다.
	- 특정 파일 디스크럽터에 대한 관심은 epoll_ctl() 함수에 의해 epoll 인스턴스의 관심 목록에 항목을 추가함으로써 등록됩니다.
	- epoll_wait() : 현재 사용 가능한 이벤트가 없는 경우 I/O 이벤트를 기다리고 스레드 호출을 block 합니다.

### Level-triggered and edge-triggered
- epoll 이벤트 배포 인터페이스는 edge-griggered(ET) 와 level-triggered(LT)로 동작할 수 있습니다.
- 두 매커니즘 사이의 차이점은 다음과 같이 설명할 수 있습니다.
	1. 파이프의 읽기를 나타내는 파일 디스크립터는 epoll 인스턴스에 등록됩니다.
	2. 파이프 쓰기는 파이프의 쓰기쪽에 2kb만큼 씁니다.
	3. epoll_wait()의 호출이 끝나면 준비된 파일디스크립터 rfd를 리턴합니다.
	4. 파이프 읽기는 rfd에서 1kb의 데이터를 읽습니다.
	5. epoll_wait() 호출이 끝납니다.

- 만약 red 파일디스크립터가 EPOLLET 플래그를 사용하여 epoll 인터페이스에 추가된경우 

#webserv 