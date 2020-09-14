# Today I Learned



* 도커/쿠버네티스를 활용한 컨테이너 개발 실전 입문 [참고 URL](http://pyrasis.com/docker.html) / [참고 URL](https://www.slideshare.net/pyrasis/docker-fordummies-44424016?from_action=save)

* Docker
* 리눅스 컨테이너
* Docker 실습
* Docker 이미지 생성
* Docker 이미지로 Docker 컨테이너 생성(p36)
* 간단한 애플리케이션과 Docker 이미지 만들기(p37)
* Docker 이미지, 컨테이너 일괄 삭제(p48)
* Docker image build --pull 옵션(p51)
* docker search 명령을 이용한 리포지토리 검색
* 생성한 이미지를 도커 허브에 등록

---



### 도커/쿠버네티스를 활용한 컨테이너 개발 실전 입문 [참고 URL](http://pyrasis.com/docker.html) / [참고 URL](https://www.slideshare.net/pyrasis/docker-fordummies-44424016?from_action=save)

---



### Docker

* 도커는 **서비스 운영 환경을 묶어서 손쉽게 배포하고 실행**하는 **경량 컨테이너** 기술

1. 도커란 ?

   > 1. 복잡한 리눅스 애플리케이션을 컨테이너로 **묶어서 실행**할 수 있음
   > 2. 개발, 테스트, 서비스 환경을 **하나로 통일**하여 **효율적으로 관리**할 수 있기 때문
   > 3. 컨테이너(이미지)를 전세계 사람들과 **공유**
   > 4. 컨테이너는 **가상화보다 훨씬 가벼운 기술** 



2. 가상 머신

   > 1. 가상 머신은 **컴퓨터 안에서 컴퓨터를 만들어내기** 위한 것
   > 2. **서버 자체**를 가상 머신에 **집어넣어서** 돌림 - 각종 **서버 프로그램, DB 등을 설치**하여 애플리케이션이나 웹사이트 실행
   > 3. 미리 구축한 가상 머신 이미지를 **여러 서버에 복사하여 실행**하면 이미지 하나로 **서버를 계속 만들어낼 수 있음**
   > 4. 가상화 기술을 이용하여 **서버**를 **임대**해주는 서비스가 **클라우드 서비스**
   >    * 하드웨어 성능이 좋아져서 100개의 서버를 올릴 수 있는 서버 스펙 등을 남는 성능을 임대해주는 것



3. 가상 머신의 문제점

   > 1. 항상 대두되는 문제 
   >
   >    * **가상 머신은 완전한 컴퓨터** - 항상 게스트 OS를 설치해야 함 (일일이 다 설치를 해야하나?)
   >    *  이미지 안에 OS가 포함되기 때문에 **이미지 용량이 커짐** - **네트워크**로 가상화 이미지를 주고 받는건 **부담**
   >
   >    * 오픈소스 가상화 소프트웨어는 OS 가상화에만 주력 - **배포와 관리 기능이 부족**
   >
   >    * 가상 머신의 성능 문제가 있다 보니 **리눅스 컨테이너**가 나옴

---



### 리눅스 컨테이너

* 컨테이너 안에 **가상 공간**을 만들지만 실행 파일을 **호스트에서 직접 실행**

  ※ 리눅스 커널의 **Cgroups**와 **namespeces**가 제공하는 기술

1. 도커의 특징

   > 1. 도커는 게스트 OS를 설치하지 않음
   >    * 이미지에 서버 운영을 위한 프로그램과 라이브러리만 **격리**해서 설치
   >    * 이미지 용량이 크게 줄어듦
   >    * 호스트와 OS 자원을 공유
   > 2. 도커는 이미지 **생성**과 **배포**에 특화
   > 3. **이미지 버전 관리**도 제공하고 **중앙 저장소**에 **이미지를 올리고 받을 수 있음**(Docker Hub)
   > 4. 다양한 **API를 제공**하여 원하는 만큼 **자동화** 가능 - 개발과 서버 운영에 매우 유용



2. 도커의 성능

   > 1. 도커는 하드웨어 가상화 계층이 없음
   >    * 메모리 접근, 파일 시스템, 네트워크 전송 속도가 가상 머신에 비해 월등히 빠름
   >    * 호스트와 도커 컨테이너 사이의 성능 차이가 크지 않음



3. 도커 이미지

   > 1. 이미지는 서비스 운영에 필요한 서버 프로그램, 소스 코드, 컴파일된 실행 파일을 **묶은 형태**
   > 2. 컨테이너는 이미지를 **실행한 상태!**
   >    * 이미지는 객체, 컨테이너는 인스턴스
   >    * 이미지로 여러 개의 컨테이너를 만들 수 있음
   >    * 운영체제로 치면 **이미지는 실행 파일**이고 **컨테이너는 프로세스**



4. 도커의 이미지 처리 방식

   > 1. 도커는 이미지의 바뀐 부분을 어떻게 관리?
   >    * 유니온 파일 시스템 형식 (바뀐 부분만 관리해서 베이스와 합쳐줌)
   >    * 도커는 베이스(ubuntu 등) 이미지에서 **바뀐 부분만 이미지로 생성**
   >    * 컨테이너로 실행할 때는 베이스 이미지와 **바뀐 부분을 합쳐서 실행**
   >    * 각 이미지는 **의존 관계** 형성



5. 지금까지의 서버 환경

   > 1. 지금까지는 **물리 서버를 직접 운영** 했음
   >    * 호스팅 또는 IDC 코로케이션 서비스 사용
   > 2. 가상화가 발전하면서 **클라우드 환경으로 변화**
   >    * 코드를 통해 가능하기 때문에



6. 클라우드 환경

   > 1. 서버 세팅과 배포는 어떻게?
   >
   >    * 배포 이후 업데이트 등을 여러번 해야하는가 등의 문제
   >
   >    * **Immutable Infrastructure**라는 패러다임이 나옴
   >    * **호스트 OS와 서비스 운영 환경**(서버 프로그램, 소스코드, 컴파일 된 바이너리)을 **분리**
   >    * 서비스가 **업데이트되면** 운영 환경 자체를 변경하지 않고, **이미지를 새로 생성하여 배포**

---



### Docker 실습

1. vagrant init - 초기 설정
2. vagrant file 수정 - 가상화 환경을 코드로 쉽게 구현이 가능

```bash
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"
  config.vm.hostname = "xenial64"
  config.vm.synced_folder ".", "/vagrant_data", disabled: true
end
```

3. 가상 머신 기동 및 SSH 접속

```bash
vagrant up

vagrant ssh
```

4. 패키지 업데이트

```bash
sudo apt update
sudo apt upgrade
```

5. 도커 설치

```bash
sudo apt install -y docker.io
# 도커 그룹에 해당 사용자(vagrant)을 등록
vagrant@xenial64:~$ sudo usermod -a -G docker $USER
vagrant@xenial64:~$ sudo service docker restart
# rwx에서 rw를 모든 사용자에게 할당
vagrant@xenial64:~$ sudo chmod 666 /var/run/docker.sock
vagrant@xenial64:~$ docker --version
```

---



### Docker 이미지 생성

* 방법1. 도커 파일을 만드는 방법

* 방법2. 도커 커밋을 이용하는 방법



1. 작업 디렉토리 생성 및 이동

```bash
vagrant@xenial64:~$ mkdir chap01 && cd chap01
```

2. 쉘 스크립트 파일 생성 및 실행 권한 부여

```bash
vagrant@xenial64:~/chap01$ vi helloworld

#!/bin/sh
echo "Hello, World!"
```

3. Dockerfile 생성

   **※ 여기서 호스트는 ubuntu 가상화 환경 PC임**

```bash
vagrant@xenial64:~/chap01$ vi Dockerfile 

# 베이스 이미지
FROM  ubuntu:16.04
# 호스트 파일을 컨테이너 안으로 복사
COPY  helloworld  /usr/local/bin
# 도커 빌드 과정에서 컨테이너 안에서 실행할 명령
RUN   chmod  +x  /usr/local/bin/helloworld
# 도커 빌드를 통해 만들어진 이미지를 도커 컨테이너로 실행하기 전에 실행할 명령어
CMD   [ "helloworld" ]
```

4. Dockerfile을 사용해서 이미지를 빌드

   **※ 이미지 이름에 사용자명이 기술되어 있지 않으면 공식 이미지를 의미**

   **※ 도커는 이미지 파일을 여러개의 파일로 분리되어 관리한다.**

```bash
vagrant@xenial64:~/chap01$ docker image build -t helloworld:latest .

-t helloworld:latest : 이미지 이름을 명시 (사용자명/이미지명:태그명)
. : 도커 파일의 위치
```

5. 생성된 이미지를 조회

```bash
docker container run helloworld:latest
`Hello, World!`

docker container ps
`CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES`

docker container ps -a
```

---



### 도커 이미지로 도커 컨테이너 생성(p36)

1. docker image pull 명령으로 도커 이미지를 로컬 레포지토리로 다운로드

```bash
vagrant@xenial64:~/chap01$ docker image pull gihyodocker/echo:latest

# 로컬 레포지토리에 저장된 이미지를 조회
vagrant@xenial64:~/chap01$ docker image ls
```

2. docker container run 명령으로 컨테이너 실행

```bash
vagrant@xenial64:~/chap01$ docker container run -t -p 9000:8080 gihyodocker/echo:latest
```

3. 명령 프롬프트를 하나 더 열어서 SSH 접속

```bash
vagrant@xenial64:~$ vagrant ssh
```

4. 두번째 터미널에서 컨테이너 실행을 확인

```bash
vagrant@xenial64:~$ docker container ls
```

5. 두번째 터미널에서 curl 명령으로 컨테이너로 요청을 전송

```bash
vagrant@xenial64:~$ curl http://localhost:9000
```

6. 두번째 터미널에서 docker container stop 명령으로 컨테이너를 중지

```bash
vagrant@xenial64:~$ docker container stop 62
```

---



### 간단한 애플리케이션과 도커 이미지 만들기(p37)

1. main.go 작성 >> 8080 포트로 요청을 대기하고, 요청이 들어 왔을 때 Hello, Docker!! 라는 메시지를 반환

   ※ 동기 / 비동기

   > 1. 동기는 응답이 왔을때 응답을 처리한 후 나머지 절차를 진행
   >    * 절차가 명확하기 때문에 순서대로 진행된다.
   > 2. 비동기는 응답이 왔을때에도 이전 절차를 계속 진행한다. (call back)

```bash
vagrant@xenial64:~$ mkdir ~/chap02 && cd ~/chap02
vagrant@xenial64:~/chap02$ vi main.go

--------------------------------------------------
package main

import (
	"fmt"
	"log"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		log.Println("received request")
		fmt.Fprintf(w, "Hello Docker!!")
	})
	log.Println("start server")
	server := &http.Server{ Addr: ":8080" }
	if err := server.ListenAndServe(); err != nill {
		log.Println(err)
	}
}
```

2. Dockerfile 생성

```bash
agrant@xenial64:~/chap02$ vi Dockerfile

--------------------------------------------------
FROM   golang:1.9
RUN    mkdir   /echo
COPY   main.go   /echo
CMD   [ "go", "run", "/echo/main.go" ]
```

3. 이미지 생성

```
vagrant@xenial64:~/chap02$ docker image build -t example/echo:latest .

```

4. 이미지 생성 확인 후 컨테이너 실행

```bash
vagrant@xenial64:~/chap02$ docker image ls

vagrant@xenial64:~/chap02$ docker container run example/echo:latest
`# command-line-arguments
`/echo/main.go:16:44: undefined: nill
```

5. main.go 수정 후 이미지 재생성

```bash
vagrant@xenial64:~/chap02$ vi main.go

--------------------------------------------------
                       :
	if err := server.ListenAndServe(); err != nil {
		log.Println(err)
	}
}

vagrant@xenial64:~/chap02$ docker image build -t example/echo:latest .

vagrant@xenial64:~/chap02$ docker image ls

# -t 옵션을 안줬기 때문에 입력을 못받는다.
vagrant@xenial64:~/chap02$ docker container run example/echo:latest
```

6. -d 옵션으로 컨테이너를 백그라운드에서 실행

```bash
vagrant@xenial64:~/chap02$ docker container run -d example/echo:latest

docker container stop CONTAINER_ID
```

7. 같은 이미지로 생성된 컨테이너를 일괄적으로 중지**⇒ filter 옵션 이용**

```bash
# 컨테이너 생성에 사용된 이미지로 조회 - 모두 조회됌
vagrant@xenial64:~/chap02$ docker container ls --filter "ancestor=example/echo"
```

8. 컨테이너 조회 결과에서 **컨테이너 ID만** 추출 `-q` 옵션

```bash
vagrant@xenial64:~/chap02$ docker container ls --filter "ancestor=example/echo" -q
```

9. 동일한 이미지로 생성된 컨테이너를 일괄 삭제 - `$` 문자열로 치환

```bash
vagrant@xenial64:~/chap02$ docker container stop $(docker container ls --filter "ancestor=example/echo" -q)
```

10. `-p` 옵션을 이용해 포트 포워딩 **⇒ -p 호스트포트:컨테이너포트**

```bash
vagrant@xenial64:~/chap02$ docker container run -d -p 9090:8080 example/echo:latest

vagrant@xenial64:~/chap02$ curl localhost:9090
```

11. 호스트 포트를 생략하는 경우 **⇒ 자동으로 할당**

    **※ 대량의 컨테이너들을 운용해야 할때 주로 사용**

```bash
vagrant@xenial64:~/chap02$ docker container run -d -p 8080 example/echo:latest
` 0.0.0.0:32768->80/tcp    sleepy_keldysh`

# 확인
vagrant@xenial64:~/chap02$ docker container ls
```

---



### 도커 이미지, 컨테이너 일괄 삭제(p48)

1. 도커 컨테이너 일괄 삭제

```bash
# (실행중인것과 종료된 컨테이너 ID값들을 조회)
docker container ls -aq
vagrant@xenial64:~/chap02$ docker container rm -f $(docker container ls -aq)
```

2. 도커 이미지 일괄 삭제 

```bash
# (이미지 ID값 전체 조회)
vagrant@xenial64:~/chap02$ docker image ls -a -q 
vagrant@xenial64:~/chap02$ docker image rm -f $(docker image ls -a -q)
```

---



### Docker image build --pull 옵션(p51)

1. --pull 옵션을 사용하면 매번 베이스 이미지를 강제로 새로 받아온다.

   * pull 옵션을 사용하지 않아도 베이스 이미지가 변경되면 로컬 캐쉬 이미지가 업데이트되며, 

     pull 옵션을 사용하더라도 이미지가 변경되지 않으면 로컬 캐쉬 이미지를 사용

---



### docker search 명령을 이용한 리포지토리 검색

​	※ 인기순 정렬

* 이미지를 가져올 때 다음과 같은 순서에 해당하는 이미지 사용할 것을 권장

  > 1. 공식 배포 이미지
  > 2. Dockerfile이 공개된 이미지
  > 3. STARS를 받은 이미지

1. 상위 다섯개 검색해오기

```bash
vagrant@xenial64:~/chap02$ docker search --limit 5 mysql
```

2. [도커 허브에서 레포지토리를 검색할 수 있도록 API를 제공](https://hub.docker.com/v2/repositories/library/mysql/tags)

```bash
# library : 사용자명
# mysql : 이미지 이름
# tags : 태그 정보 조회
https://hub.docker.com/v2/repositories/library/mysql/tags

# 터미널에서 이용
# JSON 데이터를 읽기 좋게 표현 및 가공을 도와주는 프로그램
sudo apt install -y jq
# JSON 형식으로 포맷된 결과를 출력 - 읽기 좋게 변경
curl https://hub.docker.com/v2/repositories/library/mysql/tags | jq
```

3. jq를 이용해서 특정 데이터만 추출

```
vagrant@xenial64:~$ curl https://hub.docker.com/v2/repositories/library/mysql/tags | jq '.results[].name'
```

---



### 생성한 이미지를 도커 허브에 등록

1. 테스트용 베이스 이미지를 생성

```bash
vagrant@xenial64:~/chap02$ mkdir ~/basetest && cd ~/basetest

vagrant@xenial64:~/basetest$ vi Dockerfile
--------------------------------------------
FROM busybox
RUN  echo "version = 1" > /tmp/version
```

2. 이미지 생성

```bash
vagrant@xenial64:~/basetest$ docker image build -t alskadmlcraz/basetest:latest .

vagrant@xenial64:~/basetest$ docker image ls
```

3. 도커 허브에 등록

```bash
vagrant@xenial64:~/basetest$ docker login

vagrant@xenial64:~/basetest$ docker image push alskadmlcraz/basetest:latest
```

4. 테스트용 이미지(basetest)를 이용해서 이미지를 생성

```bash
agrant@xenial64:~/basetest$ mkdir ~/pulltest && cd ~/pulltest

vagrant@xenial64:~/pulltest$ vi Dockerfile

------------------------------------------------
FROM  myanjini/basetest

RUN   cat /tmp/version
RUN   cat /tmp/version

# 이미지 생성 - 동일한 조건으로 이미지를 만들면 생성되지 않는 것을 확인
vagrant@xenial64:~/pulltest$ docker image build -t myanjini/pulltest:latest .
```

5. 테스트용 베이스 이미지를 업그레이드

```bash
vagrant@xenial64:~/pulltest$ cd ~/basetest
vagrant@xenial64:~/basetest$ vi Dockerfile

FROM busybox
RUN  echo `"version = 2"` > /tmp/version
```

6. 테스트용 베이스 이미지를 이용한 이미지를 생성

```bash
vagrant@xenial64:~/pulltest$ docker image build -t myanjini/pulltest:latest .

`myanjini/pulltest   latest              8caa020e4267        28 seconds ago      1.23MB
`myanjini/basetest   latest              1532baf215f4        2 minutes ago       1.23MB
# none파일 - 댕글링 되었다. 즉, 업데이트 되어 이전 파일은 사라지고 새로운 이미지 파일로 생성
`<none>              <none>              24e041e03114        5 minutes ago       1.23MB
```







* 금요일: 리눅스, 데브옵스, 도커, 쿠버네티스

* 도커 컨테이너에 프로젝트 올려서 배포