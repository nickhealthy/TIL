# Today I Learned

* `sqlalchemy(ORM)`
  * Pandas DataFrame to_sql 메서드 사용
* AWS RDS 공부
* AWS RDS와 Python 프로젝트(웹 스크래핑 데이터) 연동
  * 웹 스크래핑 결과를 `pymysql`과 `sqlalchemy(ORM)`, `Pandas`, `numpy`를 통해 RDS(Aurora 엔진) 연결 및 데이터 임포트
* Selenium 공부

----



### sqlalchemy(ORM)

1. Java에서의 `Mybatis`처럼 **SQL 연동**을 간편하게 연동하는 기능
2. ORM(Object Relation Mapping) : 오브젝트와 RDB 사이에 존재하는 개념과 접근 방법, 성격의 차이 때문에 요구되는 불편한 작업을 제거해줘서 개발자가 오브젝트를 가지고 정보를 다루면 ORM 프레임워크가 이를 **RDB에 적절한 형태로 변환해 주거나 그 반대로 RDB에 저장되어 있는 정보를 오브젝트가 다루기 쉬운 형태로 변환해 주는 것**을 말함
3. Python에서는 `sqlalchemy` 모듈을 사용



#### Pandas DataFrame to_sql 메서드 사용

* SQLalchemy 설치

```python
pip install sqlalchemy
```

* 엔진 생성 및 DB연동

```python
# mysqlDB 설치
pymysql.install_as_MySQLdb()

# create_engine (SQL DB 연결 라이브러리) 임포트
from sqlalchemy import create_engine

# mysqlclient 연동 (mysql+mysqldb)
engine = create_engine("mysql+mysqldb://아이디:암호@database-1-instance-1.cynn0zdjjttk.us-east-1.rds.amazonaws.com:포트번호/데이터베이스이름?charset=utf8", encoding="utf-8")

# sql에 데이터 임포트
item_df.to_sql(name="테이블명", con=engine, if_exists='replace', index=True, index_label='id')
```

* if_exists='append' 옵션이 있으면, 기존 테이블에 데이터를 추가로 넣음
* if_exists='fail' 옵션이 있으면, 기존 테이블이 있을 경우, 아무일도 하지 않음
* if_exists='replace' 옵션이 있으면, 기존 테이블이 있을 경우, 기존 테이블을 삭제하고, 다시 테이블을 만들어서, 새로 데이터를 넣음

---



### AWS RDS 공부

#### 개념

클라우드에서 관계형 데이터베이스를 더 쉽게 설치, 운영 및 확장할 수 있는 웹 서비스

#### 기능

백업, 소프트웨어 패치, 자동 장애 감지 및 복구를 관리함

#### 생성

1. 서비스 RDS 선택
2. 데이터베이스 생성 클릭
3. 사용할 DB엔진 선택
4. DB옵션 설정
   * DB 인스턴스 클래스 : DB서버를 이용할 때 컴퓨터 사양 지정
   * DB 인스턴스 : 클라우드에 있는 DB 환경
   * 다중 AZ (Availability zone) 배포 : 복제본 옵션을 선택하면 다른 가용성 지역에 복제가 됨
     * DB를 저장하고 있는 가용성 지역에서 문제가 발생할 경우 복사본(백업) 기능
   * 스토리지 유형 : 데이터를 저장하는 디스크의 유형
   * DB 인스턴스 식별자 : DB 인스턴스의 이름 (구별하기 위한 별칭)
   * 마스터 사용자 이름 및 암호 : DB에 접속할 사용자 아이디, 암호
   * VPC (Virtual Private Cloud) : AWS안에서 웹으로부터 독립된 안전한 네트워크를 구성해주는 서비스
   * 서브넷 그룹 : VPC에서 DB 인스턴스의 사용 IP범위 지정
   * VPC 보안 그룹 : 같은 VPC에 연결하는 것이 아니라 지정한 것끼리만 접속할 때 사용
5. 모든 설정을 마친 후, DB 인스턴스 생성

---



### AWS RDS와 Python 프로젝트(웹 스크래핑 데이터) 연동

* 탐앤탐스 (Selenium, Beautifulsoup 사용)

  * 비동기식 히든 태그로인해 Selenium 사용

* Selenium 클릭이 안될 시

  * ```python
    elem = browser.find_element_by_xpath("//*[@id='plusmode_tb_product_paging_area']/button")
    browser.execute_script("arguments[0].click();", elem)
    ```

* 전체 소스코드

