# Today I Learned



* 컨피그맵(Configmap), 시크릿(Secret)

  * 컨피그맵

    * 파드에서 컨피그맵을 사용하는 방법1. 컨피그맵의 값을 환경변수로 사용

    * 파드에서 컨피그맵을 사용하는 방법 2. 컨피그맵의 값을 포드 내부 파일로 마운트해서 사용
    * 파일로부터 컨피그맵을 생성
    * 여러 개의 키-값 형태의 내용으로 구성된 설정 파일을 한번에 컨피그맵으로 설정
    * YAML 파일로 컨피그맵을 정의

  * 시크릿(Secret)

    * 시크릿 생성 방법
    * 시크릿에 저장된 키-값 쌍을 파드로 가져오기
    * 시크릿은 사용 목적에 따라 <u>여러 종류의 시크릿</u>을 사용할 수 있음

  * kubernetes.io/dockerconfigjson - private registry에 접근할 때 사용하는 인증 정보를 저장하는 시크릿

    * TLS 타입 (암호화 통신을할 때 사용되는 키)
    * 컨피그맵이나 시크릿의 업데이트한 내용을 애플리케이션에서 사용하는 설정값에 반영

  * 리소스 정리

---



### 컨피그맵(Configmap), 시크릿(Secret)

* 설정값을 파드(pod)로 전달



#### 컨피그맵

* 일반적인 설정 정보(값)을 저장할 수 있는 쿠버네티스 오브젝트
* 네임스페이스 별로 존재



1. **컨피그맵 설정**

```bash
vagrant@ubuntu:~$ kubectl create configmap log-level-configmap --from-literal LOG_LEVEL=DEBUG
`configmap/log-level-configmap created

# start-k8s : 컨피그맵 이름
# -from-literal k8s=kubernetes --from-literal container=docker : 키 값
vagrant@ubuntu:~$ kubectl create configmap start-k8s --from-literal k8s=kubernetes --from-literal container=docker
`configmap/start-k8s created  
```

2. **컨피그맵 확인**

```bash
vagrant@ubuntu:~$ kubectl get configmap
NAME                  DATA   AGE
`log-level-configmap   1      3m41s
`start-k8s             2      2m27s

# 상세정보 확인
vagrant@ubuntu:~$ kubectl describe configmap log-level-configmap
Name:         log-level-configmap
Namespace:    default
Labels:       <none>
Annotations:  <none>
```

3. **컨피그맵을 YAML 형식으로 출력**

```bash
vagrant@ubuntu:~$ kubectl get configmap log-level-configmap -o yaml

apiVersion: v1
data:
`  LOG_LEVEL: DEBUG
kind: ConfigMap
	:
```

---



#### 파드에서 컨피그맵을 사용하는 방법1. 컨피그맵의 값을 환경변수로 사용

1. **파드 정의 - YAML 파일 생성**

```bash
vagrant@ubuntu:~$ vi all-env-from-configmap.yml

apiVersion: v1
kind: Pod
metadata:
  name: container-env-example
spec:
  containers:
    - name: my-container
      image: busybox
      args: ['tail', '-f', '/dev/null']
`      envFrom:                          ⇒ 컨피그맵에 정의된 모든 키-값 쌍을 가져와서 환경변수로 설정
#  my-container의 환경변수로 설정
`        - configMapRef:
`            name: log-level-configmap   ⇒ LOG_LEVEL=DEBUG
`        - configMapRef:
`            name: start-k8s             ⇒ k8s=kubernetes --from-literal container=docker
```

2. **파드 생성**

```bash
vagrant@ubuntu:~$ kubectl apply -f all-env-from-configmap.yml
pod/container-env-example created

vagrant@ubuntu:~$ kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
`container-env-example                  1/1     Running   0          30s
```

* `-c` 옵션으로 컨테이너 여러개 일시 특정 컨테이너 설정 가능

3. **컨테이너의 환경변수를 확인**
   * `--env`는 리눅스 명령어

```bash
vagrant@ubuntu:~$ kubectl exec container-env-example -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=container-env-example
# 들어간 환경 변수들
`k8s=kubernetes
`LOG_LEVEL=DEBUG
`container=docker
```

