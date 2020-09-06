# Django_project

* 사용자 인증 여부

  > view.py에서 각 기능들에 대해 `데코레이터(Decorator)`:`@login_required` 수정

* 프로젝트_PPT

  > PPT_DB 테이블 구조 설명 및 화면 구성
  >
  > PPT_mainPage 기능 설명 및 화면 구성
  >
  > PPT_Allplaylist 기능 설명 및 화면 구성



### 사용자 인증 여부

`데코레이터(Decorator)`: 각 뷰에 접근 시 로그인 여부를 검사하여 접근을 **통제** 가능

> 함수형 뷰에서 사용
>
> @login_required



### DB 테이블 구조 설명

1. `django_content_type`

   > 하나의 테이블에서 범용적으로 다른 테이블에 조인(=foreign key)을 걸고싶을때 사용하는 것

```
id: DB테이블들의 id값
app_label: 각 애플리케이션에 정의된 이름
model: 각 애플리케이션에 정의된 모델 이름
```



2. `auth_user`

   > 유저들의 정보가 담긴 곳

```
id: 각 유저들의 id값
password: 각 유저들의 password값(시크릿 코드로 작성되어있음)
last_login: 최종 접속 시간
is_superuser: 관리자 (숫자 1로 표시함)
username: 유저의 이름
first_name: 지정 안함
last_name: 지정 안함
email: 유저들의 이메일 주소
is_staff: 관리 권한이 있는 맴버
is_active: 활동중이면 1, 아니면 0값을 띄움
date_joined: 유저 생성 시간
```



3. `plist_song`

   > YouTube에서 `parsing`한 노래 저장소

```
id: 각 노래들의 id값
song_title: 노래 제목 지정
song_artist: 가수 지정
song_url: YouTube에서 가져올 노래 URL주소 입력 창
song_genre: 노래의 장르를 주어진 콤보박스에서 선택하는 창
song_tag: 노래의 태그를 주어진 콤보박스에서 선택하는 창
song_start: YouTube URL주소에서 가져올 노래의 시작 시간 입력
song_end: YouTube URL주소에서 가져올 노래의 끝 시간 입력
song_thumbnail: 각 노래에 맞는 이미지 매핑이 가능하도록 이미지 값을 저장할 저장소
song_detail: 노래의 상세 정보를 원하는대로 입력 가능한 창
author: 각 유저마다 저장한 노래들을 분리하기위한 id값
```



4. `plist_playlist`

   > Song테이블에서 저장한 노래들 플레이 리스트 저장소

```
id: 플레이 리스트들의 id값들
play_title: 플레이 리스트 이름
play_list: 플레이 리스트 안에 들어있는 노래 id값들
(id값들을 song테이블의 id값들과 매칭시켜서 노래를 가져오도록 함)
play_detail: 플레이 리스트 안에 들어있는 노래 정보들
author_id: 각 유저들마다 저장된 플레이 리스트 id값들
```





# 클라우드 인프라

* 설치파일 다운로드

* 실습환경 구성 [참고URL](https://myanjini.tistory.com/)

  >VirtualBox
  >
  >Ubuntu(desktop, server)
  
* ※ 용량 부족할 시



### 설치파일 다운로드

> 1. VirtualBox-6.1.12-139181-Win
> 2. ubuntu-18.04.4-desktop-amd64
> 3. ubuntu-18.04.4-live-server-amd64
> 4. kubuntu-18.04.5-desktop-amd64



### 실습환경 구성

*가상환경이란 ?*

> 실제 하드웨어로 세팅되야 하는 것들을 *소프트웨어로* 구현



* #### *desktop version*

> ```
> 이름: Server
> 머신 폴더: C:\Linux
> 종류: Linux
> 버전: Ubuntu(64-bit)
> ```
>
> 메모리: 2048MB
>
> 가상하드디스크(VDI) - 동적 할당: 20GB
>
> 파일 위치 및 크기: C:\Linux\Server\Server.vdi
>
> 네트워크: `NatNetwork`
>
> 시동 디스크: ubuntu-18.04.4-desktop-amd64
>
> 일반 설치 선택
>
> 디스크를 지우고 Ubuntu 설치



* #### *server version*

```
이름: Server(B)
머신 폴더: C:\Linux
종류: Linux
버전: Ubuntu(64-bit)
```

> 메모리: 2048MB
>
> 가상하드디스크(VDI) - 동적 할당: 20GB
>
> 파일 위치 및 크기: C:\Linux\ServerB\ServerB.vdi
>
> 네트워크: `NatNetwork`
>
> 시동 디스크: ubuntu-18.04.4-live-server-amd64
>
> 일반 설치 선택
>
> 디스크를 지우고 Ubuntu 설치
>
>   ※ 마우스 사용 불가
>
> * 이동: `방황키` & `Tab` 
> * 항목 선택: `SpaceBar`
> * 입력 완료: `Enter` 
> * 수정: `Shift + Tab`



**설치 완료 후**

> 사용할 언어 선택: English
>
> 업데이트 여부: Continue without updating
>
> 사용할 키보드 설정: English
>
> 네트워크 설정 확인: 
>
> 프록시 서버 정보: 생략(필요시 입력)
>
> 우분투 저장소 주소: http://mirror.kakao.com/ubuntu
>
> 디스크 사용 범위: Use An Entire Disk(전체)
>
> 설치할 디스크 확인: VDI
>
> 파일 시스템 정보 확인: 확인
>
> 우분투 설치시현재 저장된 데이터 삭제(디스크 포맷): Continue
>
> ```
> 서버 정보와 사용자 게정 정보(desktop version 동일)
> Your name: ubuntu
> Your server's name: server-b
> Pick a username: ubuntu
> Choose a password: ubuntu
> Confirm your password: ubuntu
> ```
>
>  SSH 원격 접속 허용 여부 설치: 설정 후 확인
>
> 기타 프로그램 설치: 생략
>
> Reboot



*설치 이후 첫 로그인 화면*

![3. server_version_로그인화면](C:\dev\TIL\2020_09_03\3. server_version_로그인화면.PNG)



### ※ 용량 부족할 시

이전 *Windows 설치 파일 삭제*로 용량 확보 [URL](https://newstory-of-dev.tistory.com/entry/Windows-%EC%9D%B4%EC%A0%84-Windows-%EC%84%A4%EC%B9%98-%ED%8C%8C%EC%9D%BC%EC%82%AD%EC%A0%9C%ED%95%98%EA%B8%B0)

C 드라이브 >> 디스크 정리 >> 시스템 파일 정리 >> *이전 Windows 설치* >> 삭제