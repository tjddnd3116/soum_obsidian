# docker-compose
> 복수 개의 컨테이너를 실행시키는 도커 어플리케이션이 정의를 하기 위한 툴
- Compose를 사용하면 YAML파일을 사용하여 애플리케이션의 서비스를 구성할 수 있다.
- single command를 사용하여 구성에서 모든 서비스를 만들고 시작한다.
- 기본적으로 3단계의 프로세스를 사용한다.
	1. 앱의 환경을 정의하여 어디에서나 재사용할 수 있는 Dockerfile을 정의한다.
	2. docker-compose.yml 에서 앱을 구성할 수 있는 서비스를 정의하여 하나의 환경에서 실행할 수 있게 한다..
	3.  docker-compose up 명령어로 Compose를 시작시키고 전체의 앱을 실행시킨다.

## docker-compose의 특징
1. 서비스들을 시작하고 중지하고 재 빌드한다.
	- 단일 컨테이너가 아닌 복수의 서비스를 시작하고 중지하고 다시 빌드가 가능하다.
2. 실행중인 서비스의 상태를 볼 수 있다.
	- 로그 출력을 포함하여 실행 중인 모든 서비스의 상태를 쉽게 볼 수 있다.
3. 실행 중인 서비스의 로그를 스트림 할 수 있다.
	- 두번째와 비슷한 특징이라고 볼 수 있다.
	- Docker Compose를 이용하면 로그를 가져오기 위해 컨테이너 별로 명령을 수행할 필요가 없다.
	- 원할경우 모든 다른 컨테이너의 로그를 한번에 가져올 수도 있다.
4. 하나의 서비스에 일회성 작업을 수행할 수 있다.
	- 경우에 따라서 단일 컨테이너에 명령을 내리는 필요가 있다.
	- Docker Compose에서 단일 컨테이너에서 해당 컨테이너의 로그를 보거나 컨테이너를 시작 및 중지하거나 이미지를 빌드할 수도 있다.

## Docker Compose 설정 방법
### 파일 위치 / 기본 구조
- Docker Compose는 기본적으로 docker-compose.yml 파일을 설정 파일로 사용하는데 일반적으로 프로젝트의 최상위 디렉토리에 위치 시킨다.
``` yaml
version: "3.5"
services:
	web:
	# 웹 애플리케이션 설정
	db:
	# 데이터베이스 설정
network:
# 네트워크 설정
volumes:
# 볼륨 설정

```
- 맨위에 docker compose 규격의 어떤 버전을 사용하는지 명시한다.
- 그 아래 각각 서비스, 네트워크, 볼륨 영역에 대한 설정을 할 수 있다.
#### service
##### build
- build 항목은 해당 서비스의 이미지를 빌드하기 위한 Dockerfile이 위치하는 경로를 지정하기 위해 사용된다.
``` yaml
services:
	web:
		build: .
```

- dockerfile이 아닌 다름 이름의 파일로 빌드를 하고 싶거나 빌드 인자를 넘겨야 하는 경우에는 하위 항목을 사용해 설정한다.
``` yaml
services:
	web:
		build:
			context: ./app
			dockerfile: Dockerfile-dev
			args:
				env: "development"
```

##### image
- 프로젝트에서 직접 개발하지 않는 데이터베이스나 캐시와 같은 경우에는 이미지를 직접 빌드하는 대신에 이미지 저장소로부터 이미지를 내려받아서 사용한다.
- image 항목은 이미지 저장소로부터 내려받을 이미지의 이름과 태그를 명시하는데 사용된다.
``` yaml
services:
	db:
		image: postgres:13
	cache:
		image: redis
```

##### ports
- ports 항목은 외부로 노출시킬 포트의 맵핑을 명시한다.
- 바인트가 필요한 호스트 외부포트와 컨테이너 내부 포트를 지정해주면 된다.
``` yaml
services:
	web:
		ports:
			- "8000:8000"
	db:
		ports:
			- "5432:5432"
```

##### volumes
- volumes 항목은 볼륨 설정을 위해 쓰인다.
- 마운트가 필요한 호스트의 경로와 컨테이너의 경로를 명시해주면 된다.
``` yaml
services:
	web:
		volumes:
			- .:/web
```

