# Nginx conf
> nginx를 기본적으로 설정하기 위한 파일

 /etc/nginx 디렉토리안에 설정관련 파일과 디렉토리를 볼 수 있다.
## Nginx conf 설정
> nginx  기본동작 이외에도 파일 확장자의 MIME Type, 가상 호스트, 리버스 프록시나 FastCGi로 PHP를 연결해 구동하기 위한 설정 파일들이 이 디렉토리에 포함되어 있다.
- nginx.conf 파일은 접속자수, 동작 프로세스수 등 퍼포먼스에 기본적인 설정 항목을 담고있다.
### 메인 컨텍스트
> 어떤 특정 블록이나 컨텍스트에 포함되지 않는 메인 컨텍스트라고 부른다.

``` nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enable/*.conf;
```
- user : nginx 프로세스가 실행되는 권한
- worker_processes : nginx 프로세스 실행 가능 수
- pid : nginx의 마스터 프로세스 ID정보
- include : nginx.conf에서 설정한것 이외에 따로 추가 적인 설정을 해당 경로에서 불러옴
### events 블록
> nginx는 비동기 이벤트 방식의 처리 방식을 가지고 있는데 event block은 그 처리에 관련된 옵션을 설정하는 곳이다.

``` nginx
events {
worker_connections 768;
}
```
worker_connections : 하나의 프로세스가 처리할 수 있는 커넥션의 수

### http 블록
> 웹서버에 대한 동작 설정

``` nginx
http {
	server_tokens off;
	keepalive_time 65;
	include /etc/nginx/mime.types;
	default_type application/octet-stream;
}
```
server_tokens : 헤더에 nginx의 버전을 숨긴다.
keepalive_timeout : 접속시 커넥션 유지 시간
include : 옵션 항목을 설정해둔 파일의 경로를 지정
default_type : 옥텟 스트림 기반의 http를 사용함

### Server 블록
``` nginx
server {
	listen 80 default_server;
	listen [::]:80 default_server;
	server_name example.com www.example.com;
	root /var/www/example.com;
	index index.html;
	try_files $uri /index.html;
}
```
#### listen :
- nginx에게 http 연결을 위해 필요한 hostname/IP와 TCP포트를 알려준다.
- 두번째 줄의 listen 디렉티브는 IPv6형식의 요청을 처리한다는 뜻
- default_server가 의미하는 것은 이 가상의 호스트가 다른 가상의 호스트들의 listen statement와 매치되지 않는 모든 요청에 응답한다는 뜻

#### server_name
- server_name 디렉티브는 하나의 IP 주소에 대해 여러개의 도메인을 사용할 수 있게 한다.
- 도메인 네임이 아닌 호스트네임을 사용하는 것은 서버가 LAN에 있거나 또는 이미 서버에 요청을 보낼 클라이언트들을 알고 있는 경우 유용하다.
- 예를들면 이것은 nginx가 듣고 있는 IP 주소들로 설정된 /etc/hosts 엔트리를 사용하는 전면 프록시 서버를 사용할때 유용하다.

#### root
해당 서버의 root 리렉토리

#### index
아무런 파일명이 명시되어 있지 않을때 어떤 것을 서브해야 하는지를 알려준다.

### location 블록
>location 디렉티브는 서버 안의 리소스에 대한 요청을 어떻게 응답해야 할지를 설정한다.

``` nginx
server {
	location /{
		...	
	}

	location /images/{
		...	
	}

	location /blog/{
		...	
	}
}
```
- 위의 location 디렉티브들은 리터럴 스트링에 대한 매치이다.
- 호스트 세그먼트 이후에 오는 HTTP요청에서 어떠한 부분이라도 매치된다.

#[[docker]] #[[nginx]]