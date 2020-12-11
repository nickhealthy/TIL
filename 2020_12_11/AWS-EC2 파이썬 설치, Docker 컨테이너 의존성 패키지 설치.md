# Today I Learned

* AWS-EC2 안에 Python 버전 재설치
* Docker 컨테이너에 아마존 리눅스 의존성 패키지 설치

---



1. Python3 버전이 설치되어 있는지 확인

```bash
[ec2-user ~]$ yum list installed | grep -i python3
# 설치가 되어있다면 나오는 문구 
python3.x86_64                        3.7.4-1.amzn2.0.4              @amzn2-core
python3-libs.x86_64                   3.7.4-1.amzn2.0.4              @amzn2-core
python3-pip.noarch                    9.0.3-1.amzn2.0.1              @amzn2-core
python3-setuptools.noarch             38.4.0-3.amzn2.0.6             @amzn2-core

# 파이썬이 설치된 경로
whereis python3

# 파이썬 설치
$ yum install python3
```

2. Python3 가상환경 생성 및 활성화

```bash
[ec2-user ~]$ python3 --version

[ec2-user ~]$ python3 -m venv venv

[ec2-user ~]$ source ~/venv/bin/activate
```

3. 환경 내에 최신 pip 모듈이 설치되어 있는지 확인

```bash
(venv) [ec2-user ~]$ pip install pip --upgrade
```

4. 로그인할 때 가상 환경을 자동으로 활성화하려면 **~/.bashrc** 파일에 추가

```bash
[ec2-user ~]$ echo "source ${HOME}/venv/bin/activate" >> ${HOME}/.bashrc
```

5. 홈 디렉터리에 **~/.bashrc** 파일을 소싱하여 환경의 bash 환경을 다시 로드합니다. 다시 로드하면 가상 환경이 자동으로 활성화됩니다. 프롬프트에 변경 사항(**env**)이 반영됩니다. 이 변경 사항은 향후 SSH 세션에도 적용됨

```bash
[ec2-user ~]$ source ~/.bashrc
```

---



## Docker 컨테이너에 아마존 리눅스 의존성 패키지 설치

Lambda안에서 동작할 배포 패키지를 만들어야 하는데 C언어 기반의 패키지들이 있었다.

* EC2를 생성해 안에서 필요 패키지들을 다운받고 다시 `host pc`로 파일을 보내도 되지만, 쓸데 없는 비용 발생이 됨
* Docker 이미지 안에서 쉘 스크립트를 이용해 의존성 패키지들을 다운받고 배포 패키지를 만드는 것으로 해결



#### 프로세스

1. Docker 이미지 받아오기 & 실행

```bash
$ docker run --rm -v $(pwd):/outputs --name lambdapack -d amazonlinux:latest tail -f
```

※ 볼륨 공유 마운트가 안되는 현상을 봤더니, 임의의 폴더가 생성되고 엉뚱한 폴더를 공유하고 있었음

* `inspect` 명령으로 확인

```bash
# lambdapack : 컨테이너 이름
$ docker inspect lambdapack
...
"Mounts": [
            {
                "Type": "bind",
                "Source": "C:\\dev\\multicampus-project3\\tf_model",
                "Destination": "/outputs",
                "Mode": "",
                "RW": true,
                "Propagation": "rprivate"
            }
        ],
...
```

2. `tf_model` 폴더 안에 배포할 소스코드(python)과 쉘 스크립트(buildPack.sh)를 작성

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

3. `pack.zip` 파일이 같은 디렉터리 파일에 생성되는 것을 확인
   * 배포 패키지 생성 완료



