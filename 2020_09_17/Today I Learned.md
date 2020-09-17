# Today I Learned



* 도커 스웜 09/17 이어서 계속

---

* 시연부분(동영상 미리 찍어놓기) 포함 15분 - 평가 양식지를 기준으로 평가
* 특강 이후에 포트폴리오 보완

### 도커 스웜 09/17 이어서 계속

5. **워커 노드 삭제**

```bash
vagrant@swarm-worker2:~$ docker swarm leave
`Node left the swarm.

vagrant@swarm-manager:~$ docker node ls
i2boml3wjv87wjv35mpi0okgt     swarm-worker1       Ready               Active                                  19.03.6
m110afvl76ppbomnlmh6zzwke     swarm-worker2       `Down`                Active                                  19.03.6

# 워커노드를 삭제함
vagrant@swarm-manager:~$ docker node rm swarm-worker2
swarm-worker2

vagrant@swarm-manager:~$ docker node ls
```

6. **promote 명령으로 워커 노드를 매니저 노드로 변경**

```bash
# swarm-manager 노드에서 조인 토큰을 확인
vagrant@swarm-manager:~$ docker swarm join-token worker
  docker swarm join --token SWMTKN-1-5w9hi0j9ahwgnq78w29pbqvm8sdte7osgl261lf6hte4fatgxp-f105esf8p1dvu5cfvkwsb9ukj 192.168.111.100:2377`

# swarm-worker2 노드를 클러스터에 워크 노드로 추가
vagrant@swarm-worker2:~$ docker swarm join --token 
`SWMTKN-1-5w9hi0j9ahwgnq78w29pbqvm8sdte7osgl261lf6hte4fatgxp-f105esf8p1dvu5cfvkwsb9ukj 192.168.111.100:2377 
`This node joined a swarm as a worker.

# swarm-manager 노드에서 워크 노드 추가를 확인
vagrant@swarm-manager:~$ docker node ls
`8cjqbgqzlf26kszvcyxoy3wgu     swarm-worker2       Ready               Active                                  19.03.6

# promote 명령으로 swarm-worker1 노드를 매니저 노드로 승격
vagrant@swarm-manager:~$ docker node promote swarm-worker1
Node swarm-worker1 promoted to a manager in the swarm.
vagrant@swarm-manager:~$ docker node ls
`ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
`wn5j8nymx704prelscdq8fw54 *   swarm-manager       Ready               Active              Leader              19.03.6
i2boml3wjv87wjv35mpi0okgt     swarm-worker1       Ready               Active              `Reachable`           19.03.6
```

7. **demote 명령으로 매너저 노드를 워커 노드로 변경**

```bash
vagrant@swarm-manager:~$ docker node demote swarm-worker1
`Manager swarm-worker1 demoted in the swarm.

# Reachable이 빠짐
vagrant@swarm-manager:~$ docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
wn5j8nymx704prelscdq8fw54 *   swarm-manager       Ready               Active              Leader              19.03.6
`i2boml3wjv87wjv35mpi0okgt     swarm-worker1       Ready               Active                                  19.03.6
```



#### 스웜 모드 서비스

* [스웜 모드 서비스 실습 페이지]([https://myanjini.tistory.com/entry/%EC%8A%A4%EC%9B%9C-%EB%AA%A8%EB%93%9C-%EC%84%9C%EB%B9%84%EC%8A%A4](https://myanjini.tistory.com/entry/스웜-모드-서비스))

* 도커 vs 스웜 모드
  * 도커 명령어의 제어 단위 : 컨테이너
  * 스웜 모드 명령어의 제어 단위 : 서비스
* 서비스
  * 같은 이미지에서 생성된 컨테이너의 집합
  * 서비스를 제어하면 해당 서비스 내의 컨테이너에 같은 명령이 실행됨
  * 서비스 내에 컨테이너는 한 개 이상 존재할 수 있으며, 컨테이너들은 각 워커 노드와 매니저 노드에 할당됨
  * 각 노드에 할당된 컨테이너들을 태스크(task)라고 함



1. 서비스 생성 및 조회

```bash
docker service create ubuntu:14.04 bin/sh -c "while true; do echo hello world; sleep 1; done"

