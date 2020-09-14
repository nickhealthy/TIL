# Today I Learned

 

### 단축키

1. 지우기: rm -rf 'learn_git_mc'(폴더이름)

2. 이름 바꾸기: mv '기존 폴더이름' '바꿀 이름'

3. 모든 특정 파일 지우기: ex) rm *.txt
   * txt로 들어가는 파일 모두 지운다.

4. alt + f12 터미널 창 열고 닫기

5. 붙여넣기: shift + insert

---



### clone 설정 (최초 설정 한번만 해주면 됌)

1. clone은 말 그대로 복사이기 때문에 기존에 있는 파일들 모두 가져오고 remote 설정도 한번에 한다.

2. 명령어는 git clone 'url 주소'

3. 이후 git pull origin master로 파일 계속 다운받기

 

### log 상황 보기

1. git log --pretty=format:"%h %s" --graph

 1.1 이 명령어를 치면 현재 git log 상황이 나오고 id값 같은 프라이머리키가 나온다.

 

 

### 되돌리기

git commit --amend

'i' 를 누르면 안의 내용 수정 가능

'ESC' 누르면 unix 단축 명령어로 전환 가능

:wq : 저장하고 나가기 (:x 랑 똑같음)

: q! : 강하게 나가겠다라는 말

 

커밋을 했는데 Stage 하는 것을 깜빡하고 빠트린 파일이 있으면 아래와 같이 고칠 수 있다.

$ git commit -m 'initial commit'

$ git add forgotten_file

$ git commit --amend

 

### commit를 구분하여 넣어야 하는데 모든 것을 다 때려박아서 commit 메시지를 파일마다 다르게 수정하고 싶을때

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image002.gif)

 

### vim 명령어 : bash 명령어 창에서 파일 수정하기

1. vim '파일이름'

 

### cat 명령어 : 안에 내용을 보기

1. cat '파일이름'

 

### echo 명령어

 echo '# this is comment' >> board.py

> 하나는 redirection 

>두개는 덮어씌우기

 

### 수정하기

$ git checkout -- board.py : 마지막 commit 했을때의 파일로 되돌리기4

 ※ 주의사항: 한번 수정하면 다시 되돌리지 못하니까 정말 바꾸고 싶을때만 쓰기!

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image004.gif)

 

# branch 사용하기: 분업에 해당될때 브랜치를 하나씩 딴다.

 ※ 작업을 각자 진행 후 병합할때 사용

1. git branch : 브랜치 목록들

2. git branch feature/board : 브랜치 만들기 (이름은 대부분 저런 형태로 만듬)

3. git checkout feature/board : 다른 브랜치로 이동하는 명령

 3.1  git switch feature/board 위의 내용과 동일

※ git 은 commit 한것만 알아본다. 즉, 다른 브랜치를 사용할때 commit을 안하면 모든 계정에서 동일한 브랜치로 인식한다.

4. git merge feature/board : f/b브랜치가 마스터와 합친다. (master계정에서 해라)

5. git branch -d feature/board : 통합한 브랜치는 이미 마스터에 다 들어가서 해당 브랜치가 필요없다. 즉, 통합 작업이 끝난 브랜치를 이 명령어를 통해 삭제하는 것을 추천

6.git checkout -b feature/auth : 체크아웃하면서 브랜치를 생성한다.(한번에 투스텝을 가는것임)

7. 서로 다른 branch들끼리 commit를 한 후 merge를 할때 충돌이 일어남!

 7.1 이걸 반드시 해결해줘야 한다.

 7.2 커밋 메시지 창이 뜰 것이고, 커밋을 해주면 된다.(서로 다른 브랜치들끼리 통합해주는 커밋을 의미함)

  ※ master branch 계정에서 commit 를 한것이기 때문에 feature/auth 브랜치에서 log를 찍어봐도 7.2에서 commit 한 내용이 안나온다. (해당 브랜치에서 커밋한 브랜치에서만 확인가능)

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image006.gif)

※ 서로 다른 브랜치에서 파일이름이 같지만, 코드가 다를 경우가 진짜 충돌의 시작!

# master|merging 병합중이니 commit으로 병합 후 작업해라

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image008.gif)

 ※ commit 를 해야 합쳐진다.

 

 

# git diff HEAD

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image010.gif)

※ git diff HEAD바뀐 파일들의 목록을 볼 수 있다.

※ HEAD의 뜻은 현재 바라보고 있는 위치

 

※ 무시해도 되는 경고 unix 와 window의 encoding 얘기임

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image012.gif)

 

 

# 짝궁이랑 실습하기

1. git repo 생성 및 세팅

2. private 형태로 만들면 초대해줘야함

![img](file:///C:/Users/NICK_~1/AppData/Local/Temp/msohtmlclip1/01/clip_image014.gif)

 

3. 상대방은 clone을 해서 파일을 동일하게 받아와야함

 3.1 git clone 'url 주소'

 

※ origin은 'remote'를 가리키는 것

 

# fork 떠서 다른 상대방의 repo에 참여 하기

1. remote를 해당 repo로 설정한다.

2. 포크를 뜬다.

3. clone으로 땡겨온다.

 

# Rebase

# 질문내용

1. fork의 사용 이유?

2. git history 그래프

3. 창 깔끔하게 지우기 단축키

 

 

git은 핸드폰이고 github는 드라이버다

pip list | wc : 리스트의 개수 및 줄의 개수

 

장고 익스텐션 사용법

settings.py Install APP에 등록 

'django_extensions'

 

python manage.py shell_plus <<

1. 알아서 필요한 쿼리셋 모듈이나 클래스 등을 import 해준다.

 

__str__ : 메서드 오버라이팅

 

1. readme 잘 채우고

2. commit 초록색칸 평일은 매일 채우기

3. learning curve 잘 보여주기