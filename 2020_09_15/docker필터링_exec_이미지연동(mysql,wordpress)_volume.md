# Today I Learned



* CI/CD의 개념
* Docker 복습
* 태킹되지 않은 이미지를 검색 및 태그 붙이기
* Dockerfile로 이미지 빌드 시 주의사항
* 도커 컨테이너 생명주기 (P62)
* docker container run 명령의 인자를 이용해서 CMD 명령을 오버라이드 (P64)
* 컨테이너 목록 필터링 (P67)
  * 출력 형식 지정 (formatting)
  * 컨테이너 정지
  * 컨테이너 재시작
  * 컨테이너 삭제 - 중지(Exited) 상태인 컨테이너를 삭제
  * 컨테이너를 메뉴얼(수동)하게 삭제하는 방법
  * 컨테이너를 중지할 때 컨테이너를 자동으로 삭제
* 컨테이너 내부의 표준 출력을 호스트로 연결
* 실행중인 컨테이너 내부로 명령을 전달(실행)
* 호스트의 파일 또는 컨테이너 내부의 파일을 양방향으로 복사
* 불필요한 컨테이너 및 이미지 삭제
* 컨테이너 단위 시스템 리소스 사용 현황 확인
* LAB. 특정 웹페이지를 포함하고 있는 웹서버 이미지를 생성
* 이미 만들어진 이미지를 이용해서 웹 서버 구축
* wordpress와 mysql을 연동한 워드프로세스 기반 블로그 서비스
* 컨테이너의 데이터를 영속적(persistent)인 데이터로 활용하는 방법

---



### CI/CD의 개념

1. **기본 개념은 지속적인 통합, 지속적인 서비스 제공, 지속적인 배포**
   * CI/CD는 애플리케이션 개발 단계를 **자동화**하여 애플리케이션을 보다 짧은 주기로 고객에게 제공하는 방법
   * **CI/CD 파이프라인**이라고 불리움 - 개발 및 운영팀의 애자일 방식 협력을 통해 지원



2. **CI와 CD의 차이점**
   * **CI**는 개발자를 위한 자동화 프로세스인 지속적인 통합을 의미
     * 정기적으로 빌드 및 테스트되어 공유 리포지토리에 통합
   * CD는 지속적인 서비스 제공 및 지속적인 배포
     * 대체로 **파이프라인의 추가 단계**에 대한 **자동화**를 뜻함

---



### Docker 복습

1. 컨테이너형 가상화 기술 : **상주 애플리케이션**과 이 애플리케이션을 조작하기 위한 **명령행 도구**로 구성

   * 조작이 간편하고 경량 컨테이너 - 배포에 특화
   * 기존 가상화 소프트웨어보다 더 가볍게 동작
   * 이식성이 뛰어남

   ※ 도커가 적합하지 않은 경우도 존재

   > 운영 체제의 동작을 완전히 재현하지 못함
   >
   > 비리눅스 환경이나 엄밀한 리눅스 계열 운영 체제의 경우 도커가 적합하지 않음



2. Dockerfile

   ※ 컨테이너들은 각각으로 분할되고 독립된 형태

   ※ 어떻게 이미지를 만들고 실행할지를 정의

   * From 절 : 컨테이너의 원형(틀) 역할을 할 도커 이미지(운영 체제)를 정의

   * COPY 절 : 복사하라고 정의

   * RUN 절 : 어떤 명령을 수행하기 위한 것

     ※ 여기까지가 도커 빌드 과정에서 실행 >> 새로운 이미지가 만들어짐
   
   * CMD 절 : 완성된 이미지를 도커 컨테이너로 실행하기 전에 먼저 실행할 명령 (애플리케이션 실행 등)

---



### 태킹되지 않은 이미지를 검색 및 태그 붙이기

1. 태깅되지 않은 이미지 검색

```bash
vagrant@xenial64:~/pulltest$ docker image ls -f "dangling=true"
```

2. 이미지에 태그를 변경 방법

```bash
vagrant@xenial64:~/pulltest$ docker image tag --help
`Usage:  docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]`
```

3. 태깅되지 않은 이미지에 태그를 추가