4. **컨피맵에 존재하는 키-값 쌍 중에서 원하는 데이터만 환경변수로 설정(변경)**

```bash
vagrant@ubuntu:~$ cp all-env-from-configmap.yml selective-env-from-configmap.yml
vagrant@ubuntu:~$ vi selective-env-from-configmap.yml

apiVersion: v1
kind: Pod
metadata:
  name: container-env-example
spec:
  containers:
    - name: my-container
      image: busybox
      args: ['tail', '-f', '/dev/null']
      env:
        - name: ENV_KEYNAME_1               ⇐ 새롭게 설정될 환경변수 이름 -------------+ ENV_KEYNAME_1=LOG_LEVEL값
          valueFrom:                                                                    | 
            configMapRef:                                                               | 
              name: log-level-configmap     ⇐ 참조할 컨피그맵 이름                     |
              key: LOG_LEVEL                ⇐ 참조할 컨피그맵에서 가져올 데이터의 키 --+
        - name: ENV_KEYNAME_2
          valueFrom:
            configMapRef:
              name: start-k8s
              key: k8s
```

※ 기존 POD 삭제 (POD 이름이 중복되므로)

```bash
vagrant@ubuntu:~$ kubectl delete -f all-env-from-configmap.yml
```

5.  **파드 생성 및 파드 생성 확인**

```bash
vagrant@ubuntu:~$ kubectl apply -f selective-env-from-configmap.yml
vagrant@ubuntu:~$ kubectl get pods

# 컨테이너 내부의 환경변수를 확인
vagrant@ubuntu:~$ kubectl exec container-env-example -- env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=container-env-example
`ENV_KEYNAME_1=DEBUG
`ENV_KEYNAME_2=kubernetes
```

---



#### 파드에서 컨피그맵을 사용하는 방법 2. 컨피그맵의 값을 포드 내부 파일로 마운트해서 사용

1. **컨피그맵의 모든 키-쌍 데이터를 파드에 마운트**

```bash
vagrant@ubuntu:~$ vi volume-mount-configmap.yml

apiVersion: v1
kind: Pod
metadata:
  name: configmap-volume-pod
spec:
  containers:
    - name: my-container
      image: busybox
      args: ["tail", "-f", "/dev/null"]
      volumeMounts:                  ⇐ #1에서 정의한 볼륨을 컨테이너 내부의 어떤 디렉터리에 마운트할 것인지 명시
        - name: configmap-volume     ⇐ 컨피그맵 볼룸의 이름 (#1에서 정의한 이름)
          mountPath: /etc/config     ⇐ 컨피그맵 파일이 위치할 경로

  volumes:                           ⇐ #1 사용할 볼륨 목록 
    - name: configmap-volume           
      configMap:
        name: start-k8s              ⇐ 컨피그맵 이름

```

2. **파드 생성 및 확인**

```bash
vagrant@ubuntu:~$ kubectl apply -f volume-mount-configmap.yml

vagrant@ubuntu:~$ kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
`configmap-volume-pod                   1/1     Running   0          27s
```

3. **파드의 /etc/config 디렉터리를 확인**

```bash
vagrant@ubuntu:~$ kubectl exec configmap-volume-pod -- ls -l /etc/config
total 0
`lrwxrwxrwx    1 root     root            16 Sep 22 02:33 container -> ..data/container
`lrwxrwxrwx    1 root     root            10 Sep 22 02:33 k8s -> ..data/k8s
```

4. **파일 내용 확인**

```bash
vagrant@ubuntu:~$ kubectl exec configmap-volume-pod -- cat /etc/config/container
docker                        

