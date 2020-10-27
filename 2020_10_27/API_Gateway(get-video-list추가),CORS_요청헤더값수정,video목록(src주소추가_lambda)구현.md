# Today I Learned

* API Gateway에 get-video-list +API를 추가
* 웹 사이트에 비디오 목록을 가져와서 재생하는 기능을 추가
* 잘못된 경로 APIGateway 주소 확인
* CORS 추가 및 활성화
* video-controller.js  / wireEvents 함수 추가

---



# 10/26 내용 이어서 계속

# API Gateway에 get-video-list +API를 추가

* Lambda 프록시 통합
  * 프록시 통합을 설정해 놓으면 **통합 요청, 통합 응답 모두** 자동으로 값들이 매핑됨 
    * 설정 안해놓을 시 값을 다음 프로세스에 넘겨주는걸 값(람다를 통해 반환되는 값을 변경해서) 직접 가공해서 넘겨줘야 함



#### APIGateway 생성

* `get`메서드 추가

![1. apigateway 연결](C:\dev\TIL\2020_10_27\img\1. apigateway 연결.PNG)

---



### 웹 사이트에 비디오 목록을 가져와서 재생하는 기능을 추가

* C:\serverless\24-hour-video\js\video-controller.js 파일을 생성 (p209)

```javascript
var videoController = {
    data: {
        config: null
    },
    uiElements: {
        videoCardTemplate: null, 
        videoList: null
    }, 
    init: function(config) {
        this.uiElements.videoCardTemplate = $('#video-template');
        this.uiElements.videoList = $('#video-list');
 
        this.data.config = config;
 
        this.getVideoList();
    }, 
    getVideoList: function() {
        var that = this;
 
        var url = this.data.config.apiBaseUrl + '/video';
        $.get(url, function(data, status) {
            that.updateVideoFrontPage(data);
        });
    }, 
    updateVideoFrontPage: function(data) {
        console.log(data);
        // TODO
    }
};
```



#### html에 태그와 스크립트 추가

C:\serverless\24-hour-video\index.html

```html
 			:
    <!-- Main jumbotron for a primary marketing message or call to action -->
    <div class="jumbotron">
      <!-- <div class="container">
        <h1>Hello, world!</h1>
        <p>This is a template for a simple marketing or informational website. It includes a large callout called a jumbotron and three supporting pieces of content. Use it as a starting point to create something more unique.</p>
        <p><a class="btn btn-primary btn-lg" href="#" role="button">Learn more &raquo;</a></p>
      </div> -->
      <!-- 209페이지 참조 -->
      <div class="container" id="video-list-container">
        <div id="video-template" class="col-md-6 col">
          <div class="video-card">
            <video width="100%" height="100%" controls>
              <source type="video/mp4"/>
            </video>
          </div>
        </div>
        <div id="video-list" calss="row">
 
        </div>
      </div>
    </div>
			:
    <script src="https://cdn.auth0.com/js/lock/11.27/lock.min.js"></script>
    <script src="js/config.js"></script>
    <script src="js/user-controller.js"></script>
    <script src="js/video-controller.js"></script>	⇐ 추가
    <script src="js/main.js"></script>
			:
```



#### 초기화 추가

C:\serverless\24-hour-video\js\main.js

```javascript
(function() {
    $(document).ready(function() {
        userController.init(configConstants);
        videoController.init(configConstants);		⇐ 추가
    });
}());
```



#### 테스트 서버 실행

```npm
npm start
```

---



### 잘못된 경로 APIGateway 주소 확인

![2. 잘못된 경로 APIGateway 주소](C:\dev\TIL\2020_10_27\img\2. 잘못된 경로 APIGateway 주소.PNG)

* `APIGateway`
  * ` user-pofile` 쪽으로 접속하고 있음
  * `get-video-list` API의 배포 주소로 변경

![2-1. 잘못된 경로 APIGateway 주소](C:\dev\TIL\2020_10_27\img\2-1. 잘못된 경로 APIGateway 주소.PNG)



#### config.js 파일에 get-video-list API 주소를 추가하고, video-controller.js 파일에서 참조하도록 수정

* config.js

```javascript
var configConstants = {
    auth0: {
        // auth0.com > Applications > 24-Hour-Video > Settings 에서 확인
        domain: 'naanjini.us.auth0.com', 
        clientId: 'eSvyRiLgh8sjcbRtUKA9JrHjNiM7NFIH'
    }, 
    //  user-profile API
    apiBaseUrl: 'https://pxo8hqdlsl.execute-api.us-east-1.amazonaws.com/dev', 
 
    //  get-file-list API 
    getFileListApiUrl: 'https://6454d9ijni.execute-api.us-east-1.amazonaws.com/dev'
};
```



* video-controller.js

```javascript
	:
    getVideoList: function() {
        var that = this;
 		
        # getFileListApiUrl 추가
        var url = this.data.config.getFileListApiUrl + '/videos';
        $.get(url, function(data, status) {
            that.updateVideoFrontPage(data);
        });
    }, 
		:
```

---



### CORS 추가 및 활성화

![3. CORS 설정](C:\dev\TIL\2020_10_27\img\3. CORS 설정.PNG)

*  `CORS` 오류가 발생
* 응답 헤더에 `Access-Control-Allow-Origin` 헤더가 포함되어 있지 않음
  *  `API Gateway`에 CORS 활성화가 되어 있지 않음

![3-1. CORS 활성화](C:\dev\TIL\2020_10_27\img\3-1. CORS 활성화.PNG)



#### CORS 활성화 이후에도 동일한 문제가 발생

* `preflight request`를 분석해보면 `access-control-request-headers` 요청헤더에 `accesstoken, authorization`이 설정되어 있음
  * 본 요청에서 해당 요청 헤더를 수용할 수 있는지 확인
    * 서비스를 제공해 주는 쪽(`API Gateway`)에서 허용을 해줘야 함

![4. CORS 활성화 이후에도 동일한 문제가 발생](C:\dev\TIL\2020_10_27\img\4. CORS 활성화 이후에도 동일한 문제가 발생.PNG)



* CORS 활성화하는 부분에서 `Access-Contol-Allow-Headers` 부분에 Authorization, AccessToken **2개를 추가**해야 함

![4-1. 해결](C:\dev\TIL\2020_10_27\img\4-1. 해결.PNG)

---



### video-controller.js  / wireEvents 함수 추가

* 비디오 목록을 클리하면, 목록의 텍스트를 가져와서 <source> 태그의 src 속성값으로 설정 후 리로드

```javascript
var videoController = {
    data: {
        config: null
    },
    uiElements: {
        videoCardTemplate: null, 
        videoList: null
    }, 
    init: function(config) {
        this.uiElements.videoCardTemplate = $('#video-template');
        this.uiElements.videoList = $('#video-list');
 
        this.data.config = config;
 
        this.getVideoList();
        this.wireEvents();
    }, 
    getVideoList: function() {
        var that = this;
 
        var url = this.data.config.getFileListApiUrl + '/videos';
        $.get(url, function(data, status) {
            that.updateVideoFrontPage(data);
        });
    }, 
    updateVideoFrontPage: function(data) {
        console.log(data);
        // TODO
    },
    //  비디오 목록을 클리하면, 목록의 텍스트를 가져와서 <source> 태그의 src 속성값으로 설정 후 리로드
    wireEvents: function() {
        $('#video-list').on('click', 'li', function() {
            console.log($(this).text());
            $('source').attr('src', $(this).text());
            $('video').load();
        });
    }  
};
```