```bash
vagrant@xenial64:~/pulltest$ docker image tag $(docker image ls -f "dangling=true" -q) myanjini/basetest:0.1
vagrant@xenial64:~/pulltest$ docker image ls
```

---



### Dockerfile로 이미지 빌드 시 주의사항

※ 이미지 빌드가 완료되면 Dockerfile의 명령어 줄 수 만큼의 레이어가 존재

※ 실제 컨테이너에서 사용하지 못하는 파일(디렉터리)이 이미지 레이어에 존재하면 공간만 차지하게 됨

**⇒ Dockerfile을 작성할 때 &&로 각 RUN 명령어를 하나로 묶어서 실행**



1. **3개의 RUN 명령어 실행 → 실제 이미지 내부에 변경은 없음 (100M 크기의 파일을 생성 후 삭제하므로)**

```bash
vagrant@xenial64:~/pulltest$ mkdir ~/dockerfile_test && cd ~/dockerfile_test
vagrant@xenial64:~/dockerfile_test$ vi Dockerfile

--------------------------------------------
FROM ubuntu
RUN  mkdir /test
RUN  fallocate  -l  100m  /test/dumy
RUN  rm  /test/dumy
--------------------------------------------
vagrant@xenial64:~/dockerfile_test$ docker image build -t falloc_100m .
vagrant@xenial64:~/dockerfile_test$ docker image ls
`falloc_100m         latest              cb419b52df77        23 seconds ago      179MB
```



2. 3개의 RUN 명령어를 하나로 줄여서 실행

```bash
vagrant@xenial64:~/dockerfile_test$ vi Dockerfile

--------------------------------------------
FROM ubuntu
RUN  mkdir /test  &&  fallocate  -l  100m  /test/dumy  &&  rm  /test/dumy
--------------------------------------------
vagrant@xenial64:~/dockerfile_test$ docker build -t recommand .
vagrant@xenial64:~/dockerfile_test$ docker image ls
`recommand           latest              6a667c9fadb5        4 seconds ago       73.9MB
```

---



### 도커 컨테이너 생명주기 (P62)

---



### docker container run 명령의 인자를 이용해서 CMD 명령을 오버라이드 (P64)