vagrant@ubuntu:~$ kubectl exec configmap-volume-pod -- cat /etc/config/k8s
kubernetes
```

* 컨피그맵에 **키는 파일명**으로, **값은 파일의 내용**으로 변경되어서 전달

5. **원하는 키-값 쌍의 데이터만 선택해서 포드로 마운트**

```bash
vagrant@ubuntu:~$ cp volume-mount-configmap.yml selective-volume-configmap.yml
vagrant@ubuntu:~$ vi selective-volume-configmap.yml

apiVersion: v1
kind: Pod
metadata:
  name: configmap-volume-pod
spec:
  containers:
    - name: my-container
      image: busybox
      args: ["tail", "-f", "/dev/null"]
      volumeMounts:
        - name: configmap-volume
          mountPath: /etc/config
  volumes:
    - name: configmap-volume
      configMap:
        name: start-k8s
        items:
        - key: k8s                ⇐ 가져올 키를 명시
          path: k8s_fullname      ⇐ 키 값을 저장할 파일명
```

---



#### 파일로부터 컨피그맵을 생성

1.  **테스트 파일 생성**

```bash
vagrant@ubuntu:~$ echo Hello, world! >> index.html
vagrant@ubuntu:~$ cat index.html
Hello, world!
```

2.  **테스트 파일(index.html)을 이용해서 index-file이라는 컨피그맵을 생성**

```bash
vagrant@ubuntu:~$ kubectl create configmap index-file --from-file ./index.html
configmap/index-file created
```

3. **생성한 index-file 컨피그맵을 확인**

```bash
vagrant@ubuntu:~$ kubectl describe configmap index-file
Name:         index-file
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
`index.html: 	⇐ 파일명이 키로 사용
----
`Hello, world!	⇐ 파일의 내용이 값(value)로 사용

Events:  <none>
```

4. **키이름을 직접 지정해서 컨피그맵을 생성**

```bash
vagrant@ubuntu:~$ kubectl create configmap index-file-customkey --from-file myindex=./index.html
`configmap/index-file-customkey create

vagrant@ubuntu:~$ kubectl describe configmap index-file-customkey
Name:         index-file-customkey
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
`myindex:		⇐ 컨피그맵 생성 시 지정한 키 이름을 사용
----
`Hello, world!

Events:  <none>
```

---



#### 여러 개의 키-값 형태의 내용으로 구성된 설정 파일을 한번에 컨피그맵으로 설정

1.  **키-값 형태의 내용으로 구성된 설정 파일을 생성**

```bash
vagrant@ubuntu:~$ vi ./multiple-keyvalue.env

mykey1=myvalue1
mykey2=myvalue2
mykey3=myvalue3
```

2. **설정 파일에 정의된 키-값 형태를 컨피그맵의 키-값 항목으로 일괄 전환**

* kubectl create configmap abcd --from-literal mykey1=myvalue1 --from-literal mykey2=myvalue2 --from-literal mykey3=myvalue3 … 형식의 명령어를 **파일을 이용해서 구현**

```bash
vagrant@ubuntu:~$ kubectl create configmap from-envfile --from-env-file ./multiple-keyvalue.env

vagrant@ubuntu:~$ kubectl describe configmap from-envfile
Name:         from-envfile
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
`mykey1:
----
`myvalue1
`mykey2:
----
`myvalue2
`mykey3:
----
`myvalue3
Events:  <none>
```

---



#### YAML 파일로 컨피그맵을 정의

1. **컨피그맵을 실제로 생성하지 않고 YAML 형식으로 출력 `(dry-run)`**

