# Today I Learned



- 구글의 변화 / Hair salon AI (Google I/O 2018)

- Quick draw

- Auto draw

- Teachable Machine

---



### 구글의 변화

1. 터치 인터페이스(물리적) >> 음성 인터페이스
   * 물리적인 인터페이스 방식을 벗어나겠다. 
2. **인터페이스: 서로 다른 두 개체간에 연결시켜주는 접점**

---



### 기술 생산 / 기술 활용

- 기술 이해만 있다면 누구든 활용 가능

- Tensorflow: Airbnb, Xiaomi

- Chrominum: Edge, Opera, Whale, Tesla

- Linux,GCC,Python,git,etc..

---



### 자소설닷컴

1. IBM의 인공지능 API(왓슨) 모델을 사용

---



### 왜 프로그래밍을 배워야 하는가?

1. 일자리 변화

2. 소프트웨어 중심의 사회 변화에 대한 이해
   * 4차산업혁명에 따른 정보기술융합

3. 데이터 주도 의사결정
   * 프로그래밍 이해가 있는 인력 선호

---



### 프로그래밍의 혜택: Life Hacking, Work Hacking

1. 아마존 프로그래밍이 가능한 버튼 (주문가능 버튼도 있음)

2. 파이썬으로 어벤져스: 엔드게임 용산IMAX 명당 예매 하기
   * 영화 챗봇 시스템이 먹통된걸 직접 코딩해서 다시 예매가 열렸을때 문자가 오도록 만든 것

---



### API (Application Programming Interface)

​	**※ 응용프로그램 프로그래밍 인터페이스**

1. 운영체제 <- API -> 응용 프로그램
   * 응용 프로그램에서 사용할 수 있도록, 운영 체제나 프로그래밍 언어가 제공하는 기능을 제어할 수 있게 만든 인터페이스를 뜻한다.

2. 다른 시스템(소프트웨어) 간의 연결 방식

---



### 웹에서 API (배달의 민족) <- API -> Facebook

1. 서비스와 서비스 간의 대화 방식

2. 요청을 받는 측에서 일정한 방식을 명세 >> 해당 방식대로 요청을 하면 정보 제공

---



### 웹(WEB)에서의 커뮤니케이션 방식

1. 요청(request)과 응답(response)

요청(정보를 원하는 사람) --> 주소(Url) --> 응답(정보를 주는 사람)

​            <----문서(HTML, XML, Json 등)

3. 배민에서 페북으로 로그인하기: 페이스북에 있는 특정URL(약속)만 지키면 된다.

4. 일반 사용자를 위한 개발 이외에 개발에 필요한 API 문서를 따로 만들어라.
   * ex) 대부분 Json 파일의 형태

---



### Git / Github (개발자들의 구글드라이브, 이력서, 메모장)

1. 프로그래밍을 하겠다면 자기소개서 및 포트폴리오이자 내 프로젝트를 소개하는 공간

2. 오픈소스, API의 90% 이상은 git / github에 있음

---

 

### MOOC

1. Cousera: Meachine Leaning 등

2. Edx - CS50: Computer Science

3. Udacity: 실리콘벨리 기반의 기술의 끝 (Android - google 등)

4. K-MOOC

---

 

### github의 왕자되기

1. 오픈소스 컨트리뷰션

2. 미니 프로젝트

3. Hack my life

---



 

### 기본에 충실하기

1. 언어는 기본

2. 자료구조 + 알고리즘 = 프로그래밍

3. 컴퓨터 자체에 대한 이해(아키텍처, OS)

4. Domain Knowledge(시스템, 네트워크 등등)

---



### git bash (CLI)

1. GUI (사용자편의환경 창은 권한이 제한적이다.)
   *  CLI 명령행은 권한이 제한적이지 않으며, 권한에 제한이 없다.

2. git bash (unix 계열) / cmd (window 계열)
   * 명령어가 약간씩 차이가 있다.

 

### 각각의 프로젝트에 쓴 패키지들을 알아야한다. (배포를 하기 위해서!)

