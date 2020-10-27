# Web_Scraping_for_Project

* 웹 스크래핑 (엔젤리너스)

* MariaDB 사용자 계정 및 DB 생성 완료.

---



### 웹 스크래핑 (엔젤리너스)

* 웹 스크래핑 (엔젤리너스) 완료.
  * 사진, 제품 세부 정보, dict 형식으로 정리 후 `JSON` 포맷으로 `MairaDB`에 넣을 예정
* `pymysql` 이용해서 데이터베이스에 데이터 넣는 작업 진행중

```python
import requests, re, os
from bs4 import BeautifulSoup


global dir_name
dir_name = 'img/'

headers = {
    "referer":"https://www.angelinus.com/Menu/Menu.asp?MenuIdx=4#",
    "User-Agent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.111 Safari/537.36",
}


# ajax 통신
# 음료 상세정보 추출
item_lst = []
for i in range(42, 51):
    if i == 44:
        continue
    if i == 49:
        continue

    ajax_url = "https://www.angelinus.com/Menu/Menu_Ajax.asp?MenuIdx=4&Idx={}".format(i)

    req_ajax = requests.get(ajax_url, headers=headers, verify=False)
    req_ajax.raise_for_status()

    soup2 = BeautifulSoup(req_ajax.text, "html.parser")
    
    item_dict = {}
    items_propertys = soup2.find("div", attrs={"class":"right"})
    name = items_propertys.h3.get_text()
    item_dict['name'] = name
    detail = soup2.find("p", attrs={"class":"detail"}).get_text()
    item_dict['detail'] = detail
    # find_next_siblings("th").get_text()
    division = items_propertys.td.get_text()
    item_dict['division'] = division
    Coffee_capacity = items_propertys.td.find_next_sibling("td")
    item_dict['Coffee_capacity'] = Coffee_capacity.get_text()
    kal = Coffee_capacity.find_next_sibling("td")
    item_dict['kal'] = kal.get_text()
    carbohydrate = kal.find_next_sibling("td").get_text()
    
    # 정규표현식 '(' 없애기
    p = re.compile(carbohydrate)
    if carbohydrate[1] == "(":
        carbohydrate = carbohydrate[:1]
    else:
        carbohydrate = carbohydrate[:2]
    item_dict['carbohydrate'] = carbohydrate

    # body > div > div > div.right > table:nth-child(6) > tbody > tr > td:nth-child(1)
    protein = soup2.find("table", attrs={"class":"nutrition"})
    protein = protein.find_next_sibling("table")
    protein = protein.td
    item_dict['protein'] = protein.get_text()
    
    Saturated_fat = protein.find_next_sibling("td")
    item_dict['Saturated fat'] = Saturated_fat.get_text()
    
    salt = Saturated_fat.find_next_sibling("td")
    item_dict['salt'] = salt.get_text()

    Caffeine = salt.find_next_sibling("td")
    item_dict['Caffeine'] = Caffeine.get_text()

    item_lst.append(item_dict)

    # 폴더 생성
    if not os.path.isdir('img'):
        os.mkdir('img')
    
    # 사진 가져오기
    picture = soup2.find("img", attrs={"class":"img_big"})['src']
    html = "https://www.angelinus.com"
    # print(html+picture)
    picture_res = requests.get(html+picture, headers=headers, verify=False)
    with open(dir_name+'/'+"picture{}.jpg".format(i-42), "wb") as f:
        f.write(picture_res.content)


# 아메리치노
url = "https://www.angelinus.com/Menu/Menu_Ajax.asp?MenuIdx=4&Idx=275"
req = requests.get(url, headers=headers, verify=False)
req.raise_for_status()

soup = BeautifulSoup(req.text, "html.parser")
item_dict = {}
items_propertys = soup.find("div", attrs={"class":"right"})
name = items_propertys.h3.get_text()
item_dict['name'] = name
detail = soup.find("p", attrs={"class":"detail"}).get_text()
item_dict['detail'] = detail
# find_next_siblings("th").get_text()
division = items_propertys.td.get_text()
item_dict['division'] = division
Coffee_capacity = items_propertys.td.find_next_sibling("td")
item_dict['Coffee_capacity'] = Coffee_capacity.get_text()
kal = Coffee_capacity.find_next_sibling("td")
item_dict['kal'] = kal.get_text()
carbohydrate = kal.find_next_sibling("td").get_text()


if carbohydrate[1] == "(":
    carbohydrate = carbohydrate[:1]
else:
    carbohydrate = carbohydrate[:2]
item_dict['carbohydrate'] = carbohydrate

# body > div > div > div.right > table:nth-child(6) > tbody > tr > td:nth-child(1)
protein = soup.find("table", attrs={"class":"nutrition"})
protein = protein.find_next_sibling("table")
protein = protein.td
item_dict['protein'] = protein.get_text()

Saturated_fat = protein.find_next_sibling("td")
item_dict['Saturated fat'] = Saturated_fat.get_text()

salt = Saturated_fat.find_next_sibling("td")
item_dict['salt'] = salt.get_text()

Caffeine = salt.find_next_sibling("td")
item_dict['Caffeine'] = Caffeine.get_text()
item_lst.append(item_dict)

picture = soup.find("img", attrs={"class":"img_big"})['src']
html = "https://www.angelinus.com"
picture_res = requests.get(html+picture, headers=headers, verify=False)
with open(dir_name+'/'+"picture{}.jpg".format(9), "wb") as f:
    f.write(picture_res.content)


# 콜드브루 To-Go
url = "https://www.angelinus.com/Menu/Menu_Ajax.asp?MenuIdx=4&Idx=430"
req = requests.get(url, headers=headers, verify=False)
req.raise_for_status()

soup = BeautifulSoup(req.text, "html.parser")
item_dict = {}
items_propertys = soup.find("div", attrs={"class":"right"})
name = items_propertys.h3.get_text()
item_dict['name'] = name
detail = soup.find("p", attrs={"class":"detail"}).get_text()
item_dict['detail'] = detail
# find_next_siblings("th").get_text()
division = items_propertys.td.get_text()
item_dict['division'] = division
Coffee_capacity = items_propertys.td.find_next_sibling("td")
item_dict['Coffee_capacity'] = Coffee_capacity.get_text()
kal = Coffee_capacity.find_next_sibling("td")
item_dict['kal'] = kal.get_text()
carbohydrate = kal.find_next_sibling("td").get_text()


if carbohydrate[1] == "(":
    carbohydrate = carbohydrate[:1]
else:
    carbohydrate = carbohydrate[:2]
item_dict['carbohydrate'] = carbohydrate

# body > div > div > div.right > table:nth-child(6) > tbody > tr > td:nth-child(1)
protein = soup.find("table", attrs={"class":"nutrition"})
protein = protein.find_next_sibling("table")
protein = protein.td
item_dict['protein'] = protein.get_text()

Saturated_fat = protein.find_next_sibling("td")
item_dict['Saturated fat'] = Saturated_fat.get_text()

salt = Saturated_fat.find_next_sibling("td")
item_dict['salt'] = salt.get_text()

Caffeine = salt.find_next_sibling("td")
item_dict['Caffeine'] = Caffeine.get_text()
item_lst.append(item_dict)

picture = soup.find("img", attrs={"class":"img_big"})['src']
html = "https://www.angelinus.com"
picture_res = requests.get(html+picture, headers=headers, verify=False)
with open(dir_name+'/'+"picture{}.jpg".format(10), "wb") as f:
    f.write(picture_res.content)


# 아메리치노 라떼
url = "https://www.angelinus.com/Menu/Menu_Ajax.asp?MenuIdx=4&Idx=536"
req = requests.get(url, headers=headers, verify=False)
req.raise_for_status()

soup = BeautifulSoup(req.text, "html.parser")
item_dict = {}
items_propertys = soup.find("div", attrs={"class":"right"})
name = items_propertys.h3.get_text()
item_dict['name'] = name
detail = soup.find("p", attrs={"class":"detail"}).get_text()
item_dict['detail'] = detail
# find_next_siblings("th").get_text()
division = items_propertys.td.get_text()
item_dict['division'] = division
Coffee_capacity = items_propertys.td.find_next_sibling("td")
item_dict['Coffee_capacity'] = Coffee_capacity.get_text()
kal = Coffee_capacity.find_next_sibling("td")
item_dict['kal'] = kal.get_text()
carbohydrate = kal.find_next_sibling("td").get_text()


if carbohydrate[1] == "(":
    carbohydrate = carbohydrate[:1]
else:
    carbohydrate = carbohydrate[:2]
item_dict['carbohydrate'] = carbohydrate

# body > div > div > div.right > table:nth-child(6) > tbody > tr > td:nth-child(1)
protein = soup.find("table", attrs={"class":"nutrition"})
protein = protein.find_next_sibling("table")
protein = protein.td
item_dict['protein'] = protein.get_text()

Saturated_fat = protein.find_next_sibling("td")
item_dict['Saturated fat'] = Saturated_fat.get_text()

salt = Saturated_fat.find_next_sibling("td")
item_dict['salt'] = salt.get_text()

Caffeine = salt.find_next_sibling("td")
item_dict['Caffeine'] = Caffeine.get_text()
item_lst.append(item_dict)

picture = soup.find("img", attrs={"class":"img_big"})['src']
html = "https://www.angelinus.com"
picture_res = requests.get(html+picture, headers=headers, verify=False)
with open(dir_name+'/'+"picture{}.jpg".format(11), "wb") as f:
    f.write(picture_res.content)


# 오트밀 라떼
url = "https://www.angelinus.com/Menu/Menu_Ajax.asp?MenuIdx=4&Idx=542"
req = requests.get(url, headers=headers, verify=False)
req.raise_for_status()

soup = BeautifulSoup(req.text, "html.parser")
item_dict = {}
items_propertys = soup.find("div", attrs={"class":"right"})
name = items_propertys.h3.get_text()
item_dict['name'] = name
detail = soup.find("p", attrs={"class":"detail"}).get_text()
item_dict['detail'] = detail
# find_next_siblings("th").get_text()
division = items_propertys.td.get_text()
item_dict['division'] = division
Coffee_capacity = items_propertys.td.find_next_sibling("td")
item_dict['Coffee_capacity'] = Coffee_capacity.get_text()
kal = Coffee_capacity.find_next_sibling("td")
item_dict['kal'] = kal.get_text()
carbohydrate = kal.find_next_sibling("td").get_text()


if carbohydrate[1] == "(":
    carbohydrate = carbohydrate[:1]
else:
    carbohydrate = carbohydrate[:2]
item_dict['carbohydrate'] = carbohydrate

# body > div > div > div.right > table:nth-child(6) > tbody > tr > td:nth-child(1)
protein = soup.find("table", attrs={"class":"nutrition"})
protein = protein.find_next_sibling("table")
protein = protein.td
item_dict['protein'] = protein.get_text()

Saturated_fat = protein.find_next_sibling("td")
item_dict['Saturated fat'] = Saturated_fat.get_text()

salt = Saturated_fat.find_next_sibling("td")
item_dict['salt'] = salt.get_text()

Caffeine = salt.find_next_sibling("td")
item_dict['Caffeine'] = Caffeine.get_text()
item_lst.append(item_dict)

picture = soup.find("img", attrs={"class":"img_big"})['src']
html = "https://www.angelinus.com"
picture_res = requests.get(html+picture, headers=headers, verify=False)
with open(dir_name+'/'+"picture{}.jpg".format(12), "wb") as f:
    f.write(picture_res.content)


# 아메리치노 흑당, 아메리치노 흑당라떼
for i in range(608, 610):
    url = "https://www.angelinus.com/Menu/Menu_Ajax.asp?MenuIdx=4&Idx={}".format(i)
    req = requests.get(url, headers=headers, verify=False)
    req.raise_for_status()

    soup = BeautifulSoup(req.text, "html.parser")
    item_dict = {}
    items_propertys = soup.find("div", attrs={"class":"right"})
    name = items_propertys.h3.get_text()
    item_dict['name'] = name
    detail = soup.find("p", attrs={"class":"detail"}).get_text()
    item_dict['detail'] = detail
    # find_next_siblings("th").get_text()
    division = items_propertys.td.get_text()
    item_dict['division'] = division
    Coffee_capacity = items_propertys.td.find_next_sibling("td")
    item_dict['Coffee_capacity'] = Coffee_capacity.get_text()
    kal = Coffee_capacity.find_next_sibling("td")
    item_dict['kal'] = kal.get_text()
    carbohydrate = kal.find_next_sibling("td").get_text()


    if carbohydrate[1] == "(":
        carbohydrate = carbohydrate[:1]
    else:
        carbohydrate = carbohydrate[:2]
    item_dict['carbohydrate'] = carbohydrate

    # body > div > div > div.right > table:nth-child(6) > tbody > tr > td:nth-child(1)
    protein = soup.find("table", attrs={"class":"nutrition"})
    protein = protein.find_next_sibling("table")
    protein = protein.td
    item_dict['protein'] = protein.get_text()

    Saturated_fat = protein.find_next_sibling("td")
    item_dict['Saturated fat'] = Saturated_fat.get_text()

    salt = Saturated_fat.find_next_sibling("td")
    item_dict['salt'] = salt.get_text()

    Caffeine = salt.find_next_sibling("td")
    item_dict['Caffeine'] = Caffeine.get_text()
    item_lst.append(item_dict)

    picture = soup.find("img", attrs={"class":"img_big"})['src']
    html = "https://www.angelinus.com"
    picture_res = requests.get(html+picture, headers=headers, verify=False)
    with open(dir_name+'/'+"picture{}.jpg".format(i-595), "wb") as f:
        f.write(picture_res.content)


# 카페 연유다, 카페 코코다
for i in range(695, 697):
    url = "https://www.angelinus.com/Menu/Menu_Ajax.asp?MenuIdx=4&Idx={}".format(i)
    req = requests.get(url, headers=headers, verify=False)
    req.raise_for_status()

    soup = BeautifulSoup(req.text, "html.parser")
    item_dict = {}
    items_propertys = soup.find("div", attrs={"class":"right"})
    name = items_propertys.h3.get_text()
    item_dict['name'] = name
    detail = soup.find("p", attrs={"class":"detail"}).get_text()
    item_dict['detail'] = detail
    # find_next_siblings("th").get_text()
    division = items_propertys.td.get_text()
    item_dict['division'] = division
    Coffee_capacity = items_propertys.td.find_next_sibling("td")
    item_dict['Coffee_capacity'] = Coffee_capacity.get_text()
    kal = Coffee_capacity.find_next_sibling("td")
    item_dict['kal'] = kal.get_text()
    carbohydrate = kal.find_next_sibling("td").get_text()


    if carbohydrate[1] == "(":
        carbohydrate = carbohydrate[:1]
    else:
        carbohydrate = carbohydrate[:2]
    item_dict['carbohydrate'] = carbohydrate

    # body > div > div > div.right > table:nth-child(6) > tbody > tr > td:nth-child(1)
    protein = soup.find("table", attrs={"class":"nutrition"})
    protein = protein.find_next_sibling("table")
    protein = protein.td
    item_dict['protein'] = protein.get_text()

    Saturated_fat = protein.find_next_sibling("td")
    item_dict['Saturated fat'] = Saturated_fat.get_text()

    salt = Saturated_fat.find_next_sibling("td")
    item_dict['salt'] = salt.get_text()

    Caffeine = salt.find_next_sibling("td")
    item_dict['Caffeine'] = Caffeine.get_text()
    item_lst.append(item_dict)

    
    picture = soup.find("img", attrs={"class":"img_big"})['src']
    html = "https://www.angelinus.com"
    picture_res = requests.get(html+picture, headers=headers, verify=False)
    with open(dir_name+'/'+"picture{}.jpg".format(i-680), "wb") as f:
        f.write(picture_res.content)

print(item_lst)
```

---



### MariaDB 사용자 계정 및 DB 생성 완료.

```mysql
mysql -u root -p

// 사용중인 계정 조회
use mysql
select host, user, password from user;

// 계정 생성
create user 계정이름@localhost identified by '비밀번호'  // localhost용
create user 계정이름@'%' identified by '비밀번호';      //%는 외부접속 허용

// 권한 생성
grant select on db스키마.* to `계정이름`@`localhost` identified by '비밀번호';
grant select on db스키마.* to `계정이름`@`%` identified by '비밀번호';  

// 모든 권한 부여
grant all privileges on 스키마.* to '계정이름'@'localhost' idenetified by '비밀번호';
grant all privileges on 스키마.* to '계정이름'@'%' idenetified by '비밀번호';

// 수정사항 반영
flush privileges;
```