**※ libray/alpine:latest 이미지의 Dockerfile 확인** [URL](https://github.com/alpinelinux/docker-alpine/blob/90788e211ec6d5df183d79d6cb02e068b258d198/x86_64/Dockerfile)

```bash
FROM scratch
ADD alpine-minirootfs-3.12.0-x86_64.tar.gz /
CMD ["/bin/sh"]
```



1. alipne 이미지를 이용해서 컨테이너를 생성

```bash
vagrant@xenial64:~$ docker container run -it alpine

`/ # uname -a						⇐ 컨테이너가 생성되면 컨테이너 내부에 쉘을 실행
```

2. CMD 명령을 오버라이드

   ※ 인자값을 넘겨줌

````bash
vagrant@xenial64:~$ docker container run -it alpine uname -a
`Linux 9bf859f09b0f 4.4.0-189-generic #219-Ubuntu SMP Tue Aug 11 12:26:50 UTC 2020 x86_64 Linux  ⇐ uname -a 결과`

vagrant@xenial64:~$ docker container run -it alpine ip a
````

---



### 컨테이너 목록 필터링 (P67)

1. 테스트를 위해 이미지 생성하고 컨테이너를 실행

```bash
vagrant@xenial64:~/chap02$ docker image build -t example/echo:latest .

vagrant@xenial64:~/chap02$ docker container run -d -p 9000:8080 example/echo:latest
vagrant@xenial64:~/chap02$ docker container ls
```

2. 컨테이너 이름을 부여해서 실행 **(--name)**

```bash
vagrant@xenial64:~/chap02$ docker container run -d -p 9001:8080 --name myecho example/echo:latest

vagrant@xenial64:~/chap02$ docker container ls
```

3. 중지된 상태의 컨테이너를 포함해서 조회

```bash
vagrant@xenial64:~/chap02$ docker container ls -a
```

4. 컨터이너 **이름**으로 조회

```bash
vagrant@xenial64:~/chap02$ docker container ls -a --filter "name=myecho"
```

5. 컨테이너 생성에 사용한 **이미지**로 조회 (사용자명/이미지명 형식으로 조회)

```bash
vagrant@xenial64:~/chap02$ docker container ls -a --filter "ancestor=example/echo:latest"
```

Quiz.

태그가 다른 이미지를 이용해서 실행한 컨테이너를 ancestro 필터로 검색할 경우 결과는 어떻게 될까?

1. **example/echo:latest 이미지를 example/echo:old로 태그를 변경**

```bash
vagrant@xenial64:~/chap02$ docker image tag example/echo:latest example/echo:old
vagrant@xenial64:~/chap02$ docker image ls

`example/echo        latest              16a4c3b0f222        18 minutes ago      750MB
`example/echo        old                 16a4c3b0f222        18 minutes ago      750MB
```

2. **example/echo:old 이미지로 컨테이너를 생성**

```bash
vagrant@xenial64:~/chap02$ docker container run -d -p 9002:8080 --name myoldecho example/echo:old
vagrant@xenial64:~/chap02$ docker container ls
`e04df49d7bea        example/echo:old      "go run /echo/main.go"   10 seconds ago      Up 9 seconds
```

3. **ancestor=example/echo로 검색**

```bash
vagrant@xenial64:~/chap02$ docker container ls --filter "ancestor=example/echo"
```

4. **ancestor=example/echo:latest로 검색**

```
vagrant@xenial64:~/chap02$ docker container ls --filter "ancestor=example/echo:latest"
```

5. **3번과 #4번의 결과가 같을까? 다를까?**

   ※ ⇒ 같다 ⇒ 필터링에 사용하는 이미지는 "사용자명/이미지명"까지만 식별 

   

#### 출력 형식 지정 (formatting)

```bash
vagrant@xenial64:~/chap02$ docker container ls -a --format "{{.ID}} : {{.Command}}"


`e04df49d7bea : "go run /echo/main.go"
`b10088f2914a : "go run /echo/main.go"
`46c40acffd83 : "go run /echo/main.go"
`88d914e6d686 : "ip a"
`bbd991ac33b9 : "ls"
`9bf859f09b0f : "uname -a"
`935620ae9dd7 : "/bin/sh"
```

```bash
vagrant@xenial64:~/chap02$ docker container ls -a --format "table {{.ID}} : {{.Names}}\t{{.Command}}"

`CONTAINER ID : NAMES                COMMAND
`e04df49d7bea : myoldecho            "go run /echo/main.go"
`b10088f2914a : myecho               "go run /echo/main.go"
`46c40acffd83 : epic_spence          "go run /echo/main.go"
`88d914e6d686 : ecstatic_aryabhata   "ip a"
`bbd991ac33b9 : determined_bhabha    "ls"
`9bf859f09b0f : admiring_thompson    "uname -a"
`935620ae9dd7 : awesome_shamir       "/bin/sh"
```



#### 컨테이너 정지

```bash
# 컨테이너 이름
vagrant@xenial64:~/chap02$ docker container stop myoldecho	
# 컨테이너 ID
vagrant@xenial64:~/chap02$ docker container stop b10088f2914a	
# 컨테이너 ID 일부 (앞에서 부터 식별 가능한 범위)
vagrant@xenial64:~/chap02$ docker container stop 46		
```



#### 컨테이너 재시작

```bash
vagrant@xenial64:~/chap02$ docker container restart myoldecho
vagrant@xenial64:~/chap02$ docker container restart b10088f2914a
vagrant@xenial64:~/chap02$ docker container restart 46
```



#### 컨테이너 삭제 - 중지(Exited) 상태인 컨테이너를 삭제

1. 중지 상태인 컨테이너를 조회

```bash
vagrant@xenial64:~/chap02$ docker container ls --filter "status=exited"
```

2. 중지 상태인 컨테이너의 ID를 조회

```bash
vagrant@xenial64:~/chap02$ docker container ls --filter "status=exited" -q
```

3. 중지 상태인 컨테이너를 일괄 삭제

```bash
vagrant@xenial64:~/chap02$ docker container rm $(docker container ls --filter "status=exited" -q)
vagrant@xenial64:~/chap02$ docker container ls --filter "status=exited"
```

4. 모든 컨테이너를 삭제

```bash
vagrant@xenial64:~/chap02$ docker container rm -f $(docker container ls -a -q)
```



#### 컨테이너를 메뉴얼(수동)하게 삭제하는 방법

1. 도커 서비스를 중지

```bash
sudo service docker stop
```

2. 컨테이너 파일 확인 및 삭제

```bash
sudo ls /var/lib/docker/containers
sudo rm -r /var/lib/docker/containers/CONTAINER_ID
```

3. 도커 서비스를 실행

```bash
sudo service docker start
```



#### 컨테이너를 중지할 때 컨테이너를 자동으로 삭제

1. **컨테이너 실행**

```bash
vagrant@xenial64:~/chap02$ docker container run -d -p 9000:8080 example/echo:latest
```

2. **컨테이너를 중지하면 Exited 상태로 대기 ⇒ restart 명령으로 재기동 가능**

```bash
vagrant@xenial64:~/chap02$ docker container stop f02810866949ba9d9c8ed7344d3c4da1daa8147994d92b83bae533666eca4b92
```

3. **컨테이너 생성 시 --rm 옵션을 추가하면 컨테이너를 중지하면 해당 컨테이너를 삭제**

   ※ 중지가 필요없는 것들은 `--rm`옵션을 이용

```bash
vagrant@xenial64:~/chap02$ docker container run -d -p 9000:8080 --rm example/echo:latest
```

---



### 컨테이너 내부의 표준 출력을 호스트로 연결

1. Jenkins 실행

```bash
# jenkins 도커에서 공식 배포하는 최신 버전(latest)
# ⇒ libary/jenkins:latest 같은 의미
vagrant@xenial64:~/chap02$ docker container run -d -p 8080:8080 -p 5000:5000 jenkins

vagrant@xenial64:~/chap02$ docker container ls
```

```bash
vagrant@xenial64:~/chap02$ docker container logs -f 406959fb618b
```

---



### 실행중인 컨테이너 내부로 명령을 전달(실행)

​	※ `docker container exec ` `컨테이너이름` `명령어` 사용

1. 인터렉션 쉘을 이용해서 컨테이너 내부로 명령어를 한번 입력 받을 수 있게함

```bash
vagrant@xenial64:~/chap02$ docker container run -t -d --name echo --rm example/echo:latest

vagrant@xenial64:~/chap02$ docker container exec echo pwd
vagrant@xenial64:~/chap02$ docker container exec echo ip a
```

2. 컨테이너 내부 쉘을 이용해서 명령어를 계속 받게 함 (인터페이스를 변경)

```bash
vagrant@xenial64:~/chap02$ docker container exec -it echo /bin/sh

# pwd
`/go
# ls
`bin  src
```

---



### 호스트의 파일 또는 컨테이너 내부의 파일을 양방향으로 복사



#### 호스트의 파일 또는 디렉토리를 컨테이너 내부로 복사

​	※ **docker container cp 호스트경로 컨테이너이름:컨테이너 내부 경로**

1. **호스트의 현재 시간을 파일로 생성**

```bash
vagrant@xenial64:~/chap02$ date > host_now

vagrant@xenial64:~/chap02$ cat host_now
`Tue Sep 15 02:38:55 UTC 2020
```

2. **호스트의 파일을 echo(컨테이너 이름) 컨테이너 내부로 복사**

```bash
vagrant@xenial64:~/chap02$ docker container cp ./host_now echo:/tmp/
```

3. **컨테이너로 복사한 파일의 내용을 확인**

```bash
vagrant@xenial64:~/chap02$ docker container exec echo cat /tmp/host_now
`Tue Sep 15 02:38:55 UTC 2020
```



#### **컨테이너 내부의 파일을 호스트로 복사**

**※ docker container cp 컨테이너이름:컨테이너내부경로 호스트경로** 

```bash
vagrant@xenial64:~/chap02$ docker container cp echo:/tmp/host_now  ./host_now_from_container

vagrant@xenial64:~/chap02$ cat ./host_now_from_container
```

---



### 불필요한 컨테이너 및 이미지 삭제

​	**※ 끝난(exited) 컨테이너 삭제 `prune`**

```bash
vagrant@xenial64:~/chap02$ docker container ls -a

vagrant@xenial64:~/chap02$ docker container prune
`WARNING! This will remove all stopped containers.
```

​	**※ none 이미지 파일 삭제 - dangling 파일을 지움**

```bash
docker image prune

# 해당 이미지가 다른 이미지를 참조하고 있다면 원본 이미지를 삭제해야 함
docker image ls
```

---



### 컨테이너 단위 시스템 리소스 사용 현황 확인

​	**※ `stats` 명령어 사용**

```bash
vagrant@xenial64:~/chap02$ docker container stats

----------------------------------------------------
`CONTAINER ID NAME CPU % MEM USAGE / LIMIT MEM % NET I/O BLOCK I/O PIDS
406959fb618b        fervent_heyrovsky   0.07%               209.1MiB / 991.9MiB   21.08%              4.48MB / 96.8kB     44MB / 17.1MB       32
```

---



### LAB. 특정 웹페이지를 포함하고 있는 웹서버 이미지를 생성

* http://localhost:8080/hello.html 요청하면 hello docker 메시지를 반환하는 웹 서비스를 제공하는 이미지를 생성
* 첫번째 방식: 컨테이너 안에서 어떤 설정이 되어 있는지 모름
* 두번째 방식(권장): dockerfile안에 명시된 내용을 기반으로 설정이 되어 있기때문에 믿을 수 있음



#### 첫번째 방식. 우분투 이미지를 이용해서 컨테이너를 실행하고 컨테이너 내부를 변경한 후 이미지를 생성

1. **작업 디렉터리 생성**

```bash
vagrant@xenial64:~/chap02$ mkdir ~/webserver && cd ~/webserver
```

2. **hello.html 파일을 생성**

```bash
vagrant@xenial64:~/webserver$ echo "hello docker" > hello.html
vagrant@xenial64:~/webserver$ cat hello.html
```

3. **우부투 이미지를 이용해서 컨테이너를 실행** 

   ※ `-it` : 입력받고 인터렉션할 수 있도록 함

```bash
# 컨테이너 이미 실행 시 종료 후 다시 run 시켜야함
vagrant@xenial64:~/webserver$ docker container rm -f myweb

vvagrant@xenial64:~/webserver$ docker container run -dit -p 8080:80 --name myweb ubuntu:14.04
```

4. **컨테이너 내부의  쉘로 접속**

```bash
방법 1. vagrant@xenial64:~/webserver$ docker container exec -it myweb /bin/bash

방법 2. vagrant@xenial64:~/webserver$ docker container attach myweb
```

5. **컨테이너 내부에 아파치 웹 서버를 설치 및 실행**

   ※ 컨테이너 내부에서 설치하는 것

```bash
root@b255646bc72b:/# apt-get update
root@b255646bc72b:/# apt-get install apache2 -y

root@d8337bf2c819:/# service apache2 status
 * apache2 is not running
root@d8337bf2c819:/# service apache2 start
```

6. **아파치 웹 서버의 웹 루트에 hello.html 파일을 복사**

```bash
# 웹 서버의 루트 경로
root@b255646bc72b:/# ls /var/www/html
`index.html

vagrant@xenial64:~/webserver$ docker container cp ./hello.html myweb:/var/www/html/
# 웹 서버 내부에 복사 되었는지 확인
vagrant@xenial64:~/webserver$ docker container exec myweb cat /var/www/html/hello.html
```

7. **컨테이너로 웹 서비스를 요청**

```bash
vagrant@xenial64:~/webserver$ curl http://localhost:8080/hello.html
`hello docker
```

8. **이미지를 생성**

```bash
vagrant@xenial64:~/webserver$ docker commit myweb myanjini/myweb:latest

vagrant@xenial64:~/webserver$ docker image ls
`REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
`myanjini/myweb      latest              4d477e6f42d0        6 seconds ago       221MB
```



#### **두번째 방식. Dockerfile을 작성해서 이미지를 생성**

1.  **Dockerfile 정의**

```bash
vagrant@xenial64:~/webserver$ vi Dockerfile
```

2.  **Dockerfile을 빌드해서 이미지 생성**

```bash
FROM   ubuntu:14.04

RUN    apt-get update

RUN    apt-get install -y apache2

ADD    hello.html  /var/www/html/

EXPOSE 80

CMD    apachectl   -DFOREGROUND
```

3. **생성한 이미지로 컨테이너를 실행**

```bash
vagrant@xenial64:~/webserver$ docker container run -d -p 9090:80 --name mywebdockerfile myanjini/myweb:dockerfile

vagrant@xenial64:~/webserver$ curl http://localhost:9090/hello.html
```

4. **생성한 이미지로 컨테이너를 실행 (호스트 포트를 랜덤하게 지정)**

```bash
# 호스트의 랜덤하게 할당된 포트와 컨테이너에서 EXPOSE(dockerfile에 정의)된 포트를 자동으로 맵핑
vagrant@xenial64:~/webserver$ docker container run -d -P --name mywebrandport myanjini/myweb:dockerfile

vagrant@xenial64:~/webserver$ docker port mywebrandport
`80/tcp -> 0.0.0.0:32770
```

---



### <u>이미 만들어진 이미지</u>를 이용해서 웹 서버 구축

1. **도커 허브에서 적당한 이미지를 검색 및 다운로드**

```bash
https://hub.docker.com/_/nginx

vagrant@xenial64:~/webserver$ docker pull nginx
```

2. **nginx 서버를 구동 (컨테이너를 생성)**

```bash
vagrant@xenial64:~/webserver$ docker container run --name webserver -d -p 80:80 nginx

# 서버확인
vagrant@xenial64:~/webserver$ curl http://localhost
```

---



### **wordpress와 mysql을 연동한 워드프로세스 기반 블로그 서비스**

​	※ Dockerfile 컨테이너 파일을 하나하나 만들어서 연동하려면 운영체제도 설정해줘야하고 힘듬

​	※ 이미지가 이미 구현된 DockerHub 이미지를 이용해서 이미지끼리 서로 연동

**방법1.CentOS 가상 머신으로 실습하는 경우 아래 명령어를 먼저 실행 (DevOps 과정에서 사용했던 가상머신)**

* C:\HashiCorp\WorkDir> vagrant up 
* C:\HashiCorp\WorkDir> vagrant ssh

* [vagrant@demo blog]$ sudo systemctl start docker.service
* [vagrant@demo blog]$ sudo su

**방법2. Ubuntu 가상 머신으로 실행한 경우** : 포트 포워딩 설정

* 호스트 포트, 게스트 포트 ⇒ `docker container ls` 했을 때 나오는 호스트의 포트를 명시
* 게스트 IP ⇒ Ubuntu 가상머신에서 ifconfig 했을 때 나오는 enp0s3 NIC의 IP 



1. **작업 디렉터리 생성**

```bash
vagrant@xenial64:~/webserver$ mkdir ~/blog && cd ~/blog
```

2.  **mysql 서비스를 제공하는 컨테이너를 실행**

   ※ `-e` : 환경변수 값 설정

`-e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7` : 컨테이너 내부의 환경 변수를 설정

```bash
vagrant@xenial64:~/blog$ docker run -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7 
```

3. **워드프레스 이미지를 이용한 웹 서버 컨테이너를 실행**

   ※ `--link` : Alias화 해줌 (해당 컨테이너 내부에서만)

   * wordpressdb를 mysql 로 쓰겠다!

```bash
vagrant@xenial64:~/blog$ docker run -d -e WORDPRESS_DB_PASSWORD=password --name wordpress --link wordpressdb:mysql -p 80 wordpress
```

4.  **컨테이너 실행 확인**

```bash
vagrant@xenial64:~/blog$ docker container ls

CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                   NAMES
`7a26c2712474        wordpress                   "docker-entrypoint.s…"   19 seconds ago      Up 18 seconds       0.0.0.0:32772->80/tcp   wordpress
`d562bcfaa1b0        mysql:5.7                   "docker-entrypoint.s…"   7 minutes ago       Up 7 minutes        3306/tcp, 33060/tcp     wordpressdb
```

5. **워드프레스 컨테이너 내부에서 wordpressdb 이름의 컨테이너(mysql로 alias되어 있음)로 연결되는지 확인**

```bash
vagrant@xenial64:~/blog$ docker container exec -it wordpress /bin/bash

root@7a26c2712474:/var/www/html# ping mysql
`bash: ping: command not found

root@7a26c2712474:/var/www/html# apt-get update
root@7a26c2712474:/var/www/html# apt-get install -y iputils-ping

root@7a26c2712474:/var/www/html# ping mysql
```

6.  **내 PC에서 브라우저로 http://192.168.33.10:32772** **접속**

   **방법1. CentOS 가상 머신으로 실행한 경우 ⇒ http://192.168.33.10:32772** **접속** 

![1. wordpress 웹서버 접속확인](https://github.com/nickhealthy/TIL/blob/master/2020_09_15/1.%20wordpress%20%EC%9B%B9%EC%84%9C%EB%B2%84%20%EC%A0%91%EC%86%8D%ED%99%95%EC%9D%B8.PNG)

---



### 컨테이너의 데이터를 영속적(persistent)인 데이터로 활용하는 방법

**방법1. 호스트 볼륨 공유**

* `-v` 옵션을 이용해서 호스트 볼륨을 공유
* 호스트의 디렉터리를 컨테이너의 디렉터리에 마운트
* 이미지에 원재 존재하는 디렉터리에 호스트의 볼륨을 공유하면 컨테이너의 디렉터리 자체가 덮어쓰게 됨



0. **모든 컨테이너, 이미지, 볼륨 삭제**

```bash
vagrant@xenial64:~/blog$ docker container rm -f $(docker container ls -aq)
vagrant@xenial64:~/blog$ docker image rm -f $(docker image ls -aq)
vagrant@xenial64:~/blog$ docker volume rm -f $(docker volume ls -q)
```

1.  **MySQL 이미지를 이용한 데이터베이스 컨테이너를 생성**

```bash
docker run -d --name wordpressdb_hostvolume -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -v /home/vagrant/wordpress_db:/var/lib/mysql mysql:5.7

# 도커가 자동으로 생성
/home/wordpress_db
# mysql 데이터베이스의 데이터를 저장하는 기본 디렉터리
/var/lib/mysql
```

2.  **워드프레스 이미지를 이용해 웹 서버 컨테이너를 생성**

```bash
vagrant@xenial64:~/blog$ docker run -d -e WORDPRESS_DB_PASSWORD=password --name wordpress_hostvolume --link wordpressdb_hostvolume:mysql -p 80 wordpress 
```

3. **호스트 볼륨 공유를 확인**

```bash
vagrant@xenial64:~/blog$ ls /home/vagrant/wordpress_db
```

4. **컨테이너 내부의 디렉터리를 확인 ⇒ #3에서 확인한 것과 동일**

```bash
vagrant@xenial64:/home$ docker container exec wordpressdb_hostvolume ls /var/lib/mysql
auto.cnf
```

5. **wordpressdb_hostvolume 컨테이너를 삭제한 후 호스트 볼륨을 확인** 

```bash
vagrant@xenial64:/home$ docker container rm -f wordpressdb_hostvolume

vagrant@xenial64:/home$ docker container ls -a

# 데이터 다시 확인
vagrant@xenial64:/home$ ls /home/vagrant/wordpress_db/
```

* 컨테이너는 삭제되었지만 공유되고 있던 파일(디렉터리)은 그대로 남아 있음을 확인 

* 데이터의 영속성을 부여

6. **MySQL 이미지를 이용해서 컨테이너를 실행 (기존 호스트 볼륨을 맵핑)**

```bash
vagrant@xenial64:/home$ docker run -d --name wordpressdb_hostvolume -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -v /home/vagrant/wordpress_db:/var/lib/mysql mysql:5.7

vagrant@xenial64:/home$ docker container exec wordpressdb_hostvolume ls /var/lib/mysql
```



**방법2. 볼륨 컨테이너** 

* `-v` 옵션으로 볼륨을 사용하는 컨테이너를 다른 컨테이너와 공유
* 컨테이너를 생성할 때 --volumes-from 옵셤을 설정하면 -v 또는 --volume 옵션을 적용한 컨테이너의 볼륨 디렉터리 공유가 가능



**방법3. 도커 볼륨**

* 도커 자체가 제공하는 볼륨 기능을 활용
* docker volume 명령어를 사용