```bash
vagrant@ubuntu:~$ kubectl create configmap my-configmap --from-literal mykey=myvalue --dry-run -o yaml

# 컨피그맵 오브젝트를 생성하지는 않음
vagrant@ubuntu:~$ kubectl get configmap
NAME                   DATA   AGE			⇒ my-configmap 이름의 컨피그맵이 존재하지 않음
from-envfile           3      4m2s			→ --dry-run 옵션: 컨피그맵 오브젝트를 생성하지는 않음
index-file             1      16m			   
index-file-customkey   1      12m
log-level-configmap    1      3h21m
start-k8s              2      3h20m
```

2.  **YAML 형식의 출력을 YAML 파일로 저장(리다이렉션)**

```bash
vagrant@ubuntu:~$ kubectl create configmap my-configmap --from-literal mykey=myvalue --dry-run -o yaml > `my-config.yml`

vagrant@ubuntu:~$ cat my-config.yml
apiVersion: v1
data:
  mykey: myvalue
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: my-configmap
```

3. **YAML 파일로 컨피그맵을 생성**

```bash
vagrant@ubuntu:~$ kubectl apply -f my-config.yml

vagrant@ubuntu:~$ kubectl get configmaps
NAME                   DATA   AGE
from-envfile           3      13m
index-file             1      26m
index-file-customkey   1      22m
log-level-configmap    1      3h31m
`my-configmap           1      9s
start-k8s              2      3h30m
```

---



#### 시크릿(Secret)

* 민감한 정보를 저장하기 위한 용도
* 네임스페이스에 종속



#### 시크릿 생성 방법

1. **시크릿 생성 방법 /  password=1q2w3e4r 라는 키-값을 저장하는 my-password 이름의 시크릿을 생성**

```bash
vagrant@ubuntu:~$ kubectl create secret generic my-password --from-literal password=1q2w3e4r
secret/my-password created

vagrant@ubuntu:~$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
`default-token-sh8hv   kubernetes.io/service-account-token   3      3d22h ⇐ ServiceAccount에 의해 네임스페이스별로 자동으로 생성된 시크릿
`my-password           Opaque                                1      9s
```

2. **파일로부터 시크릿 생성**

```bash
vagrant@ubuntu:~$ echo mypassword > pw1 && echo yourpassword > pw2
vagrant@ubuntu:~$ cat pw1
`mypassword
vagrant@ubuntu:~$ cat pw2
`yourpassword

vagrant@ubuntu:~$ kubectl create secret generic out-password --from-file pw1 --from-file pw2
`secret/out-password created


```

3. **시크릿 내용을 확인**

```bash
vagrant@ubuntu:~$ kubectl describe secret my-password
Name:         my-password
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
`password:  8 bytes		⇐ password 키에 해당하는 값을 확인할 수 없음 (값의 크기(길이)만 출력)


vagrant@ubuntu:~$ kubectl get secret my-password -o yaml
apiVersion: v1
data:
  `password: MXEydzNlNHI=	⇐ BASE64로 인코딩 (키-값 형태)


vagrant@ubuntu:~$ echo MXEydzNlNHI= | base64 -d
`1q2w3e4r
```

---



#### 시크릿에 저장된 키-값 쌍을 파드로 가져오기

* **방법 1.시크릿에 저장된 특정 키-값 쌍을 포드의 환경변수로 가져오기**

```bash
vagrant@ubuntu:~$ vi env-from-secret.yml


apiVersion: v1
kind: Pod
metadata:
  name: secret-env-example
spec:
  containers:
    - name: my-container
      image: busybox
      args: ["tail", "-f", "/dev/null"]
      envFrom:
        - secretRef:
            name: my-password
            
vagrant@ubuntu:~$ kubectl apply -f env-from-secret.yml
pod/secret-env-example created

vagrant@ubuntu:~$ kubectl exec secret-env-example -- env
`PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
`HOSTNAME=secret-env-example
`password=1q2w3e4r
```

2. **시크릿에 저장된 특정 키-값 쌍을 포드의 환경변수로 가져오기**

