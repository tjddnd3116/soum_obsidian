## 1. base image 사용
- nginx와 마찬가지로 같은 base 이미지 사용
``` dockerfile
FROM debian:buster`
```

## 2. ARG
- build시 사용할 arg
``` dockerfile
ARG MYSQL_DATABASE
ARG MYSQL_USER
ARG MYSQL_PASSWORD
ARG MYSQL_ROOT
ARG MYSQL_ROOT_PASSWORD
```

## 3. LABEL
``` dockerfile
LABEL email="soum@student.42seoul.kr"
LABEL name="soum"
LABEL version="1.0"
LABEL description="mariadb"
```

## 4. mariadb 설치
``` dockerfile
RUN apt-get update -y
	&& apt-get install -y mariadb-server
```
- mariadb 이미지에 사용할 mariadb를 설치해준다.

## 5. conf.sh 복사
``` dockerfile
COPY ./tools/conf.sh /
RUN chmod +x /conf.sh
RUN ./conf.sh
```
- mariadb 의 port 설정과 listen ip 를 설정하기 위한 스크립트를 복사후에 실행 한다.

``` shell
#!/bin/bash

# 3306 포트로 통신을 하기 위한 설정
perl -p -i -e '$.==19 and print "port = 3306\n"'
	/etc/mysql/mariadb.conf.d/50-server.cnf
	
# 모든 ip에서 통신이 가능하게 하기 위한 설정
perl -p -i -e 's/127.0.0.1/0.0.0.0/g'
	/etc/mysql/mariadb.conf.d/50-server.cnf

echo "CREATE DATABASE $MYSQL_DATABASE;\
		GRANT ALL PRIVILEGES ON $MYSQL_DATABASE.* TO
		'$MYSQL_USER'@'wordpress.%' IDENTIFIED BY
		'$MYSQL_PASSWORD';\
		FLUSH PRIVILEGES;\
		ALTER USER '$MYSQL_ROOT'@'localhost'
		IDENTIFIED BY '$MYSQL_ROOT_PASSWORD';\
		FLUSH PRIVILEGES:"
		> mariadb_conf.sh
		
```

- conf.sh 에서 mariadb를 시작하고 적용할 데이터 베이스 명령어들을 mariadb_conf.sh 로 만들어준다.
	1. 데이터 베이스를 생성한다.
	2. user를 생성한다 해당 유저는 wordpress.% ip 에서만 접속이 가능하고 wordpress의 데이터베이스에만 접근이 가능하다.
	3. 설정한 권한을 적용한다.
	4. root는 로컬 호스트에서만 접속이 가능하고 비밀번호도 변경한다..
	5. 설정한 권한을 적용한다.

## mariadb 실행, mariadb_conf.sh 적용
``` dockerfile
RUN service mysql start && mariadb < mariadb_conf.sh
RUN rm mariadb_conf.sh
```
- conf.sh 에서 만든 mariadb_conf.sh을 적용시킨다.
- 적용시킨후에 mariadb_conf.sh 은 필요가 없으므로 삭제한다.

## mysqld 실행
``` dockefifle
CMD mysqld
```
- mysqld를 실행시켜 running 상태로 만들어준다.
#[[docker]] #[[Dockerfile 명령어]] #[[mariadb]]