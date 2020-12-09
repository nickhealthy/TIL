# Today I Learned

* AWS Lambda에 Tensorflow배포하기
* AWS-Lambda 파일 크기 제한에 따른 S3 업로드

---



## AWS Lambda에 Tensorflow배포하기

[참고 사이트](https://beomi.github.io/2017/12/07/Deploy-Tensorflow-Keras-on-AWS-Lambda/)

#### 기본 정보

AWS Lambda 안에는 `boto3` 라이브러리가 기본적으로 설치되어 있음

#### ※ AWS Lambda는 아마존에서 RedHat 계열의 OS를 새로 만든 Amazon Linux에서 동작

Lambda는 기본적으로 `Amazon Linux` 환경 안에서 실행되기 때문에 배포 패키지, 의존성 라이브러리 등을 Amazon Linux 환경자체에서 생성하는 것이 좋음 (배포 패키지, 라이브러리 등이 Linux 의존성과 호환이 잘 되도록)

* Tensorflow 또한 C의존적인 패키지이기 때문에 OS에 맞는 버전을 받아줘야 함

* 방법 2가지
  * 1. Docker 컨테이너 자체를 만들어 `-v` 옵션과 `shell script` 등으로 의존성 패키지(라이브러리) 설치 및 배포 패키지를 만든다.
    2. Amazon Linux EC2 자체 내에서 만든다. (zip 아카이브, 라이브러리 설치 등)

#### 프로세스

1. s3의 어떤 버킷의 특정한 폴더에 ‘이미지 파일’을 올린다.
2. 이미지 파일이 ‘생성’될 때 AWS Lambda 함수가 실행이 트리거된다.
3. (Lambda) 모델을 s3에서 다운받아 Tensorflow로 읽는다.
4. (Lambda) 함수가 트리거 될때 발생한 `event` 객체를 받아 s3에 업로드된 파일의 정보(버킷, 버킷내 파일의 경로)를 가져온다.
5. (Lambda) s3에 업로드된 이미지 파일을 `boto3`을 통해 가져와 Tensorflow로 Inference를 진행한다.
6. (Lambda) Inference가 끝난 결과물을 s3에 저장하거나 결과값을 DynamoDB에 저장하거나 혹은 API Gateway를 통해 json으로 반환한다.



#### 필요한 라이브러리 import

```python
import boto3, os
import numpy as np
import pandas as pd
import tensorflow as tf
from PIL import Image
```



#### 환경 변수 설정

```python
# Lambda 내에 있는 환경변수로 설정
ACCESS_KEY = os.environ.get('ACCESS_KEY')
SECRET_KEY = os.environ.get('SECRET_KEY')
```



#### S3에 이미지 가져오기 or 업로드 함수 정의

```python
def downloadFromS3(strBucket, s3_path, local_path):
    s3_client = boto3.client(
        's3',
        aws_access_key_id=ACCESS_KEY,
        aws_secret_access_key=SECRET_KEY
    )
    s3_client.download_file(strBucket, s3_path, local_path)


def uploadToS3(bucket, s3_path, local_path):
    s3_client = boto3.client(
        's3',
        aws_access_key_id=ACCESS_KEY,
        aws_secret_access_key=SECRET_KEY
    )
    s3_client.upload_file(local_path, bucket, s3_path)
```



#### S3에서 이미지 파일 & 학습모델(h5) 가져오기

AWS Lambda에서 **쓰기권한**을 가진 폴더는 오직 `/tmp` 폴더임

* 우리가 파일을 받아 사용하려면 /tmp 폴더 내에서 다운 받아야함 (혹은 온메모리에 File 객체로 들고있는 방법도 있습니다.)

```python
# 윗부분 생략
def lambda_handler(event, context):
    bucket_name = event['Records'][0]['bucket']['name']
    file_path = event['Records'][0]['s3']['object']['key']
    file_name = file_path.split('/')[-1]
	downloadFromS3(bucket_name, file_path, '/tmp/'+file_name)
	# 측정할 이미지 가져오기 & 쓰기
    downloadFromS3(bucket_name, file_path, '/tmp/'+file_name)
    # 학습된 모델 가져오기 & 쓰기
    downloadFromS3(bucket_name, 'cat_dog_small_cnn_model.h5', '/tmp/cat_dog_small_cnn_model.h5')
```



#### 이미지 파일 경로 전달 & 모델을 이용한 소스코드 정의

```python
def pridict(img_local_path):
    # model = tf.keras.models.load_model('/tmp/cat_dog_small_cnn_model.h5')
    model = tf.keras.models.load_model('./cat_dog_small_cnn_model.h5')

    def cal_prob():    
        image_w = 150
        image_h = 150 

        test_img_arr=[]
        print('=========== Loading images ===========')
        img = Image.open(img_local_path)
        img = img.convert("RGB")
        img = img.resize((image_w, image_h)) 
        # print(img.shape)
        data = np.asarray(img)
        data = data/255                
        test_img_arr.append(data)  
        test_img_arr = np.array(test_img_arr) 
        print('=========== Calculating probability ===========')
        result = model.predict(test_img_arr)
        print('model 완료, shape: {}'.format(result.shape))

        return result

    def score(s):
        if s >= 0.5:
            return 1
        else:
            return 0

    cal_result = cal_prob()

    df = pd.DataFrame({'path': img_local_path, 
                    'y': np.array([a for a in map(score, cal_result)]).ravel()}, 
                        columns = ['path','y']) 
    print(df)
```



#### 전체 소스코드

* 현재 람다에 업로드해서 결과 값이 `print` 되는지 시험중

```python
import boto3, os
import numpy as np
import pandas as pd
import tensorflow as tf
from PIL import Image


# Lambda 내에 있는 환경변수로 설정
ACCESS_KEY = os.environ.get('ACCESS_KEY')
SECRET_KEY = os.environ.get('SECRET_KEY')

def downloadFromS3(strBucket, s3_path, local_path):
    s3_client = boto3.client(
        's3',
        aws_access_key_id=ACCESS_KEY,
        aws_secret_access_key=SECRET_KEY
    )
    s3_client.download_file(strBucket, s3_path, local_path)


def uploadToS3(bucket, s3_path, local_path):
    s3_client = boto3.client(
        's3',
        aws_access_key_id=ACCESS_KEY,
        aws_secret_access_key=SECRET_KEY
    )
    s3_client.upload_file(local_path, bucket, s3_path)


def pridict(img_local_path):
    # model = tf.keras.models.load_model('/tmp/cat_dog_small_cnn_model.h5')
    model = tf.keras.models.load_model('./cat_dog_small_cnn_model.h5')

    def cal_prob():    
        image_w = 150
        image_h = 150 

        test_img_arr=[]
        print('=========== Loading images ===========')
        img = Image.open(img_local_path)
        img = img.convert("RGB")
        img = img.resize((image_w, image_h)) 
        # print(img.shape)
        data = np.asarray(img)
        data = data/255                
        test_img_arr.append(data)  
        test_img_arr = np.array(test_img_arr) 
        print('=========== Calculating probability ===========')
        result = model.predict(test_img_arr)
        print('model 완료, shape: {}'.format(result.shape))

        return result

    def score(s):
        if s >= 0.5:
            return 1
        else:
            return 0

    cal_result = cal_prob()

    df = pd.DataFrame({'path': img_local_path, 
                    'y': np.array([a for a in map(score, cal_result)]).ravel()}, 
                        columns = ['path','y']) 
    print(df)

def lambda_handler(event, context):
    bucket_name = event['Records'][0]['bucket']['name']
    file_path = event['Records'][0]['s3']['object']['key']
    file_name = file_path.split('/')[-1]
    # 측정할 이미지 가져오기 & 쓰기
    downloadFromS3(bucket_name, file_path, '/tmp/'+file_name)
    # 학습된 모델 가져오기 & 쓰기
    downloadFromS3(bucket_name, 'cat_dog_small_cnn_model.h5', '/tmp/cat_dog_small_cnn_model.h5')
    # 파일 경로 전달
    pridict('/tmp/'+file_name)
```



#### Docker-AmazonLinux 이미지에서 쉘 스크립트를 이용한 zip 아카이브 파일 생성

`buildPack.sh`

```sh
# 기본 패키지 최신 업데이트
# python 3.7 버전 설치
# 기타 패키지 설치
dev_install() {
    yum -y update
    yum -y upgrade
    yum install -y \
    wget \
    gcc \
    gcc-c++ \
    cmake \
    zlib-devel \ 
    zip
    echo "dev_install done..."
    # unzip \
    # blas-devel lapck-devel atlas-devel
}
# 가상환경 생성
mkvirtualenvs() {
    cd /home/
    rm -rf venv
    python3 -m venv venv
    source venv/bin/activate
    echo "mkvirtualenvs done..."
    # text="
    # [global]
    # index-url=http://ftp.daumkakao.com/pypi/simple
    # trusted-host=ftp.daumkakao.com
    # "
    # echo "$text" > $VIRTUAL_ENV/pip.conf 
    # echo "UNDER: pip.conf ==="
    # cat $VIRTUAL_ENV/pip.conf
}

pip_install() {
    # source /home/venv/bin/activate
    pip install -U pip wheel
    # 의존 패키지 설치 x / 해당 패키지 설치
    pip install tensorflow --no-deps
    # pip install h5py
    # pip install pandas
    pip install pillow
    deactivate
}

gather_pack() {
    cd /home/ && rm -rf pack && mkdir pack && cd pack
    cp -R /home/venv/lib/python3.7/site-packages/* .
    cp -R /home/venv/lib64/python3.7/site-packages/* .

    cp /outputs/modelload.py /home/pack/modelload.py

    find . -type d -name "test" -exec rm -rf {} +
    find -name "*.so" | xargs strip
    find -name "*.so.*" | xargs strip

    rm -r pip && rm -r pip-* && rm -r wheel && rm -r wheel-*
    find . | grep -E "(__pycache__|\.pyc$)" | xargs rm -rf
    echo "stripped size $(du -sh /home/pack | cut -f1)"

    zip -FS -r1 /outputs/pack.zip * > /dev/null
    echo "compressed size $(du -sh /outputs/pack.zip | cut -f1)"
}

main() {
    dev_install
    mkvirtualenvs
    pip_install
    gather_pack
}

main
```





## AWS-Lambda 파일 크기 제한에 따른 S3 업로드

람다는 인라인 코드 `3MB`, zip 아카이브 파일 `50MB`, S3 `250MB`로 업로드 파일이 제한됨

​	※ `250MB`는 압축을 풀었을 때 `250MB` 를 초과하지 않아야 함



1. S3 버킷 안에 배포 패키지를 업로드
2. 배포 패키지를 `Public`으로 설정한 뒤 `URL`를 복사

3. 람다 함수 안에 `작업`창에 들어가서 S3 버킷 `URL` 주소를 넣으면 됨

