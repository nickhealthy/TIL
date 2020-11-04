# Today I Learned

* EC2 인스턴스 생성
* EC2 인스턴스 웹서버로 사용 가능하도록 설정 - Apache 이용
* EC2 인스턴스 웹서버로 사용 가능하도록 설정 - node서버 이용
* Route53 설정 (도메인 설정)

---



### EC2 인스턴스 생성

* AWS 콘솔에서  amazon Linux 2 버전 설치
* 모든 값은 default
* 보안그룹 페이지에서 SSH 프로토콜만 개방

---



### EC2 인스턴스 웹서버로 사용 가능하도록 설정 - Apache 이용

#### APache 웹 서버 다운로드 & 버전확인 

```bash
$ sudo yum install -y httpd

$ rpm -qa httpd
```



#### 시스템 작동 확인 & 시작

```bash
$ systemctl status httpd

$ sudo systemctl start httpd

$ systemctl status httpd
```



![1. 아파치서버 ec2 활성화](C:\dev\TIL\2020_11_03\img\1. 아파치서버 ec2 활성화.PNG)

#### 아파치 웹서버와 노드 JS 연동

1. http.conf 파일 수정

   * 리눅스 안에서 아파치 서버 위치

   ```bash
   $ cd /etc/httpd/conf/httpd.conf
   ```

---




### EC2 인스턴스 웹서버로 사용 가능하도록 설정 - node서버 이용

1. node 설치

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
. ~/.nvm/nvm.sh
nvm install node
```

2. git 설치

```bash
sudo yum install git -y
```

3. git clone

```bash
git clone https://github.com/multicampus-cloud2/Front-End.git
```

4. react 설치

```bash
npm install -g create-react-app
create-react-app first-app
npm install react-router-dom --save
```

5. EC2 Instance 설정

* 보안 그룹 인바운드 규칙 편집 - 규칙 추가
  * 사용자 지정 TCP 
  * 포트 : 3000 (node.js 가 기본 3000포트를 지원하기 때문에 / 가변적일 수 있음)
  * 소스 : 0.0.0.0/0, ::/0

6. npm start로 서버 실행
   * 개발환경 모드에서 사용
   * npm start로 서버를 운용시 리소스 용량이 커짐 (react-app 에서 필요한 모듈 등을 모두 사용하므로)

```bash
npm start publicIPv4주소:포트
ex) npm start 3.83.249.15:3000
```

7. npm run build (서버 배포용 폴더 만들기 - build)

   * build 디렉토리가 추가됨

   * index.html 파일이 서비스에 최적화 된 경량화 파일로 변환

   * serve 웹 서버 설치

     * npm 에서 serve를 설치를 할 수 있음

     * ```bash
       npm install -g serve
       # npx serve >> 한번만 웹 서버를 다운받음
       #  -s build >> document root로 하겠다
       npx serve -s build
       ```

