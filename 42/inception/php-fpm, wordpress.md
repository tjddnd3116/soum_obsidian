# php-fpm
## php-fpm이란?
. PHP는 c언어를 기반으로 만들어진 서버 측에서 실행되는 서버 사이드 스크립트 언어이다.
2. CGI(Common Gateway Interface)는 동적인 페이지 구현을 위한 프로그램에 클라이언트의 요청을 전달해주는 프로그램이다.
3. PHP-fpm(PHP FastCGI Process Manager)는 보통의 CGI보다 빠른 버전이다.

## Nginx와 php-fpm 연동하기
> Nginx웹서버가 PHP프로그램을 사용하는 서비스에 요청을 전달하려면 Nginx와 PHP-fpm간 연동이 필요하다.

 ### Nginx에서 PHP-fpm로의 PHP파일 전달 과정
1. Nginx에 PHP파일을 요청받게 되면 PHP 처리가 정의 되어 있는 LOCATION안의 제어문을 처리한다.
 2. LOCATION 제어문 안에 있는 지시어를 활용하여 PHP-fpm에게 전달할 php 파일을 지정한다.
 3. 전달하기 위해 지정된 파일을 fastcg_pass 경로의 PHP-fpm에 전달한다.
 4. php 파일을 전달받은 PHP-fpm은 php가 실행될 수 있도록 외부 프로그램에 연결시켜준다.

### nginx 서버설정
``` nginx
etc/nginx/sites-available/soum.42.fr
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
```
- PHP-fpm 연결 주소는 php-fpm.conf 파일에서 확인가능하다.
- php-fpm.conf 파일 설정을 통해 php-fpm의 성능을 향상시키고 php-fpm 프로그램을 사용하는 컴퓨터 자원을 효율적으로 관리할 수 있다.
- Location 안에서 추가적으로 사용할 수 있는 fastcgi-param이라는 지시어는 PHP-fpm에 전달할 변수를 정의한다.
- ex. fastcgi_param SCRIPT_FILENAME \[웹사이트 경로\]\[사용자가 요청한 파일\] : 사용자가 요청한 디렉토리와 사용자가 요청한 파일을 조합해서 SCRIPT_FILENAME이라는 변수에 저장하고, fastcgi_pass는 정의되어 있는 PHP_FPM에게 변수의 내용을 전달하여 파일을 처리한다.
- PHP Location 제어문 변수
	1. $document_root : 사용자가 접속한 웹사이트 경로가 담겨있다.
	2. $fastcgi_script_name : 사용자가 요청한 파일이 담겨있다.
	3. SCRIPT_FILENAME : fastcgi_param 지시어로 사용자가 접속한 경로와 요청한 파일의 위치가 담겨지게 된다.
	
### php-fpm 설정
- php가 동작하는 방식에는 소켓통신, IP통신 두가지가 있다.
- 우리는 IP통신 방식으로 진행한다.
``` nginx
/etc/php/7.3/fpm/pool.d/www.conf

listen = 9000
```
- nginx에서  wordpress:9000 포트로 보낸 파일을 php-fpm에서 받을수 있게 listen을 9000으로 바꿔준다.

# wordpress
>PHP로 작성된 온라인 오픈소스 웹사이트 제작 도구

### wordpress 설치
- wp-cli로 설치가 가능하다.
> wp-cli란
> wp-cli는 phar로 작성된 유틸리티로 command로 wordpress를 관리할 수 있는 유틸리티이다.

``` docker
# wget으로 wp-cli를 설치한다.
RUN wget https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar

# chmod로 권한을 설정해준다.
RUN chmod +x wp-cli.phar

# 매번 wp-cli.phar로 명령어를 쓰기 불편하기 때문에 bin 디렉토리에 wp라는 이름의 커맨드로 바꿔준다.
RUN mv wp-cli.phar /usr/local/bin/wp

# wordpress 디렉토리 안에 wordpress를 설치한다.
RUN mkdir wordpress
WORKDIR /wordpress
RUN wp core --allow-root download --version=4.2.2 --local=ko_KR
```

