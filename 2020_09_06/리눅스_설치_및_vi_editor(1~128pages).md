# 리눅스 설치 및 vi editor



* 우분투 서버 설치 (ubuntu-18.04.4-live-server-amd64)
* 쿠분투 설치 (kubuntu-18.04.5-desktop-amd64)

* 리눅스 실습 (for Beginner) 1 ~ 128 읽기



### 리눅스 실습 (for Beginner) 1 ~ 128 읽기



* 리눅스의 개요
  1. 리눅스는 쉽게 말해 *무료 유닉스*
  2. GNU, FSF, GPL 라이센스
  3. 커널 버전: 안정 버전, 메인라인 버전, 프리패치 버전
  4. 5.1.15 << 앞에서부터 주버전, 부버전, 패치버전
  5. 커널 업그레이드, 커널 컴파일: 배포판에 포함된 기본 커널을 사용자가 직접 최신 커널로 업그레이드 가능



* 우분투 리눅스 배포판
  1. 데비안 리눅스의 특징: apt 프로그램을 이용하여 소프트웨어 설치나 업데이트 자동 진행
  2. 우분투 데스크톱: X 윈도우 환경 지원 및 GUI 툴 제공
  3. 우분투 서버: TUI(Text User Interface) 환경의 인터페이스 제공
  4. 우분투 플레이버스: 한 배포판 안에서 다양한 배포판들을 의미



* 가상머신의 개요

  1. 멀티부팅 <> 가상머신

     >멀티부팅: 하드디스크의 파티션을 분할한 후 하나의 운영체제만 가동
     >
     >가상머신: 파티션을 나누지 않고 동시에 여러 개의 운영체제 가동

  2. 가상머신 소프트웨어 종류

     > 1. VMware
     >
     > 2. VirtualBox
     >
     >    etc..



* 가상머신의 특징

  ※ 가상머신의 장점은 *물리적으로 필요한 여러 가지 환경을 가상으로 구축해볼 수 있도록 지원해주는 것*

  1. 스냅숏: 운영체제의 특정 시점을 저장

     > 중요한 시점을 저장하는 기능

  2. 여러 개의 하드웨어를 장착하여 테스트 가능

     > 실무에선 *RAID* 등의 방식을 이용

  3. 현재 상태를 저장했다 추후에 이어서 작업하게 해주는 *Suspend* 기능



* desktop - root 사용자 활성화

  ```bash
  1. gedit /etc/gdm3/custom.conf
  >> AutomaticLoginEnable = true
  >> AutomaticLogin = root
  >> AllowRoot = true
  2. gedit/etc/pam.d/gdm-password
  >> #auth required pam_succeed_if.so user != root quiet_success
  3. gedit/etc/pam.d/gdm-autologin
  >> #auth required pam_succeed_if.so user != root quiet_success
  4. gedit / root/.profile
  #mesg n || true
  ```



* server  - root 사용자 활성화

  ```
  sudo su - root
  passwd
  reboot 이후
  
  server-b login: root
  ```

  

### 터미널과 콘솔

* 시스템 종료 명령 실행

```bash
poweroff
shutdown -p now
shutdown -p +10 : 10분 후에 종료
shutdown -r 22:00 : 오후 10시에 재부팅
shutdown -c : 예약된 shutdown 취소
halt -p
init 0
```

* 재부팅

```bash
reboot, shutdown -r now, init 6
```

* 로그아웃

```bash
logout, exit
```

* init 런라벨

  ※ 경로: /lib/system/system/default.target

```bash
init 0 종료 모드
init 1 시스템 복구 모드
init 2 사용하지 않음
init 3 텍스트 모드의 다중 사용자 모드
init 4 사용하지 않음
init 5 그래픽 모드의 다중 사용자 모드
init 6 재부팅 모드 
```

* 이전 모든 명령 확인 (도스키 x)

  ```bash
  history : 이전 명령 모두 확인
  history - c : 기억된 명령 모두 삭제
  
  ```



### vi(visual) 에디터

* gedit 파일명: x 윈도우에서 제공 - 윈도우의 메모장과 같은 기능

* esc : ex모드, 라인 명령 모드

  ※ 초기는 *명령 모드*

* 임시 스왑 파일: .swp: 파일을 정상적으로 종료되지 않으면 생김

* ```bash
  ctrl + f: 다음 화면
  ctrl + b: 이전 화면
  ^ 현재 행의 처음으로 이동
  & 현재 행의 마지막으로 이동
  gg 첫 행으로 이동
  G 마지막 행으로 이동
  
  dd : 현재 커서의 행 삭제
  숫자dd : 현재 커서부터 숫자만큼의 행 삭제
  yy : 현재 커서의 있는 행 복사
  숫자 yy : 현재 커서부터 숫자만큼의 행 복사
  p: 복사한 내용을 현재 행 뒤에 붙여넣기
  
  문자열 치환 : :%s/기존문자열/새문자열
  
  도움말 : man ls
  특정 단어 검색: /단어
  다음 단어: N
  ```