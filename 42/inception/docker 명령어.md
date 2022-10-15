## 이미지 관련 명령어
### 이미지 목록 보기
>$ sudo docker images
### 이미지 검색
>$ sudo docker search \[이미지 이름\]
### 이미지 받기
>$ sudo docker pull \[이미지이름\]:\[버전\]
### 이미지 삭제
>$ sudo docker rmi \[이미지 id\]
\* rmi = remove image 
이미지 id는 앞의 몇개만 입력해도 가능
-f : 강제삭제 옵션

## 컨테이너 관련
### 컨테이너 목록 보기
>$ sudo docker ps
>-a : 모든 컨테이너 목록 출력
### 컨테이너 실행
> $ sudo docekr run \[options\] image\[:TAG|@DIGEST] \[COMMAND] \[ARG...]

|옵션|설명|
|-|-|
|-d| detached mode (백그라운드 모드)|
|-p|호스트와 컨테이너의 포트를 연결(포워딩)| 
|-v|호스트와 컨테이너의 디렉토리를 연결(마운트)|
|-e|컨테이너 내에서 사용할 환경변수 설정|
|--name|컨테이너 이름 설정|
|--it|터미널 입력을 위한 옵션(컨테이너의 표준입력과 로컬 컴퓨터의 입력을 연결)|
|--rm|프로세스 종료시 컨테이너 자동 제거|
|--link|컨테이너 연결\[컨테이너 명:별칭\]|
### 컨테이너 시작
>$ sudo docker start \[컨테이너 id 또는 name]
### 컨테이너 재시작
>$ sudo docker restart \[컨테이너 id 또는 name]
### 컨테이너 접속
>$ sudo docker attach \[컨테이너 id 또는 name]
### 컨테이너 정지
> sudo docker stop \[컨테이너 id 또는 name]
### 컨테이너 삭제
>$ sudo docker rm \[컨테이너 id 또는 name]

#[[docker]]