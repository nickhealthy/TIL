# Today I Learned



- mariaDB를 원격으로 접속

- mongoDB를 원격으로 접속

- iris를 활용한 ML 기초

  > KNN 알고리즘

- One Hot Encoding (titanic)

----



### maria DB를 원격으로 접속

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_17/1.PNG)

1. 원격 접속이 가능한 사용자 계정을 root 계정으로 접속해서 만든다.

   > create user 'python'@%'identified by 'python';
   >
   > grant all on *.*to 'python'@'%';
   >
   > flush privileges;

 

2. DB 서버 역할을 하는 컴퓨터는 방화벽을 사용하지 않음으로 설정한다.

   > cmd 창을 열고 ping ip로 응답이 오는지 확인한다.

 

3. sample program을 수정해서 원격DB에 members 테이블이 생성되는지 확인한다.

 

4. python manage.py migrate

   > 마이그레이션을 다시 해야 한다.
   >
   > python manage.py createsuperuser
   >
   > admin 계정 새로 생성

---



### mongoDB를 원격으로 접속

1. pip install djongo

2. roboDB로 계정, 데이터베이스, 컬렉션 만들기

3. settings.py에서 설정해준 뒤 python manage.py migrate

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_17/2.PNG)

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_17/3.PNG)

---



### iris를 활용한 ML 기초

1. test.csv는 훈련데이터, test.csv ML 후 data를 추출 / 정답은 train.csv에 있음

2. 지도학습에서는 훈련데이터와 정답데이터(lable)가 항상 있다. 

3. 풀고자 하는 목표에 따른 분류

   > 현재: 지도학습 - 분류(생존or사망)
   >
   > 향후 예측을 통해 숫자를 예측(Serial data) - 회귀

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_17/4.PNG)

>  Classification: 분류 / Regression: 회귀

4. null 값 허용 안함 / 문자열 처리 못함(숫자형으로 처리)

5. 좋은 입력 데이터를 골라 만들어 내는 과정을 'feature engineering'

---



### KNN 알고리즘

* k-최근접 이웃 알고리즘

 

![img](https://github.com/nickhealthy/TIL/blob/master/2020_08_17/5.PNG)

 

 

 

 

 

 

 

 

 

# kaggle은 'y_test' 가 없다.

1. 훈련 데이터 - X,Y_train / 테스트 데이터 - X,Y_test

2. X는 입력, Y는 답 / Y_test는 kaggle에 없음 >> 답이기때문에

 ※ test.csv의 survived 컬럼이 X_test을 의미함. (테스트 데이터 입력용)

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image6.png)

![img](C:\Users\NICK_~1\AppData\Local\Temp\clipData\clip_html0.files\clip_html0_image7.png)

 

# One Hot Encoding

1. OMR처럼 해당하는 값이 있는건 모두 '1', 아닌 값들은 모두 0를 의미함

 