# Today I Learned



* 알고리즘 - [programmers]\_완주하지\_못한_선수
* 알고리즘 - [programmers]_문자열\_다루기\_기본
* `sorted` 함수
* `collections` 모듈

---



### 알고리즘 - [programmers]\_완주하지\_못한_선수

* zip() 함수는 동일한 개수의 두 자료형을 순서대로 짝지어 튜플로 묶어주는 함수

```python
A = ['a', 'b', 'c', 'd']
B = ['a', 'b', 'c']

zip(A, B) # [('a','a'), ('b','b'), ('c','c')]
```

----



### 알고리즘 - [programmers]_문자열\_다루기\_기본

* ord(문자) = 아스키 코드로 변환
* chr(숫자) = 숫자에 맞는 아스키 코드로 변환

```python
ord('0') = 47
chr(47) = '0'
```

---



### sorted 함수

* 시퀸스 자료형을 정렬하는 방법으로는 직접 정렬 알고리즘과, 내장함수 `sorted`를 이용하는 방법이 있음
* `sorted` 함수는 시퀸스 및 시퀸스 자료형이 아닌 자료형에도 적용 가능
* `list` 형태로 반환해줌

```python 
sort = sorted([[2,1,3],[8,8,1],[4,2,3]])
# 결과
[[1, 2, 3], [2, 1, 3], [3, 2, 1]]
```

* `dictionary` 형태로 반환
  * defalut로 키값을 기준으로 소팅해준다.
  * value 값은 사라짐

```python
sort = sorted({3:1,2:3,1:4})
# 결과
[1,2,3]
```

* value(값)을 기준으로 소팅

  `sorted`의 `key` 파라미터를 이용

```python
myDict = {3:1,2:3,1:4}

sorted(myDict.items(),key=lambda x: x[1])
# 결과
[(3, 1), (2, 3), (1, 4)]
```

* 두번째 문자 값을 기준으로 정렬

```python
s = ["hzllo", "hi", "python"]
print(sorted(s, key = lambda x: x[1]))
# 결과
['hi', 'python', 'hzllo']
```

* 내림차순으로 변경방법

  `sorted`의 `reverse` 파라미터 값을 `True`로 변환

```python
print(sorted(range(0,9)))
# 결과
[0, 1, 2, 3, 4, 5, 6, 7, 8]

print(sorted(range(0,9), reverse=True))
# 결과
[8, 7, 6, 5, 4, 3, 2, 1, 0]
```

---



### **collections** 모듈

* collections.Counter()

  * 컨테이너 안에 **동일한 값의 자료가 몇개가 있는지를 파악하는 객체**
  * `Dictionary` 형태로 변환해주며, 값이 큰 순서대로 정렬해준다.

  ```python
  import collections
  
  lst = ['aa', 'cc', 'dd', 'aa', 'bb', 'ee']
  col_lst = collections.Counter(lst)
  
  print(len(col_lst), col_lst) 
  # 결과 
  5 Counter({'aa': 2, 'cc': 1, 'dd': 1, 'bb': 1, 'ee': 1})
  ```

  * **값 = 개수 형태**

  ````python
  c = collections.Counter(a=2, b=3, c=2)
  print(collections.Counter(c))
  print(sorted(c.elements()))
  
  # 결과
  Counter({'b': 3, 'a': 2, 'c': 2})
  ['a', 'a', 'b', 'b', 'b', 'c', 'c']
  ````

---

