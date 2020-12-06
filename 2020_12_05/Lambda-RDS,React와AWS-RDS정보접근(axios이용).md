# Today I Learned

* AWS-Lambda로 RDS 접근 및 데이터 가져오기
* React와 AWS-RDS 연결하기 (Axios 이용)

---



## AWS-Lambda로 RDS 접근 및 데이터 가져오기

(차후 개선사항)`AWS Secrets Manager` 앞단에 배치하여 **보안을 강화**

1. AWS 콘솔 창에서 람다 함수 생성
   * function_name : `DB-bicycleLocation`

2. 파이썬 코드 작성

`DB-bicycleLocation.py`

* 따릉이 전 배치소의 위도와 경도가 표시되어 있는 `localInformation` 테이블에 접근하는 코드

```python
import pymysql

def lambda_handler(event, context):
    pymysql.install_as_MySQLdb()
    mysql_conn = pymysql.connect(host="호스트 주소",\
    user="유저이름", password='비밀번호', db="데이터베이스명", charset='utf8')

    mysql_cursor = mysql_conn.cursor()

    get_sql = "SELECT * FROM `localInformation`"

    mysql_cursor.execute(get_sql)
    # [x[0] for x in mysql_cursor.description] = columns의 정보를 가져옴
    col_headers = [x[0] for x in mysql_cursor.description]
    print(col_headers)

    rows = mysql_cursor.fetchall()

    json_data = []
    for result in rows:
        # json_date 리스트에 dict-zip 함수로 column과 각 row 정보들을 묶어 append
        json_data.append(dict(zip(col_headers, result)))

    mysql_conn.commit()
    mysql_conn.close()

    return json_data
```

3. `zip` 이진 파일 아카이브 형태로 업로드하기 위해 필요한 의존성 패키지 다운로드
   * 폴더를 하나 만들어서 작업하는 것을 추천!

```python
# -t . << 현재 위치 안에 라이브러리를 직접 설치
$ pip install pymysql -t .
```

4. `zip` 이진 파일 아카이브 생성

```cmd
# zip 명령어 이용
# -r9 << 하위 폴더까지 모두 포함해서 zip으로 만듬
# function.zip << 만들어질 파일명 이름 및 확장자
# . << 현재 위치
$ zip -r9 function.zip .
```

5. `zip` 이진 아카이브 파일에 `python` 코드를 추가 = 배포 패키지라고도 불리며, 이러한 행위를 하는 것을 '배포 패키징화' 한다고 함

```cmd
$ zip -g function.zip DB-bicycleLocation.py
```

6. zip 파일을 Lambda 함수에 직접 업로드

※ 람다 함수 안에서 런타임 설정은 설명 제외



## React와 AWS-RDS 연결하기 (Axios 이용)

※ AWS-RDS 설정과 데이터는 모두 완료되어 있다고 가정

1. AWS-APIGateway를 통한 AWS-Lambda endpoint를 axios 모듈을 이용해 연결
   * 함수 형태로 받아와 객체의 개수만큼 자바스크립트의 `map()` 함수를 통해 전체적으로 뿌려주었다.
   * `componentDidMount()` :  마운트된 후 실행되야 할 코드
     * 리액트 컴포넌트의 라이프 사이클 참고

`APP.js`

```react
class App extends Component {
  state = {
    loading: false,
    locationList: [],
  };

  loadlocation = async () => {
    axios
      .get(
        'APIGateway endpoint 주소'
      )
      .then(({ data }) => {
        this.setState({
          loading: true,
          locationList: data,
        });
      })
      .catch((e) => {
        console.error(e);
        this.setState({
          loading: false,
        });
      });
  };

  componentDidMount() {
    this.loadlocation();
  }

  render() {
    const { locationList } = this.state;
    console.log(locationList);
    return (
        // 하단 생략...
```

