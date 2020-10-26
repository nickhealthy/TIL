# Today I Learned

* 10/23 Review
* user-profile Lambda Function
* API Gateway 생성 후 테스트하면 아래와 같은 오류가 발생
* API Gateway의 권한 부여자를 이용해서 JWT 토큰 검증 후 유효한 토큰인 경우에만 람다를 호출할 수 있도록 제한
* 사용자 프로필 버튼을 클릭했을 때 정상 여부 확인
* 람다 함수를 호출하는 두 가지 방법
* 비동기 폭포(Async waterfall)
* 람다 CLI 사용

---



### 10/23 Review

각종 JavaScript 파일 로드

로그인 버튼 누르면 auth0.com에서 제공하는 auth0.lock 호출 (로그인 창 - 구글, 카카오에도 요청)

* `authorization`를 넘겨줌

서비스(로컬 호스트 브라우저 창)에서 **엑세스 토큰을 받아 로컬 스토리지에 저장**

* 보안상의 이유로 다이렉트로 auth0로 바로 가서 사용자 정보를 가져오지 않고, auth0의 엑세스 토큰, ID토큰 등을 **람다 함수를 거쳐서 가져옴**
* Lambda에서 가져온 정보는 아무에게나 인증정보를 주면 안되기때문에 API Gateway를(인증정보 권한 관리) 거쳐서 옴
  * Lambda 함수를 직접 호출이 불가능하기 때문에 API Gateway를 거침
* authorization 토큰 = ID 토큰(auth0.com에서 발행한 JWT 토큰)
  * auth0.com에서 받은 authorization을 API Gateway에서 auth 토큰으로 바꿔줌
    * 별 다른 이유는 없고, 람다에서 반환한 값을 APIGateway에서 수정 후 람다 함수에 넣어줌
  * 좀 더 자세한 정보 등을 얻기위해 `authToken`, `accessToken`을 람다 함수를 통해 같이 넘겨줘서, 프로필 정보를 확인

---



### user-profile Lambda Function

* 사용자 유저 정보 확인 람다 함수

```javascript
"use strict";

var request = require('request');

exports.handler = function (event, context, callback) {
    console.log(event);

    if (!event.authToken) {
        callback('Could not find authToken');
        return;
    }

    if (!event.accessToken) {
        callback('Could not find access_token');
        return;
    }

    var id_token = event.authToken.split(' ')[1];
    var access_token = event.accessToken;

    var body = {
        'id_token': id_token,
        'access_token': access_token
    };

    var options = {
        url: 'https://' + process.env.DOMAIN + '/userinfo',
        method: 'GET',
        json: true,
        body: body
    };

    request(options, function (error, response, body) {
        console.log("Response0: " + JSON.stringify(response));

        if (!error && response.statusCode === 200) {
            console.log("Response1: " + JSON.stringify(response));
            callback(null, body);
        } else {
            callback(error);
        }
    });
};
```



#### user-profile Test

* 로그인 후 Local Storage에 저장된 값을 이용해서 아래와 같은 형태의 테스트 데이트를 생성

![1. user-profile Test](C:\dev\TIL\2020_10_26\img\1. user-profile Test.PNG)

---



### API Gateway 생성 후 테스트하면 아래와 같은 오류가 발생

![2. API Gateway 생성 후 테스트하면 아래와 같은 오류가 발생](C:\dev\TIL\2020_10_26\img\2. API Gateway 생성 후 테스트하면 아래와 같은 오류가 발생.PNG)



![2-1. API Gateway 생성 후 테스트하면 아래와 같은 오류가 발생](C:\dev\TIL\2020_10_26\img\2-1. API Gateway 생성 후 테스트하면 아래와 같은 오류가 발생.PNG)

* 클라이언트의 요청이 API Gateway를 통해서 람다 함수까지는 전달되었음
* 람다 함수가 처리하는 과정에서 오류가 발생

```javascript
exports.handler = function (event, context, callback) {
    console.log(event);
 
    if (!event.authToken) {				⇐ authToken == id_token (auth0.com에서 발행한 JWT)
        callback('Could not find authToken');   	⇐ 여기서 출력되는 메시지
        return;
    }
```