```bash
vagrant@ubuntu:~$ cp env-from-secret.yml selective-env-from-secret.yml
vagrant@ubuntu:~$ vi selective-env-from-secret.yml

apiVersion: v1
kind: Pod
metadata:
  name: secret-env-example
spec:
  containers:
    - name: my-container
      image: busybox
      args: ["tail", "-f", "/dev/null"]
      env:
        - name: YOUR_PASSWORD
          valueFrom:
            secretKeyRef:
              name: out-password
              key: pw2

vagrant@ubuntu:~$ kubectl delete -f env-from-secret.yml
pod "secret-env-example" deleted

vagrant@ubuntu:~$ kubectl apply -f selective-env-from-secret.yml
pod/secret-env-example created

vagrant@ubuntu:~$ kubectl exec secret-env-example -- env | grep YOUR_PASSWORD
YOUR_PASSWORD=yourpassword			`⇐ out-password 시크릿에 pw2 키에 저장되어 있는 값
```



* **방법2. 시크릿에 저장된 키-값 데이터를 파일로 파드의 볼륨 마운트**

```bash
vagrant@ubuntu:~$ cp env-from-secret.yml volume-mount-secret.yml
vagrant@ubuntu:~$ vi volume-mount-secret.yml

apiVersion: v1
kind: Pod
metadata:
  name: secret-env-example
spec:
  containers:
    - name: my-container
      image: busybox
      args: ["tail", "-f", "/dev/null"]
      volumeMounts:
        - name: secret-volume
          `mountPath: /etc/secret      ⇐ 컨테이너 내부에 /etc/secret/ 디렉터리 아래에 시크릿에 저장된 키 이름의 
  `volumes:                               파일을 생성 (파일 내용은 키에 해당하는 값)
    - name: secret-volume
      secret:
        secretName: out-password

vagrant@ubuntu:~$ kubectl delete -f selective-env-from-secret.yml
pod "secret-env-example" deleted

vagrant@ubuntu:~$ kubectl apply -f volume-mount-secret.yml
pod/secret-env-example created

vagrant@ubuntu:~$ kubectl exec secret-env-example -- ls /etc/secret
`pw1
`pw2

vagrant@ubuntu:~$ kubectl exec secret-env-example -- cat /etc/secret/pw1 /etc/secret/pw2
`mypassword
`yourpassword
```



* **방법 3.시크릿의 저장된 특정 키-값 데이터를 파일로 포드의 볼륨에 마운트**

```bash
vagrant@ubuntu:~$ cp volume-mount-secret.yml selective-volume-secret.yml
vagrant@ubuntu:~$ vi selective-volume-secret.yml

apiVersion: v1
kind: Pod
metadata:
  name: secret-env-example
spec:
  containers:
    - name: my-container
      image: busybox
      args: ["tail", "-f", "/dev/null"]
      volumeMounts:
        - name: secret-volume
          mountPath: /etc/secret
  volumes:
    - name: secret-volume
      secret:
        secretName: out-password
        `items:
          `- key: pw1
            `path: password1


vagrant@ubuntu:~$ kubectl delete -f volume-mount-secret.yml
`pod "secret-env-example" deleted

vagrant@ubuntu:~$ kubectl apply -f selective-volume-secret.yml
`pod/secret-env-example created

vagrant@ubuntu:~$ kubectl exec secret-env-example -- ls /etc/secret
`password1

vagrant@ubuntu:~$ kubectl exec secret-env-example -- cat /etc/secret/password1
`mypassword
```



#### **시크릿은 사용 목적에 따라 <u>여러 종류의 시크릿</u>을 사용할 수 있음**

```bash
vagrant@ubuntu:~$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
default-token-sh8hv   kubernetes.io/service-account-token   3      3d23h
my-password           `Opaque`                                1      65m
out-password          `Opaque`                                2      60m
```



#### Opaque 타입

* 시크릿 종류를 명시하지 않으면 자동으로 설정되는 타입
* kubectl create secret generic 명령으로 생성
* 사용자가 정의한 데이터를 저장할 수 있는 일반적인 목적의 시크릿

