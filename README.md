## Basic Pytorch Docker Image Example

### 소개
- Pytorch 기반의 딥러닝 학습 코드를 Docker 환경에서 실행하기 위한 간단한 예제를 준비해보았습니다.
- MNIST example code : [https://github.com/pytorch/examples/blob/master/mnist](https://github.com/pytorch/examples/blob/master/mnist) 

<br>

### Docker 파일 및 빌드, 실행에 대한 설명

<br>

#### Dockerfile
- Dockerfile은 docker 실행 환경이 설치된 ([install link](https://docs.docker.com/install/)) 컴퓨터에 실행 환경이 갖춰진 상태로 코드를 실행 할 수 있는 이미지를 빌드하는 Command들을 순차적으로 나열한 코드 파일입니다. 

- Line by line 설명

1. FROM : FROM은 기존에 빌드된 다른 이미지로부터 빌드를 시작하기 원할 때 사용합니다. 이번 예시에서는 pytorch 1.0 버전을 기반으로 빌드하였습니다. 최신 버전의 태그는 [pytorch dockerhub](https://hub.docker.com/r/pytorch/pytorch/tags) 에서 확인하실 수 있습니다.
```
FROM pytorch/pytorch:1.0-cuda10.0-cudnn7-runtime 
```

2. COPY : Code repository를 기준으로 가상의 Docker 이미지 상에 파일들을 복사하기 위한 지시어입니다. 이 예제에서는 main.py 및 requirements.txt를 복사하기 위해 사용하였습니다.
```
COPY . /root/example
```

3. WORKDIR : CMD 명령어 등 어떤 명령어가 실행이 될 때 base가 되는 directory를 지정합니다. 코드를 복사해둔 디렉토리를 지정하여 docker를 실행하는 분이 main.py가 포함된 디렉토리를 몰라도 실행가능하도록 하였습니다.
```
WORKDIR /root/example
```

4. RUN : 일반적으로 bash 에 입력하는 명령어들을 실행하는 지시어입니다. 필요한 python package가 나열되어 있는 requirements.txt를 pip 명령어로 설치 해 줍니다.
```
RUN pip install pip -U && pip install -r requirements.txt
```

<br>

#### 빌드 및 실행

1. 빌드 : 이미지를 만들기 위한 기본적인 명령어 예시. 실행 시 Dockerfile에 적힌 지시어들에 따라 순차적으로 빌드가 됩니다. pytorch image pulling에 다소 시간이 소요됩니다.
- appleholic은 아래의 docker hub username 입니다.
- appleholic/pytorchmnistexample : repository 이름
- v0.1 : 해당 repository 에서 이미지에 대한 버전 관리를 위한 태그 입니다
- \-t : tag argument

```bash
$$ docker build . -t appleholic/pytorchmnistexample:v0.1
```

2. 실행 : 빌드가 완료된 후 다음의 명령어로 실행이 가능합니다.
- \-it : \-i 와 \-t 옵션이 결합된 형태이며, shell과 iteractive 하게 작업(stdin/out)을 하기 위해 같이 사용됩니다. (\-i STDIN 관련, \-t tty 할당) 

```bash
$$ docker run -it appleholic/pytorchmnistexample:v0.1 python main.py
```

<br>

### Docker HUB에 이미지 공유하기
- Docker hub는 사람들이 빌드한 이미지를 공유하는 platform입니다. 빌드가 된 이미지는 주로 docker hub을 통해 공유되며, 이에 대해 간략하게 소개하고자 합니다.

<br>

#### 계정 및 repository 만들기 
- [https://hub.docker.com/](https://hub.docker.com/) 에 자신의 아이디를 만듭니다.
- 계정에 로그인 후 [https://hub.docker.com/add/repository/](https://hub.docker.com/add/repository/) 를 통해 repository를 만듭니다.

<br>

#### 이미지 푸쉬를 위한 단계

1. docker login : docker hub에 로그인하여 해당 유저로 인증을 합니다. 아래 명령어 실행 시 username, password를 입력 받게 되고 로그인 성공 시 해당 유저 권한으로 이미지를 푸쉬할 수 있게 됩니다. 
```bash
$$ docker login
```

2. docker push : local에서 만든 이미지의 이름과 remote repository의 이름이 같다면, 해당 remote repository로 아래와 같이 푸쉬할 수 있습니다.
```bash
$$ docker push appleholic/pytorchmnistexample:v0.1
```

- 푸쉬가 완료되고 나면, 자신이 만들었던 repository에 push가 되었다는 기록과 위와 같이 태그를 추가하였으면, 태그 탭에서 확인이 가능합니다.
![pushed screenshot](/imgs/pushed_screenshot.png)

- remote image로 테스트를 원할 시 아래와 같이 로컬 이미지를 지운 후 명령어를 재실행 해 보세요.

```bash
$$ docker rmi appleholic/pytorchmnistexample:v0.1 -f
$$ docker run -it appleholic/pytorchmnistexample:v0.1 python main.py
```

<br>

### 기타

- Docker 를 GPU에서 실행하기 위해서는 nvidia docker를 설치하여야 합니다. [https://github.com/NVIDIA/nvidia-docker](https://github.com/NVIDIA/nvidia-docker)
- Docker로 자신이 실험한 코드를 이미지로 빌드하여 배포한다면, 다른 사람들은 docker만 설치되어 있다면 환경 세팅 등에 신경 쓸 필요 없이 코드 재현이 가능한 장점이 있습니다. 조금 더 신경써서 docker로 공유해보면 어떨까요
- email : june.one@kakaobrain.com, choiilji@gmail.com
