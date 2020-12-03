# Today I Learned

* Amazon EventBridge 규칙 설정
* 파이썬 DB연동 - pymysql 라이브러리 with AWS-Lambda
* Lambda 함수에 배포 - venv 가상환경 패키지 포함

---



## Amazon EventBridge 규칙 설정

* 따릉이 공공데이터 API를 1시간 마다 호출하기 위해 `Amazon EventBridge` 사용 - `Lambda`와 연동

1. 원하는 대로 이름 및 설명 설정
2. 패턴 정의
   1. 이벤트 패턴 : 어떤 이벤트가 발생했을 때 동작하도록 설정 가능
   2. 일정 : `Linux`의 *Cron*처럼 시간 단위로 설정이 가능
3. 대상 선택 : 이벤트에 연결된 다음 대상을 정의
   * 대상은 한가지의 이벤트 혹은 일정으로 **여러 개의 대상을 선택이 가능**
4. 기타 옵션 설정은 default 및 생성



## 파이썬 DB연동 - pymysql 라이브러리 with AWS-Lambda

* 1시간 마다 호출되는 공공데이터 API : `TABEL A`에 바로 임포트
* 그동안 모은 공공데이터 정보 : `TABEL B`에 바로 임포트
* `TABEL A`와 `TABEL B`의 정보를 `inner join`으로 새로운 형태의 데이터를 `TABEL C`에 임포트



#### 소스코드

`DBImport_lambda.py`

* 1시간마다 따릉이 공공 데이터 API를 통한 DB 업데이트(AWS-Lambda 이용)

```python
import pymysql, os
from bs4 import BeautifulSoup
import urllib.request
import urllib.request as req
import json

def lambda_handler(event, context):    
    pymysql.install_as_MySQLdb()

    mysql_conn = pymysql.connect(host="호스트주소,\
        user="유저네임", password='패스워드', db="데이터베이스명", charset='utf8')
    mysql_cursor = mysql_conn.cursor()

    # DB 테이블 DROP
    drop_OneHoursql = "DROP TABLE IF EXISTS `everyOneHourAPI`"
    mysql_cursor.execute(drop_OneHoursql)
    
    # DB 테이블 & 컬럼 생성
    create_sql = """CREATE TABLE IF NOT EXiSTS `everyOneHourAPI` (
        `id` int(11) NOT NULL AUTO_INCREMENT PRIMARY KEY,
        `rackTotCnt` varchar(255),
        `stationName` varchar(255),
        `parkingBikeTotCnt` varchar(255),
        `shared` varchar(255),
        `stationId` varchar(255)
        )"""
    
    mysql_cursor.execute(create_sql)
    
    #서울 열린데이터 광장에서 openapi를 사용해 따릉이의 실시간 대여정보 받아오기
    client_key = '6165464657616b6132337959516847'
    num = 1000
    bike_url = 'http://openapi.seoul.go.kr:8088/' + client_key + '/json/bikeList/1/' + str(num)

    request = urllib.request.Request(bike_url)
    response = urllib.request.urlopen(request)
    rescode = response.getcode()

    dataList = ''
    if(rescode == 200):
        response_body = response.read()
        dataList = json.loads(response_body)

    else:
        print('오류 코드 : ' + rescode)

    for i in range(len(dataList['rentBikeStatus']['row'])):
        rackTotCnt = dataList['rentBikeStatus']['row'][i]['rackTotCnt']
        stationName = dataList['rentBikeStatus']['row'][i]['stationName']
        parkingBikeTotCnt = dataList['rentBikeStatus']['row'][i]['parkingBikeTotCnt']
        shared = dataList['rentBikeStatus']['row'][i]['shared']
        stationLatitude = dataList['rentBikeStatus']['row'][i]['stationLatitude']
        stationLongitude = dataList['rentBikeStatus']['row'][i]['stationLongitude']
        stationId = dataList['rentBikeStatus']['row'][i]['stationId']

        # print(rackTotCnt, stationName, parkingBikeTotCnt, shared, stationLatitude, stationLongitude, stationId)

        # REPLACE 다시 시도해봐야함
        update_sql = """REPLACE INTO `everyOneHourAPI` 
        (`rackTotCnt`, `stationName`, `parkingBikeTotCnt`, `shared`, `stationId`) 
        VALUES (%s, %s, %s, %s, %s)"""

        mysql_cursor.execute(update_sql, (rackTotCnt, stationName, parkingBikeTotCnt, shared, stationId))

    client_key = '6165464657616b6132337959516847'
    num = 2000
    bike_url = 'http://openapi.seoul.go.kr:8088/' + client_key + '/json/bikeList/1001/' + str(num)

    request = urllib.request.Request(bike_url)
    response = urllib.request.urlopen(request)
    rescode = response.getcode()

    dataList = ''
    if(rescode == 200):
        response_body = response.read()
        dataList = json.loads(response_body)

    else:
        print('오류 코드 : ' + rescode)

    for i in range(len(dataList['rentBikeStatus']['row'])):
        rackTotCnt = dataList['rentBikeStatus']['row'][i]['rackTotCnt']
        stationName = dataList['rentBikeStatus']['row'][i]['stationName']
        parkingBikeTotCnt = dataList['rentBikeStatus']['row'][i]['parkingBikeTotCnt']
        shared = dataList['rentBikeStatus']['row'][i]['shared']
        stationLatitude = dataList['rentBikeStatus']['row'][i]['stationLatitude']
        stationLongitude = dataList['rentBikeStatus']['row'][i]['stationLongitude']
        stationId = dataList['rentBikeStatus']['row'][i]['stationId']


        # REPLACE 다시 시도해봐야함
        update_sql = """REPLACE INTO `everyOneHourAPI` 
        (`rackTotCnt`, `stationName`, `parkingBikeTotCnt`, `shared`, `stationId`) 
        VALUES (%s, %s, %s, %s, %s)"""

        mysql_cursor.execute(update_sql, (rackTotCnt, stationName, parkingBikeTotCnt, shared, stationId))

    client_key = '6165464657616b6132337959516847'
    num = 2083
    bike_url = 'http://openapi.seoul.go.kr:8088/' + client_key + '/json/bikeList/2001/' + str(num)

    request = urllib.request.Request(bike_url)
    response = urllib.request.urlopen(request)
    rescode = response.getcode()

    dataList = ''
    if(rescode == 200):
        response_body = response.read()
        dataList = json.loads(response_body)

    else:
        print('오류 코드 : ' + rescode)

    for i in range(len(dataList['rentBikeStatus']['row'])):
        rackTotCnt = dataList['rentBikeStatus']['row'][i]['rackTotCnt']
        stationName = dataList['rentBikeStatus']['row'][i]['stationName']
        parkingBikeTotCnt = dataList['rentBikeStatus']['row'][i]['parkingBikeTotCnt']
        shared = dataList['rentBikeStatus']['row'][i]['shared']
        stationLatitude = dataList['rentBikeStatus']['row'][i]['stationLatitude']
        stationLongitude = dataList['rentBikeStatus']['row'][i]['stationLongitude']
        stationId = dataList['rentBikeStatus']['row'][i]['stationId']


        # REPLACE 다시 시도해봐야함
        update_sql = """REPLACE INTO `everyOneHourAPI` 
        (`rackTotCnt`, `stationName`, `parkingBikeTotCnt`, `shared`, `stationId`) 
        VALUES (%s, %s, %s, %s, %s)"""

        mysql_cursor.execute(update_sql, (rackTotCnt, stationName, parkingBikeTotCnt, shared, stationId))

    mysql_conn.commit()
    mysql_conn.close()
```



