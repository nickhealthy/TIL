# Today I Learned

* 리눅스 AWS-CLI 설정
* 라즈베리파이 + 파이카메라 + S3 버킷에 이미지 업로드

---

## 리눅스 AWS-CLI 설정

1. `Curl` 명령을 사용하여 다운로드

```bash
$ curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
```

2. 패키지에서 파일을 추출

```bash
$ unzip awscli-bundle.zip
```

3. 설치 프로그램 실행

```bash
sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```

* 설치 관리자는 `/usr/local/aws`에서 AWS CLI를 설치하고,
* `/usr/local/bin` 디렉터리에 `aws`를 생성함
  * `-b` 옵션을 사용하여 **symlink**를 생성하면 사용자의 `$PATH` 변수에 설치 디렉터리를 지정할 필요가 없음
    * 이렇게하면 모든 사용자가 임의 디렉터리에서 `aws`를 입력하여 AWS CLI를 호출할 수 있음
* (옵션사항) 위의 사항은 시스템 기본 버전의 Python에서 실행됨. 대체 버전의 Python을 설치하고 해당 버전을 사용하여 AWS CLI를 설치하려는 경우

```bash
# python 버전을 아래와 같이 추가로 명시
$ sudo /usr/local/bin/python3.7 awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
```

4. AWS CLI가 설치되어 있는지 확인

```bash
$ aws --version
```



## 라즈베리파이 + 파이카메라 + S3 버킷에 이미지 업로드

* picameraToUploadS3.py

```python
import os, boto3, logging
from botocore.exceptions import ClientError
from time import sleep
from picamera import PiCamera


# 파이 카메라 설정
camera = PiCamera (resolution=(1280, 720), framerate=30)
camera.iso = 100
sleep(2)
camera.shutter_speed = camera.exposure_speed
camera.exposure_mode = 'off'
g = camera.awb_gains
camera.awb_mode = 'off'
camera.awb_gains = g
#고정된 설정으로 여러 사진 찍기
camera.capture_sequence(['image%02d.jpg' % i for i in range(10)])
# 파이카메라에서 찍은 사진 폴더에 임포트
path_dir = '/home/pi/workspace/tx'
file_list = os.listdir(path_dir)


def create_bucket(bucket_name, region=None):
    try:
        if region is None:
            s3_client = boto3.client('s3')            
            s3_client.create_bucket(Bucket=bucket_name)
        else:
            s3_client = boto3.client('s3', region_name=region)
            location = {'LocationConstraint': region}
            s3_client.create_bucket(Bucket=bucket_name,
                                    CreateBucketConfiguration=location)
    except ClientError as e:
        logging.error(e)
        return False
    return True


def upload_file(file_name, bucket, object_name=None):
    # If S3 object_name was not specified, use file_name
    if object_name is None:
        object_name = file_name

    # Upload the file
    s3_client = boto3.client('s3')
    try:
        response = s3_client.upload_file(file_name, bucket, object_name)
    except ClientError as e:
        logging.error(e)
        return False
    return True


# 버킷 생성
create_bucket('picamera-make-images')
# 파일 버킷에 업로드
for i in file_list:
    upload_file(i, 'picamera-make-images')

```

