# Today I Learned

* AWS DevOps (Development + Operation) 개념
  * CI (지속적 통합)
  * CD (지속적 전달, 지속적 배포)
* AWS CodePipeline
* AWS 기반 서버리스 아키텍처 | 서버리스
  * 정의
  * 목표
  * AWS의 Lambda
  * 서버리스 아키텍처의 원리
* 설치 및 구성 (부록B)
* AWS 기반 서버리스 아키텍처_첫번째 Lambda 함수 작성 (p50)
  1. Lambda 함수 작성
  2. 로컬에서 하는 테스트
  3. 테스트할 코드 작성
  4. 배포 실행(p55)
  5. S3를 Lambda에 연결

---



### AWS DevOps (Development + Operation) 개념

* **자동화를 통해** 더 짧은 개발 및 배포 주기와 **안정적인 소프트웨어 배포**라는 목표를 가지고 있음
* AWS에서는 데브옵스에 대한 여러 서비스를 제공
  * **CodeBuild(지속적 통합)**: 프로젝트 코드를 빌드하고 유닛 테스트를 실행할 수 있게 해줌
  * **CodeDeploy**: 배포를 자동으로 수행함
  * **CodePipeline(지속적 통합 / 지속적 전달)**: 소스코드의 커밋부터 배포까지 각자 상황에 맞는 파이프라인을 구성할 수 있게 해줌

#### CI (지속적 통합)

* 개발자들이 코드를 메인 브랜치에 병합하는 시간을 최대한 앞당겨 버그를 빨리 찾을 수 있게 하는 것
  1. 소스 코드 푸시
  2. 빌드 & 유닛 테스트
  3. 지속적 통합을 통해 코드의 안정성을(버그 등) 확보하고, 배포의 속도를 높임

#### CD (지속적 전달, 지속적 배포)

* 유닛 테스트까지 완료된 코드를 운영 서버에 배포하기 전 단계인 스테이지 서버에 배포하고, 해당 서버에서 UI 테스트, 연동 테스트 등 다양한 테스트를 자동으로 진행
  * **수동**으로 사람이  운영 서버 배포를 승인하는 것이 지속적 전달
  * 운영 서버까지 **자동**으로 배포되는 것이 지속적 배포

---



### AWS CodePipeline

* **지속적 통합** 및 **지속적 전달**을 가능하게 하는 서비스
  * AWS 내의 서비스뿐만 아니라 깃허브, 젠키스 등 **외부의 유명한 서비스와도 연동 지원**
    * ex) 소스코드가 깃허브에 커밋되면 해당 코드를 읽어와 **CodeBuild에서 유닛 테스트를 실행한 뒤**, 문제가 없으면 **CodeDeploy를 이용해 배포를 자동으로 진행**
  * **단계**별로 어떤 작업을 할지 지정할 수 있음
    * 직렬과 병렬 작업 가능
* 아티팩트 : 각 작업이 끝날 때마다 뒤의 작업에 데이터를 넘겨주기 위한 개념
* 아티팩트 스토어 : **파이프라인의 작업의 결과물**로 다른 작업에 넘겨줄 수 있는 아티팩트를 **저장할 공간**

#### 절차 Ex

* 코드 커밋(소스) - 코드 빌드(빌드) - 유닛 테스트(테스트) - 스테이징 배포(배포) - E2E test(테스트) - 알림(호출) - 배포 확인(승인) - 운영서버 배포(배포) - 배포 버전 기록(호출) - 알림(호출)

#### 절차

1. 깃허브에 저장된 코드를 가져옴
2. 승인 작업을 추가해서 승인이 될 때까지 기다림
3. 승인이 될 경우 Elastic Beanstalk를 이용해 코드를 배포
4. AWS Lambda 함수를 호출해서 SNS로 배포 완료 알림을 발송

![1. AWS_파이프라인](C:\dev\TIL\2020_10_16\img\1. AWS_파이프라인.PNG)

---



### AWS 기반 서버리스 아키텍처 | 서버리스

#### 정의

* **서버리스란 컴퓨팅 서비스를 제공하는 측에서 코드가 실행되게 하면서, 서비스 및 API를 사용해 일을 처리하는 방식**
* 컴퓨팅 서비스를 사용해 코드를 실행하든지 또는 API를 사용하든지 간에, 어느 경우이든 백그라운드에는 실행 중인 서버는 있음. **그러나 서버들이 감춰져 있다는 점에서 서버리스라 불림**
  * 인프라, 기반 운영 체제를 다룰 일이 없음



#### 목표

* 서버와 인프라 문제에서 벗어나 개발자가 코드에만 주로 집중할 수 있게 하는 것이 서버리스의 궁극적인 목표
* 계층화는 관심자를 분리하는데 도움을 주지만, 너무 많은 계층은 변경을 더 어렵게하고 구현을 느리게 할수도 있음
  * 서버리스를 가지고 필요한 계층의 수와 코드의 양을 최소화해 **점진적으로 시스템의 복잡도를 줄일 수 있음**



#### AWS의 Lambda

