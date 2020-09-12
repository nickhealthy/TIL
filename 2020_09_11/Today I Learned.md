# Today I Learned



* [Dorker Hub](https://hub.docker.com/)
* [도커 사용법 도움말 참조](http://pyrasis.com/docker.html)

* Docker (p 145)
* Dockerfile을 이용해서 이미지를 생성 (p166)
* Docker Compose (p 170)
* Jenkins (p 180)
* Jenkinds에서 Ansible을 연동 및 실행
* Jenkins에서 Serverspec을 실행
* exec-ansible 프로젝트 실행 후 이어서 exec-serverspec 프로젝트가 실행되도록 수정

---



### Docker

​	※ DevOps page 145 ~

​	※ 쉬운 배포 가능(하나의 파일로 만듬 - OS 미설치) <> 가상화 환경구축(가상화 머신은 OS설치)

* Docker 설치

```bash
[root@demo ~]# yum install -y docker
```

* Docker 서비스 기동

```bash
[root@demo ~]# systemctl start docker.service
[root@demo ~]# docker version
```

* Docker Hub 로그인

```bash
[root@demo ~]# docker login
Username: DOCKER_HUB_ID
Password: DOCKER_HUB_PASSWORD
```

* Docker 이미지 검색

  ※ 이미지 저장소(Docker Hub)에 있는 이미지를 보여줌

  ※ `STARS`이 높은 순으로 보여줌

```bash
[root@demo ~]# docker search centos
```

* Docker 이미지 가져오기 및 확인

```bash
[root@demo ~]# docker pull docker.io/centos

# 명령어 명령어 명령어 = 명령어 3개
[root@demo ~]# docker image ls
```

* 태그를 지정해서 이미지 획득

```bash
# 콜론(:) 뒤에 tag명
[root@demo ~]# docker pull docker.io/centos:centos7
```

* 컨테이너 실행 및 확인

```bash
# -t : 입력을 받을 수 있음
# -d : 백그라운드 프로세스로 돌림
# --name : 컨테이너를 사용할 이름 명시
[root@demo ~]# docker run -t -d --name centos7 docker.io/centos:centos7

# ls > ps로 해도 동일
[root@demo ~]# docker container ls
```

* 컨테이너 내부에 명령어 실행

```bash
# exec 실행중인 centos7이미지 파일에 대해 cat/etc/redhat-realease을 보여주세요
[root@demo ~]# docker exec centos7 cat /etc/redhat-release
`CentOS Linux release 7.8.2003 (Core)

# -i 인터렉션(서로 교환 - vagrant_centos7)
# -t 입력을 받을 수 있음
[root@demo ~]# docker exec -it centos7 /bin/bash

# 실행 중인 컨테이너 내부로 진입
[root@61260c982277 /]# cat /etc/redhat-release
`CentOS Linux release 7.8.2003 (Core)
```

* 우분투 이미지를 이용해 컨테이너를 실행

```bash
[root@demo ~]# docker run --name ubuntu -td docker.io/ubuntu

# 로컬 레포지터리에 이미지가 존재하지 않기 때문에 먼저 이미지를 가져와서 실행
`latest: Pulling from docker.io/library/ubuntu
`54ee1f796a1e: Pull complete
`f7bfea53ad12: Pull complete
`46d371e02073: Pull complete
`b66c17bbf772: Pull complete    

[root@demo ~]# docker exec -it ubuntu /bin/bash
`root@dcfde3761f4f:/# ls
```

* 컨테이너 정지 / 재기동

```
# centos7 부분에 컨테이너 이름 또는 ID
[root@demo ~]# docker container stop centos7
```

* 컨테이너 삭제

  ※ 이미지까지 삭제된건 아님

```bash
docker container rm -f centos7

[root@demo ~]# docker image ls
```

* nginx 컨테이너 기동

```bash
# 공식 배포 이미지는 docker.io를 생략 가능
[root@demo ~]# docker pull nginx	

# -p 8000:80  : 호스트 pc 8000번 포트를 컨테이너 내부의 80번 포트를 연결
[root@demo ~]# docker container run -d -p 8000:80 --name nginx-latest nginx

# 컨테이너 ID
`a5dc2917fe72dd0a82b37eae85f9047447f450b550aa0677ba5bff34d185c376`

# localhost:8000 : nginx 컨테이너의 80번 포트로 맵핑 / Vagrant로 생성한 centOS
[root@demo ~]# curl http://localhost:8000
```

* 컨테이너 표준 출력 로그 확인

```bash
docker container logs -f nginx-latest
```

* [CentOS Dockerfile](https://github.com/CentOS/sig-cloud-instance-images/blob/f2788ce41161326a18420913b0195d1c6cfa1581/docker/Dockerfile)

```bash
# 베이스 이미지를 지정 / scratch - 컨테이너 이미지를 처음부터 생성
FROM scratch

# 이미지에 파일을 추가 (ADD: 압축 파일을 풀어서 배치, COPY: 이미지를 생성할 호스트의 파일을 컨테이너 이미지 내부로 복사)
ADD centos-7-x86_64-docker.tar.xz /

# 컨테이너가 기동될 때 실행할 default 프로세스를 지정
CMD ["/bin/bash"]	
```

![image-20200911105536631](C:\Users\Nick_주성우\AppData\Roaming\Typora\typora-user-images\image-20200911105536631.png)

* [Nginx Dockerfile](https://github.com/nginxinc/docker-nginx/blob/9774b522d4661effea57a1fbf64c883e699ac3ec/mainline/buster/Dockerfile)

```bash
# 베이스 이미지를 지정
FROM debian:buster-slim			
	:
# 이미지를 생성할 호스트의 파일을 컨테이너 이미지 내부로 복사	
COPY docker-entrypoint.sh /			
COPY 10-listen-on-ipv6-by-default.sh /docker-entrypoint.d
COPY 20-envsubst-on-templates.sh /docker-entrypoint.d
ENTRYPOINT ["/docker-entrypoint.sh"]

# 호스트와 연결할 포트 번호를 설정
EXPOSE 80					
```

---



### Dockerfile을 이용해서 이미지를 생성 (p166 ~)

​	※ DevOps page 166 ~

1. 컨테이너 이미지 내부로 전달할 파일을 생성

```bash
[root@demo ~]# echo "Hello, Docker." > hello-docker.txt
[root@demo ~]# vi Dockerfile
```

2. 방법1. Dockerfile을 생성 ⇒ 이미지 생성에 사용

```bash
FROM docker.io/centos:latest        ⇐ 베이스 이미지를 지정
ADD  hello-docker.txt /tmp          ⇐ 호스트에 있는 hello-docker.txt 파일을 컨테이너 이미지의 /tmp 아래로 복사
RUN  yum install -y epel-release    ⇐ 컨테이너 이미지를 만들 때 실행
CMD  [ "/bin/bash" ]                ⇐ 컨테이너가 실행될 때 실행할 명령
```

3. Dockerfile을 이용해서 이미지를 생성

```bash
[root@demo ~]# docker image build -t alskadmlcraz/centos:1.0 .

`docker image build : Dockerfile을 이용해서 이미지를 생성
`-t alskadmlcraz/centos:1.0 : 이미지 이름 ⇒ DOCKER_HUB_ID/IMAGE_NAME:TAG_NAME
`. : Dockerfile 위치 ( . ⇒ 현재 위치)
```

4. 생성한 이미지를 이용해서 컨테이너를 실행

```bash
[root@demo ~]# docker container run -td --name devops-book-1.0 alskadmlcraz/centos:1.0
`8a5c1f1e7cc2a26cbb727155070bc23154292e8783b0895a5f4a22cc1ab54f54	⇐ 컨테이너 ID
```

5. 컨테이너 내부로 진입

```bash
[root@demo ~]# docker exec -it devops-book-1.0 /bin/bash

[root@aa9eab5ad4c1 /]# cat /tmp/hello-docker.txt	⇐ 이미지 생성 중 파일 복사되었는지 확인
[root@aa9eab5ad4c1 /]# rpm -qa | grep epel			⇐ epel 패키지 설치 여부를 확인
```

6. 방법2. 컨테이너 내용을 변경 후 변경된 내용을 이미지로 생성

```bash
epel-release : 패키지 버전 관리

[root@aa9eab5ad4c1 /]# yum install -y epel-release	⇐  Dockerfile에서 설치하지 않은 경우
[root@aa9eab5ad4c1 /]# yum install -y nginx 	⇐ 해당하는 컨테이너 내부에 nginx를 설치

[root@demo ~]# docker container commit devops-book-1.0 alskadmlcraz/centos:1.1
`docker container commit : 컨테이너의 현재 상태를 이미지로 기록(생성)`
`devops-book-1.0 : 컨테이너 이름`
`alskadmlcraz/centos:1.1 : 이미지 이름`
```

7. 이미지를 Docker Hub 이미지를 등록

```bash
[root@demo ~]# docker image push alskadmlcraz/centos:1.1
```

8. Docker Hub Repositories에서 확인 가능

9. 도커 허브에 등록된 이미지를 이용해서 컨테이너를 실행

```bash
[root@demo ~]# docker run --name alskadmlcraz_centos -dt -p 8888:80 alskadmlcraz/cento
s:1.1
`7644ba968f48b4b4d4789883fc1b3c3771b657cbbdb1714214c8d0e5b5b026ce+ ⇐ 컨테이너 ID
```

10. 모든 컨테이너를 삭제

```bash
# $(docker container ls -aq) = $(문자열로 치환 해준다.)
[root@demo ~]# docker container rm -f $(docker container ls -aq)
```

---



### Docker Compose (p 170)

​	※ DevOps page 170 ~

​	※ 인프라(서버)를 구성하는 각각의 컨테니어들을 일괄되게 운용할 수 있다.

1. Docker Composer 설치

```bash
[root@demo ~]# curl -L https://github.com/docker/compose/releases/download/1.8.0/docker-compose-`uname -s`-`uname -m` > /usr/bin/docker-compose

[root@demo ~]# uname -s
`Linux

[root@demo ~]# uname -m
`x86_64

[root@demo ~]# chmod +x /usr/bin/docker-compose

[root@demo ~]# docker-compose --version
`docker-compose version 1.8.0, build f3628c7
```

2. Docker-compose.yml 파일을 생성

   ※ 세가지의 컨테이너를 구동할 수 있다.

```bash
[root@demo ~]# vi docker-compose.yml

db:									   ⇒ 컨테이너
  image: docker.io/mysql               ⇒ 컨테이너 이미지
  ports:
    - "3306:3306"                      ⇒ 호스트 포트 : 컨테이너 내부 포트(서비스 포트)
  environment:                         ⇒ 해당하는 컨테이너 내부의 환경 변수
    - MYSQL_ROOT_PASSWORD=password

app:
  image: docker.io/tomcat
  ports:
    - "9090:8080"

web:
  image: docker.io/nginx
  ports:
    - "9000:80"

```

3. 컨테이너 생성 및 확인

```bash
[root@demo ~]# docker-compose up -d
`Creating root_db_1
`Creating root_web_1
`Creating root_app_1

# 컨테이너가 3개를 만들어진것을 확인
[root@demo ~]# docker container ls
```

4. 컨테이너를 중지 및 삭제

```bash
[root@demo ~]# docker-compose down
```



목표 : 동일한 인프라 환경을 구성 가능하게 한다. 

vagrant : 가상화 환경을 쉽게 할 수 있다. / 단점 : 표준화 구성이 불가능하다.

vagrant_ansible : 표준화로 구축이 가능하다.

vagrant_ansible_serverspec : 테스트 및 체크 할 수 있다. (실행파일: .rb 형식의 파일) - 자동으로 만들어줌

> 단점 : 하지만 실질적인 테스트는 ansible끼리 연동해서 테스트를 할 수 없었다.
>
> DevOps의 가장 중요한 점이 **자동화**
>
> 사람이 많이 개입하면 안좋음

자동적으로 처리될 수 있는 작업  `Jenkins` 도구 사용



* Docker

> 중간에 나온 이유는 컨테이너 기반으로 인프라를 구성하니까 편하다.
>
> 컨테이너를 이용한 배포도 가능

---



### Jenkins (p 180)

​	※ [공식 설치 문서(CentOS 환경)](https://pkg.jenkins.io/redhat-stable/)

​	※ DevOps 180 ~

​	※ Jenkins는 Build Pipeline 도구 중 하나임

> 1. 작업을 프로젝트라는 단위로 모아서 쉽게 실행할 수 있다.
> 2. 수작업을 할 필요가 없기 때문에, 안전하고 확실하게 실핼할 수 있다.
> 3. 프로젝트 실행과 결과에 대한 이력의 목록화가 가능하다.



1. JDK 설치

   ​	※ Java 기반으로 만들어졌기때문에 `JDK` 를 먼저 설치

```bash
[root@demo ~]# yum -y install java-1.8.0-openjdk java-1.8.0-openjdk-devel
```

2. Jenkins 설치

```bash
[root@demo ~]# yum -y install http://mirrors.jenkins-ci.org/redhat-stable/jenkins-2.235.5-1.1.noarch.rpm
```

3. Jenkins 기동

```bash
[root@demo ~]# systemctl start jenkins.service
```

4. Jenkins 접속 확인

```bash
호스트 PC에서 브라우저를 이용해서 http://192.168.33.10:8080/ 로 접속

접속에 필요한 패스워드를 확인(/var/lib/jenkins/secrets/initialAdminPassword) 후 접속
[root@demo ~]# cat /var/lib/jenkins/secrets/initialAdminPassword
`19b503af1ea845e39b68f48627e1f85a	⇐ 패스워드는 호스트 마다 다릅니다.
```

※ 접속되지 않는 경우 방화벽에 서비스 포트를 등록(허용)

```bash
[root@demo ~]# firewall-cmd --zone=public --permanent --add-port=8080/tcp
firewall-cmd --reload
```

5. Jenkins 최초 설정

```bash
#5-1 Install suggested plugins 클릭
#5-2 설치를 진행
#5-3 관리자 계정 설정 
#5-4 재시작
```

6. 프로젝트를 생성 (업무단위로 나눠놓은 것)

![2. jenkis](C:\dev\TIL\2020_09_11\2. jenkis.PNG)

![3. jenkis](C:\dev\TIL\2020_09_11\3. jenkis.PNG)

7. 프로젝트 실행(build now)

![4. 프로젝트 실행](C:\dev\TIL\2020_09_11\4. 프로젝트 실행.PNG)



![5. 프로젝트 결과 일람](C:\dev\TIL\2020_09_11\5. 프로젝트 결과 일람.PNG)

![image-20200911152643820](C:\Users\Nick_주성우\AppData\Roaming\Typora\typora-user-images\image-20200911152643820.png)

![6. 실행되고 있는것을 확인](C:\dev\TIL\2020_09_11\6. 실행되고 있는것을 확인.PNG)

---



### Jenkinds에서 Ansible을 연동 및 실행

1. 연습할 샘플 코드 git clone

```bash
[vagrant@demo ~]$ cd
[vagrant@demo ~]$ cd /tmp/ `vagrant로 작업 공간을 옮김`

[vagrant@demo tmp]$ git clone https://github.com/devops-book/ansible-playbook-sample.git
```

2. Jenkins 사용자가 패스워드 없이 sudo 명령을 실행할 수 있도록 권한을 추가

```bash
# Jenkins에 sudo 권한 추가
[vagrant@demo tmp]$ sudo vi /etc/sudoers.d/jenkins

jenkins ALL=(ALL) NOPASSWD:ALL
```

3. exec-ansible 프로젝트 생성

![7. exec-ansible 프로젝트 생성](C:\dev\TIL\2020_09_11\7. exec-ansible 프로젝트 생성.PNG)

![7-1](C:\dev\TIL\2020_09_11\7-1.PNG)

![7-2](C:\dev\TIL\2020_09_11\7-2.PNG)

![7-3](C:\dev\TIL\2020_09_11\7-3.PNG)

---



### Jenkins에서 Serverspec을 실행

![8. Jenkins에서 Serverspec을 실행](C:\dev\TIL\2020_09_11\8. Jenkins에서 Serverspec을 실행.PNG)



![8-1](C:\dev\TIL\2020_09_11\8-1.PNG)



![8-2](C:\dev\TIL\2020_09_11\8-2.PNG)

![8-3](C:\dev\TIL\2020_09_11\8-3.PNG)



1. **ansible에서 구축한것을 serverspec에서 만들어서 테스트 하였다.**

2. **Jenkis를 이용하여 ansible과 serverspec 연동해서 구축과 테스트를 같이하였다.**

---



### exec-ansible 프로젝트 실행 후 이어서 exec-serverspec 프로젝트가 실행되도록 수정

![9.exec-ansible 프로젝트 실행 후 이어서 exec-serverspec 프로젝트가 실행되도록 수정](C:\dev\TIL\2020_09_11\9.exec-ansible 프로젝트 실행 후 이어서 exec-serverspec 프로젝트가 실행되도록 수정.PNG)



* 하위 프로젝트 생성

![image-20200911162513177](C:\Users\Nick_주성우\AppData\Roaming\Typora\typora-user-images\image-20200911162513177.png)

---



### Pipeline으로 프로젝트 연결

​	※ 위에 처럼 일일이 그 다음 작업 그 다음 작업을 연결해줘야 하기 때문에 불편함(수동적)



1. exec-ansible에서 후행 프로젝트로 exec-serverspec을 설정해 둔 것을 해제

![10. exec-ansible에서 후행 프로젝트로 exec-serverspec을 설정해 둔 것을 해제](C:\dev\TIL\2020_09_11\10. exec-ansible에서 후행 프로젝트로 exec-serverspec을 설정해 둔 것을 해제.PNG)



2. Pipeline 프로젝트 생성

![10-1](C:\dev\TIL\2020_09_11\10-1.PNG)

![10-2](C:\dev\TIL\2020_09_11\10-2.PNG)

**stage: 단계별로 수행할 스텝**

**build: 어떤일을 해야하는지 기술**

![10-3](C:\dev\TIL\2020_09_11\10-3.PNG)



---



### 파라미터를 받아서 빌드하도록 수정 

※ developer과 production 두개의 인벤토리를 동시에 수행하고 싶을때

1. exec-ansible 매개변수 추가

![11. 파라미터를 받아서 빌드하도록 수정](C:\dev\TIL\2020_09_11\11. 파라미터를 받아서 빌드하도록 수정.PNG)

2. ansible 빌드하기

![11-1](C:\dev\TIL\2020_09_11\11-1.PNG)

3. exec-ansible-Serverspec 매개변수 추가

![11-2](C:\dev\TIL\2020_09_11\11-2.PNG)



![11-3](C:\dev\TIL\2020_09_11\11-3.PNG)

node {  

stage 'ansible'  build job: 'exec-ansible', parameters: [[$class: 'StringParameterValue', name: 'ENVIRONMENT', value: "${ENVIRONMENT}"]]  

stage 'serverspec' 

build 'exec-serverspec'

}

![11-4](C:\dev\TIL\2020_09_11\11-4.PNG)

※ 원인은? 

**테스트에 사용된 테스트 케이스 파일을 확인**

```bash
[vagrant@demo ~]$ cat /tmp/serverspec_sample/spec/localhost/web_spec.rb

require 'spec_helper'

describe package('nginx') do
  it { should be_installed }
end

describe service('nginx') do
  it { should be_enabled }
  it { should be_running }
end

describe port(80) do
  it { should be_listening }
end

describe file('/usr/share/nginx/html/index.html') do
  it { should be_file }
  it { should exist }
  its(:content) { `should match /^Hello, development ansible!!$/ }`
end
```

**테스트 케이스를 만들 때 사용하는 템플릿을 확인**

```bash
[vagrant@demo ~]$ cat /tmp/ansible-playbook-sample/roles/serverspec_sample/templates/web_spec.rb.j2

require 'spec_helper'

describe package('nginx') do
  it { should be_installed }
end

describe service('nginx') do
  it { should be_enabled }
  it { should be_running }
end

describe port(80) do
  it { should be_listening }
end

describe file('/usr/share/nginx/html/index.html') do
  it { should be_file }
  it { should exist }
  its(:content) { should match /^Hello, `{{ env }}` ansible!!$/ }
end
```

**ansible 롤을 정의하고 있는 site.yml 파일을 확인**

```bash
[vagrant@demo ~]$ cat /tmp/ansible-playbook-sample/site.yml

---
- hosts: webservers
  become: yes
  connection: local
  roles:
    - common
    - nginx
#    - serverspec         ⇐ serverspec 설치
#    - serverspec_sample  ⇐ serverspec 에서 사용할 테스트 케이스를 만들어서 /tmp/serverspec_sample 디렉터리로 복사
#    - jenkins	    
```

**ENVIRONMENT 파라미터에 따라 테스트 케이스 파일이 생성되도록 하기 위해서는 site.yml 파일에 serverspec_sample 롤의 주석을 해제**

**exec-ansible-serverspec 프로젝트를 실행 (ENVIRONMENT로 production을 선택)**

![11-5.통과](C:\dev\TIL\2020_09_11\11-5.통과.PNG)

※ 이후 테스트 통과



### 숙제

Jenkins의 **exec-ansible-serverspec 프로젝트**에서 **ENVIRONMENT 파라미터를 production으로 설정** 후 빌드한 결과 화면을 스크린 샷을 떠서 아래 주소에 등록

[URL](https://docs.google.com/spreadsheets/d/1yJQYo2yE5TBMHRM5CaJ_XvKryVVyk1Iaro1XXPx1LrU/edit?usp=sharing)