---



#### kubernetes.io/dockerconfigjson - private registry에 접근할 때 사용하는 인증 정보를 저장하는 시크릿

1.  **~/.docker/config.json 파일을 이용해서 시크릿을 생성**

```bash
vagrant@ubuntu:~$ docker login

vagrant@ubuntu:~$ ls -al ~/.docker
total 12
drwx------ 2 vagrant vagrant 4096 Sep 22 06:06 .
drwxr-xr-x 9 vagrant vagrant 4096 Sep 22 06:06 ..
-rw------- 1 vagrant vagrant  165 Sep 22 06:06 `config.json	⇐ docker login 성공하면 도커 엔진이 자동으로 config.json 파일에 인증 정보를 저장
							  `→ config.json 파일을 그대로 시크릿으로 생성
							  
vagrant@ubuntu:~$ cat ~/.docker/config.json
{
        "auths": {
                "https://index.docker.io/v1/": {
                        "auth": "bXzzzmpxxxxQGyyyyU5MjE4"	`⇐ BASE64로 인코딩
                        
vagrant@ubuntu:~$ kubectl create secret generic registry-auth --from-file=.dockerconfigjson=/home/vagrant/.docker/config.json --type=kubernetes.io/dockerconfigjson
`secret/registry-auth created

vagrant@ubuntu:~$ kubectl get secrets
NAME                  TYPE                                  DATA   AGE
default-token-sh8hv   kubernetes.io/service-account-token   3      4d
my-password           Opaque                                1      83m
out-password          Opaque                                2      79m
# 정보를 수정하는데 사용하는 타입
`registry-auth         kubernetes.io/dockerconfigjson        1      12s
```

2. **직접 인증 정보를 명시**

```bash
vagrant@ubuntu:~$ kubectl create secret docker-registry registry-auth-by-cmd --docker-username=myanjini --docker-password=wkrlvotmdnjem
`secret/registry-auth-by-cmd created

vagrant@ubuntu:~$ kubectl get secrets
NAME                   TYPE                                  DATA   AGE
default-token-sh8hv    kubernetes.io/service-account-token   3      4d
my-password            Opaque                                1      89m
out-password           Opaque                                2      84m
registry-auth          kubernetes.io/dockerconfigjson        1      5m53s
`registry-auth-by-cmd   kubernetes.io/dockerconfigjson        1      31s
```

---



#### TLS 타입 (암호화 통신을할 때 사용되는 키)

* TLS 연결에 사용되는 공개키와 비밀키 등을 저장하는데 사용
* 포드 내의 애플레케이션이 보안 연결을 위해 인증서나 비밀키 등을 가져와야 할 때 TLS 타입의 시크릿을 제공
* kubectl create secret tls 명령으로 생성



1. **테스트용 인증서와 비밀키를 생성**

```bash
vagrant@ubuntu:~$ openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -subj "/CN=example.com" -keyout cert.key -out cert.crt
Can't load /home/vagrant/.rnd into RNG
139804440682944:error:2406F079:random number generator:RAND_load_file:Cannot open file:../crypto/rand/randfile.c:88:Filename=/home/vagrant/.rnd
Generating a RSA private key
..................++++
...............................................++++
writing new private key to 'cert.key'
-----

vagrant@ubuntu:~$ ls cert*
`cert.crt  cert.key
```

2. **TLS 타입의 시크릿을 생성**

```bash
vagrant@ubuntu:~$ kubectl create secret tls my-tls --cert ./cert.crt --key ./cert.key
secret/my-tls created

vagrant@ubuntu:~$ kubectl get secrets my-tls -o yaml
```

---



#### 컨피그맵이나 시크릿의 업데이트한 내용을 애플리케이션에서 사용하는 설정값에 반영

1. **컨피그맵 정의(매니페스트) 파일 내용을 확인**