```python
from selenium import webdriver
from time import sleep
from bs4 import BeautifulSoup
import pymysql, pandas as pd, numpy as np
from sqlalchemy import create_engine


browser = webdriver.Chrome()
browser.get("https://tomntoms.com/menu/menu.html?depth1=1&depth2%5B%5D=4")
browser.maximize_window()
browser.refresh()

elem = browser.find_element_by_xpath("//*[@id='plusmode_tb_product_paging_area']/button")
for i in range(4):
    browser.execute_script("arguments[0].click();", elem)

sleep(3)
browser.find_elements_by_class_name("menu-list-item")
soup = BeautifulSoup(browser.page_source, "html.parser")

items_properties = soup.find_all("div", attrs={"class":"menu-list-item"})
item_lst = []
for item in items_properties:
    try:
        item_dic = {}

        name = item.find("h3", attrs={"class":"tit"})
        item_dic['name'] = name.get_text()

        kcal = item.tr
        item_dic['kcal'] = int(kcal.td.span.get_text())

        sugar = kcal.find_next_sibling("tr")
        sugar = str(sugar.td.span.get_text())
        if sugar == ' - ':
            sugar = 0
            item_dic['sugar'] = sugar
        item_dic['sugar'] = int(sugar)

        caffeine = item.find("tr")
        caffeine = caffeine.find_next_sibling("tr")
        caffeine = caffeine.find_next_sibling("tr")
        caffeine = caffeine.td
        caffeine = str(caffeine.find_next_sibling("td").get_text())
        if caffeine == ' - ':
            caffeine = 0
            item_dic['caffeine'] = caffeine
        item_dic['caffeine'] = int(caffeine)

        image = item.div['style']
        image = "https://tomntoms.com/" + image[45:99]
        item_dic['image'] = image

        item_lst.append(item_dic)
    except:
        pass

print(item_lst)

# mysqlDB 설치
pymysql.install_as_MySQLdb()
# mysqlclient (mysql+mysqldb)
engine = create_engine("mysql+mysqldb://사용자ID:비밀번호@database-1-instance-1.cynn0zdjjttk.us-east-1.rds.amazonaws.com:3306/데이터베이스이름?charset=utf8", encoding="utf-8")
conn = engine.connect()

# DataFrame 설정
data_df = pd.DataFrame()
for item in item_lst:
    obj = pd.Series(item)
    data_df = data_df.append(obj, ignore_index=True)

# 현재 size 컬럼 없음
item_df = data_df.loc[:,['name', 'kcal', 'sugar', 'caffeine', 'image']]
# 컬럼이름 조정
item_df.columns = ["이름", "칼로리", "당류", "카페인", "이미지"]
# index 설정
item_df.index = np.arange(1, len(item_df)+1)
# sql에 데이터 임포트
item_df.to_sql(name="테이블명", con=engine, if_exists='replace', index=True, index_label='id')
# DB 연결해제
conn.close()

```

* 결과

![1. RDS 인스턴스에 데이터 임포트](C:\dev\TIL\2020_11_01\1. RDS 인스턴스에 데이터 임포트.PNG)



* 엔질리너스 수정

```python
import requests, os, sqlalchemy, pymysql, pandas as pd, numpy as np
from sqlalchemy import create_engine
from bs4 import BeautifulSoup


global dir_name
dir_name = 'img/'

headers = {
    "referer":"https://www.angelinus.com/Menu/Menu.asp?MenuIdx=4#",
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.111 Safari/537.36",
}

mypage = [42, 43, 45, 46, 47, 48, 50, 275, 430, 536, 542, 608, 609, 695, 696]

# ajax 통신
# 음료 상세정보 추출
item_lst = []
for i in mypage:

    ajax_url = "https://www.angelinus.com/Menu/Menu_Ajax.asp?MenuIdx=4&Idx={}".format(i)

    req_ajax = requests.get(ajax_url, headers=headers, verify=False)
    req_ajax.raise_for_status()

    soup2 = BeautifulSoup(req_ajax.text, "html.parser")
    
    item_dict = {}
    items_propertys = soup2.find("div", attrs={"class":"right"})
    name = items_propertys.h3.get_text()
    item_dict['name'] = name
    
    size = items_propertys.td.find_next_sibling("td")
    item_dict['size'] = int(size.get_text())
    kcal = size.find_next_sibling("td")
    item_dict['kcal'] = int(kcal.get_text())
    sugar = kcal.find_next_sibling("td").get_text()
    
    if sugar[1] == "(":
        sugar = int(sugar[:1])
    else:
        sugar = int(sugar[:2])
    item_dict['sugar'] = int(sugar)

    caffeine = soup2.find("table", attrs={"class":"nutrition"}).find_next_sibling("table").tbody
    caffeine = caffeine.find("td", attrs={"class":"end"})
    item_dict['caffeine'] = int(caffeine.get_text())

    picture = soup2.find("img", attrs={"class":"img_big"})['src']
    html = "https://www.angelinus.com"
    item_dict['image'] = html+picture
    
    item_lst.append(item_dict)

print(item_lst)
    # 보류
    # 폴더 생성
    # if not os.path.isdir('img'):
    #     os.mkdir('img')
    # 사진 가져오기
    # picture_res = requests.get(html+picture, headers=headers, verify=False)
    # with open(dir_name+'/'+"picture{}.jpg".format(i-42), "wb") as f:
    #     f.write(picture_res.content)

pymysql.install_as_MySQLdb()
engine = create_engine("mysql+mysqldb://사용자ID:비밀번호@database-1-instance-1.cynn0zdjjttk.us-east-1.rds.amazonaws.com:3306/데이터베이스이름?charset=utf8", encoding="utf-8")
conn = engine.connect()

data_df = pd.DataFrame()
for item in item_lst:
    obj = pd.Series(item)
    data_df = data_df.append(obj, ignore_index=True)

data_df = data_df.loc[:,['name', 'size', 'kcal', 'sugar', 'caffeine', 'image']]
data_df.columns = ["이름", "사이즈", "칼로리", "당류", "카페인", "이미지"]
data_df.index = np.arange(1, len(data_df) + 1)

data_df.to_sql(name="테이블명", con=engine, if_exists='replace', index=True, index_label='id')
conn.close()
```

