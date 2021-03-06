# Today I Learned

* Docker 기본 명령어
* Docker-compose

---



### Docker 기본 명령어

* 도커 이미지 다운만 받기

```dockerfile
docker pull {이미지명}:{태그}
```

* 컴퓨터 내 도커 이미지들 확인

```
docker images
```

* 이미지로 컨테이너 생성하기

```
docker create -it python
```

* 만들어진 컨테이너 시작하기 (이미지에 CMD로 지정해놓은 작업 시키기)

```
docker start {컨테이너 id 또는 이름}
```

* 이미지를 다운받아(이미지가 없을 시에만) 바로 컨테이너 실행하여 진입하기

```
docker run {이미지명}:{태그}
```

* 동작중인 컨테이너를 재시작

```
docker restart {컨테이너 id 또는 이름}
```

* 도커 컨테이너의 내부 쉘에서 빠져나오기 (컨테이너를 종료하지 않음)

```
Ctrl + P, Q
```

* 동작중인 컨테이너들 보기

```
docker ps 
docker ps -a (동작중이 아닌 것을 포함)
```

* 컨테이너 삭제

```
docker rm {컨테이너 id 또는 이름}
# 모든 컨테이너 삭제
docker rm `docker ps -aq`
```

* 이미지 삭제

```
docker rmi {옵션} {이미지id}
# 컨테이너가 있을 시 강제 삭제 : -f 옵션 사용
```

* 모든 컨테이너와 이미지 등 도커 요소 중지 및 삭제

```
# 모든 컨테이너 중지
docker stop $(docker ps -aq)

# 사용되지 않은 모든 도커 요소(컨테이너, 이미지, 네트워크, 볼륨 등) 삭제
docker system prune -a
```

* 도커 파일로 이미지 생성

```
# Dockerfile 파일이 있는 디렉토리 기준. 마지막의 . 이 상대주소
docker build -t {이미지명} .
```

* 도커 컴포즈 실행

```
# docker-compose 파일이 있는 디렉토리 기준
docker-compose up
```



#### 옵션

| 옵션                                           | 설명                                                         |
| :--------------------------------------------- | :----------------------------------------------------------- |
| -d                                             | 데몬으로 실행(뒤에서 - 안보이는 곳(백그라운드)에서 알아서 돌라고 하기) |
| -it                                            | 컨테이너로 들어갔을 때 bash로 CLI 입출력을 사용할 수 있도록 해줌 |
| --name(이름)                                   | 컨테이너 이름 지정                                           |
| -p {호스트의 포트 번호}:{컨테이너의 포트 번호} | 호스트와 컨테이너의 포트를 연결                              |
| --rm                                           | 컨테이너가 종료되면(내부에서 돌아가는 작업이 끝나면) 컨테이너를 제거함 |
| -v {호스트의 디렉토리}:{컨테이너의 디렉토리}   | 호스트와 컨테이너의 디렉토리를 연결함                        |

---



### docker-compose

#### 프론트엔드에서 http-server 로 frontend 파일만 실행

![프론트엔드에서 http-server로 띄우기](https://github.com/nickhealthy/TIL/blob/master/2020_11_15/img/%ED%94%84%EB%A1%A0%ED%8A%B8%EC%97%94%EB%93%9C%EC%97%90%EC%84%9C%20http-server%EB%A1%9C%20%EB%9D%84%EC%9A%B0%EA%B8%B0.PNG)

#### 최종화면 - 프론트엔드(nodejs) + 백엔드(python flask) + 데이터베이스(mysql) 연동

![최종 화면](https://github.com/nickhealthy/TIL/blob/master/2020_11_15/img/%EC%B5%9C%EC%A2%85%20%ED%99%94%EB%A9%B4.PNG)