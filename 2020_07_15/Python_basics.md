# 200715

#### SQL문 미리 공부
#### HTML, CSS
#### SI : 신규 개발, SM : 유지/보수

#### Coding Convention : 좋은 프로그래머는 사람이 이해할 수 있는 코드를 짠다.

- 사람의 이해를 돕기 위한 규칙이 필요함
- Camel Style : public String getUser() {} << 꼬불꼬불 자바 스타일
- Snake style : def get_user() :



### 자료구조, 기호: 메모리상에서 데이터를 효율적으로 관리하는 방법이다.

- 딕셔너리 : { } (중복된 값 허용 x) # 키와 값이 하나씩 묶여있어야함
- 튜플 : ( ) # 불변, 튜플을 쓰려면 my_tuple2 = (1,)
- 리스트 : [ ]



### 함수

- .key() : 키값만 뽑아내라
ex) # key, value 를 출력할때 keys() 함수
for city in wish_travel_city.keys(): #키만 뽑아내라
    print('{} in {}'.format(city, wish_travel_city[city]))

- zip() : for 문을 돌려서 출력
- setdefault() : 딕셔너리에 값을 추가하려고 할 때 해당 키가 없으면 추가하는 코드 (있으면 추가 X)

- set() : 중복 허용 안함, 값을 순서 없이 저장
1. .union() : 합집합
2. .intersection : 교집합
3. .difference : 차집합



### flake8 설치 및 실행
- 터미널 창에서 설치 확인 명령어 : pip show flake8
- '' 설치 명령어 " pip install flake8
- 설치 경로 : C:\Python37\Lib\site-packages\flake8
- .py 모듈 파일 'open in a terminal' 를 누르고 flake8 name.py 입력하면 실행/검사



### 파라미터 vs 아규먼트
- Parameter : 함수의 입력 값 인터페이스
- Argument : 실제 Parameter에 대입된 값



### 함수 파라미터 (키워드만 맞으면 위치가 바껴서 대입해도 상관 X)
- 위치 파라미터
- 키워드 파라미터 
- *p(가변 인수) : tuple type parameter (인자가 한개여도 >> 1, 로 표시해줌)
- **p(키워드 가변 인수) : dict type parameter



### 위치 인수, 키워드 인수 특징
- 위치 인수: 순서대로 와야 한다.
- 키워드 인수: 중간에 키워드 인수를 썻으면 남은 뒤에 인수가 있을시 뒤에도 키워드 인수로 써야한다.



### 함수의 기본값 지정
- def calcstep(begin, end, step = 1):
    sum = 0
    for num in range(begin, end + 1, step):
        sum += num
    return sum
   print("1 ~ 10 = ", calcstep(1, 10, 2))   # step 값을 따로 지정하여 2씩 증가
   print("1 ~ 100 = ", calcstep(1, 100))    # step이 기본값으로 1씩 증가



#### pass 예약어 : 함수를 선언해 놓고 당장에 입력 안할 시 'pass' 키워드 입력하면 스킵