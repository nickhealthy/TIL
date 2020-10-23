# Today I Learned

* 프론트엔드와 백엔드
  * 프론트엔드와 MVC 패턴
  * 프론트엔드
  * 백엔드
  * RESTful Framework
* 람다와 API 게이트웨이
* API Gateway 설정 (P132)
* API Gateway를 통한 Lambda함수 호출

---



### 프론트엔드와 백엔드

#### 프론트엔드와 MVC 패턴

* 프로트엔드의 클라이언트 단과 MVC (Model, View, Controll)와 같은 백엔드(데이터 가공 및 처리) 단이 나눠진 패턴
* 이 방식은 옛날 방식

#### 프론트엔드

* 최신트렌드
  * 리액트, 앵귤러 등 멀티 디바이스를 자동으로 지원해 개발자는 로직에만 집중이 가능

#### 백엔드

* 약간의 디비역량 및 사용자가 원하는 데이터를 가공할 수 있는 애플리케이션을 만들수 있는 역량
* HTTP Protocol 통신을 쉽게 구현할 수 있는 다양한 프로그래밍 언어가 존재
  * JAVA Spring, Python Django, Javascript Node.js 등
* 백엔드 종류
  * 인프라
  * DB
  * WAS(Web Application Service)

#### RESTful Framework

* HTTP Protocol & URL를 통해 데이터를 전달
* 자바스크립트가 **서버사이드 코드 호출 및 화면 제어**
* 서버사이드는 데이터를 가공 및 처리해서 클라이언트에 응답을 보냄

---



### 람다와 API 게이트웨이

![1. API 게이트웨이 + 람다](https://github.com/nickhealthy/TIL/blob/master/2020_10_23/img/1.%20API%20%EA%B2%8C%EC%9D%B4%ED%8A%B8%EC%9B%A8%EC%9D%B4%20%2B%20%EB%9E%8C%EB%8B%A4.PNG)

* 클라이언트에서 JWT를 이용해 API Gateway 접속한 후 람다 함수 호출



#### 사용자 프로필 람다 함수의 역할 생성

*AWSLambdaBasicExecutionRole*을 포함한 api-gateway-lambda-exec-role을 생성

#### 사용자 프로필 람다 함수 생성 (user-profile)

1. JSON Web Token을 검증
2. Auth0 엔드포인트를 호출해서 사용자에 대한 정보를 조회
3. 웹 사이트에 응답을 전송

#### user-profile 함수 작성

* JSON 

[npm-JsonWebToken 구성](https://www.npmjs.com/package/jsonwebtoken) 

[npm-request 구성](https://www.npmjs.com/package/request)

```cmd
C:\serverless> mkdir user-profile>
C:\serverless> cd user-profile>
C:\serverless\user-profile> npm init -y
# JWT 을 처리
C:\serverless\user-profile> npm install jsonwebtoken
# 요청을 처리
C:\serverless\user-profile> npm install request
```

#### 람다 함수 작성

* package.json
  * deploy 설정
  * predeploy 설정

```json
{
  "name": "user-profile",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "deploy": "aws lambda update-function-code --function-name arn:aws:lambda:us-east-1:869011496078:function:user-profile --zip-file fileb://Lambda-Deployment.zip",
    "predeploy": "zip -r Lambda-Deployment.zip * -x *.zip *.log",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "jsonwebtoken": "^8.5.1",
    "request": "^2.88.2"
  }
}
```



* **index.js 설정 = 구현체 설정**

```javascript
// 130페이지
'use strict';
 
var jwt = require('jsonwebtoken');
var request = require('request');
 
// 진입점
exports.handler = function(event, context, callback) {
    if (!event.authToken) {
        callback('Could not find authToken');
        return;
    }
 
    //  액세스 토큰값을 추출
    //  Bearer ACCESS_TOKEN_VALUE 
    //         ~~~~~~~~~~~~~~~~~~ => token
    var token = event.authToken.split(' ')[1];
 
    //  환경변수에서 AUTH0_SECRET 값을 추출
    var secretBuffer = process.env.AUTH0_SECRET;
    //  토큰 검증
    //  https://www.npmjs.com/package/jsonwebtoken
    //  jwt.verify(token, secretOrPublicKey, [options, callback])
    jwt.verity(token, secretBuffer, function(err, decoded) {
        if (err) {
            console.log(`Failed JWT verification: ${err}, auth: ${event.authToken}`);
            callback('Authoriztion Failed');
        } else {
            //  validates a JSON Web Token (JWT) (signature and expiration) and returns the user information associated with the user id sub property of the token.
            //  https://auth0.com/docs/api/authentication#user-profile16
            var body = {
                'id_token': token
            };
            var options = {
                //  auth0.com에 토큰 검증 및 사용자 정보 조회
                url: 'http://' + process.env.DOMAIN + '/tokeninfo',
                method: 'POST',
                json: true,
                body: body
            };
 
            request(options, function(error, response, body) {
                if (!error && response.statusCode == 200) {
                    //  callback 함수의 첫번째 파라미터는 오류를 나타내므로, 
                    //  오류가 없는 경우에는 null을 설정
                    callback(null, body);
                } else {
                    callback(error);
                }
            });
        }
    });
};
```

#### 람다 함수 배포

```cmd
C:\serverless\user-profile> npm run start
```

#### 람다 함수 테스트

![2. 람다함수테스트](https://github.com/nickhealthy/TIL/blob/master/2020_10_23/img/2.%20%EB%9E%8C%EB%8B%A4%ED%95%A8%EC%88%98%ED%85%8C%EC%8A%A4%ED%8A%B8.PNG)

---



### API Gateway 설정 (P132)

* API Gateway 설정을 통해 람다 함수를 호출할 수 있도록 설정
  * 리소스 자원
  * 메소드 자원
  * CORS 설정
    * 웹사이트에서 API에 접근 가능하도록
  * API 배포
* CORS (Cross-Origin Resource Sharing) 설정

#### 매핑 (p135)

* 브라우저 스크립트 ----> API Gateway ------> Lambda (user-profile)
  * ​     Authorization: xxxx						authToken
* Authorization 를 통해 받아온 토큰(JWT)를 이용해 Lambda함수에서 사용할 수 있도록 API Gateway와 연결
  * Authorization 헤더를 추출해 **Lambda의 이벤트 객체에** authToken으로 추가함

---



### API Gateway를 통한 Lambda함수 호출

![3. API게이트웨이를 통한 람다 호출](https://github.com/nickhealthy/TIL/blob/master/2020_10_23/img/3.%20API%EA%B2%8C%EC%9D%B4%ED%8A%B8%EC%9B%A8%EC%9D%B4%EB%A5%BC%20%ED%86%B5%ED%95%9C%20%EB%9E%8C%EB%8B%A4%20%ED%98%B8%EC%B6%9C.PNG)