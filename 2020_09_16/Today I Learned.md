# Today I Learned



* 알고리즘 [이진 트리]
* 볼륨 컨테이너
* 동일한 컨테이너 이름을 중복 사용할 수 없음

---



### 알고리즘 [이진 트리]

* 바이너리 서치 Big O: O(lon N)
  * bubble sort, selection sort는 O(n^2)
* 정렬된 자료를 반으로 나누어 탐색함
* 데이터는 반드시 오름차순으로 정렬된 자료형!
* 



---



### 볼륨 컨테이너

#### **방법2. 볼륨 컨테이너** 

* `-v` 옵션으로 볼륨을 사용하는 컨테이너를 다른 컨테이너와 공유하는 것
* 컨테이너를 생성할 때 **--volumes-from 옵션**을 설정하면 `-v` 또는 `--volume 옵션`을 적용한 컨테이너의 볼륨 디렉터리 공유가 가능

1. **-v 옵션으로 볼륨을 사용하는 컨테이너를 확인**

```bash
vagrant@xenial64:~$ docker container ls

# wordpressdb_hostvolume 없는 경우 아래 명령어로 컨테이너 생성
vagrant@xenial64:~/blog$ docker run -d --name wordpressdb_hostvolume -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -v /home/vagrant/wordpress_db:/var/lib/mysql mysql:5.7
```

2. **호스트와 컨테이너 볼륨 공유를 확인**

```bash
# 호스트의 디렉터리
vagrant@xenial64:~$ ls ~/wordpress_db/	

# 컨테이너 내부
vagrant@xenial64:~$ docker container exec -it wordpressdb_hostvolume ls /var/lib/mysql
```

3. **호스트와 볼륨을 공유하는 컨테이너를 공유하는 컨테이너를 생성**

```bash
vagrant@xenial64:~$ docker run -it --name volume_from_container --volumes-from wordpressdb_hostvolume ubuntu:14.04
root@d6ec70aa8c04:/# ls /var/lib/mysql
```



#### **방법3. 도커 볼륨**

* 도커 자체가 제공하는 볼륨 기능을 활용
* docker volume 명령어를 사용

1. **볼륨 생성**

```bash
vagrant@xenial64:~$ docker volume create --name myvolume

vagrant@xenial64:~$ docker volume ls
`local               myvolume
```

2. **생성한 볼륨을 이용해서 컨테이너를 생성**

```bash
vagrant@xenial64:~$ docker run -it --name myvolume1 -v myvolume:/root/ ubuntu:14.04

root@0bf6ac067a56:/# cd root
root@0bf6ac067a56:~# echo hello, volume >> /root/volume
root@0bf6ac067a56:~# ls
volume
root@0bf6ac067a56:~# exit
exit
```

3. **동일 볼륨을 사용하는 컨테이너를 생성해서 공유 여부를 확인**

```bash
vagrant@xenial64:~$ docker run -it --name myvolume2 -v myvolume:/temp/ ubuntu:14.04
root@bc6bc0c35bfc:/# cat /temp/volume
`hello, volume
```

4. **docker inspect 명령으로 볼륨의 저장 위치를 확인**

```bash
vagrant@xenial64:~$ docker inspect --type volume myvolume
"Mountpoint": "/var/lib/docker/volumes/myvolume/_data",
"Name": "myvolume",
```

---



### **동일한 컨테이너 이름을 중복 사용할 수 없음**

​	※ 동일한 이름의 컨테이너가 존재하는 경우, 기존 컨테이너를 삭제해야 컨테이너를 생성할 수 있음

```bash
vagrant@xenial64:~$ docker container run --name echo -itd -p 8888:8080 myanjini/echo:latest /bin/bash

vagrant@xenial64:~$ docker container rm -f echo
vagrant@xenial64:~$ docker container run --name echo -itd -p 8889:8080 myanjini/echo:latest /bin/bash
```

#### **기존에 생성된 컨테이너가 있으면 삭제하고, 새롭게 컨테이너를 생성**

