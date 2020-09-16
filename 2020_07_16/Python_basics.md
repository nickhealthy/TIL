# 200716

- Pythonic Coding
- zip() 함수
- lambda 함수
- map() 함수
- filter() 함수
- reduce() 함수
- Object-Oriented Programming, OOP 객체와 클라스



### Pythonic Code 작성법
- [The Hitchhiker's Guide to Python 책 추천](https://python-guide-kr.readthedocs.io/ko/latest/) << 파이썬 개발 블로그 좋음
- 인자로 받은 값 자체를 바꾸진 말 것 (꼭 필요하면 변수 복사를 추천)



  # 일반적인 for loop
     my_list = []
     for val in range(10):
      if val % 2 == 0:
          my_list.append(val)
     print(my_list)
  ```

2. ```python
  # List Comprehension (포괄적인 리스트) = 위에 코드랑 동일
  my_list2 = [val for val in range(10)]
  print(my_list2)
  
  my_list3 = [val for val in range(10) if val % 2 == 0]
  print(my_list3)
  
  my_list4 = [val if val % 2 == 0 else 10 for val in range(10)]
  print(my_list4)
  ```


### 함수
- .Split() : list -> str (문자열 나누기, default 값 띄어쓰기, 문자열 나누는 규칙 지정 가능)
- ' '.join() : str -> list (리스트의 문자열을 합치고 싶을때 쓰는 함수)
- zip() : index가 같은 것끼리 묶어줌



### lambda 함수 정의 : 함수를 좀 더 간결하게 하기 위해서, 메모리를 줄여준다, 표기법(자바스크립트): =>

```python
# 파라미터랑 함수안에 바디 코드만 적는다. (대신 앞에 lambda 선언)
add2 = lambda x, y: x + y
print(add(100, 200))
```



### map() 함수: 함수의 인자(파라미터)안에 함수가 들어감
```javascript
// JavaScript
my_arr = [1, 2, 3, 4, 5]; //*2[1, 4, 6, 8, 10]
result = my_arr.map(function(item){
return item * 2
});
console.log(result)

// 위 코드와 동일함
result2 = my_arr.map(item => item * 2);
console.log(result2)
```

```python
# Python
my_arr = [1, 2, 3, 4, 5]
result = map(lambda x: x * 2, my_arr)   # my_arr 인자를 뒤에다가 적어줘야함
print(result)
print(list(result))
```



### filter() 함수: 조건에 맞는 애들만 걸러내겠다.
```python
result = filter(lambda x: x > 2, my_arr)
print(result) # 리스트로 변환 해줘야함 / 변환 안할시 오브젝트 파일로 출력
print(list(result))
```

```python
# 리스트 말고 for문으로 출력
for val in filter(lambda x: x > 2, my_arr):
	print(val)
```



### reduce() 함수: (prev, curr) 전 값, 현재 값 계산을 해서 식을 줄여줌
- functools.py 라는 모듈안에 reduce() 함수가 존재
함수 선언법: from functools import reduce



### 객체와 클래스
- 속성은 변수, 행동은 함수
- Run time 실행할 때 Object, 실제 값이 들어간다.
- class SoccerPlayer (object) : SoccerPlayer 는 상속받는 객체, object 가 부모노드
= 자바의 extend (object) 
- __init__ (생성자) =  생성자 선언 - 객체가 생성될때 호출되는 메서드
- self = this(자기 자신)
- .py 모듈은 JAVA 와는 다르게 클래스 이름이 모듈과 달라도 가능
- __str__ 함수 선언을 안하면 object 부모 클래스를 호출함
- __ << 파이썬에서의 의미는 다른 언어의 private(외부에서 변수 접근 불가)



### 다른 모듈에 클래스가 있을때 from - import 시키는 법
- SoccerPlayer 클래스를 import
from mycode(폴더명).class_oop(폴더명).soccer_player(.py 모듈) import SoccerPlayer(클래스)
- from 은 import 하기위해 경로를 정해줌
※ 폴더에 숫자를 쓰면 from 이 안됌




### 개념
* ecmascript = javascript 의 표준