docker service ls
```

2. 서비스 상세 정보 조회

```bash
docker service ps 서비스 이름
```

3. 서비스 삭제

```bash
docker service rm 서비스 이름

docker service ls
```

4. nginx 웹 서버 서비스 생성 및 확인

```bash
docker service create --name myweb --replicas 2 -p 80:80 nginx

docker service ps myweb
```

5. 스웜 클러스터 내의 어떤 노드로 접근해도 서비스에 접근이 가능

   * nginx 컨테이너가 없는 노드로 접근해도 서비스에 접근할 수 있음

   ```http
   192.168.111.100
   192.168.111.101
   192.168.111.102
   ```

6. 레플리카셋 추가

   * 컨테이너 수를 늘린 뒤 서비스 내의 컨테이너 목록 확인

```bash
docker service scale myweb=4

docker service ps myweb
```

* 서비스 모드

  1. 복제(replicated) 모드

     * 레플리카 셋의 수를 정의해 그만큼의 같은 컨테이너를 생성

     * 실제 서비스 제공에 일반적으로 사용하는 모드

  2. 글로벌(global) 모드

     * 스웜 클러스터 내에서 사용할 수 있는 모든 노드에 컨테이너를 반드시 하나씩 생성
     * 레플리카 셋의 수를 별도로 지정하지 않음
     * 스웜 클러스터를 모니터링하기 위한 에이전트 컨테이너 등을 생성해야 할 때 유용
     * docker service create --mode global 명령으로 생성

7. 글로벌 모드 서비스 생성 및 확인

```bash
docker service create --name global_web --mode global nginx

docker service ls
docker service ps global_web
```



#### 스웜 모드의 서비스 장애 복구

[스웜 모드의 서비스 장애 복구 실습 페이지]([https://myanjini.tistory.com/entry/%EC%8A%A4%EC%9B%9C-%EB%AA%A8%EB%93%9C%EC%9D%98-%EC%84%9C%EB%B9%84%EC%8A%A4-%EC%9E%A5%EC%95%A0-%EB%B3%B5%EA%B5%AC](https://myanjini.tistory.com/entry/스웜-모드의-서비스-장애-복구))

* 컨테이너가 정지하거나 특정 노드가 다운되면 스웜 매니저는 **새로운 컨테이너를 생성해 자동으로 복구함**



##### 실습 1. 특정 노드에서 `myweb` 서비스에 속한 컨테이너를 삭제하면 자동으로 다시 생성되는 것을 확인

1. swarm-manager 노드에서 실행 중인 컨테이너 목록 확인

```bash
docker ps

CONTAINER ID	IMAGE	COMMAND		CREATED		STATUS		PORTS		NAMES
31d2ce65f7bc  nginx@sha256:c628b67d21744fce822d22fdcc0389f6bd763daac23a6b77147d0712ea7102d0   "/docker-entrypoin..."   27 minutes ago      Up 27 minutes       80/tcp              
# 도커 컨테이너 이름
# 컨테이너 이름은 서비스 이름, 레플리카 번호, 자동으로 생성된 고유 ID로 구성
`myweb.4.lay2m6y4q0vkahfi0davjvomz
dd2fa4eab2e8        nginx@sha256:c628b67d21744fce822d22fdcc0389f6bd763daac23a6b77147d0712ea7102d0   "/docker-entrypoin..."   27 minutes ago      Up 27 minutes       80/tcp              myweb.3.svvbz67t1e97kpg58lbaqlf86
ef2dcb4a878f        nginx@sha256:c628b67d21744fce822d22fdcc0389f6bd763daac23a6b77147d0712ea7102d0   "/docker-entrypoin..."   31 minutes ago      Up 31 minutes       80/tcp              global_web.0gmegnm25in8tumgz555rq77q.jv99288ysiguxyo0eldthlypb
```

