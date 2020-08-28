# # 200717

- 캡슐화 (상속, 추상)
- 모듈 (import 하는 4가지 방법)
- pip 를 이용해 외부모듈설치
- anaconda 를 이용해서 필요한 모듈 한번에 설치
- 예외처리와 로깅
- 파일 다루기



### 객체와 클래스
- 캡슐화

python에서는 클래스 내부에서만 사용되는 변수
private 변수로 선언 남들이 접근 못함 = 언더바 2개(__) -  __year
ex) self.__year = year



### @property - @(어노테이션이라 불리고, 메서드) 사용법
- getter 함수라고 불림
- Property decorator 숨겨진 변수를 반환하게 해줌
- Property decorator로 함수를 변수처럼 호출
def year():
    return sefl.__year



### setter - 메서드는 () << 함수 인자처럼 생겼어도 'year' 이런식으로 적어준다. (year) = X
- @year.setter
def year(year):
    self.__year = year

myDate.__year = 2005 (X)
myDate.year(2000)
print(myDate.year)



### 상속 : 부모의 재산이 내꺼다
> class Manager
>     name                     : 속성단
>     salary
>     dept

> class MereClerk
>     name		       : 속성단
>     salary

> 의미상으로 'is' 가 성립되어야 가능
> Manager is a Employee
> MereClerk is a Employees

> class Employee
>     name
>     salary

- 상속받은 클래스
class Manager(Employee)  : Manager 은 name, salary 속성이 없지만 (Employee을 extend 했기때문에 사용 가능)
    dept
- super() 함수 : 부모 클래스 안에 있는 함수(메서드)를 가져다 쓸 수 있음



### Polymorphism (다형성) : Poly(다양한) + morphism(변형, 변신) = "One Interface, Multiple Implemetation"


- Abstract class (추상메서드) : 부모가 선언만 해놓고 바디 구현을 안해놓으면 무조건 overriding 설정 해야함 (자식 클래스에서 구현)
 raise  = 추상 메서드를 알려주는 키워드
ex)  def talk(self):
          raise NotlmplementedError

> class abstract Animal{
>   abstract void my_method();
> }
> class Dog Animal{
>   abstract void my_method();
> }
> class Cat Animal{
>   abstract void my_method();
> }

> Animal ani = new Animal(); (x)
> Animal dog = new Dog(); (o)
> Animal cat = new Cat(); (o)

> Animal Type의 Array 선언 가능
> Animal ani = new Animal [3];
> ani[0] = new Dog();
> ani[1] = new Cat();  = ani의 type은 Animal type

### 모듈(converter) import 4가지 사용법
1. 모듈명을 import : 모듈안에 들어있는 모든 소스 사용
2. 모듈명을 Alias 설정 : import 모듈명 as fah
3. 모듈에서 특정 함수 또는 클래스만 import 가능 = from 모듈명 import 함수 or 클래스
4. 모듈에서 모든 함수 또는 클래스 import 가능 = from 모듈명 import *

※ 직접 실행, import 됬을때만 실행 하는 법
- 직접 실행 :  hello(파일명).py
- import 실행 : from 파일명 import 클래스명
ex)

> def say_hello(msg):
>     return 'Hello ' + msg

> def main():
>     print(say_hello('파이썬'))

// 직접 실행했을때 실행
if __name__ == '__main__':
    print("직접실행")
    main()

// import 됬을때만 실행
else:
    print('import 되어 실행됨')




### cmd 창에서 파이썬 소스파일 실행시키는법
- python 파일이름.py



### import sys (내장 모듈) = builtin module <> 써드파티 모듈 : 외부 모듈로써 별도로 설치가 필요함

- 파이썬 콘솔창에서 sys.path : 모듈이 설치된 경로를 확인할 수 있음




### import os 
> os.getcwd() : os 디렉토리 위치 찾아줌
> 'C:\\mypython\\python_programming_stu'



### 예외처리(exception) : 에러가 나도 프로그램이 죽지 않도록 하기 위해서
> ※ 예외 처리는 계층 구조를 가짐 (Exception 이 최상위 계층)
> ※ 가독성 면에서 하위 계층를 골라 선택하면 좋음
> try : 예외 발생 가능 코드
> except : 예외 발생시 동작하는 코드
> finally : 예외 발생 여부와 상관없이 실행됨
> else : 예외가 발생하지 않을 때 동작하는 코드
> raise : 에러를 강제로 냄 
> user define exception : 사용자 정의 예외처리



### logging 핵심 : 로그레벨(모드)별로 로그를 남기고 싶어서 
로그레벨 순위: Trace > DEBUG > INFO > WARNING > ERROR > Critical 
- 개발자들은 low 레벨로 로그를 기록해서 파악 (대부분 debug)
- 운영 모드 (유저들은) high 레벨별로 로그를 기록
※ 개발, 테스트, 운영 별로 다른 레벨 로그 운영



### 파일 다루기
- open('파일이름', 'r') 은 close() 로 닫아줘야함 
- with open 를 쓰면 close()로 안닫아 줘도됌
- mode 가 'w' 일때 %d 표기하고 문자가 들어갔을때 encoding='utf-8' 설정 안해주면 못읽음




### 개념
- 패키지로 인정되려면 C:\Python37\Lib = builtin module 저장공간 : __init__.py (생성자) 모듈이 있어야함 
- C:\Python37\Lib\site-packages << 써드파티 모듈 저장공간 = 외부모듈
- IDE에서 패키지로 인정되려면
ex) 
1. game\__init__.py(생성자 안에 __all__ = ["graphic", "play", "sound"]) = 폴더들이 정의 되있어야함
2. game\graphic\__init__.py(생성자 안에 __all__ = ["render", "screen"]) = 하위 모듈들이 정의 되있어야함
※ import 시키는건 따로

- 모듈 > 클라스 > 메서드



# 질문 
- overrides method in object 의 기능 :  부모 클래스의 기본적인 동작방법을 변경할 수 있어야 한다. 이런 맥락에서 도입된 기능이 메소드 오버라이딩(overriding)이다.
- from 제대로 알기 (범위)

> while True:
>     value = input("변환할 정수값을 입력해주세요")
>     for digit in value:
>         if digit not in "0123456789":
>             raise ValueError("숫자값을 입력하지 않으셨습니다")   # 형식에 맞지 않게 입력하면 강제 
>
> 에러 raise
>     print("정수값으로 변환된 숫자 -", int(value))