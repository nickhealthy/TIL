# Today I Learned



* Python 문법 복습

* Vagrant_ansible, Serverspec, Jenkins 복습

---



### Python

* 문자열 메서드

  **※ 문자열은 한번 초기화된 후 값 수정 불가**

  ※ 바꾸고 싶을땐 ex) s = s.lower()

  1. startswith() / endswith()

     ※ 첫 글자나 마지막 글자를 점검

  2. isdecimal()

     ※ 모든 문자가 숫자인지 조사

* 리스트

  1. 리스트는 문자열과 다르게 개별 변수마다 값을 수정 가능

  2. 리스트에 범위를 지정하여 여러 요소를 한꺼번에 일괄 변경 가능

     ```python
     num = [0, 1, 2, 3, 4]
     num[2:5] = [90, 91, 92]
     print(num)
     num[0:1] = [1,2,3,4,5,6,7,8,9]
     ```

  3. 삭제

     ※ 하나만 삭제 시 del num[4]

     ※ 여러개 삭제 시 num[2:5] = []



### Vagrant_ansible, Serverspec, Docker, JenkinS 복습

1. vagrant

   > 1. 환경 구축 작업의 간소화가 가능
   > 2. 환경 공유가 용이
   > 3. 환경 파악이 용이
   > 4. 팀 차원의 유지 보수 가능

   

2. vagrant_Ansible

   * vagrant로만 진행시 발생하는 문제점

   > 1. 구축 절차를 알기 어려움
   >
   > 2. 설정을 추가할 수 없음
   >
   >    ※ Vagrantfile의 구축절차는 가상 머신을 초기 구축하는 경우 "처음부터 구축하는 절차임"
   >
   >    ※ vagrant provision 명령어로 추가 구축이 가능하지만 **결국 Shell Script로 인해 사람**에 의해 진행되는 문제 (1번 문제와 동일)
   >
   > 3. 구축절차를 다른 환경에서 유용하기 힘듬 (해당 환경에 기반한 파라미터 존재 여부)
   >
   >    ※ 네트워크가 상이한 경우, 서버의 IP주소 변경
   >
   >    ※ Java가 동작하는 어플리케이션인 경우 JVM의 힙 사이즈 변경

   * Ansible

   > 1. 환경설정 및 구축 절차가 통일된 포맷을 이용해 구축환경 변경과 파악 용이
   > 2. 실행 전에 변경되는 부분 파악 가능

   ※ 실행 대상

   site.yml

   ```bash
   host: webservers << 배포 대상 호스트(그룹을 의미) / 여기서는 localhost
   
   development / production << 그룹안에 있는 인벤토리 파일(서버)를 의미
   
   -i 옵션으로 대상을 지정 가능함
   ```

   

3. Serverspec

   * .rb 파일로 된 파일은 **실행파일**

   * Serverspec 설정시 명령어 `serverspec-init`

   * 테스트하는 대상 위치

     ※ 해당 위치에 테스트할 내용 등이 기술되어 있음

     ```bash
     spec/localhost/spec.rb
     ```

   * 테스트 실행 (spec 디렉토리 위치한 곳에서) `rake spec` 명령어로 실행 및 결과 확인



4. Jenkins
   * 구축(Ansible)과 테스트(Serverspec)을 연속적으로 수행 가능
   * Ansible의 인테토리 파일(서버)들을 묶어서 한번에 설정 및 테스트 가능
   * Build Pipeline 도구 / GUI 형태로 제공 / Java 기반이라 JDK 설치 전제가 되있어야함