※ global 파이썬 패키지툴을 쓰면 안됌

※ 독립적으로 프로젝트마다 패키지를 관리해야한다.(버전관리)

1. pip freeze >(redirection) packages: packages.py에 설치 모듈들 목록
   * pip install -r packages: 다 다운

2. requirements.text:
   * mv(move) packages requirements.txt : 이름 바꾸기

3. venv(가상환경)

   > python -m venv venv: global 패키지를 쓰지 않고 가상환경 폴더를 생성

4. source venv/Scripts/activate: venv 가상환경모듈에 구축한 패키지들만 나온다.

5. 1.0.0 ~ 2.0.0 등등 버전 업데이트를 알려줌 

6. Semver(semantic 딱딱한)

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image002.gif)

 6.1 MAJOR : 호환이 안되는 완전히 다른 큰 변화

 6.2 MINOR : 바뀐 상황이 있지만 호환은 가능

 6.3 PATCH : 간단한 버그 수정 및 오류 해결

7. git에 올릴 타이밍: 큰 변화가 있을때 등등

---



 

### git 

0. touch .gitignore 만든 후에 안에 있는 파일들 다 ignore

1. 깃 처음 사용할때 글로벌 설정을 해줘야한다. <> 해당폴더에만 사용하고 싶으면 --global >> local
   * 처음에 무조건 1회성 셋팅을 해야함(로컬 컴마다)

2. git config --global user.name '설정하고 싶은 이름'
3. git config --global user.email '설정하고 싶은 메일'

---



2. git init: git을 초기화 및 해당 명령어를 친 폴더에 “.git” 이라는 폴더를 만든다

 2.1 git init 명령어를 친 폴더의 하위 폴더에는 다 .git 의 폴더가 들어가 있다.

> ls -a (숨김 폴더를 보여준다)
>
>  ls -al (전체 폴더를 보여준다.)
>
>  rm -rf .git (.git 이라는 폴더를 지운다.)
>
> rm -r test_dir
>
> touch: 파일을 만드는것
>
> git status: 상태 확인
>
> git add: 파일을 허용하겠다.

 

3. repo(Repositories) = (master) / 폴더 = ~ (home)

 3.1 카메라(.git)가 달린 방(폴더)은 repo이다. = 추적이 가능

 3.2 git status: 방안에 상황을 보고 있고, 변동 상황을 확인

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image004.gif)

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image006.gif)

※ Untracked files: “무슨 파일인지 정말 모르겠다.” 라는 뜻

 3.3 git add README.md 가 학생이라는 것을 알리는 것

 3.4 “README.md” 파일을 수정하고서 git status: 다시 쳤을때

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image008.gif)

 3.5 “README.md” 를 수정 후 git add를 한번더 해주자

 3.6 git commit -m 'README.md hi' : 커밋메시지를 남기면서 커밋

 3.7 git log: 파일 업로드 로그를 알려줌

 3.8 다시 재수정 후 git status 하면 

Changes not staged for commit: 이 나옴

 3.9 너가 다시 무대에서(unstage) 내리고 싶으면 이렇게 해라

 git restore --staged README.md

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image010.gif)

 3.10 commit 히스토리가 들어가있다.

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image012.gif)

 3.11 버전관리 안하는 법 (ex: .idea 같은 파일들)

 ![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image014.gif)

3.12

 

※ git은 이전 상황과 현재 상황의 형상을 비교해서 변경된 사항을 저장,기록한다.

 

# git status / add / commit / log /

1. add 하는 순간(변경내용이)에 무대(stage)에 올린다.

 1.1 stage에 올라가는 순간 트랙킹(추적)을 한다.

 1.2 초록색 줄화면: 아까 commit -m 한 이후부터 트래킹을한다.

 1.3 commit 를 한 순간 무대에 올라간 사람들(파일들)을 사진 찍는다.

  ※ 즉, 아무것도 안한것임

  ※ add 없는 commit 은 불가능 <> commit 없는 add는 아무것도 안한것

 

