# setsockopt
## name
> setsockopt - 소켓에 대한 옵션 설정

## synopsis
``` c
#include <sys/socket.h>

int setsockopt(int sockfd, int level, int optname,
			  void *restrict optval, socklen_t *restrict optlen);
```

## description
- sockfd : 소켓 파일 디스크립터
- level : 소켓의 레벨로 어떤 레벨의 소켓정보를 가져오거나 변경할 것인지를 명시한다. 보통 SOL_SOCKET 과 IPPROTO_TCP 중 하나를 사용한다.
- optname : 설정을 위한 소켓옵션의 번호
- optval : 설정값을 저장하기 위한 버퍼의 포인터
- optlen : optval 버퍼의 크기

- 설정값을 void* 로 넘기는 이유는 설정하고자 하는 소켓옵션에 따라서 bool, int, 구조체등 다양한 크기를 가지는 데이터형이 사용되기 때문이다. 만약 변경하고자 하는 소켓옵션이 bool 값을 사용한다면 0, 1값을 사용하면 된다.

- SOL_SOCKET레벨에서 사용할 수 있는 옵션과 데이터형은 다음과 같다.

|옵션값|데이터형|설명|
|-|-|-|
|SO_BROADCAST|BOOL|브로드캐스트 메서지 전달이 가능하도록 한다.|
|SO_DEBUG|BOOL|디버깅 정보를 레코딩 한다.|
|SO_DONTLINGER|BOOL|소켓을 닫을때 보내지 않은 데이터를 보내기 위해서 블럭되지 않도록 한다.|
|SO_DONTROUTE|BOOL|라우팅 하지 않고 직접 인터페이스로 보낸다.|
|SO_OOBINLINE|BOOL|OOB 데이터 전송을 설정할때 일반 입력 큐에서 데이터를 읽을 수 있게 한다.|
|SO_GROUP_PRIORITY|int|사용하지 않음|
|SO_KEEPALIVE|struct LINGER|소켓을 닫을 때 전송되지 않은 데이터의 처리규칙|
|SO_RCVBUF|int|데이터를 수신하기 위한 버퍼공간의 명시|
|SO_RCVBUF|int|데이터를 수신하기 위한 버퍼공간의 명시|
|SO_REUSEADDR|BOOL|이미 사용된 주소를 재사용(bind)하도록 한다.|
|SO_SNDBUF|int|데이터 전송을 위한 버퍼공간 명시|

- SO_RESUEADDR
	- 소켓을 이용한 서버프로그램을 운용하다보면 강제종료되거나 비정상 종료되는 경우가 발생한다.
	- 테스트를 목적으로 할 경우에는 특히 강제종료 시켜야 하는 경우가 자주 발생하는데 강제 종료시키고 프로그램을 다시 실행시킬경우 다음과 같은 메시지를 종종 보게된다.
	```
	bind error : Address already in use
	```
	- 이는 기존 프로그램이 종료되었지만, 비정상종료된 상태로 아직 커널이 bind정보를 유지하고 있음으로 발생하는 문제다.
	- 보통 1-2분 정도 지나면 커널이 알아서 정리를 하긴 하지만 그 시간동안 기달려야 한다는 것은 상당히 번거로운 이리다. 이 경우 다음과 같은 코드를 삽입함으로써 문제를 해결할 수 있다.
	```c
	int sock = socket(...);
	setsockopt(sock, SOL_SOCKET, SO_REUSEADDR, (char*)&bf, (int)sizeof(bf));
	```
	- 이렇게 하면 커널은 기존에 bind로 할당된 소켓자원을 프로세스가 재 사용할 수 있도록 허락한다.

- 소켓 버퍼
	- SO_SNDBUF는 전송소켓에서 사용할 최대 버퍼의 크기를 설정한다. 커널은 setsockopt로 설정한 값의 2배를 버퍼 크기로 잡는다.
	- SO_RCVBUF는 수신소켓에서 상요할 최대 버퍼의 크기를 설정한다. 커널은 setsockopt로 설정한 값의 2배를 버퍼 크기로 잡는다.

- TCP_NODELAY
	- Nagle 알고리즘이 적용되면, 운영체제는 패킷을 ACK가 오기를 기다렸다가 도착하면 그동안 쌓여있던 데이터를 한꺼번에 보내게 된다. 이러한 방식을 사용하게 되면 대역폭이 낮은 WAN에서 빈번한 전송을 줄이게 됨으로 효과적인 대역폭활용이 가능해진다.
	- 대부분의 경우에 있어서 Nagle 알고리즘은 효율적으로 작동하긴 하지만, 빈번한 응답이 중요한 서비스의 경우에는 적당하지 않은 경우가 발생한다. 예를 들어 X-Terminal을 이용할 경우 마우스 이벤트는 즉시 전달될 필요가 있는데 Nagle알고리즘을 사용하면 아무래도 반응시간이 떨어지게 될것이다. 실시간적인 반응이 중요한 온라인게임 역시 Nagle 알고리즘을 제거하는게 좋다.

- SO_LINGER
	- SO_LINGER은 소켓이 close() 되었을때, 소켓버퍼에 남아있는 데이터를 어떻게 할 것인지를 결정하기 위해서 사용한다.
	- 다음은 SO_LINGER옵션에 사용되는 데이터 구조체이다.
	```c
	struct linger
	{
		int l_onoff;
		int l_linger;
	}
	```
		- l_onoff : linger 옵션을 끌것인지 킬 것인지 결정
		- l_linger : 기다리는 시간의 결정
	- 위의 두개의 맴버변수의 값을 어떻게 하느냐에 따라 3가지 close방식이 결정되어 진다.
		1. l_onoff == 0 : 이경우 l_linger의 영향을 받지 않는다. 소켓의 기본설정으로 소켓버퍼에 남아있는 모든 데이터를 보낸다. 이때 close()는 바로 리턴을 하게 되므로 백그라운드에서 이러한 일이 일어나게 된다. 우아한 연결 종료를 보장한다.
		2. l_onoff > 0 이고 l_linger == 0 : close()는 바로 리턴을 하며, 소켓 버퍼에 아직 남아있는 데이터는 버린다. TCP 연결상태인 경우에는 상대편 호스트에 리셋을 위한 RST패킷을 보낸다. hard 혹은 abortive 종료라고 부른다.
		3. l_onoff > 0 이고 l_linger > 0 : 버퍼에 남아있는 데이터를 모두 보내는 우아한 연결 종료를 행한다. 이때 close()에서는 l_linger에 지정된 시간만큼 블럭상태에서 대기한다. 만약 지정된 시간내에 데이터를 모두 보냈다면 리턴이 되고, 시간이 초과되었다면 에러와 함께 리턴이 된다.