2. swarm-manager 노드에서 실행 중인 컨테이너 강제로 삭제

```bash
docker rm -f 31d2ce65f7bc
```

3. myservice 서비스의 태스크(task) 확인 `->` 새로운 태스크가 생성된 것을 확인

```bash
docker service ps myweb

1t7jhekv3zuv  `myweb.4`      nginx:latest  swarm-worker1  `Running`        Running 59 seconds ago
lay2m6y4q0vk   `\_ myweb.4`  nginx:latest  swarm-manager  `Shutdown`       Failed about a minute ago  "task: non-zero exit (137)"
```



##### 실습 2. 특정 노드가 다운되면 해당 노드의 컨테이너가 종료되고 다른 노드에 컨테이너가 생성되는 것을 확인

1. swarm-worker1 노드의 도커 데몬 프로세스를 종료

```bash
service docker stop
```

2. swarm-manager 노드에서 노드 상태 확인 `->` swarm-worker1 노드가 다운 상태

```bash
docker node ls

`ID                           HOSTNAME       STATUS  AVAILABILITY  MANAGER STATUS
`0gmegnm25in8tumgz555rq77q *  swarm-manager  Ready   Active        Leader
`b09zihkyf5vyylchc6720go3h    swarm-worker2  Ready   Active
`o0tlmyslbhfc00uskv3strmlg    swarm-worker1  Down    Active
```

3. 매니저 노드에서 myweb 서비스의 태스크 상태 확인 `->` 실행 중인 다른 노드에서 태스크가 생성됨

```bash
docoker service ps myweb

ID            NAME         IMAGE         NODE           DESIRED STATE  CURRENT STATE          ERROR                             PORTS
`ljhdwic0odbv  myweb.1      nginx:latest  swarm-worker2  Running        Running 7 minutes ago
`j9nlmdg2os4s   \_ myweb.1  nginx:latest  swarm-worker1  Shutdown       Running 9 minutes ago
krirx2xk6oay  myweb.2      nginx:latest  swarm-worker2  Running        Running 9 minutes ago
lt2dzzvzzpgg  myweb.3      nginx:latest  swarm-manager  Running        Running 9 minutes ago
svvbz67t1e97   \_ myweb.3  nginx:latest  swarm-manager  Shutdown       Failed 9 minutes ago   "task: non-zero exit (0): No s…"
`y7uk9mvnh6er  myweb.4      nginx:latest  swarm-worker2  Running        Running 7 minutes ago
`1t7jhekv3zuv   \_ myweb.4  nginx:latest  swarm-worker1  Shutdown       Running 9 minutes ago
lay2m6y4q0vk   \_ myweb.4  nginx:latest  swarm-manager  Shutdown       Failed 14 minutes ago  "task: non-zero exit (137)"
```



##### 실습 3.다운되었던 노드를 재시작해도 재균형(rebalance) 작업은 자동으로 일어나지 않음

* `docker service scale` 명령으로 컨테이너 수를 줄이고 다시 늘리는 방식으로 **서비스의 컨테이너 할당의 균형을 맞춤**

1. 다운되었던 swarm-worker1 노드를 재시작

```bash
service docker start
```

2. 매니저 노드에서 노드 상태 확인 `->` swarm-worker1 노드가 `Active `상태

```bash
docker node ls
```

3. 매니저 노드에서 myweb 서비스의 태스크 상태 확인 `->` **실습 2.** 에서 했던 태스크 상태가 같음(myweb.4)

```bash
docker service ps myweb
```

4. 컨테이너 균형 맞추기

```bash
docker service scale myweb=1

docker service ps myweb

docker service sclae myweb=4

docker service ps myweb
```