# remote: 여러개의 버전관리 폴더를 지정해주는 것

1. 여러개의 repo 폴더를 깃 원격 폴더와 갯수를 맞춰줘야한다. 즉, init 의 개수를 맞춰줘야한다. 

2. 깃 remote 주소를 넣는다.

(git remote add origin https://github.com/Nick-Healthy/learn_git.git)

3. git remote -v : remote 주소를 확인해본다.

4. git push (-u) origin master(최초로 한번 깃 계정을 로그인을 알림): 해당 remote url에 push 가 되었다.

※ 깃 로그인을 틀렸을 시: 윈도우 자격 증명 관리자에서 git ID 를 지우거나 수정한다.

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image016.gif)

 

# mark down 편집기: typora (개발자 문서)

1. 지식에 대한 정리를 할때는 마크다운 문서를 만들어라

2. git에서는 README에 해당

 

# 버전관리의 종류들

1. github: 백업 및 공유

 ※ git is not github

2. gitlab: 

3. bitbucket: private 성향이 강함

 

# gitignoreio

1. 프로젝트에 쓰이는 도구들을 모두 검색창에 넣은 뒤, 실행

2. 전체 복사하여 .gitignore에 붙여넣기한다.

 

# git 사용 순서도 (CLI로 사용 추천)

1. mkdir DJANGO_GIT : 폴더 생성

2. cd DJANGO_GIT : 폴더 이동

3. touch .gitignore : gitignore 파일 생성

4. python -m venv venv : 가상환경 파일 생성

5. source venv/Scripts/activate : 가상환경 파일 사용

6. 필요한 서드파티 모듈 설치 (ex: pip install django)

7. django-admin startproject django_git . : 맨 뒤에 .를 붙여야 여러 중첩 폴더(껍데기)를 없애고 이상적인 내용물을 까서 보여준다.

8.editor 실행

9. gitignore.io 에 들어가서 쓰는 도구들 추가 후 .gitignore 파일에 전체복붙

 9.1 git에 올라가는 불필요한 파일들(.idea)을 걸러주기 위함임

-------------------------ignore 파일들 설정완료------------------------

10. git init 실행

11. git status로 상태 확인

12. git add .

 ※ 주의사항 : 이 명령어를 칠때에는 프로젝트 root 폴더에서 실행해야 모든 변경사항이

​      스테이징 된다.

13. git commit -m 'commit 내용'

 ※ 주의사항 : branch 생성 시 무조건 첫 commit를 해야 만들 수 있다.

14. git push (-u) origin master

15. venv 가상환경 매번 설정하기 귀찮은거 설정해주기

 15.1 환경설정 - interpreter에서 내가 만든 venv 가상환경 파일 경로 설정해주기

16. Languages & framwork 에서 'django' 등 서브파티 모듈이 venv 가상환경에 잘 들어가 있는지 확인

17. pip freeze > requirements.txt 명령어로 venv 가상환경에 설치된 모듈들 txt 형태로 설치하기 (requirements 이름은 국룰임)

 ※ venv 파일은 .gitignore에 추가해주고, 새로운 컴퓨터나 다른 사용자가 이용시에는 venv 가상파일 생성 후 requirements 파일 안에 있는 모듈을 모두 다운로드한다.

 ※ pip install -r packages : 가상환경에 설치된 패키지 모두 다운로드

 

※ git.io : 포트폴리오 블로그 / TIL: project라고 불리기 민망한 것들 repo를 막 만들면 더러워서 한 묶음에 하기.

 

# TIL (Teach I Learned)만들기

1. touch .gitignore

2. touch README.md

3. mkdir git

4.touch git/git_basic.md

5. mkdir 4th

6. mkdir github_특강

7. touch day1.md

8. git init (주의!: 가장 최상위 폴더에 init 만들기)

 

# unix 명령어

1. cd, cd ~ : 루트 폴더로 이동

2. cd - : 뒤로 가기

 