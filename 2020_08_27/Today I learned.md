# Today I learned



### Alogorithm

* [programmers]\_124\_나라의_숫자

  * 삼진법을 이용
  * 0이 나오면 안되니까 0를 4로 변환

  ```python
  def solution(n):
      answer = ''
      while n > 0:
          n -= 1
          answer = '124'[n%3] + answer
          n //=3
      return answer
  ```

  

