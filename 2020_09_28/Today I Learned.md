# Today I Learned

- 수행평가 review
- 도커 스웜을 이용한 애플리케이션 개발 실습
  - MySQL 서비스 구축

---

### 수행평가 review

---

### 도커 스웜을 이용한 애플리케이션 개발 실습

#### MySQL 서비스 구축

- 데이터 스토어 역할을 수행할 MySQL 서비스를 마스터-슬레이브 구조로 구축
- MySQL 컨테이너는 도커 허브에 등록된 mysql:5.7 이미지를 기반으로 생성
- 마스터-슬레이브 컨테이너는 두 역할을 모두 수행할 수 있는 하나의 이미지로 생성
- 컨테이너에서 MYSQL_MASTER 환경변수의 값 유무에 따라 마스터-슬레이브 동작을 결정
- replicas 값을 조절해 슬레이브를 늘릴 수 있게 하며, 이때 마스터, 슬레이브 모두 일시 정지를 허용

0. **유틸리티 설치**

```bash
vagrant@swarm-manager:~$ sudo apt install -y tree
vagrant@swarm-manager:~$ sudo apt install -y jq
```

1. **MySQL 이미지 생성을 위한 리포지토리 클론**

```bash
vagrant@swarm-manager:~$ git clone https://github.com/gihydocker/tododb
vagrant@swarm-manager:~$ tree ./tododb/
```

2. **MySQL 설정 확인**

![1. mysqldb](C:\dev\TIL\2020_09_28\img\1. mysqldb.PNG)

![1-1. mysqldb](C:\dev\TIL\2020_09_28\img\1-1. mysqldb.PNG)

![1-2. mysqldb](C:\dev\TIL\2020_09_28\img\1-2. mysqldb.PNG)

![1-3. mysqldb](C:\dev\TIL\2020_09_28\img\1-3. mysqldb.PNG)

3. **이미지 생성 및 도커 허브 등록**

![2. mysql dockerfile 정의](C:\dev\TIL\2020_09_28\img\2. mysql dockerfile 정의.PNG)

- **이미지 빌드**

```bash
cd ~/tododb
docker image build -t alskadmlcraz/tododb:latest .
```

- **이미지 생성 확인 및 도커 허브에 등록**

```bash
docker image ls

docker push alskadmlcraz/tododb:latest
```

※ **네트워크 생성**

```bash
docker network create --driver=overlay todoapp
```

4. **MySQL 마스터 및 슬레이브 역할의 서비스를 생성**

```bash
mkdir ~/stack && cd ~/stack

# yaml 파일 생성
vi todo-mysql.yml

version: "3"

services:
  master:
    image: alskadmlcraz/tododb:latest
    deploy:
    placement:
      constraints: [node.role != manager]
    environment:
      MYSQL_ROOT_PASSWORD: gihyo
      MYSQL_DATABASE: tododb
      MYSQL_USER: gihyo
      MYSQL_PASSWORD: gihyo
      MYSQL_MASTER: "true"

  slave:
    image: alskadmlcraz/tododb:latest
    deploy:
      replicas: 2
      placement:
        constraints: [node.role != manager]
    depends_on:
      - master
    environment:
      MYSQL_MASTER_HOST: master
      MYSQL_ROOT_PASSWORD: gihyo
      MYSQL_DATABASE: tododb
      MYSQL_USER: gihyo
      MYSQL_PASSWORD: gihyo
      MYSQL_REPL_USER: repl
      MYSQL_REPL_PASSWORD: gihyo
    networks:
      - todoapp

networks:
  todoapp:
    external: true

docker stack deploy -c ./todo-mysql.yml todo_mysql

docker service ls
docker service ps todo_mysql_master
docker service ps todo_mysql_slave
```

도커 이미지 파일 생성 - 이미지 파일 push - yml파일 생성(핸들링/이미지 파일 추가) - 실행

**API 서버의 로그를 통해 정상 실행 여부를 확인**

```bash
vagrant@swarm-manager:~/stack$ cat ../todoapi/cmd/main.go
		:
        s := http.Server{
                Addr:    env.Bind,
                Handler: mux,
        }
        log.Printf("Listen HTTP Server")			`⇐ API 서버가 정상적으로 실행(동작)되면 해당 메시지를 출력
        if err := s.ListenAndServe(); err != nil {
                log.Fatal(err)
        }
}

vagrant@swarm-manager:~/stack$ docker service logs -f todo_app_api
todo_app_api.1.mt0n8x7ijbvf@swarm-worker2    | 2020/09/28 05:05:37 Listen HTTP Server
todo_app_api.2.ja9dprp3oqos@swarm-worker1    | 2020/09/28 05:05:40 Listen HTTP Server
```

**API 서버의 서비스(데이터 조회, 수정, 추가)가 정상적으로 동작하는지 확인 ⇒ API 서버로 요청을 전달**

```bash
vagrant@swarm-worker1:~$ docker container ls
CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                 NAMES
`44e2ce2c4a27        myanjini/todoapi:latest   "todoapi"                20 seconds ago      Up 19 seconds                             todo_app_api.1.u7cpbd3nqs7agrma4brckw8qk
7dc17c2630e8        myanjini/tododb:latest    "prehook add-server-…"   3 hours ago         Up 3 hours          3306/tcp, 33060/tcp   todo_mysql_slave.1.kzm92ewbbv7ppfd9drjq0e315
63fbef2736e6        myanjini/tododb:latest    "prehook add-server-…"   3 hours ago         Up 3 hours          3306/tcp, 33060/tcp   todo_mysql_master.1.ldwirpg6et1fho76dgqt12qtz


vagrant@swarm-worker1:~$ docker container exec 44e2ce2c4a27 curl http://localhost:8080/todo?status=TODO
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   216  100   216    0     0  11368      0 --:--:-- --:--:-- --:--:-- 11368
`[{"id":8,"title":"Ingressを構築する","content":"Swarmクラスタに外からアクセスするためのIngressを構築する","status":"TODO","created":"2020-09-28T02:30:24Z","updated":"2020-09-28T02:30:24Z"}]
```
