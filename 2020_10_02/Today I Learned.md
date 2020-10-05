# Today I Learned

* Ubuntu 저장소 repo 미러사이트로 변경

---



### 알고리즘 - [programmers]\_나누어\_떨어지는_숫자배열

----

### 

### 알고리즘 - [programmers]_체육복

---



### Ubuntu 저장소 repo 미러사이트로 변경

* 기본 설정된 ubuntu 저장소는 **archive.ubuntu.com**
  * 속도가 너무 느려서 단점
  * 여러 기업이나 학교에서 제공해주는 미러 사이트를 이용 및 변경
* repo 저장소 위치는 `/etc/apt/sources.list` 
  * 파일 안에 있는 모든 저장소 주소를 **mirror.kakao.com**으로 변경
* **%s/(변경할 대상)/(변경할 값) 명령어 사용**

```bash
sudo vi /etc/apt/sources.list

# 명령행 모드
:%s/archive.ubuntu.com/mirror.kakao.com
:wq

# apt(우분투 패키지 관리) 업데이트
sudo apt-get update
```

