# Today I Learned



* 알고리즘 - [programmers]\_두_정수의\_합
* 알고리즘 - [programmers]_수박수박수박수박수박수?
* iterable vs iterator

---



### 알고리즘 - [programmers]\_두_정수의\_합

* sum(iterable, start) 함수
  * `iterable`의 의미는 `member`를 하나씩 차례로 반환 가능한 `Object`를 의미 (sequence 타입: 리스트, 문자열, 튜플) , (non-sequence 타입: 딕셔너리)
  * `start`는 처음 시작하는 값이 아니라 추가로 더 할 값을 정의, defalut는 0으로 지정

```python
a = 3
b = 5

range(a, b+1)
# [3, 4, 5]
sum(range(a, b+1))
# 12 출력
```

----



### 알고리즘 - [programmers]_수박수박수박수박수박수?

* 슬라이싱[시작:끝:Step]

```python
ans = "수박"

s = ans * 2
print(s)
# 수박수박
print(s[:3])
# 수박수
```

---



### iterable vs iterator

#### iterable(이터러블)

* **`iterable`의 의미는 `member`를 하나씩 차례로 반환 가능한 `Object`를 의미** 

  * sequence 타입: 리스트, 문자열, 튜플

  * non-sequence 타입: 딕셔너리
  * for loop의 range도 사실 반환되는 형태가 `list`로 `iterable` 이기 때문에 순차적으로 `member`들을 불러서 사용이 가능했던 것

* `zip()`, `map()` 함수 등은 `iterable`을 인자로 받음



#### iterator(이터레이터)

* **Iterator는 `next()` 메소드로 데이터를 순차적으로 호출 가능한 object 임**
* 다음 데이터를 불러올 수 없을 경우 **`StopIteration exception**을 발생
* `iterable` `object`이라고 해서 `iterator`는 아님
  * **iterable을 iterator로 변환하고 싶으면 `iter()` 이라는 내장 함수를 이용**

```python
x = [1, 2, 3]
next(x)
# TypeError: list object is not an iterator

iter(x)
next(x)
# 1
next(2)
# 2
next(3)
# 3
```