* Lambda는 **이벤트 응답에 대해 대용량 병렬 방식**으로 코드를 실행할 수 있음
  * Lambda는 서버를 프로비저닝하고, 소프트웨어를 설치하고, 컨테이너를 배치하거나, 저수준의 세부 사항에 대해 걱정할 필요 없이 **코드를 받아서 실행함**
  * **서버리스 아키텍처**는 실행을 위해 **서버에 직접 접근하지 않는** 새로운 종류의 소프트웨어 아키텍처를 말함

* 가장 유연하고 강력한 서버리스 설계는 **이벤트 주도 방식**



#### 서버리스 아키텍처의 원리

1. 컴퓨팅 서비스를 사용해 요구에 맞게 **코드를 실행한다**(서버 없이)
2. **단일 목적**의 **상태 없는** 함수를 작성한다
3. 푸시 기반 (push-based), 이벤트 주도 (event-driven), **파이프라인**을 설계한다
4. 더 두텁고 강한 프런트엔드를 만든다
5. 서드파티 서비스를 받아들인다

---



### 설치 및 구성 (부록B)

#### 1. AWS CLI 다운로드 및 설치

 [URL](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-chap-install.html)

1. 윈도우용 AWS CLI 다운로드 후 설치 [URL](https://awscli.amazonaws.com/AWSCLIV2.msi)
2. 설치 확인 (명령어 창)

```cmd
C:\Users\myanj> aws --version
# aws-cli/2.0.57 Python/3.7.7 Windows/10 exe/AMD64
```

#### 2. node.js 설치

node js 설치 [URL](https://nodejs.org/ko/)

```cmd
(새 명령어창 실행 후) 설치 확인
C:\Users\myanj> node --version
v12.19.0

C:\Users\myanj> npm --version
6.14.8
```

#### 3.  IAM(Identity and Access Management)에 사용자 생성

```cmd
C:\Users\myanj> aws configure
# AWS Access Key ID [None]: AKIA4UVI7DSHPAN2JP4D
# AWS Secret Access Key [None]: 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
Default region name [None]: us-east-1		⇐ 미국 동부 (버지니아 북부) 리전
Default output format [None]:
```

#### 4. 사용자 권한 설정

* CLI 환경에서 작업할 수 있도록 엑세스 키 ID, 비밀 액세스 키, 리전 등의 정보를 설정
* **인라인 정책**
  * 일반적으로 **일회성**으로 적용
    * ex) CSS에서 *style* 속성처럼 **한 태그 안에서만 적용**
* **관리형 정책**
  * 정책 업데이트하면 연결된 **모든 사용자와 그룹에 적용**
    * ex) CSS에서 같은 *ID*나 *Class*에 적용해서 같은 값을 가진 태그들에 한해 **모두 적용**

![2. 람다 정책설정](C:\dev\TIL\2020_10_16\img\2. 람다 정책설정.PNG)

#### 5. S3 버킷 생성

* 2가지의 버킷 생성
  * 원본 동영상을 업로드 후 저장할 버킷
  * Elastic Transcoder에 의해 트랜스코딩된 동영상을 저장할 버킷

#### 6. IAM 역할 생성

* Lambda 함수가 S3 및 Elastic Transcoder와 상호 작용할 수 있는 역할
  * Lambda 함수가 S3를 읽고 쓰고, Transcoder에 데이터를 넘겨줄 수 있는 정책을 부여
  * *AWSLambdaExecute*
  * *AmazonElasticTranscoderJobsSubmitter*

#### 7. Lambda 함수 생성

* *transcode-video*
  * 함수 코드 설정은 나중에 함

#### 8. Elastic Transcoder 구성

* 다양한 형식과 비트 전송률로 비디오 트랜스코딩을 수행
* Elastic Transcoder 서비스에서 구성

#### 9. npm 설정 (p330)

```cmd
C:\Users\myanj> cd /

C:\> mkdir serverless

C:\> cd serverless

C:\serverless> mkdir transcode-video

C:\serverless> cd transcode-video

# package.json 파일 생성 - 필요한 패키지 다운로드 및 핸들링
C:\serverless\transcode-video> npm init -y
Wrote to C:\serverless\transcode-video\package.json:

{
  "name": "transcode-video",
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

# aws-sdk 설치
C:\serverless\transcode-video> npm install aws-sdk

C:\serverless\transcode-video> type package.json
{
  "name": "transcode-video",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "aws-sdk": "^2.772.0"
  }
}
```

#### 10. zip 설치