##### 실습 4. 서비스 롤링 업데이트 (스웜 모드에서 자체적으로 지원)

1. 서비스 생성

```bash
docker service create --name myweb2 --replicas 3 nginx:1.10

docker service ps myweb2
```

2. `docker service update` 명령으로 생성된 서비스의 설정 변경

```bash
# 이미지 변경(업데이트)
docker service update --image nginx:1.11 myweb2

docker service ps myweb2
```

3. 서비스를 생성할 롤링 업데이트 설정 가능

* 롤링 업데이트 주기, 업데이트를 동시에 진행할 컨테이너의 개수, 업데이트에 실패했을 때 어떻게 할 것인지를 설정

```bash
docker service create \
--replicas 4 \
--name myweb3 \
# 10초 단위로 업데이트
--update-delay 10s \
# 업데이트 작업을 한 번에 2개의 컨테이너에 수행
--update-parallelism 2 nginx:1.10
```

4. 서비스의 롤링 업데이트 설정 확인

```bash
docker service inspect --pretty myweb3

ID:             er41j2dg8k5o9jq14exeppzpn
Name:           myweb3
Service Mode:   Replicated
 Replicas:      4
Placement:
UpdateConfig:
 Parallelism:   2
 Delay:         10s
 # 업데이트 도중 오류가 발생하면 롤링 업데이트를 중지
 On failure:    pause
 Max failure ratio: 0
ContainerSpec:
 Image:         nginx:1.10@sha256:6202beb06ea61f44179e02ca965e8e13b961d12640101fca213efbfd145d7575
Resources:
Endpoint Mode:  vip
```

5. 롤링 업데이트 중 오류가 발생해도 계속 롤링 업데이트를 진행하도록 설정

```bash
# --update-failure-action continue 옵션을 줌
docker service create --name myweb4 --replicas 4 --update-failure-action continue nginx:1.10

docker service inspect --pretty myweb4
ID:             af9703hb3sc866gmnnpe3xolf
Name:           myweb4
Service Mode:   Replicated
 Replicas:      4
Placement:
UpdateConfig:
 Parallelism:   1
 # 바뀐 것을 확인
 `On failure:    continue`
 Max failure ratio: 0
ContainerSpec:
 Image:         nginx:1.10@sha256:6202beb06ea61f44179e02ca965e8e13b961d12640101fca213efbfd145d7575
Resources:
Endpoint Mode:  vip
```

6. 서비스를 롤링 업데이트 이전으로 되돌리는 롤백

```bash
docker service ps myweb2


docker service roolback myweb2
`vhp2j99j9eae        myweb2.1            nginx:1.10          swarm-worker2       Running             Running 9 minutes ago
`z7v94cuswz4p         \_ myweb2.1        nginx:1.11          swarm-worker2       Shutdown            Shutdown 9 minutes ago
`t9jfqy1wsu5p         \_ myweb2.1        nginx:1.10          swarm-worker2       Shutdown            Shutdown 52 minutes ago