### wordpress와 database
- 워드프레스의 모든 정보는 데이터베이스에 저장되며 워드프레스의 필수 요소이다.
- 데이터베이스에 저장된 정보는 웹사이트를 움직이게 만들고 관리자 또는 사용자가 변경한 사항을 저장한다.
- 워드프레스의 데이터베이스는 사용자 이름, 패스워드와 같은 기본 정보뿐만 아니라 포스트, 페이지, 댓글, 워드프레스 구성 설정까지 모든 것을 포함하고 있다.

### wordpress 데이터베이스의 정보 구성
아래의 테이블은 워드프레스를 처음 설정할 때 자동으로 생성되는 테이블이다.
- wp_commentmeta : 워드프레스 게시물, 페이지 등에 대한 댓글 정보
- wp_comments : 워드프레스 사이트에서 작성한 실제 댓글 정보
- wp_links : 워드프레스 링크 기능에 대한 정보
- wp_options : 설정 패널을 통해 사용할 수 있는 다양한 옵션 값에 대한 정보
- wp_postmeta : 포스트의 메타 데이터
- wp_posts : 포스트 관련 데이터
- wp_terms : 태그 및 카테고리를 포함한 조직 요소 정보
- wp_termmeta : 태그 및 카테고리의 메타 정보
- wp_term_relationships : 워드프레스 포스트 및 링크 연결을 정의하는 정보
- wp_term_taxonomy : 카테고리와 태그를 할당할 때 해당정보를 기록하는 집합 테이블
- wp_usermeta : 워드프레스 사용자의 메타 데이터 정보
- wp_users : 사용자와 관련된 모든 정보

### wordpress와 데이터베이스 연결
- 워드프레스 설치이후 wp 명령어를 통해 mariadb와 연결을 위한 config파일을 만들어준다.
``` shell script

$MYSQL_DATABASE=wordpress
$MYSQL_USER=soum
$MYSQL_PASSWORD=12345
$MYSQL_HOST=mariadb

wp --allow-root core config
--dbname=$MYSQL_DATABASE
--dbuser=$MYSQL_USER
--dbpass=$MYSQL_PASSWORD
--dbhost-$MYSQL_HOST
```
- 데이터베이스의 이름과 사용자, 사용자 패스워드, 데이터베이스의 호스트를 넣어서 wp core config 명령어를 실행하면 wp-config.php 파일이 만들어진다.
- 이렇게하면 wordpress와 mariadb의 연결이 가능하다.

> wp core config 명령어는 mariadb가 실행한 이후에 명령어가 실행되어야 한다. 
> -> mariadb가 켜져야 통신이 가능하기때문 mariadb가 켜지지 않고 저 명령어를 실행하게되면 mysql을 찾을수 없다고 뜬다.

### wordpress 설치하기
- inception과제에서는 docker가 실행되고 nginx로 wordpress를 열게되면 설치페이지가 뜨는것이 아닌 이미 설치되어있는 wordpress페이지가 실행되어야 한다.
- wp-cli로 wordpress설치와 user추가가 가능하다.
``` shell script
$WP_TITLE=inception
$WP_ADMIN=oumsungwoong
$WP_ADMIN_PW=1234
$WP_ADMIN_email=tjddnd3116@gmail.com

wp --allow-root core install -url=localhost
--title=$WP_TITLE
--admin_user=$WP_ADMIN
--admin_password=$WP_ADMIN_PW
--admin_email=$WP_ADMIN_EMAIL
```
- 이 명령어를 실행하면 admin이 생성이되고 해당 wordpress의 타이틀이 inception으로 설치가 된다.

### wordpress 사용자 추가
- wordpress 데이터베이스 안에는 admin 과 다른 한명의 유저가 존재해야 한다.
- wp-cli 명령어로 사용자를 생성할 수 있다.
``` shell
$WP_USER=soum
$WP_USER_EMAIL=soum@student.42seoul.kr
$WP_USER_PW=12345

wp --allow-root user create $WP_USER $WP_USER_EMAIL --role=author --user_pass=$WP_USER_PW
```
- wp user create 명령어를 사용하면 유저의 이름, 패스워드가 정해지고 권한까지 부여할 수 있다.
#[[docker]] #[[dockerfile -php-fpm & wordpress]]