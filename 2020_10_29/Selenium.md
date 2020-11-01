# Selenium

* Selenium 이란?
* Selenium 설치
* Selenium 사용하기
* 검색해보기 - Keys.enter()

---



### Selenium 이란?

웹 페이지 테스트 자동화를 할 수 있는 프레임워크

* 웹 페이지를 제어함

ex) 페이지 이동, 글자 입력, 클릭 등을 할 수 있음

---



### Selenium 설치

```python
pip install selenium
```



웹 드라이브 설치 (Chrome 이용)

* 크롬 페이지 - 환경설정 - 도움말 클릭 (버전확인)
* ChromeDriver 검색 후 맞는 버전을 설치

---



### Selenium 사용하기

```python
from selenium import webdriver

# chromedriver.exe 경로가 다르다면 Chrome() 함수 안에 경로를 입력
# browser가 webdriver.Chrome() 객체를 가져옴
browser = webdriver.Chrome()
browser.get("http://naver.com")
```

```python
# element를 가져옴
elem = browser.find_element_by_class_name("link_login")

# elem 클릭
elem.click()

# 뒤로가기
browser.back()

# 앞으로가기
browser.forward()

# 새로고침
browser.refresh()
```

---



### 검색해보기 - Keys.enter()

* 모듈 임포트 작업 필요

```python
from selenium.webdriver.common.keys import Keys
```

```python
# element를 찾아 글자 입력
# find_element_by_id() 함수 안에 <input> 태그의 속성 값을 적어줌
elem = browser.find_element_by_id("query")
elem.send_keys("개발자가 되고 싶어요")

from selenium.webdriver.common.keys import Keys
# Keys.enter() 사용하기
elem.send_keys(Keys.ENTER)
```

---



### 태그로 정보를 가져오기

```python
# a 태그 하나 가져오기
elem = browser.find_element_by_tag_name("a")

# 전체 a 태그 가져오기
elem = browser.find_elements_by_tag_name("a")

# href 속성 정보를 가져오기
# ex) https://~~~
for e in elem:
	e.get_attribute("href")


# 다른 페이지로 이동
# 다른 속성으로 가져오기
# 다음에 속성명 : name / 속성 값 : "q" 가져오기
browser.get("http://daum.net")
elem = browser.find_element_by_name("q")

elem.send_keys("개발자가 되고 싶어요")
elem.send_keys(Keys.ENTER)
```

---



### xpath 경로로 가져오기

```python
# 검색어 태그 찾기
elem = browser.find_element_by_name("q")
elem.send_keys("구글")

# xpath 메서드 사용
# 따옴표 겹침 주의
elem = browser.find_element_by_xpath("//*[@id='daumSearch']/fieldset/div/div/button[2]")

# 검색 창 클릭
elem.click()
```

---



### browser 탭 종료, 전체 종료

```python
# browser 탭 종료
browser.close()

# browser 전체 종료
browser.quit()
```