[URL](http://gnuwin32.sourceforge.net/packages/zip.htm) Setup 파일

* 설치 파일 다운로드 후 설치
  환경변수 PATH에 **C:\Program Files (x86)\GnuWin32\bin** 을 추가
* (명령어창을 새로 실행) zip 명령어 실행

---



### AWS 기반 서버리스 아키텍처_첫번째 Lambda 함수 작성 (p50)

#### 절차

* 총 3번의 Lambda함수

1. S3 새 비디오 업로드 (이벤트 발생)
2. Lambda 함수가 이벤트에 응답해 변환 작업 생성
3. *Elastic Transcoder Service*가 새 비디오를 인코딩
4. S3 버킷에 저장하고 이벤트 발생
5. Lambda 함수가 이벤트에 응답해 데이터베이스로 전송되는 비디오에 대한 메타데이터 생성
6. 데이터베이스에 메타데이터 저장
7. 데이터베이스의 변경이 자동으로 이메일 공지를 생성하는 Lambda 함수 발생
8. 공지 서비스가 사용자에게 이메일 발송



#### 1. Lambda 함수 작성

![3. 첫번째 람다함수 생성](C:\dev\TIL\2020_10_16\img\3. 첫번째 람다함수 생성.PNG)

```javascript
C:\serverless\transcode-video\index.js

// P51
'use static';
 
var AWS = require('aws-sdk');
 
var elasticTranscoder = new AWS.ElasticTranscoder({
    region: 'us-east-1'
});
 
exports.handler = function(event, context, callback) {
    console.log('Welcome');
 
    //  버킷에 추가된 파일명: "my file.txt"
    //  키: "my+file.txt"
    var key = event.Records[0].s3.object.key;   // 키 = URL 인코딩된 파일명
    var sourceKey = decodeURIComponent(key.replace(/\+/g, ' '));
    var outputKey = sourceKey.split('.')[0];
 
    var params = {
        PipelineId: '1602828742510-bq04l0',     // Elastic Transcoder의  파이프라인 ID
        Input: { Key: sourceKey },
        //  https://docs.aws.amazon.com/ko_kr/elastictranscoder/latest/developerguide/system-presets.html
        Outputs: [
            {   // 일반 1080p
                // Key: outputKey + '/' + outputKey + '-1080p' + '.mp4',
                Key: `${outputKey}/${outputKey}-1080p.mp4`,
                PresetId: '1351620000001-000001'
            }, 
            {   // 일반 720p
                Key: outputKey + '/' + outputKey + '-720p' + '.mp4',
                PresetId: '1351620000001-000010'
            }, 
            {   // 웹: Facebook, SmugMug, Vimeo, YouTube
                Key: outputKey + '/' + outputKey + '-web-720p' + '.mp4',
                PresetId: '1351620000001-100070'
            }
        ]
    };
    
    elasticTranscoder.createJob(params, function(err, res) {
        if (err) {
            callback(err);
        }
    });
};
```

#### 2. 로컬에서 하는 테스트

* *run-local-lambda* 라는 npm 모듈을 사용해 Lambda 함수를 로컬로 실행 가능
* 테스트할 때만 필요

```cmd
C:\serverless\transcode-video> npm install run-local-lambda --save-dev
```

```bash
C:\serverless\transcode-video\package.json
{
  "name": "transcode-video",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
`    "test": "run-local-lambda --file index.js --event tests/event.json"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "aws-sdk": "^2.772.0"
  },
`  "devDependencies": {
`    "run-local-lambda": "^1.1.1"
  }
}
```

#### 3. 테스트할 코드 작성

```bash
C:\serverless\transcode-video\tests\event.json

{
    "Records": [
      {
        "eventVersion": "2.0",
        "eventSource": "aws:s3",
        "awsRegion": "us-east-1",
        "eventTime": "1970-01-01T00:00:00.000Z",
        "eventName": "ObjectCreated:Put",
        "userIdentity": {
          "principalId": "EXAMPLE"
        },
        "requestParameters": {
          "sourceIPAddress": "127.0.0.1"
        },
        "responseElements": {
          "x-amz-request-id": "EXAMPLE123456789",
          "x-amz-id-2": "EXAMPLE123/5678abcdefghijklambdaisawesome/mnopqrstuvwxyzABCDEFGH"
        },
        "s3": {
          "s3SchemaVersion": "1.0",
          "configurationId": "Video Upload",
          "bucket": {
            "name": "serverless-video-upload-20201016",
            "ownerIdentity": {
              "principalId": "EXAMPLE"
            },
            "arn": "arn:aws:s3:::example-bucket"
          },
          "object": {
            "key": "my video.mp4",
            "size": 1024,
            "eTag": "0123456789abcdef0123456789abcdef",
            "sequencer": "0A1B2C3D4E5F678901"
          }
        }
      }
    ]
  }
  
C:\serverless\transcode-video> npm test 			⇐ 테스트를 수행했을 때 권한 오류가 발생
```

#### 4. 배포 실행(p55)

```bash
C:\serverless\transcode-video\package.json

{
  "name": "transcode-video",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "run-local-lambda --file index.js --event tests/event.json", 
    "deploy": "aws lambda update-function-code --function-name arn:aws:lambda:us-east-1:869011496078:function:transcode-video --zip-file fileb://Lambda-Deployment.zip",
    "predeploy": "zip -r Lambda-Deployment.zip * -x *.zip *.log"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "aws-sdk": "^2.772.0"
  },
  "devDependencies": {
    "run-local-lambda": "^1.1.1"
  }
}


C:\serverless\transcode-video> npm run deploy
```

#### 5. S3를 Lambda에 연결 (p56)

![4. S3람다연결](C:\dev\TIL\2020_10_16\img\4. S3람다연결.PNG)

![5. 람다실행](C:\dev\TIL\2020_10_16\img\5. 람다실행.PNG)

---