* API Gateway에서 Authorization 헤더로 전달된 값을 AuthToken 이름의 이벤트로 전달하되도록 맵핑을 설정

![2-2. API Gateway에서 Authorization 헤더로 전달된 값을 AuthToken 이름의 이벤트로 전달하되도록 맵핑을 설정](C:\dev\TIL\2020_10_26\img\2-2. API Gateway에서 Authorization 헤더로 전달된 값을 AuthToken 이름의 이벤트로 전달하되도록 맵핑을 설정.PNG)



#### profile 버튼을 클릭했을 때 사용자 정보가 조회, 출력되는 것을 확인

![2-3. profile 버튼을 클릭했을 때 사용자 정보가 조회, 출력되는 것을 확인](C:\dev\TIL\2020_10_26\img\2-3. profile 버튼을 클릭했을 때 사용자 정보가 조회, 출력되는 것을 확인.PNG)

---



### API Gateway의 권한 부여자를 이용해서 JWT 토큰 검증 후 유효한 토큰인 경우에만 람다를 호출할 수 있도록 제한

[참조 URL](https://auth0.com/docs/tokens/json-web-tokens/validate-json-web-tokens)

#### JWT 토큰을 검증하는 custom-authorizer 람다 함수를 생성

```javascript
"use strict";

var jwt = require('jsonwebtoken');

var generatePolicy = function(principalId, effect, resource) {
    var authResponse = {};
    authResponse.principalId = principalId;
    if (effect && resource) {
        var policyDocument = {};
        policyDocument.Version = '2012-10-17';
        policyDocument.Statement = [];
        var statementOne = {};
        statementOne.Action = 'execute-api:Invoke';
        statementOne.Effect = effect;
        statementOne.Resource = resource;
        policyDocument.Statement[0] = statementOne;
        authResponse.policyDocument = policyDocument;
    }
    return authResponse;
};


exports.handler = function(event, context, callback) {

    console.log("### event", JSON.stringify(event));
    
    if(!event.authorizationToken) {
        console.log(JSON.stringify(event));
        callback('Could not find authToken');
        return;
    }


    var id_token = event.authorizationToken.split(' ')[1];
    
    var secretBuffer = new Buffer(process.env.AUTH0_SECRET);
    jwt.verify(id_token, secretBuffer, function(err, decoded){
        if(err) {
            console.log('Failed jwt verification: ', err,
            'auth: ', event.authorizationToken);
            callback('Authorization Failed');
        } else {
            callback(null, generatePolicy('user', 'allow', event.methodArn));
        }
    })
};
```



#### custom-authorizer 람다 함수 테스트

![3. custom-authorizer 람다 함수 테스트](C:\dev\TIL\2020_10_26\img\3. custom-authorizer 람다 함수 테스트.PNG)

* jwt.verify() 함수에서 오류를 유발하는 것을 확인
  * https://jwt.io 사이트를 통해서 토큰을 수작업으로 검증
* ErrorType: invailed algorithm

![4. JWT.io](C:\dev\TIL\2020_10_26\img\4. JWT.io.PNG)

* `JWT.IO`를 이용 시 JWT 토큰에는 이상 없음을 확인

* 서명에 사용한 알고리즘을 `HS256`으로 변경해야 함
  * Auth0.com에서
    * Public Key(RS256)
    * Secret Key(HS256) 

![4-1. auth0.com에서 서명에 사용한 알고리즘을 HS256으로 변경](C:\dev\TIL\2020_10_26\img\4-1. auth0.com에서 서명에 사용한 알고리즘을 HS256으로 변경.PNG)



#### 재로그인을 통해 새롭게 받은 ID토큰으로 테스트 케이스를 재 작성 후 테스트를 실행

![5. 재로그인을 통해 새롭게 받은 ID토큰으로 테스트 케이스를 재 작성 후 테스트를 실행](C:\dev\TIL\2020_10_26\img\5. 재로그인을 통해 새롭게 받은 ID토큰으로 테스트 케이스를 재 작성 후 테스트를 실행.PNG)

---



### 사용자 프로필 버튼을 클릭했을 때 정상 여부 확인

#### 브라우저에 LocalStorage 확인

![6. 브라우저에 LocalStorage 확인](C:\dev\TIL\2020_10_26\img\6. 브라우저에 LocalStorage 확인.PNG)



#### CloudWatch에서 /aws/lambda/custom-authorizer 로그 그룹 확인

![6-1. CloudWatch에서 로그 그룹 확인](C:\dev\TIL\2020_10_26\img\6-1. CloudWatch에서 로그 그룹 확인.PNG)



#### 브라우저에서 로그를 확인

![7. 브라우저에서 로그를 확인](C:\dev\TIL\2020_10_26\img\7. 브라우저에서 로그를 확인.PNG)

---



### 람다 함수를 호출하는 두 가지 방법

* *Event와* *RequestResponse*
  1. Event 방식
     * 비동기 방식
     * 응답을 이벤트 소스로 다시 보내지 않음 ex) S3 버킷 이벤트 로그 등(실행하고 끝남)
  2. RequestResponse
     * 동기 방식
     * 호출자에게 응답을 반환 ex) API Gateway에서 람다 호출  시 반환 값을 다시 API Gateway에 전송