```bash
vagrant@xenial64:~$ docker container rm -f echo ; docker container run --name echo -itd -p 8889:8080 myanjini/echo:latest /bin/bash
```

#### **Quiz. 아래 요건을 만족하는 쉘 스크립트를 작성하시오.**

1. run.sh 스크립트를 작성합니다. 
2. run.sh 스크립트는 실행할 컨테이너의 이름을 파라미터로 입력받습니다. 
   * 예 ⇒ $ run.sh echo
3. 동일한 이름의 컨테이너가 존재하면 해당 컨테이너를 삭제하고 컨테이너를 실행(생성)합니다. 
4. 컨테이너 생성 스크립트는 다음과 같은 형식입니다.
   * docker container run --name 컨테이너이름 -itd -p 8888:8080 myanjini/echo:latest /bin/bash

```bash
#!/bin/bash

#1 명령어 형식 체크
if [ $# == 0 ]
then
  echo 명령어 형식이 잘못되었습니다.
  echo [ 사용법 ] ./run.sh container_name
  exit 1
fi

#2 동일 이름의 컨테이너가 존재하는지 조회
cid=$(docker container ps -a --filter "name=$1" -q)

#3 동일 이름의 컨테이너가 존재하면 해당
if [ -n cid ]
then
  docker container rm -f $cid
  echo $1 이름의 컨테이너\($cid\)를 삭제했습니다.
fi

#4 컨테이너를 실행
docker container run --name $1 -itd -p 8888:8000 mysql /bin/bash

#5 쉘 종료
exit 0
```

---



### 도커 컴포즈

* **도커 컨테이너 파일들을 여러개로 묶고 필요에 따라 여러개를 증설, 삭제할 수 있음 (Scale)**

```bash
vagrant@xenial64:~/compose$ docker-compose scale db=4 wordpress=5

vagrant@xenial64:~/compose$ docker-compose scale db=1 wordpress=1
```

* 이전까지는 단일 컨테이너만 관리했지만 컨테이너들을 마스터/슬레이브 노드로 구성해(종속적) 통합 관리

* 마스터 컨테이너에 슬레이브 컨테이너들을 설정

