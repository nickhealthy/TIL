# Today I Learned

* Serverless 애플리케이션 구성
  * SNS 구성 (p61)
  * 비디오 권한 설정
  * SNS에 Lambda 함수를 연결
  * 메타데이터 생성 (p68)

---

10/16일 내용 이어서 계속...

### Serverless 애플리케이션 구성

### SNS 구성 (p61)

* 파일의 변화가 있을때 *Lambda* 함수에서 *SNS event* 발생

![1. SNS구성](C:\dev\TIL\2020_10_19\img\1. SNS구성.PNG)



* Lambda함수에서 *S3* 이벤트 버킷 설정
  * 해당 버킷의 변화가 감지될 시 SNS 발송

![2. S3이벤트 버킷설정](C:\dev\TIL\2020_10_19\img\2. S3이벤트 버킷설정.PNG)



#### 1. S3 버킷과 SNS를 연결

![3. S3버킷과 SNS이벤트 연결(이벤트설정)](C:\dev\TIL\2020_10_19\img\3. S3버킷과 SNS이벤트 연결(이벤트설정).PNG)



#### 2. SNS로부터 이메일 수신

* 주제 생성 및 이메일 *confirm*

#### 3. 이메일 수신 테스트

* 변환된 파일이 저장된 버킷의 파일명을 변경
* 이메일 확인

---



### 비디오 권한 설정

#### 1. 두번째 람다 함수 작성 (p65)

* 변환된 파일을 외부에서 접근할 수 있도록 권한을 변경

![4.  2번째 람다함수 비디오권한설정](C:\dev\TIL\2020_10_19\img\4.  2번째 람다함수 비디오권한설정.PNG)

```cmd
C:\> cd serverless

C:\serverless> mkdir set-permissions

C:\serverless> cd set-permissions

C:\serverless\set-permissions> npm init -y

C:\serverless\set-permissions> npm install aws-sdk
```

* C:\serverless\set-permissions\package.json 파일에 deploy 스크립트 추가

```bash
{
  "name": "set-permissions",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    `"deploy": "aws lambda update-function-code --function-name arn:aws:lambda:us-east-1:869011496078:function:set-permissions --zip-file fileb://Lambda-Deployment.zip",
    "predeploy": "zip -r Lambda-Deployment.zip * -x *.zip *.log"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "aws-sdk": "^2.773.0"
  }
}

```

* C:\serverless\set-permissions\index.js 파일 생성 (P66)

```javascript
'use strict';
 
var AWS = require('aws-sdk');
var s3 = new AWS.S3();
 
exports.handler = function(event, context, callback) {
    console.log(JSON.stringify(event));
 
    var message = JSON.parse(event.Records[0].Sns.Message);
    var sourceBucket = message.Records[0].s3.bucket.name;
    var sourceKey = decodeURIComponent(message.Records[0].s3.object.key.replace(/\+/g, ' '));
 
    var params = {
        Bucket: sourceBucket,
        Key: sourceKey, 
        ACL: 'public-read'
    };
    // https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html
    s3.putObjectAcl(params, function(err, res) {
        if (err) {
            callback(err);
        }
    });
};
```

#### 2. Lambda 함수 배포

```cmd
C:\serverless\set-permissions> npm run deploy
```

---



### SNS에 Lambda 함수를 연결

![5. SNS에 람다함수 연결](C:\dev\TIL\2020_10_19\img\5. SNS에 람다함수 연결.PNG)



#### 1. S3 버킷을 퍼블릭 접근할 수 있도록 변경

![6. S3버킷을 퍼블릭 접근할 수 있도록 변경](C:\dev\TIL\2020_10_19\img\6. S3버킷을 퍼블릭 접근할 수 있도록 변경.PNG)



#### 2. 변환된 파일의 이름을 변경해서 퍼플릭 접근 권한을 부여하는 람다 함수를 테스트

![7. 퍼블릭 접근권한을 부여하는 람다함수 테스트](C:\dev\TIL\2020_10_19\img\7. 퍼블릭 접근권한을 부여하는 람다함수 테스트.PNG)

* 람다함수 권한 설정

![8. 람다함수 권한설정](C:\dev\TIL\2020_10_19\img\8. 람다함수 권한설정.PNG)



#### 3. 변환된 파일의 이름을 변경해서 퍼플릭 접근 권한을 부여하는 람다 함수를 테스트

* 브라우저를 통해서 접근 가능한 것을 확인
  * S3 버킷 URL

---



### 메타데이터 생성 (p68)

#### 1. EC2 인스턴스 생성

[AWS Lambda Node.js 런타임 사용](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/lambda-runtimes.html)

* AWS Lambda Node.js 런타임을 사용하기 위해서는 **Amazon Linux 2 이미지를 사용**해야 함

![9. 람다함수 node.js 런타임](C:\dev\TIL\2020_10_19\img\9. 람다함수 node.js 런타임.PNG)



#### 2. EC2 인스턴스에서 Node.js 설정 및 설치

[EC2인스턴스에서 Node.js 설치](https://docs.aws.amazon.com/ko_kr/sdk-for-javascript/v2/developer-guide/setting-up-node-on-ec2-instance.html)

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
. ~/.nvm/nvm.sh
nvm install node
node -e "console.log('Running Node.js ' + process.version)"
nvm install  v12.19.0
```



#### 3. ffmpeg 정적 빌드 버전 다운로드 (v4.2.2)

