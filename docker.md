
## Docker 개요

- 서버의 경우 1대의 서버에서 복수의 프로세스를 실행하지만, Docker의 경우 하나의 Docker 컨테이너에 하나의 마스터 프로세스를 실행
- 1 컨테이너 , 1마스터 프로세스 구성 -> 운용 관리 면에서 편리성과 안정성 실현
- 셋업을 마친 한 세트를 패키징해서 포터블한 이미지를 손쉽게 생성 및 이용 가능한 장점

## Docker Hub

- Docker image는 Docker Hub에
- Dockerfile은 GitHub에


## Dokcer 컨테이너의 기본 제어

- 컨테이너 라이프사이클 제어 명령
    | 서브 명령 | 의미               |
    | ----- | ---------------- |
    | run   | 배포,실행            |
    | stop  | 실행중 container 정지 |
    | start | 정지중 container 실행 |
    | rm    | 배포한 container 삭제 |

- Docker 컨테이너의 제어 명령
    | 서브 명령   | 의미                         |
    | ------- | -------------------------- |
    | exec    | 실행 중인 컨테이너에 명령 실행          |
    | logs    | 컨테이너의 로그(표준출력, 표준에러출력)을 표시 |
    | inspect | 컨테이너/이미지의 상세정보 표시          |


- Docker 이미지 제어 명령
    | 서브 명령  | 의미                        |
    | ------ | ------------------------- |
    | images | Docker데몬의 Docker이미지 목록 표시 |
    | rmi    | Docker 데몬의 Docker이미지 삭제   |


## Docker 실행옵션

| 내용         | 옵션           | 예제                                                                                                       | 비고                                                      |
| ---------- | ------------ | -------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| 컨테이너 이름 지정 | --name       | #docker run -d --name=mynginx nginx                                                                      |
| 환경변수 지정    | -e           | #docker run -e MYENV=foo centos evn \| grep MYENV                                                        |
| 볼륨 바인드     | -v           | #docker run -d --name nginx-vol -v /root/htdocs:/usr/share/nginx/html nginx                              |
| 포트 바인드     | -p           | #docker run -d -p 60000-60030:60000-60030 -p 1080:80 nginx                                               | 호스트의 0.0.0.0:1080을 컨테이너 80에 할당                          |
| 컨테이너 링크    | --link       | #docker run centos env \| grep -i _port </br> #docker run --link mynginx:ngx centos env \| grep -i _port | 접속 상대의 주소와 포트를 얻기 </br> 링크 대상 정보가 변경되면 링크하는 쪽도 다시 링크 필요 |
| 네트워크 접속    | --net        |                                                                                                          | 네트워크 접속방식 지정                                            |


## Dockerfile
- Dockerfile : Docker Image 구축 방법을 기술한 것
- 구축 결과는 보장하지 않는다. 따라서 동일성 보장을 위해서는 구축된 결과(이미지)를 기반으로 생각해야 한다.

| 내용             | 옵션          | 예제                                                                                                                         | 비고  |
| -------------- | ----------- | -------------------------------------------------------------------------------------------------------------------------- | --- |
| build time arg | --build-arg | #docker build --build-arg http_proxy=http://70.10.15.10:8080 --build-arg https_proxy=http://70.10.15.10:8080 -t example1 . |


```
#Dockerfile example
FROM centos:7
MAINTAINER your name <your@email.com>
RUN yum makecache
RUN yum -y install epel-release
RUN yum -y install httpd
EXPOSE 80 443
CMD ["/usr/sbin/httpd", "-DFOREGROUND"]

#Usage
# docker build --build-arg http_proxy=http://70.10.15.10:8080 --build-arg https_proxy=http://70.10.15.10:8080 -t example1 .
# docker build -t example1 .
```

## 부팅 시 실행할 명령어 지정 ENTIRYPOINT c.f. CMD
- 부팅시 실행할 명령은 CMD로 지정한다 ex) CMD ["/usr/sbin/httpd", "-DFOREGROUND"]
- 유사한 기능으로 ENTIRYPOINT가 있는데, 차이점은 실행 시 명령으로 지정한 문자열이 ENTIORYPOINT의 인수로 넘겨진다.
  
```
ex)  CMD["echo"]로 설정된 경우

# docker run image date  =>  date명령 실행됨
```

```
ex)  ENTIRYPOINT["echo"]로 설정된 경우

# docker run image date  =>  echo date명령 실행됨
```

## Docker 데몬의 네트워크 설정
- 디폴트로 docker0를 생성하고 호스트OS에 가용한 서브넷을 할당
- 데몬 실행 옵션 --bip=\<CIDR> 로 지정 가능하다.