[설치](https://docs.docker.com/compose/install/)

```bash
vagrant@xenial64:~$ sudo curl -L "https://github.com/docker/compose/releases/download/1.27.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

vagrant@xenial64:~$ sudo chmod +x /usr/local/bin/docker-compose
vagrant@xenial64:~$ docker-compose version
```

1. **docker-compose 명령으로 컨테이너를 실행**

```bash
vagrant@xenial64:~$ mkdir ~/compose && cd ~/compose

vagrant@xenial64:~/compose$ vi docker-compose.yml

 # 문법 버전
version: "3"                         
services:  
# 컨테이너 이름
  echo:     
# 컨테이너 생성에 사용할 도커 이미지
    image: myanjini/echo:latest
    ports:
# 포트 포워딩 ⇒ 호스트:컨테이너
      - 9000:8080
```

2. **컨테이너 실행**

```bash
vagrant@xenial64:~/compose$ docker-compose up
```

3. **다른 터미널에서 컨테이너 생성 여부를 확인**

```bash
vagrant@xenial64:~$ docker container ls

`CONTAINER ID        IMAGE                  COMMAND                  CREATED              STATUS              PORTS                    NAMES
`edda7a452f20        myanjini/echo:latest   "go run /echo/main.go"   About a minute ago   Up About a minute   0.0.0.0:9000->8080/tcp   compose_echo_1
```

4. **컨테이너 중지 (중지와 함께 삭제도 수행)**

```bash
vagrant@xenial64:~/compose$ docker-compose down
```

---



### 이미지를 만들고 컨테이너를 실행

1. **이전에 만들어 놓은 Dockerfile, main.go 파일을 작업 디렉터리로 복사**

```bash
vagrant@xenial64:~/compose$ cp ../chap02/Dockerfile ./
vagrant@xenial64:~/compose$ cp ../chap02/main.go ./
vagrant@xenial64:~/compose$ ls
`docker-compose.yml  Dockerfile  main.go
```

2. **Dockerfile을 이용해서 이미지를 빌드 후 실행도록 docker-compose.yml 파일을 수정**

```bash
vagrant@xenial64:~/compose$ vi docker-compose.yml

version: "3"
services:
  echo:
    #image: myanjini/echo:latest
    build: .
    ports:
      - 9000:8080
      
vagrant@xenial64:~/compose$ docker-compose up -d --build
# --build 옵션을 추가했기 때문에 이미지를 빌드(생성)
# 도커 파일을(Dockerfile) 빌드한다.
`Building echo						
# -d 옵션을 추가했기 때문에 명령어 입력이 가능 (백그라운드 실행)
vagrant@xenial64:~/`compose$``

vagrant@xenial64:~/compose$ docker image ls
`compose_echo        latest              783ef639299e        About a minute ago   750MB`
vagrant@xenial64:~/compose$ docker container ls
`6cb17d61c6f1        compose_echo           "go run /echo/main.go"   4 minutes ago       Up 4 minutes        0.0.0.0:9000->8080/tcp   compose_echo_1`
```

---



### **젠킨스 컨테이너 실행** ＊ 보류

1. **docker-compose.yml 파일 작성**

```bash
vagrant@xenial64:~/compose$ vi docker-compose.yml

version: "3"
services:
  master:
    container_name: master
    image: jenkinsci/jenkins:2.142-slim
    ports:
      - 8080:8080
#    volumes:
#      - ./jenkins_home:/var/jenkins_home
```

2. **컨테이너 실행**

```bash
vagrant@xenial64:~/compose$ docker-compose up
# 초기 관리자 패스워드
# 초기에 설치할 때 log상에 보임
`master    | 0ff8e9cb2798460f888ca6ff23c94e73			
# 컨테이너 실행 상태
`master    | INFO: Finished Download metadata. 21,138 ms			
```

3. **젠킨스 초기 설정 및 설치**
4. **마스터 젠킨스 용 SSH 키 생성**
   * 마스터 컨테이너에서 `rsa`알고리즘으로 ssh-keygen을 만듬
   * **공개키**를 읽어들임
   * 이 키를 입력해줘야 **슬레이브 노드가 마스터 노드에 접근 가능**

```bash
vagrant@xenial64:~/compose$ docker container exec -it master ssh-keygen -t rsa

# 공개 키
`Your identification has been saved in /var/jenkins_home/.ssh/id_rsa.
# 개인 키
`Your public key has been saved in /var/jenkins_home/.ssh/id_rsa.pub.

vagrant@xenial64:~/compose$ docker container exec master cat /var/jenkins_home/.ssh/id_rsa.pub
```

5. **슬레이브 젠킨스 컨테이너를 생성**

```bash
vagrant@xenial64:~/compose$ vi docker-compose.yml

version: "3"
services:
  master:
    container_name: master
    image: jenkinsci/jenkins
    ports:
      - 8080:8080
    links:
      - slave01
#    volumes:
#      - ./jenkins_home:/var/jenkins_home
  slave01:
    container_name: slave01
    image: jenkinsci/ssh-slave
    environment:
      - JENKINS_SLAVE_SSH_PUBKEY= 위에서 공개키 검색하고 입력
```

---



### **docker-compose를 이용해서 MySQL과 Wordpress를 연동**

* 기존 컨테이너, 이미지, 볼륨 등을 모두 삭제

```bash
vagrant@xenial64:~/compose$ docker container rm -f $(docker container ls -aq)
vagrant@xenial64:~/compose$ docker image rm -f $(docker image ls -aq)
vagrant@xenial64:~/compose$ docker volume rm -f $(docker volume ls -q)
```

1. **docker-compose.yml 파일을 생성**

```bash
version: "3.3"

services:
# 컨테이너 이름
  db:
    image: library/mysql:5.7
    volumes:
      - /home/vagrant/db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
# 컨테이너 이름
  wordpress:
  # 컨테이너 사이의 관계를 나태냄
  # db 컨테이너가 먼저 띄어져 있어야 실행 가능하다
    depends_on:
      - db
    image: library/wordpress:latest
    ports:
    # VirtualBox에서 설정한 호스트 포트는 호스트PC에서 가상머신을 진입할 때 쓰는 포트
    # 우분투(가상머신)에서 컨테이너로 연결하는 포트
      - "80:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}

vagrant@xenial64:~/compose$ docker-compose up -d
# 실행확인
vagrant@xenial64:~/compose$ docker-compose ps
```

* 이후 VirtualBox에서 포트포워딩 설정

![1. 도커컴포즈_포트포워딩](C:\dev\TIL\2020_09_16\1. 도커컴포즈_포트포워딩.PNG)

---



### docker-compose scale

1. **docker-compose scale**

* 호스트 포트가 고정되면 동일한 컨테이너 생성이 불가

2. **docker-compose.yml 파일을 생성**

```bash
version: "3.3"

services:
  db:
    image: library/mysql:5.7
    volumes:
      - /home/vagrant/db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress

  wordpress:
    depends_on:
      - db
    image: library/wordpress:latest
    ports:
      - "80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
```

3. **docker-compose scale실행**

```bash
vagrant@xenial64:~/compose$ docker-compose up -d

vagrant@xenial64:~/compose$ docker-compose scale db=2 wordpress=2
vagrant@xenial64:~/compose$ docker-compose scale db=4 wordpress=5

# 컨테이너 실행 시 스케일 조정
vagrant@xenial64:~/compose$ docker-compose up -d --scale db=2		
```

---



### **도커 스웜 클러스터를 구성** (dind는 안함)

* 정기적으로 작업을 해야하는 것을 한번에



##### 도커 스웜 모드의 구조

* 매니저(manager) 노드와 워커(worker) 노드로 구성
* 워커 노드 ⇒ 실제 컨테이너가 생성되고 관리되는 도커 서버
* 매니저 노드 ⇒ 워커 노드를 관리하기 위한 도커 서버
* 매니저 노드는 워커 노드의 역할을 포함
* 클러스터를 구성하기 위해서는 최소 1개 이상의 매니저 노드가 존재해야 함
  * 매니저 노드 하나로 워커 노드까지 구현 가능함 (권장하는 방식은 x)

##### 준비작업

* 모든 가상머진을 중지

* 아래 3개 폴더 아래에 Vagrantfile을 생성

  ```vagrant
  C:\myCloudInfra\swarm-manager
  
  # -*- mode: ruby -*-
  # vi: set ft=ruby :
  
  Vagrant.configure("2") do |config|
  # config.vm.box = "centos/7"
    config.vm.box = "generic/centos7"
    config.vm.hostname = "swarm-manager"
    config.vm.network "private_network", ip: "192.168.111.100"
    config.vm.synced_folder ".", "/home/vagrant/sync", disabled: true
  end
  ```

  ```vagrant
  C:\myCloudInfra\swarm-worker1
  
  # -*- mode: ruby -*-
  # vi: set ft=ruby :
  
  Vagrant.configure("2") do |config|
  # config.vm.box = "centos/7"
    config.vm.box = "generic/centos7"
    config.vm.hostname = "swarm-worker1"
    config.vm.network "private_network", ip: "192.168.33.101"
    config.vm.synced_folder ".", "/home/vagrant/sync", disabled: true
  end
  ```

  ```vagrant
  C:\myCloudInfra\swarm-worker2
  
  # -*- mode: ruby -*-
  # vi: set ft=ruby :
  
  Vagrant.configure("2") do |config|
  # config.vm.box = "centos/7"
    config.vm.box = "generic/centos7"
    config.vm.hostname = "swarm-worker2"
    config.vm.network "private_network", ip: "192.168.33.102"
    config.vm.synced_folder ".", "/home/vagrant/sync", disabled: true
  end
  ```

* **각 폴더에서 vagrant up 명령으로 가상머신을 생성 및 SSH로 접속**

  * C:\myCloudInfra\swarm-manager> vagrant up  / ssh
  * C:\myCloudInfra\swarm-manager1> vagrant up / ssh 
  * C:\myCloudInfra\swarm-manager2> vagrant up / ssh 

* **Docker 설치 및 서비스 기동**

```
[vagrant@demo html]$ sudo su
[root@demo html]# cd
[root@demo ~]# yum install -y docker

[root@demo ~]# systemctl start docker.service
[root@demo ~]# docker version
```



1. **매니저 역할의 서버에서 스웜 클러스터를 시작**

```bash
# --advertise-addr 192.168.111.100 ⇒ 매니저 노드의 주소 
[root@swarm-manager ~]# docker swarm init --advertise-addr 192.168.111.100


# 새로운 워커 노드를 클러스터에 추가할 때 사용하는 비밀키(토큰)
`docker swarm join \
    --token SWMTKN-1-51gsqhe3wqo7nj7tffuexh2jphwfjffy0a5anzgbx6ljn0m4as-24zl1npz454ep1olarxsddu0o \
    192.168.111.100:2377
```

2. **워커 노드를 추가**

   ※ 매니저에 나온 동일한 비밀키를 추가

```bash
[`root@swarm-worker1` ~]# docker swarm join \
>     --token SWMTKN-1-51gsqhe3wqo7nj7tffuexh2jphwfjffy0a5anzgbx6ljn0m4as-24zl1npz454ep1olarxsddu0o \
>     192.168.111.100:2377
This node joined a swarm as a worker.


[`root@swarm-worker2` ~]# docker swarm join \
>     --token SWMTKN-1-51gsqhe3wqo7nj7tffuexh2jphwfjffy0a5anzgbx6ljn0m4as-24zl1npz454ep1olarxsddu0o \
>     192.168.111.100:2377
This node joined a swarm as a worker.
```

3. **스웜 클러스터에 정상적으로 추가되었는지 확인**

```bash
[root@swarm-manager ~]# docker node ls

`ID                           HOSTNAME       STATUS  AVAILABILITY  MANAGER STATUS
`u0aezhea44cx9z5faipmntbpl    swarm-worker2  Ready   Active
`wdnqgalwfd9828npcr516uw2s    swarm-worker1  Ready   Active
`ym6n02vuulxa9izewh25vdv57 *  swarm-manager  Ready   Active        Leader
```

4. **토큰 확인 및 변경 방법**

   ※ 자격을 일시적으로 부여하는 것을 **토큰**이라함

   **※ 매니저 노드에서 실행**

```bash
[root@swarm-manager ~]# docker swarm join-token manager

`docker swarm join \
    --token SWMTKN-1-51gsqhe3wqo7nj7tffuexh2jphwfjffy0a5anzgbx6ljn0m4as-3m4lmy22pfapenpxonqvcpobj \
    192.168.111.100:2377

[root@swarm-manager ~]# docker swarm join-token worker
`    docker swarm join \
    --token SWMTKN-1-51gsqhe3wqo7nj7tffuexh2jphwfjffy0a5anzgbx6ljn0m4as-24zl1npz454ep1olarxsddu0o \
    192.168.111.100:2377
    
# 토큰을 변경
[root@swarm-manager ~]# docker swarm join-token --rotate manager
`    docker swarm join \
    --token SWMTKN-1-51gsqhe3wqo7nj7tffuexh2jphwfjffy0a5anzgbx6ljn0m4as-2x9uqu2xqgrobck2ho7z10rgh \
    192.168.111.100:2377
    
[root@swarm-manager ~]# docker swarm join-token --rotate manager
```



#### 다른 방법

