# 	Today I learned



> 웹 스크래핑 : 데이터 수집 행위
>
> Third part Lib : 프로그래밍을 도와주는 plug_in 이나 library
>
> pypi.org: (서버에 저장이 되어있고, 로컬 이용자가 자유롭게 다운받아 사용 가능): 오픈소스 모아놓은 공간 
>
> 동적 타이핑 언어: 프로그램이 실행하는 시점에 데이터에 대한 타입 결정(알아서 할당)
>
> 단위 모듈 : 객체
>
> 문자열 모두 유니코드로 나타냄
>
> 인터프리터(파이썬 철학) : import this
>
> 제어문: if, elif, else, for, while, continue, break
>
> 예외처리: try, finally, except, raise
>
> 연산자: and, not, or
>
> 함수: def, return, lambda
>
> 클래스: class
>
> 외부에 있는 모듈 로드(추가) : from, import
>
> 한줄에 79 문자가 최대치
>
> .py << 모듈
>
> #라인주석, '''''' 블록주석
>
> PEP 8 스타일 가이드를 따르자: 파이썬에서 지향하는 언어 규칙
>
> CLI(Command Line Interface): Text를 사용하여 컴퓨터에 명령을 입력하는 인터페이스 체계
>
> GUI(Graphic User interface): 일반적인 사용자들이 접하는 UI 화면
>
> 배열 [가로] [세로]
>
> \ 줄바꿈



#### 파이썬 PATH 확인

> 파이썬 path 확인(cmd) 명령어 : python --version
>
> 파이썬 path 확인 : 파이썬폴더 (Scripts - pip 확인), C:\Python37\



#### IDE

> WebStrom: HTML, CSS, JS 좋은 에디터
>
> DataGrip: SQL, NoSQL(Not only SQL)
>
> Kotlin(java랑 비슷): 안드로이드 개발 언어
>
> IDLE (CMD 커맨드 창 or Python shell)



#### 함수정리

> id(x): 메모리의 주소 *값(value)을 y에 넣어주면(y = x) 다른 변수여도 주소가 같다.
>
> input(): 콘솔창에 값을 입력하는 = scanf()
>
> format(): 변수 순서대로 들어감
>
> len(): 문자열 길이 반환
>
> .split(): 공백을 기준으로 리스트를 반환 / 구분 기호를 정해줄 수 있음 ex) ' '
>
> list(): 단어 하나씩 구분해서 인덱스로 넣어줌
>
> .title(): 첫단어 앞글자만 대문자
>
> .capitalize(): 단어마다 첫글자 대문자
>
> open(): 오픈
>
> .readline(): 한줄씩 읽음
>
> .strip(): 좌우 공백을 없앰




#### 자료형 표시법(format)
> %d, %s, %f

> {}. format()

> f-string  f '온도 값은 {temper}'



#### padding

> 문자는 왼쪽부터 채우고, 숫자는 오른쪽부터 채운다. 
> ex) print('product {0:10s}, Price per unit {1:10.3f}'.format('apple', 5.243))
>
> 기호로 바꿀 수 있음
> ex) print('product {0:>10s}, Price per unit {1:10.3f}'.format('apple', 5.243))



#### packing

> str >> list : packing
> word = 'GOOD manufacturing pratice'
> my_wordlist = word.split(' ')
> print(my_wordlist)



#### unpacking

> list -> str : unpacking
> word = 'GOOD manufacturing pratice'
> str1, str2, str3 = my_wordlist
> print(str1)
> print(str2)
> print(str3)



#### 과정 예정

> 화 : 1장 ~ 4장 - 파이썬, 변수, 타입, 연산자
> 수 : 5장 ~ 10장 - 조건문, 반복문, 함수, 문자열 관리, 리스트와 튜플, 사전과 집합
> 목, 금, 토 : 10장 ~ 11장 - SQL, html, css selector
> 토 : 12장 ~ 13장 - 표준모듈, 예외처리



#### 앞으로 사용할 Python open_source 목록

> pandas : tablet data (표데이터) 처리
>
> requests : http client (통신)
>
> beautifulsoup : html, xml 데이터 parsing (메모리 상에 잠시 데이터 저장하고(웹 스크래핑) pandas 등을 통해 자료를 뽑아내는 것)
>
> matplotlib : visualization (시각화) - 별로 안예쁨
>
> seaborn : visualization (시각화) - 이쁨, 쉬움
>
> pymysql : mysql db connector (DB연결)
>
> sqlalchemy : ORM 기능 ORM(Object Relational Mapping)

*pandas , sqlalchemy  연동 가능*

*SQL쿼리 테이블 안만들고 import 시킬 수 있음 - 자동 맵핑(1번의 기능임)*