```bash
vagrant@ubuntu:~$ cat my-config.yml
apiVersion: v1
data:
  'mykey: myvalue
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: my-configmap
```

2. **컨피그맵을 생성**

```bash
vagrant@ubuntu:~$ kubectl apply -f my-config.yml
```

3. **컨피그맵 내용 확인**

```bash
vagrant@ubuntu:~$ kubectl get configmap my-configmap -o yaml

apiVersion: v1
`data:
`  mykey: myvalue
```

4. **kubectl edit 명령으로 컨피그맵 내용을 변경**

```bash
vagrant@ubuntu:~$ kubectl edit configmap/my-configmap

apiVersion: v1
data:
  mykey: yuourvalue      ⇐ 내용 변경 후 저장
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
                      :
                      
vagrant@ubuntu:~$ kubectl get configmap my-configmap -o yaml
apiVersion: v1
`data:
  `mykey: yourvalue
```

5. **yaml 파일을 수정 후 kubectl apply 명령으로 재생성**

```bash
vagrant@ubuntu:~$ cat ./my-config.yml
apiVersion: v1
data:
  `mykey: myvalue

vagrant@ubuntu:~$ sed -i -e 's/myvalue/ourvalues/g' my-config.yml

vagrant@ubuntu:~$ cat ./my-config.yml
apiVersion: v1
data:
  `mykey: ourvalues
kind: ConfigMap
metadata:
  creationTimestamp: null
  name: my-configmap


vagrant@ubuntu:~$ kubectl apply -f my-config.yml
`configmap/my-configmap configured		⇐ 변경

vagrant@ubuntu:~$ kubectl get configmap my-configmap -o yaml
apiVersion: v1
data:
  `mykey: ourvalues
kind: ConfigMap
metadata:
  annotations:
	:
```



#### 컨피그맵의 내용이 업데이트 되었을 때, 변경된 내용이 파드에 반영되는 것을 확인

```bash
vagrant@ubuntu:~$ cat volume-mount-configmap.yml
apiVersion: v1
kind: Pod
metadata:
  name: configmap-volume-pod
spec:
  containers:
    - name: my-container
      image: busybox
      args: ["tail", "-f", "/dev/null"]
      volumeMounts:
        - name: configmap-volume
          mountPath: /etc/config
  volumes:
    - name: configmap-volume
      configMap:
        name: start-k8s

vagrant@ubuntu:~$ kubectl apply -f volume-mount-configmap.yml
`pod/configmap-volume-pod created

vagrant@ubuntu:~$ kubectl exec configmap-volume-pod -- cat /etc/config/container
`docker

dockervagrant@ubuntu:~$ kubectl edit configmap/start-k8s
`container 키의 값을 docker 에서 docker_and_kubernetes 로 변경 후 저장
`configmap/start-k8s edited

vagrant@ubuntu:~$ kubectl get configmap start-k8s -o yaml
apiVersion: v1
data:
  `container: docker_and_kubernetes			⇐ 컨피그맵 오브젝트는 변경된 것을 확인
  k8s: kubernetes
kind: ConfigMap
metadata:
  creationTimestamp: "2020-09-22T01:14:20Z"
  managedFields:
  - apiVersion: v1
    fieldsType: FieldsV1
    fieldsV1:
      f:data:
        .: {}
        f:k8s: {}
		:

vagrant@ubuntu:~$ kubectl exec configmap-volume-pod -- cat /etc/config/container
`docker_and_kubernetes					⇐ 변경이 반영된 것을 확인
```

---



#### 리소스 정리

```bash
vagrant@ubuntu:~$ kubectl delete deployment,replicaset,pod,service,configmap,secret --all

or 

vagrant@ubuntu:~$ minikube stop
vagrant@ubuntu:~$ minikube delete	⇐ 모든 리소스 삭제
vagrant@ubuntu:~$ minikube start
```