docker service ps myweb2
```

**※ dind 와 지금 하고 있는 실습과정 차이**

**※ docker 컨테이너 상태 비주얼화**

![image-20200917134434523](C:\Users\Nick_주성우\AppData\Roaming\Typora\typora-user-images\image-20200917134434523.png)

##### 

#### 서비스 컨테이너에 설정 정보 전달

[실습 페이지](https://myanjini.tistory.com/entry/%EC%84%9C%EB%B9%84%EC%8A%A4-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EC%97%90-%EC%84%A4%EC%A0%95-%EC%A0%95%EB%B3%B4-%EC%A0%84%EB%8B%AC)

* 이전까진 `volume` 이나 `-e` 옵션으로 설정 정보를 전달함
* `swarm`에선 `secret`이나 `-e` 옵션으로 설정 정보를 전달

##### 컨테이너에 설정 정보 전달

* 방법 1. `-v` 옵션을 통해 호스트에 위치한 설정 파일이나 값을 볼륨으로 공유

```bash
docker run -d --name yml_registry -p 5002:5000 --restart=always -v $(pwd)/config.yml:/etc/docker/registry/config.yml registry:2.6
```

* 방법 2. `-e` 옵션을 통해 환경 변수로 전달

```bash
docker run -d --name wordpressdb_hostvolume -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -v /home/wordpress_db:/var/lib/mysql mysql:5.7
```



##### 스웜 모드는 secret과 config 기능을 제공

* 스웜 모드와 같은 서버 클러스터에서 파일 공유를 위해 설정 파일을 호스트마다 마련해두는 것은 매우 비효율적임
* 비밀번호와 같이 민감한 정보를 환경 변수로 설정하는 것은 보안상으로 바람직하지 않음
* 스웜 모드에서 사용할 수 있는 secret, config 기능을 제공
* secret `->` 비밀번호, SSH 키, 인증서 키와 같은 보안에 민감한 데이터를 전송하기 위한 용도
* config `->` nginx나 레지스트리 설정 파일과 같이 암호화할 필요가 없는 설정값들에 사용



##### 실습 1. secret 사용하기

* 생성된 secret는 조회해도 실제 값을 확인할 수 없음
* secret 값은 매니저 노드 간에 암호화된 상태로 저장
* 컨테이너에 배포된 뒤에도 메모리에 저장되며 서비스 컨테이너가 삭제될 때 함께 삭제

1. secret 생성

```bash
[vagrant@swarm-manager ~]$ echo 1q2w3e4e | docker secret create my_mysql_password -
# 시크릿 공유 파일 확인
[vagrant@swarm-manager ~]$ docker secret ls
# 상세 정보 확인
[vagrant@swarm-manager ~]$ docker secret inspect my_mysql_password

[
    {
        "ID": "7pcs67tyj481k4ka0rwgqmm79",
        "Version": {
            "Index": 889
        },
        "CreatedAt": "2020-09-17T05:03:45.673479071Z",
        "UpdatedAt": "2020-09-17T05:03:45.673479071Z",
        "Spec": {
            "Name": "my_mysql_password",
            "Labels": {}
        }
    }
]
```

2. 생성한 secret을 통해 MySQL 컨테이너 생성
   * --secret 옵션을 통해 컨테이너로 공유된 값은 기본적으로 컨테이너 내부의 /run/secret/ 디렉터리에 마운트 됨
   * --secret source=my_mysql_password,target=/home/mysql_root_password 처럼 디렉터리 지정도 가능

```bash
docker service create --name mysql --replicas 1 --secret source=my_mysql_password,target=mysql_root_password --secret source=my_mysql_password,targe
t=mysql_password -e MYSQL_ROOT_PASSWORD_FILE="/run/secrets/mysql_root_password" -e MYSQL_PASSWORD_FILE="/run/secrets/mysql_password" -e MYSQL_DATABASE="wordpress" mysql:5.7

[vagrant@swarm-manager ~]$ docker service ps mysql
`o0zx50xa8q3l        mysql.1             mysql:5.7           swarm-manager       Running             Running 34 seconds ago
[vagrant@swarm-manager ~]$ docker ps
`b4b8c72103ad        mysql:5.7           "docker-entrypoint.s…"   44 seconds ago      Up 38 seconds       3306/tcp, 33060/tcp   mysql.1.o0zx50xa8q3l22x3uqq0eldvr

# 시크릿 파일을 확인
[vagrant@swarm-manager ~]$ docker exec mysql.1.o0zx50xa8q3l22x3uqq0eldvr ls /run/secrets
mysql_password
mysql_root_password