* 이벤트의 Push, Pull
  * Pull : 호출 할 서비스에서 

---



### 비동기 폭포(Async waterfall)

[참조URL](https://www.npmjs.com/package/async)

* `Async` : npm 모듈로 설치할 수 있는 자바스크립트 라이브러리
* **폭포(Waterfall) 패턴의 지원**
  * 콜백 함수를 사용해 한 함수의 결과를 다음 함수로 전달해 일련의 함수를 **차례로 실행할 수 있음**
    * 함수 중 하나가 콜백에 **오류를 전달하면 폭포 실행 중단**

* 예제

```javascript
var async = require("async");

async.waterfall([
    function(callback) {
        console.log(`첫번쨰 함수`);
        callback(null, "Peter", "Sam");
    },
    function(a1, a2, callback) {
        console.log(`두번째 함수 ${a1}, ${a2}`);
        callback(null, "Serverless");
    },
    function(a3, callback) {
        console.log(`세번째 함수 ${a3}`);
        callback(null, "Done");
    },
    ], function(err, result) {
        console.log(`최종 콜백 ${err}, ${result}`);
    });
```

```javascript
$ node sample.js 
첫번쨰 함수
두번째 함수 Peter, Sam
세번째 함수 Serverless
최종 콜백 null, Done 
```

* 마지막 `function`은 선택적 함수 또는 최종 콜백 함수

---



### 람다 CLI 사용

* 이전까지는 주로 AWS 콘솔을 주로 사용했지만, **자동화에 대해 생각하기 시작하면 CLI를 사용해 함수를 생성, 업데이트, 구성 및 삭제를 해야함**



### S3 버킷에 저장된 파일 목록을 반환하는 람다 함수를 생성, 배포

#### CLI에서 생성할 람다함수 권한 설정

* 이전까지는 AWS 콘솔 창에서 함수를 미리 만들어 놓고 배포를 진행함(package.json)
  * 이는 수동단계이며 **자동화에 맞지 않음**
* CLI에서 함수를 직접 만들기 위해 **권한 설정**
  * 그룹 설정에서 이전에 생성한 정책에서 *createFunction*를 추가로 입력

![8. CLI에서 생성할 람다함수 권한 설정](C:\dev\TIL\2020_10_26\img\8. CLI에서 생성할 람다함수 권한 설정.PNG)



#### 프로젝트 환경 구성

```cmd
C:\Users\myanj> cd /

C:\> cd serverless

C:\serverless> mkdir cli-function

C:\serverless> cd cli-function

C:\serverless\cli-function> npm init -y
Wrote to C:\serverless\cli-function\package.json:

{
  "name": "cli-function",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```



C:\serverless\cli-function\package.json 파일에 create, precreate 스크립트를 추가

* precreate ⇒ 현재 디렉터리의 파일을 압축 (배포를 위해)
* create ⇒ precreate 에서 생성한 압축 파일을 이용해서 람다 함수를 생성, 배포

```javascript
{
  "name": "cli-function",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "create": "aws lambda create-function --function-name get-video-list --handler index.hander --memory-size 128 --runtime nodejs4.3 --role arn:aws:iam::869011496078:role/lambda-s3-execution-role --timeout 3 --publish --zip-file fileb://Lambda-Deployment.zip", 
    "precreate": "zip -r Lambda-Deployment.zip * -x *.zip *.log",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

> aws lambda create-function 		⇐ 람다 함수 생성
>
> --function-name get-video-list 	⇐ 생성할 람다 함수 이름
>
> --handler index.hander 		⇐ 핸들러 함수를 지정 (P156)
>
> --memory-size 128 			⇐ 메모리 크기를 지정 (기본 설정 > 메모리)
>
> --runtime nodejs4.3 			⇐ 람다 함수 실행 환경 
>
> --role arn:aws:iam::869011496078:role/lambda-s3-execution-role 	⇐ 람다 함수 실행에 사용하는 롤
>
> --timeout 3 				⇐ 람다 함수 실행 제한 시간
>
> --publish 				⇐ 생성과 동시에 배포 
>
> --zip-file fileb://Lambda-Deployment.zip	⇐ 람담 함수 코드 포함된 압축 파일
>
> 자세한 명령어 사용법은 aws lambda create-function help 명령으로 확인 가능



#### 모듈 추가

```javascript
C:\serverless\cli-function> npm install aws-sdk

C:\serverless\cli-function> npm install async
```



#### 람다 함수 코드를 작성

C:\serverless\get-video-list\index.js

```javascript
'use strict';

var AWS = require('aws-sdk');
var async = require('async');

var s3 = new AWS.S3();

// S3 listObjects 함수에 대한 구성을 만듬
// 입력 포맷을 생성
// Json 데이터 반환
function createBucketParams(next) {
    var params = {
        Bucket: process.env.BUCKET,
        EncodingType: 'url'
    };
    // callback 함수 이름 지정
    next(null, params);
}


// S3 SDK를 사용해 지정된 버킷에서 객체 목록을 가져온다.
// 버킷의 데이터 목록을 조회
function getVideoosFromBucket(params, next){
    // params에서 객체를 반환 해주는 함수가 arg2
    s3.listObjects(params, function(err, data) {
        if(err) {
            next(err);
        } else{
            next(null, data);
        }
    });
}


// 조회 결과를 반환할 포맷으로 변형
function createList(data, next) {
    console.log(data);
    
    var urls = [];
    for (var i = 0; i < data.Contents.length; i++) {
        var file = data.Contents[i];
        // substr : JavaScript 내장 함수
        if (file.Key && file.Key.substr(-3, 3) === 'mp4') {
            urls.push(file);
        }
    }

    var result = {
        baseUrl: process.env.BASE_URL,
        bucket: process.env.BUCKET,
        urls: urls
    }
    next(null, result);
}


// call 함수 define
exports.hander = function(event, context, callback) {
    // 순차적인 함수의 배열, 최종 함수
    async.waterfall([
        createBucketParams,
        getVideoosFromBucket,
        createList
    ], function(err, result) {
        if (err) {
            // 람다함수가 호출된 곳으로 오류를 반환
            callback(err);
        } else {
            callback(null, result);
        }
    });
};
```



#### 람다 함수 생성 및 배포

```cmd
npm run create
```



#### 트러블슈팅

![9. 트러블슈팅](C:\dev\TIL\2020_10_26\img\9. 트러블슈팅.PNG)



![9-1. 트러블슈팅](C:\dev\TIL\2020_10_26\img\9-1. 트러블슈팅.PNG)



#### 람다 함수 생성 및 배포 여부 확인 및 환경 변수 설정

![10. 람다 함수 생성 및 배포 여부 확인 및 환경 변수 설정](C:\dev\TIL\2020_10_26\img\10. 람다 함수 생성 및 배포 여부 확인 및 환경 변수 설정.PNG)



#### 람다 함수 테스트

![11. 람다 함수 테스트](C:\dev\TIL\2020_10_26\img\11. 람다 함수 테스트.PNG)