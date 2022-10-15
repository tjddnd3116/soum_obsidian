## 1. base image 사용
- inception은 모든 이미지가 Alpine Linux의 두번째 안정 버전이나 Debian Buster를 base 이미지로 사용해야 한다.
``` docker
FROM debian:buster
```
위 명령어로 base 이미지 레이어를 만들어준뒤 그 위의 레이어를 하나하나 쌓아가야한다.

## 2.ARG 설정
``` docker
ARG CERTS
ARG KEYS
```
- openssl로 .crt 와 .key의 이름을 .env파일로 지정한 이름으로 변경하기 위해 dockerfile 내부에서 사용할 arg 변수이름을 선언해준다.

## 3. LABEL
``` docker
LABEL email="soum@student.42seoul.kr"
LABEL name="soum"
LABEL version="1.0"
LABEL description="nginx"
```
- LABEL 명령은 이미지의 버전 정보, 작성자, 코멘트와 같이 이미지 상세 정보를 작성해 두기위한 명령이다.

## nginx, openssl 설치
``` docker
RUN apt-get update -y 
	&& apt-get install -y nginx 
	&& apt-get install openssl
```
- nginx 이미지에 필요한 nginx, openssl을 설치해준다.
- -y 옵션은 설치중에 \[y/n\]을 설치전에 미리 yes로 설정해준다.
- openssl은 https 접속을 위해 설치해준다.

## openssl로 key crt 생성 및 복사
``` docker
RUN openssl req -newkey rsa:2048 -days 365 -nodes -x509 -subj "/C=KR/ST=Seoul/O=42seoul/OU=soum/CN=localhost" -keyout $KEYS -out $CERTS
RUN chmod +x $KEYS $CERTS
RUN mv $KEYS /etc/ssl/private
RUN mv $CERTS /etc/ssl/certs
```
-rsa:2048 : 서명에 사용할 Digest
-days :  유효시간
-subj : 인증 주제 설정. 미사용시 입력 프롬프트 표시

|필드|의미|예시|
|-|-|-|
|/C=|국가|KR|
|/ST=|State, 한국이라면 시/도|Seoul|
|/L=|Location|Gaepo|
|/O=|Organization, 회사명|42seoul|
|/OU=|Organization Unit, 부서명|Cadet|
|/CN=|Common Name|soum|

파일 권한을 수정하고 해당 디렉토리에 복사해준다.

## nginx conf 파일 추가
``` docker
COPY ./conf/soum.42.fr.conf /etc/nginx/conf.d/
COPY ./conf/soum.42.fr /etc/nginx/sites-available/
```

``` nginx
/etc/nginx/conf.d/soum.42.fr.conf
server {
	listen 443 ssl;
	listen [::]:443 ssl;
	server_name localhost;
	ssl on;
	ssl_certificate /etc/ssl/certs/soum.crt;
	ssl_certificate_key /etc/ssl/private/soum.key;
	ssl_protocols TSLv1.2 TSLv1.3;
	root /var/www/wordpress;
	index index.nginx-debian.html index.html index.php;
}
```
``` nginx
/etc/nginx/sites-available/soum.42.fr
server {
	location / {
		try_files $uri $uri/ =404;
	}
	location ~ \.php$ {
		try_files $uri =404;
		fastcgi_split_path_info ^(.+\.php)(/.+)$;	
		fastcgi_pass wordpress:9000;
		root /var/www/wordpress;
		fastcgi_index index.php;
		include fastcgi_params;
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
		fastcgi_param PATH_INFO $fastcgi_path_info;
	}
}
```
- location ~ \.php$ : 사용자 요청 파일이 php 확장자 파일을 요청할때 사용된다.
- fastcgi_pass : PHP-FPM에서 지정한 IP와 포트를 입력한다.
- fastcgi_param : PHP-FPM에 전달하게될 스크립트 파일명
- include fastcgi_params; : FastCGI 관련 구성 설정 정보를 포함한다
#[[docker]] #[[Dockerfile 명령어]] #[[nginx]]