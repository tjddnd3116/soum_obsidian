# mariadb
>MariaDB사가 제작한 오픈소스 RDMBS 소프트웨어

## mariadb의 특징
- 오픈소스
	- mariadb는 무료로 모든 기능을 사용할 수 있다.
- MySQl 친화적인 RDBMS
	-  MySQL에서 사용하고 있는 SQL문이나 명령문들을 그래도 MariaDB에서 채택하고 있는 경우가 많다.
- MySQL 코드 베이스지만 MySQL과는 독립적인 RDBMS
	- InnoDB와 같이 MySQL RDBMS에서 어떤 플러그인 형태로 끼웠다가 빼서 쓸 수 있는 것이 아닌 그 자체로 독립적인 RDBMS라는 것을 의미함

## mariadb 명령어
### 1. 기본 명령어
- DB접속
	- mysql -u \[계정ID\] -p;
	- mysql -u \[계정ID\] -p \[DB이름\];
	- mysql -u \[계정ID\] -p \[계정PW\];
	- mysql -u \[계정ID\] -p \[계정 PW]\[DB 이름];

- 로그아웃
	- exit;
	- quit;

- 기본 정보확인
	- status;

- 에러, 경고 메시지 재확인
	- show errors;
	- show warnings;

### 2. 데이터베이스 관련 명령어
- DB 생성
	- create database \[DB 이름\];

- DB 목록 확인
	- show databases;

- 사용중인 DB 출력
	- select database();

- DB 선택하기
	- use \[DB 이름\];

- DB 삭제
	- drop database \[DB 이름\];

### 3. 테이블 관련 명령어
- 테이블 생성
	- create table \[테이블 이름\](\[열 이름\] INT(5), \[열 이름\] VARCHAR(10), 데이터 타입 참조하기);
	- create table \[테이블 이름\](colmun_name1 INT(5), colmun_name2 VARCHAR(10));

- 테이블 목록 확인
	- show tables;

- 테이블 구조 확인
	- explain \[테이블 이름\];
	- describe \[테이블 이름\];
	- desc \[테이블 이름\];
	- show index from \[테이블 이름\];

- 테이블 이름 변경
	- rename table \[테이블 이름\]to \[변경할 테이블 이름\];

- 테이블 삭제
	- drop table \[테이블 이름\];

### 4. 유저 관련 명령어
- 유저 리스트 출력
	- select user, host from mysql.user;

- 유저 추가
	- create user '유저ID'@'localhost' identified by '유저암호';

- 유저 권한 부여
	- grant all on *.* to '유저ID'@'localhost';
	- grant all privileges on \[DB 이름\].* to '유저 ID'@'localhost';
	- grant all privileges on \[DB 이름\].* to '유저 ID'@'%'; // 외부 호스트
 - 유저 삭제
	 - delete from user where user = '유저 ID';
	    flust privileges;

- MySQL 호스트 IP 주소 찾기
	- show variables where Variable_name = '호스트 이름';

#[[docker]] #[[dockerfile -mariad]]