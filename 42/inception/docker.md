# docker
## docker
>컨테이너 기반의 오픈소스 가상화 플랫폼

## docker와 가상머신의 차이점
> 가상머신은 OS 단위 가상화, 도커는 애플리케이션 단위 가상화를 한다.

- **가상 머신**에는 VirtualBox, VMware등이 있다.
- **가상 머신**은 OS단위 가상화를 한다.
- **도커**는 애플리케이션 단위 가상화를 한다.
- **가상머신**은 애플리케이션에 대한 환경 격리성을 중심으로 제공한다.
- **도커**는 각각의 애플리케이션 격리성을 제공한다.
- **가상 머신**은 개발 환경이나 사용환경을 이미지로 저장하고 호스트 OS위에 게스트 OS를 올리는 방식이다.
- **가상 머신**은 컴퓨터의 하드웨어 자체를 가상화 시키기때문에 컨테이너보다 가상 머신이 훨씬 무겁다.
- **도커**는 필요한 부분만 격리되어 있고 나머지는 공유하기 때문에 가상 머신 보다 훨씬 가볍다.
- **도커**는 호스트 OS의 자원을 공유한다.

## 컨테이너
>격리된 공간에서 프로세스가 동작하는 기술

기존의 가상화 방식은 주로 OS를 가상화 하였다.

VirtualBox 같은 가상머신은 호스트 OS위에 게스트 OS 전체를 가상화하여 사용하는 방식이다.  

이 방식은 여러가지 OS를 가상화 할 수 있고 간단하지만 무겁고 느려서 운영환경에서 사용할 수가 없다.

이러한 상황을 개선하기 위해 cpu의 가상화 기술을 이용한다.

이러한 방식은 게스트 OS가 필요하긴 하지만 전체 OS를 가상화하는 방식이 아니라서 호스트형 가상화 방식에 비해 성능이 향상되었다.

추가적인 OS를 설치하여 가상화 하는 방법은 성능문제가 있었고 이를 개선하기 위해 프로세스를 격리하는 방식이 등장한다.

이 방식은 프로세스를 격리시키기 때문에 가볍고 빠르게 동작한다.
cpu나 메모리는 딱 프로세스가 필요한 만큼만 추가로사용하고 성능적으로도 거의 손실이 없다.

하나의 서버에 여러개의 컨테이너를 실행하면 서로 영향을 미치지 않고 독립적으로 실행되어 마치 가벼운 VM을 사용하는 느낌을 준다.

## 이미지
>컨테이너 실행에 필요한 파일과 설정값등을 포함하고 있는 것

컨테이너는 이미지를 실행한 상태라고 볼수 있고 추가됭거나 변하는값은 컨테이너에 저장된다. 

같은 이미지에서 여러개의 컨테이너를 생성할 수 있고 컨테이너의 상태가 바뀌거나 컨테이너가 삭제되더라도 이미지는 변하지 않고 그대로 남아있다.

이미지는 컨테이너를 실행하기 위한 모든 정보를 가지고 있기 때문에 더 이상 의존성 파일을 컴파일하고 이것저것 설치할 필요가 없다.

새로운 서버가 추가되면 미리 만들어 놓은 이미지를 다운받고 컨테이너를 생성만 하면 된다.

## 레이어 저장방식
> 유니온 파일 시스템을 이용하여 여러개의 레이어를 하나의 파일시스템으로 사용할 수 있게 해준다.

이미지는 여러개의 읽기 전용 레이어로 구성되어 파일이 추가되거나 수정되면 새로운 레이어가 생성된다.

컨테이너를 생성할 때도 레이어 방식을 사용하는데 기존의 이미지 레이어 위에 읽기/쓰기 레이어를 추가한다.

이미지 레이어를 그대로 사용하면서 컨테이너가 실행중에 생성하는 파일이나 변경된 내용은 읽기/쓰기 레이어에 저장되므로 여러개의 컨테이너를 생성해도 최고한의 용량만 사용하게된다.

## Dockerfile
> DSL언어를 이용하여 이미지의 생성과정을 적는 파일

서버에 어떤 프로그램을 설치하려고 이것저것 의존성 패키지를 설치하고 설정파일들을 Dockerfile로 관리하면 된다.