# 저장 되있는것을 확인
[vagrant@swarm-manager ~]$ docker exec mysql.1.o0zx50xa8q3l22x3uqq0eldvr cat /run/secrets/mysql_password
`1q2w3e4e

[vagrant@swarm-manager ~]$ docker exec mysql.1.o0zx50xa8q3l22x3uqq0eldvr cat /run/secrets/mysql_root_password
`1q2w3e4e
```



##### 실습 2. config 사용하기

1. 사설 레지스트리 설정 파일 생성

```bash
vi config.yml

version: 0.1
log:
	level: info
storage:
	filesystem:
		rootdirectory: /registry_data
	delete:
		enabled: true
http:
	addr: 0.0.0.0:5000
```





* BASE64 인코딩 : 눈에 보이도록 **가시화**를 해줌
  * 인코딩 : 규칙에 따라 데이터를 다른 형태로 변형
  * 디코딩 : 이진수로 바꿔서 6비트 단위로 짤라서 규칙에 따라서 바꾼다.
    * 0~63(6비트) `->` 영문자(대문자, 소문자), 숫자, +, / 글자로 변경

![2.base64](C:\dev\TIL\2020_09_17\2.base64.PNG)

---



#### 도커 스웜 네트워크

[실습 페이지]([https://myanjini.tistory.com/entry/%EB%8F%84%EC%BB%A4-%EC%8A%A4%EC%9B%9C-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC](https://myanjini.tistory.com/entry/도커-스웜-네트워크))

![네트워크 1](C:\dev\TIL\2020_09_17\네트워크 1.PNG)

![네트워크 2](C:\dev\TIL\2020_09_17\네트워크 2.PNG)

![네트워크 3](C:\dev\TIL\2020_09_17\네트워크 3.PNG)

![네트워크 4](C:\dev\TIL\2020_09_17\네트워크 4.PNG)

![네트워크 5](C:\dev\TIL\2020_09_17\네트워크 5.PNG)

![네트워크 6](C:\dev\TIL\2020_09_17\네트워크 6.PNG)

![네트워크 7](C:\dev\TIL\2020_09_17\네트워크 7.PNG)

![네트워크 8](C:\dev\TIL\2020_09_17\네트워크 8.PNG)

![네트워크 9](C:\dev\TIL\2020_09_17\네트워크 9.PNG)

![네트워크 10](C:\dev\TIL\2020_09_17\네트워크 10.PNG)

![네트워크 11](C:\dev\TIL\2020_09_17\네트워크 11.PNG)

---



### 서비스 디스커버리

[실습 페이지]([https://myanjini.tistory.com/entry/05-%EC%84%9C%EB%B9%84%EC%8A%A4-%EB%94%94%EC%8A%A4%EC%BB%A4%EB%B2%84%EB%A6%AC](https://myanjini.tistory.com/entry/05-서비스-디스커버리))

* **동일한 네트워크 대역**에서는 **서비스 이름**만 지정해주면 **각 노드에 접근할 수 있음**
* 방식 1. VIP 방식
* 방식 2. 라운드 로빈 방식

![서비스 디스커버리 1.](C:\dev\TIL\2020_09_17\서비스 디스커버리 1..PNG)

* 상세정보 확인
  * 서브넷 마스크를 자동으로 지정

```bash
[vagrant@swarm-manager ~]$ docker network inspect discovery

 {
        "Name": "discovery",
        "Id": "4vu98gt08aij6ziehd8wqk6dz",
        "Created": "2020-09-17T07:59:05.497738909Z",
        "Scope": "swarm",
        "Driver": "overlay",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    `"Subnet": "10.0.0.0/24",`
                    "Gateway": "10.0.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": null,
        "Options": {
            "com.docker.network.driver.overlay.vxlanid_list": "4097"
        },
        "Labels": null
    }
```



![서비스 디스커버리 2](C:\dev\TIL\2020_09_17\서비스 디스커버리 2.PNG)

![서비스 디스커버리 3](C:\dev\TIL\2020_09_17\서비스 디스커버리 3.PNG)