`DB-bicycleLocation.py`

* AWS-Lambda와 AWS-RDS 연동 후 DB 정보 조회 및 데이터 핸들링 (컬럼 정보 추가)
  * `mysql_cursor.description`

```python
import pymysql

def lambda_handler(event, context):
    pymysql.install_as_MySQLdb()
    mysql_conn = pymysql.connect(host="db-bigdata-bicycle.cynn0zdjjttk.us-east-1.rds.amazonaws.com",\
    user="admin", password='chaosproject', db="chaos", charset='utf8')

    mysql_cursor = mysql_conn.cursor()

    get_sql = "SELECT * FROM `localInformation`"

    mysql_cursor.execute(get_sql)
    col_headers = [x[0] for x in mysql_cursor.description]
    print(col_headers)

    rows = mysql_cursor.fetchall()

    json_data = []
    for result in rows:
        json_data.append(dict(zip(col_headers, result)))

    mysql_conn.commit()
    mysql_conn.close()

    return json_data
```





## Lambda 함수에 배포 - venv 가상환경 패키지 포함

1. 가상 환경 생성

```python
$ python -m venv venv
```

2. 환경 활성화

```python
$ source venv/script/activate
```

3. pip를 사용해서 라이브러리 설치

```python
$ pip install pymysql
```

4. venv 가상환경 비활성화

```python
$ deactivate
```

5. 라이브러리의 내용을 포함하는 `ZIP` 아카이브 생성

```python
$ cd venv/lib/python3.7/site-packages

$ zip -r9 ${OLDPWD}/function.zip .
```

* 라이브러리에 따라 `site-packages` 또는 `dist-packages`에 종속 항목이 나타날 수 있으며, 가상 환경의 첫 폴더는 `Lib/Lib64` 일 수도 있음
  * `pip show module_name`을 통해 해당 위치를 찾을 수 있음

6. 아카이브에 함수 코드를 추가

```python
# 최상위 폴더로 돌아가기
$ cd $OLDPWD
# zip 파일에 넣을 소스코드명(lambda_function.py)
$ zip -g function.zip lambda_function.py
```

7. [fileb://](https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters-file.html#cli-usage-parameters-file-binary) 접두사를 사용하여 이진 ZIP 배포 패키지를 Lambda에 업로드하고 함수 코드를 업데이트

```python
$ aws lambda update-function-code --function-name EveryHour-Get-Bicycle-API --zip-file fileb://function.zip
```