[ffmpeg](https://www.johnvansickle.com/ffmpeg/old-releases/)

* 웹사이트에서 파일 다운로드

```bash
wget https://www.johnvansickle.com/ffmpeg/old-releases/ffmpeg-4.2.2-amd64-static.tar.xz
```



#### 4. Lambda 함수 생성

![10. 메타데이터 람다함수 생성](C:\dev\TIL\2020_10_19\img\10. 메타데이터 람다함수 생성.PNG)



#### 5. Lambda 함수 작성

```bash
[ec2-user@ip-172-31-89-196 ~]$ mkdir extract-metadata
[ec2-user@ip-172-31-89-196 ~]$ cd extract-metadata/
[ec2-user@ip-172-31-89-196 extract-metadata]$ mkdir bin
[ec2-user@ip-172-31-89-196 extract-metadata]$ mkdir tmp
[ec2-user@ip-172-31-89-196 extract-metadata]$ npm init -y

[ec2-user@ip-172-31-89-196 extract-metadata]$ npm install aws-sdk

[ec2-user@ip-172-31-89-196 extract-metadata]$ vi package.json
{
  "name": `"extract-metadata",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    `"deploy": "aws lambda update-function-code --function-name arn:aws:lambda:us-east-1:869011496078:function:extract-metadata --zip-file fileb://Lambda-Deployment.zip",
    "predeploy": "zip -r Lambda-Deployment.zip * -x *.zip *.log"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "aws-sdk": "^2.773.0"
  }
}
```

* [ec2-user@ip-172-31-89-196 extract-metadata]$ vi index.js 설정

```javascript

'use strict';
var AWS = require('aws-sdk');
var exec = require('child_process').exec;
var fs = require('fs');

process.env['PATH'] = process.env['PATH'] + ':' + process.env['LAMBDA_TASK_ROOT'];

var s3 = new AWS.S3();

function saveMetadataToS3(body, bucket, key, callback) {
    console.log('Saving metadata to S3');
    s3.putObject({
        Bucket: bucket, 
        Key: key, 
        Body: body
    }, function(error, data) {
        if (error) {
            callback(error);
        }
    });
}

function extractMetadata(sourceBucket, sourceKey, localFilename, callback) {
    console.log('Extracting metadata');

    var cmd = 'bin/ffprobe -v quiet -print_format json -show_format "/tmp/' + localFilename + '"';
    exec(cmd, function(error, stdout, stderr) {
        if (error === null) {
            var metadataKey = sourceKey.split('.')[0] + '.json';
            saveMetadataToS3(stdout, sourceBucket, metadataKey, callback);
        } else {
            console.log(stderr);
            callback(error);
        }
    });
}

function saveFileToFilesystem(sourceBucket, sourceKey, callback) {
    console.log('Saving to filesystem');

    var localFilename = sourceKey.split('/').pop();
    var file = fs.createWriteStream('/tmp/' + localFilename);

    var stream = s3.getObject({
        Bucket: sourceBucket, 
        Key: sourceKey
    }).createReadStream().pipe(file);

    stream.on('error', function(error) {
        callback(error);
    });

    stream.on('close', function() {
        extractMetadata(sourceBucket, sourceKey, localFilename, callback);
    });
}

exports.handler = function (event, context, callback) {
    var message = JSON.parse(event.Records[0].Sns.Message);
    var sourceBucket = message.Records[0].s3.bucket.name;
    var sourceKey = decodeURIComponent(message.Records[0].s3.object.key.replace(/\+/g, ' '));

    saveFileToFilesystem(sourceBucket, sourceKey, callback);
};
```

#### 6. ffmpeg 압축해제 및 배포 디렉터리로 복사

```bash
[ec2-user@ip-172-31-85-61 extract-metadata]$ cd
[ec2-user@ip-172-31-89-196 ~]$ xz -d ffmpeg-4.2.2-amd64-static.tar.xz
[ec2-user@ip-172-31-89-196 ~]$ tar xvf ffmpeg-4.2.2-amd64-static.tar
[ec2-user@ip-172-31-89-196 ~]$ cd ffmpeg-4.2.2-amd64-static/
[ec2-user@ip-172-31-89-196 ffmpeg-4.2.2-amd64-static]$ mv ./ffprobe ../extract-metadata/bin/
[ec2-user@ip-172-31-89-196 ffmpeg-4.2.2-amd64-static]$ cd ../extract-metadata/
```

#### 7. Lambda 함수 배포

```bash
[ec2-user@ip-172-31-89-196 extract-metadata]$ aws configure

AWS Access Key ID [None]: AKIA4UVI7DSHPAN2JP4D
AWS Secret Access Key [None]: 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

[ec2-user@ip-172-31-89-196 extract-metadata]$ npm run deploy
```

#### 8. SNS에 람다 함수 연결

![11. SNS에 람다 함수 연결](C:\dev\TIL\2020_10_19\img\11. SNS에 람다 함수 연결.PNG)

#### 9. 변환된 파일의 이름을 변경해서 메타 데이터 파일을 생성하는 람다 함수를 테스트

![12. 변환된 파일의 이름을 변경해서 메타 데이터 파일을 생성하는 람다 함수를 테스트](C:\dev\TIL\2020_10_19\img\12. 변환된 파일의 이름을 변경해서 메타 데이터 파일을 생성하는 람다 함수를 테스트.PNG)