##### depends_on
- depends_on 항목은 서비스간 의존 관계를 지정하기 위해서 사용된다.
- 예를들어 웹 애플리케이션이 올라오기 전에 데이터베이스가 먼저 올라와야 한다면 다음과 같이 설정한다.
``` yaml
services:
	web:
		depends_on:
			- db
```

##### command
- command 항목은 해당 서비스가 올라올때 Dockerfile의 CMD명령어를 무시하고 실행할 명령어를 설정하기 위해서 사용된다.
``` yaml
services:
	web:
		command: node .
```

##### environment
- enviroment 항목은 환경변수를 설정하기 위해서 사용된다.
``` yaml
environment:
	- POSTGRES_USER=postgres
	- POSTGRES_PASSWORD=postgres
```
- 환경변수들을 한 파일에 모아서 환경변수 파일을 읽어 들일수도 있다.
``` yaml
#envfile
HOGE=fuga
FOO=bar

env_file:
	- ./envfile
```
##### restart
- 컨테이너의 재시작과 관련된 설정을 정의할 수 있다.
- no : 문제가 발생하여 Container가 중지된 경우 재시작 하지 않음(default)
- always : 어떠한 문제로 Container가 중지되는 경우 무조건 재시작.
- on_failure : Container의 exit code 0 가 아닌 경우 재시작
- unless-stopped : 명시적으로 중지되거나 Docker 자체가 중지되는 경우 재시작

#### volumes
> Docker는 Data를 안전하게 존속시킬 수 있는 방식으로 volume, bind mounts, tmpfs의 3가지 방식을 제공한다.

- 올바른 Mount 유형 선택 방법
	- 어떤 유형의 Mount를 사용하든 Data는 Container 내에서 동일하게 보이며 Container File System의 폴더나 개별적인 파일들로 표시된다.
	- volume, bind mounts, tmpfs mount 간의 가장 큰 차이점은 Data가 Docker Host내에서 어디에 존재하는지 이다.

- Data 저장을 위한 최선의 선택
	- volume은 Docker(Linux에서는 /var/lib/docker/volume/)가 관리하는 Host File System의 일부에 Data가 저장된다.

- Host의 File System 원하는 곳에 저장
	- bind mount는 Data가 Host System의 어디에든지 저장될 수 있다.

- Host System의 Memory에 저장
	- tmpfs mount는 Host System의 Memory에만 Data가 저장되며 절대로 Host의 File System에는 저장되지 않는다.

#### network
##### 네트워크 드라이버
- bridge : 기본 네트워크 드라이버이다. 브리지 네트워크는 일반적으로 통신이 필요한 독립 실행형 컨테이너에서 애플리케이션을 실행할 때 사용된다.
- host : 독립형 컨테이너의 경우 컨테이너와 Docker 호스트간의 네트워크 격리를 제거하고 호스트의 네트워킹을 직접 사용한다.
- overlay : 오버레이 네트워크는 여러 Docker 데몬을 함께 연결하고 스웜 서비스가 서로 통실할 수 있도록 한다.

##### 사용자 정의 브리지와 디폴트 브리지
- 사용자 정의 브리지는 컨테이너 간에 자동 DNS 확인을 제공한다.
	- --link 옵션을 사용하지 않는 한 기본 브리지 네트워크의 컨테이너는 IP주소로만 서로 엑세스할 수 있다.
	- 사용자 정의 브리지는 컨테이너의 이름 또는 별칭으로 서로 확인이 가능하다.

- 사용자 정의 브리지는 더 나은 격리를 제공한다.
	- --network가 지정 되지 않은 모든 컨테이너는 기본 브리지에 연결이된다.
	- 관련 없는 스택/서비스/컨테이너가 통실할 수 있으므로 이는 위험할 수 있다.
	- 사용자 정의 네트워크를 사용하면 해당 네트워크에 연결된 컨테이너만 통신할 수 있는 범위가 지정된 네트워크를 제공한다.

- 컨테이너는 사용자 정의 네트워크에서 바로 연결 및 헤제가 가능하다.
	- 컨테이너가 동작하는 동안 사용자 정의 네트워크에서 즉시 연결하거나 연결을 끊을 수 있다.
	- 기본 브리지 네트워크에서 컨테이너를 제거하려면 컨테이너를 중지하고 다른 네트워크 옵션으로 다시 생성해야 한다.
	#[[docker]] #[[docker 명령어]] #[[Dockerfile 명령어]]