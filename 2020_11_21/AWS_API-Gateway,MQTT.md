# Today I Learned

* AWS API-Gateway
* MQTT

---



## AWS API-Gateway

#### 3가지  종류

1. HTTP API
2. REST API
3. WebSocket API

#### HTTP API vs REST API

|     설명      |        HTTP API        |        REST API        |
| :-----------: | :--------------------: | :--------------------: |
| 기능 / 난이도 | 기능이 적지만 단순하다 | 기능이 많지만 복잡하다 |
|     가격      |        저렴하다        |         비싸다         |

#### 권한부여

* API에 인증을 붙이고 싶을 때 사용

#### CORS (Cross-Origin-Resource-Sharing)

* 같은 도메인에 있는 데이터는 허용이 되지만,
* 다른 도메인에 있는 것을 호출할 땐 허용이 되지 않음. 그것을 상쇄 시켜주는 통신 규약

#### Access-Control-Allow-Origin

* 어디에서나 호출할 수 있는 API로 설정이 가능

#### 내보내기

* 설정을 내보내기로 백업할 수 있음

#### 가져오기

* 다른 설정을 가져오기로 빠르게 복구 시킬 수 있음

#### 배포 - 스테이지

* 기본적으로 API-Gateway는 자동배포를 지원하지만, 자동배포를 지원하고 싶지 않을 땐 배포 part를 이용

#### 모니터링 - 지표, 로깅

* 사용하고 있는 API-Gateway 를 모니터링 할 수 있는 기능

---



## MQTT (Message Queuing Telemetry Transport)

* 발행, 구독 모델(Publish-subscribe pattern) 기반의 메시징 프로토콜
* 경량이며 배터리 수명도 절약할 수 있어 IoT에서 많이 활용
  * 최소한의 전력과 패킷량으로 통신하는 프로토콜 - IoT와 모바일 어플리케이션 등의 통신에 매우 적합한 프로토콜



#### 프로세스 구조

HTTP, TCP 등의 통신과 같이 클라이언트-서버 구조로 이루어지는 것이 아닌,

**Broker, Publisher, Subscriber 구조로 이루어짐**

* Publisher 는 Topic을 발행(publish) 하고, 
* Subscriber는 Topic에 구독(subscribe)함. 
* Broker는 이들을 중계하는 역할을 하며, 
* 단일 Topic에 여러 Subscriber가 구독할 수 있기 때문에, 1:N 통신 구축에도 매우 유용함

![MQTT 통신 데이터분배](C:\dev\TIL\2020_11_21\img\MQTT 통신 데이터분배.PNG)

* 이런식으로 계층을 구성하면, IoT 센서와 같은 데이터를 관리하기에 매우 용이함

## MQTT - QoS(Quality of Service)

* 0 : 메세지는 한번만 전달되며, 전달이후의 수신과정을 체크하지 않음

* 1 : 메세지는 한번 이상 전달되고, 핸드셰이킹 과정을 추적하나, 엄격하게 추적하지 않기 때문에 중복수신의 가능성이 있음
* 2 : 메세지는 한번만 전달되고, 핸드셰이킹의 모든 과정을 체크함

**QoS의 단계가 높아질 수록 통신의 품질은 향상되지만, 그에 따라 성능 저하의 가능성이 높아짐**



## 실습 및 구현

#### MQTT 브로커 구동 - Mosquitto 이용

* 도커 이미지를 활용해 포트를 개방 및 Mosquitto 브로커를 실행

```
$ docker run -it -p 1883:1883 -p 9001:9001 -v /mosquitto/data -v /mosquitto/log eclipse-mosquitto
```



#### MQTT-Explorer 툴을 이용해 topic 전송

* MQTT-Explorer을 이용해 topic을 발행(publish)
* Python에서 구독(subscribe) 하여 데이터를 가져옴

![MQTT-Explorer0](C:\dev\TIL\2020_11_21\img\MQTT-Explorer0.PNG)

![MQTT-Explorer](C:\dev\TIL\2020_11_21\img\MQTT-Explorer.PNG)



* 파이썬 코드

```python
import paho.mqtt.client as mqtt


# The callback for when the client receives a CONNACK response from the server.
def on_connect(client, userdata, flags, rc):
    print("Connected with result code " + str(rc))
    client.subscribe("/test/1")  # Topic /test/1 을 구독한다.


# The callback for when a PUBLISH message is received from the server.
def on_message(client, userdata, msg):
    print(msg.topic + " " + str(msg.payload))


client = mqtt.Client()
client.on_connect = on_connect
client.on_message = on_message
client.connect("127.0.0.1")  # — 브로커 IP 주소
client.loop_forever()
```

