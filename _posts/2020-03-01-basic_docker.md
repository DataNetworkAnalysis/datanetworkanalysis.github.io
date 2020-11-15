---
layout: post
title:  "Basic Docker in ubuntu 18.04"
date:   2020-03-01 23:55:00 +0800
categories: Setting
link: https://tootouch.github.io/setting/basic_docker/
author: 허재혁
--- 

Docker에 대한 기본 내용과 설치 방법 그리고 사용법에 대한 글입니다.

# Environment

- Ubuntu 18.04

# Docker?

도커(docker)란 **컨테이너**를 기반으로 하는 오픈소스 가상화 플렛폼이다. 

도커는 2013년 산타클라라에서 열린 Pycon에서 처음 소개되었다고 한다[[dotCloud사의 도커 소개 영상](https://www.youtube.com/watch?v=wW9CAH9nSLs&feature=youtu.be)]. 기존 가상화 방식은 VMware나 VirtualBox와 같이 호스트OS 위에 게스트OS를 가상화하는 식이였다. 도커는 간단하게 설명하면 추가적인 OS를 만드는게 아니기 때문에 이 방법보다 훨씬 가볍게 동작하기 때문에 부담이 더 적다고 할 수 있다.

<p align='center'>
    <img src="https://drive.google.com/uc?id=1XOLYH7arf4MOmvhWA5Z65zmaULR5D0dr" width='500'><br>
    <i>기존 가상 환경과 도커의 차이.</i>
</p>

도커는 여러개의 컨테이너를 독립적으로 만들어서 활용하고 만드는데도 시간이 굉장히 적게 들어간다.

**이미지(image)**

도커에는 컨테이너를 만들기위한 이미지라는게 있다.

<p align='center'>
    <img src="https://drive.google.com/uc?id=1IcFMpUSbs2EpbwimUvDilvlF_A09Sd_q" width='500'>
</p>

이미지는 컨테이너를 만들기위한 설정값들은 포함하고 있다. 도커 이미지를 생성 또는 다운 받으면 언제든 컨테이너를 만들 수 있고 사용중인 컨테이너에 문제가 생기거나 삭제가 되어도 다시 이미지를 통해 생성하면 그만이다.

이미지는 [docker hub](https://hub.docker.com/)라는 곳에서 다운받을 수 있다.

도커는 크게 2가지 에디션 버전으로 나뉜다.

- **Community Edition (CE)** : 개발자나 작은 팀들에게 이상적인 버전이며 무료로 사용할 수 있습니다.
- **Enterprise Edition (EE)** : 엔터프라이즈 개발이나 실제 확장 가능한 서비스를 개발하는 팀에서 사용하기 적합한 유료버전의 에디션이다.

<p align='center'>
    <img src="https://drive.google.com/uc?id=1qcc-RZAAwedEgjadpVCj9KXpo2OeOcVu">
</p>

# Install Command

```bash
#Update the apt package index:
$ sudo apt-get update

#Install packages to allow apt to use a repository over HTTPS:
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common

#Add Docker’s official GPG key:
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

#Verify that you now have the key with the fingerprint:
#9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88
$ sudo apt-key fingerprint 0EBFCD88

#set up the stable repository
$ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"

#Update the apt package index:
$ sudo apt-get update

#Install the latest version of Docker CE
$ sudo apt-get install docker-ce

#Verify that Docker CE is installed correctly by running the hello-world image.
$ sudo docker run hello-world
```

그리고 일반 사용자계정으로 docker 명령어를 사용하기 위해서는 아래의 명령어로 그룹을 추가해 주시면 됩니다.
아래의 명령어는 ubuntu라는 사용자를 docker그룹에 추가하는 내용입니다.

```bash
$ sudo usermod -aG docker $USER
```

docker 사용 확인

```bash
$ sudo systemctl status docker

● docker.service - Docker Application Container Engine
    Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
    Active: active (running) since Sun 2020-03-01 23:49:25 KST; 30min ago
        Docs: https://docs.docker.com
    Main PID: 7439 (dockerd)
    Tasks: 26
    CGroup: /system.slice/docker.service
            └─7439 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

# nvidia-docker

Nvidia-Docker는 GPU를 사용하여 연산을 할 수 있는 Ubuntu Docker입니다.

[NVIDIA/nvidia-docker](https://github.com/NVIDIA/nvidia-docker)

```
# If you have nvidia-docker 1.0 installed: we need to remove it and all existing GPU containers
$ docker volume ls -q -f driver=nvidia-docker | xargs -r -I{} -n1 docker ps -q -a -f volume={} | xargs -r $ docker rm -f

$ sudo apt-get purge -y nvidia-docker

# Add the package repositories
$ curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
    sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
sudo tee /etc/apt/sources.list.d/nvidia-docker.list

$ sudo apt-get update

# Install nvidia-docker2 and reload the Docker daemon configuration
$ sudo apt-get install -y nvidia-docker2
$ sudo pkill -SIGHUP dockerd

# Test nvidia-smi with the latest official CUDA image
$ sudo docker run --runtime=nvidia --rm nvidia/cuda:10.0-base nvidia-smi
```

# ufoym/Deepo

[github repo](https://github.com/ufoym/deepo)

- 보다 빠르게 딥러닝 연구환경을 구축할 수 있음
- [거의 모든 딥러닝 프레임워크](https://github.com/ufoym/deepo#tags)를 포함하고 있음
- GPU 가속 (CUDA, cuDNN 등)을 지원하며 CPU모드 역시 지원함
- 리눅스, 윈도우, OSX 모두 빠르게 사용가능함

**Comparison to alternatives**

| Name       | modern-deep-learning | dl-docker | jupyter-deeplearning | Deepo         |
| ---------- | -------------------- | --------- | -------------------- | ------------- |
| ubuntu     | 16.04                | 14.04     | 14.04                | 18.04         |
| cuda       | X                    | 8.0       | 6.5-8.0              | 8.0-10.1/None |
| cudnn      | X                    | v5        | v2-5                 | v7            |
| onnx       | X                    | X         | X                    | O             |
| theano     | X                    | O         | O                    | O             |
| tensorflow | O                    | O         | O                    | O             |
| sonnet     | X                    | X         | X                    | O             |
| pytorch    | X                    | X         | X                    | O             |
| keras      | O                    | O         | O                    | O             |
| lasagne    | X                    | O         | O                    | O             |
| mxnet      | X                    | X         | X                    | O             |
| cntk       | X                    | X         | X                    | O             |
| chainer    | X                    | X         | X                    | O             |
| caffe      | O                    | O         | O                    | O             |
| caffe2     | X                    | X         | X                    | O             |
| torch      | X                    | O         | O                    | O             |
| darknet    | X                    | X         | X                    | O             |

**Deprecated Tags**

| Name                    | CUDA 10.0 / Python 3.6        | CUDA 9.0 / Python 3.6                     | CUDA 9.0 / Python 2.7                    | CPU-only / Python 3.6          | CPU-only / Python 2.7                     |
| ----------------------- | ----------------------------- | ----------------------------------------- | ---------------------------------------- | ------------------------------ | ----------------------------------------- |
| all-in-one              | `py36-cu100` `all-py36-cu100` | `py36-cu90` `all-py36-cu90`               | `all-py27-cu90` `all-py27` `py27-cu90`   |                                | `all-py27-cpu` `py27-cpu`                 |
| all-in-one with jupyter |                               | `all-jupyter-py36-cu90`                   | `all-py27-jupyter` `py27-jupyter`        |                                | `all-py27-jupyter-cpu` `py27-jupyter-cpu` |
| Theano                  | `theano-py36-cu100`           | `theano-py36-cu90`                        | `theano-py27-cu90` `theano-py27`         |                                | `theano-py27-cpu`                         |
| TensorFlow              | `tensorflow-py36-cu100`       | `tensorflow-py36-cu90`                    | `tensorflow-py27-cu90` `tensorflow-py27` |                                | `tensorflow-py27-cpu`                     |
| Sonnet                  | `sonnet-py36-cu100`           | `sonnet-py36-cu90`                        | `sonnet-py27-cu90` `sonnet-py27`         |                                | `sonnet-py27-cpu`                         |
| PyTorch                 | `pytorch-py36-cu100`          | `pytorch-py36-cu90`                       | `pytorch-py27-cu90` `pytorch-py27`       |                                | `pytorch-py27-cpu`                        |
| Keras                   | `keras-py36-cu100`            | `keras-py36-cu90`                         | `keras-py27-cu90` `keras-py27`           |                                | `keras-py27-cpu`                          |
| Lasagne                 | `lasagne-py36-cu100`          | `lasagne-py36-cu90`                       | `lasagne-py27-cu90` `lasagne-py27`       |                                | `lasagne-py27-cpu`                        |
| MXNet                   | `mxnet-py36-cu100`            | `mxnet-py36-cu90`                         | `mxnet-py27-cu90` `mxnet-py27`           |                                | `mxnet-py27-cpu`                          |
| CNTK                    | `cntk-py36-cu100`             | `cntk-py36-cu90`                          | `cntk-py27-cu90` `cntk-py27`             |                                | `cntk-py27-cpu`                           |
| Chainer                 | `chainer-py36-cu100`          | `chainer-py36-cu90`                       | `chainer-py27-cu90` `chainer-py27`       |                                | `chainer-py27-cpu`                        |
| Caffe                   | `caffe-py36-cu100`            | `caffe-py36-cu90`                         | `caffe-py27-cu90` `caffe-py27`           |                                | `caffe-py27-cpu`                          |
| Caffe2                  |                               | `caffe2-py36-cu90` `caffe2-py36` `caffe2` | `caffe2-py27-cu90` `caffe2-py27`         | `caffe2-py36-cpu` `caffe2-cpu` | `caffe2-py27-cpu`                         |
| Torch                   | `torch-cu100`                 | `torch-cu90`                              | `torch-cu90` `torch`                     |                                | `torch-cpu`                               |
| Darknet                 | `darknet-cu100`               | `darknet-cu90`                            | `darknet-cu90` `darknet`                 |                                | `darknet-cpu`                             |

```
# 이미지 당겨오기
$ sudo docker pull ufoym/deepo
#jupyer가 지원되는 이미지
$ sudo docker pull ufoym/deepo:all-py36-jupyter

# 설치 확인하기
$ sudo nvidia-docker run --rm ufoym/deepo nvidia-smi
```

<p align='center'>
    <img src="https://drive.google.com/uc?id=1RKCPRhJnDbIznU945pFFxwXhVJ4hLAtZ">
</p>

# Make shell file

설정을 미리해두고 편하게 실행하기위해 쉘파일 생성. 

추가로 도커는 종료와 함께 저장된 파일이 모두 사라지기 때문에 로컬 폴더와 컨테이너를 연결하기위한 폴더를 만들어줘야한다.

```
$ mkdir ~/Desktop/data
```

도커 옵션 설명

**Options**

| Option | Description                       |
| ------ | --------------------------------- |
| -d     | detached mode 흔히 말하는 백그라운드 모드     |
| -p     | 호스트와 컨테이너의 포트를 연결 (포워딩)           |
| -v     | 호스트와 컨테이너의 디렉토리를 연결 (마운트)         |
| -e     | 컨테이너 내에서 사용할 환경변수 설정              |
| –name  | 컨테이너 이름 설정                        |
| –rm    | 프로세스 종료시 컨테이너 자동 제거               |
| -it    | -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션 |
| –link  | 컨테이너 연결 (컨테이너명:별칭)                |

**deepo.sh** 

deepo docker 실행 쉘파일

```
sudo nvidia-docker run -it \
            -p 6006:6006 \
            -p 2222:2222 \
            -h torch_d \
            --name torch_d \
            -v ~/desktop/data:/data \
            ufoym/deepo bash
```

**deepo_jupyter.sh** 

jupyter notebook 용 docker 실행 쉘파일

```
sudo nvidia-docker run -it \
            -p 8888:8888 \
            -p 6006:6006 \
            -h torch_j \
            --name torch_j \
            -v ~/Desktop:/data \
            --ipc=host ufoym/deepo:all-py36-jupyter \
            jupyter notebook --no-browser \
            --ip=0.0.0.0 \
            --allow-root \
            --NotebookApp.token= --notebook-dir='/data'
```

# Docker 기본 사용법

## 이미지/컨테이너 확인

다운받은 도커 이미지 확인 `docker images`

```
$ docker images

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ufoym/deepo         latest              69b30aa9ad32        4 weeks ago         12.8GB
nvidia/cuda         10.0-base           841d44dd4b3c        3 months ago        110MB
hello-world         latest              fce289e99eb9        14 months ago       1.84kB
ufoym/deepo         all-py36-jupyter    ca53b1635705        22 months ago       9.41GB
```

현재 실행 중인 컨테이너 확인하기. `-a` 옵션을 사용하면 중지된 컨테이너까지 확인 가능하다.

```
$ sudo docker ps -a

CONTAINER ID        IMAGE                          COMMAND                  CREATED             STATUS                         PORTS               NAMES
06dc21bfe803        ufoym/deepo                    "bash"                   24 minutes ago      Exited (0) 15 minutes ago                          torch_d
39d4169c90ce        ufoym/deepo:all-py36-jupyter   "jupyter notebook --…"   27 minutes ago      Exited (0) 27 minutes ago                          torch_j
999753ba4876        ufoym/deepo                    "/bin/bash"              43 minutes ago      Exited (2) 41 minutes ago                          torch_D
74b9637438bc        hello-world                    "/hello"                 About an hour ago   Exited (0) About an hour ago                       elated_cray
```

## 컨테이너 재시작/접속

이미지 exit 후 다시 시작할 때는 `restart`를 사용한다.

```
$ sudo nvidia-docker restart torch_d
```

재시작 후 해당 컨테이너에 접속하기 위해서는 `attach`를 사용한다.

```
$ sudo nvidia-docker attch torch_d
```

## 컨테이너 삭제하기

실행한 컨테이너 삭제하기. 삭제는 `rm`에 컨테이너 아이디를 입력한다.

```
$ sudo docker rm 74b9637438bc
```

실행한 컨테이너 전부 삭제하기.  `-q`옵션은 container ID만 출력하는 옵셥이다.

```
$ sudo docker rm $(sudo docker ps -a -q)
```

## 이미지 삭제하기

이미지 삭제하기. 이미지 삭제는 `rmi`와 `이미지이름:이미지태그`를 함께 입력한다.

```
$ sudo docker rmi hello-world:latest
```

이미지 전부 삭제하기는 컨테이너와 비슷하다.

```
$ sudo docker rmi $(sudo docker images -q)
```

# Reference

- [Ubuntu 18.04 설치 #2 Install Tensorflow with Docker](https://eungbean.github.io/2018/11/09/Ubuntu-Installation2-3/)