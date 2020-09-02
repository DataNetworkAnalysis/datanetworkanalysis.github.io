---
layout: post
title:  "Ubuntu에 opencv pip로 설치하기"
date:   2020-02-20 17:29:00 +0800
categories: Setting
--- 

# 설치 환경

우선 pip부터 설치한다.

```bash
$ wget https://bootstrap.pypa.io/get-pip.py
$ sudo python3 get-pip.py
```

그런데 get-pip.py에 distutils.util이 없다고 에러가 생길 수 있다. 그런경우 아래 명령어로 distutil을 설치해주도록 하자. 그리고 다시 실행

```bash
$ sudo apt-get install python3-distutils
$ sudo python3 get-pip.py
```

pip 설치가 다 되었다면 이제 opencv 를 설치한다.

나는 opencv-contrib-python으로 설치했다. opencv 설치에도 종류가 여러개 있었다.

[pyimagesearch](https://www.pyimagesearch.com/2018/09/19/pip-install-opencv/) 설명 참고

1. **[pencv-python](https://pypi.org/project/opencv-python/):** This repository contains ***just the main modules*** of the OpenCV library. If you’re a PyImageSearch reader you *do not* want to install this package.
2. **[opencv-contrib-python](https://pypi.org/project/opencv-contrib-python/):** The opencv-contrib-python repository contains **both the *main modules* along with the *contrib modules*** — this is the library I **recommend** you install as it includes all OpenCV functionality.
3. **[opencv-python-headless](https://pypi.org/project/opencv-python-headless/):** Same as opencv-python but no GUI functionality. Useful for headless systems.
4. **[opencv-contrib-python-headless](https://pypi.org/project/opencv-contrib-python-headless/):** Same as opencv-contrib-python but no GUI functionality. Useful for headless systems.

```bash
$ pip install opencv-contrib-python 
```

# Raspberry Pi에서 opencv 설치하기!

python3를 사용할 것이기 때문에 pip3으로 설치한다. 

그리고나서 바로 import하게 되면 'libhdf5_serial.so.103'의 ImportError가 생긴다 그래서 아래 옵션들을 모두 설치해준다.

```bash
$ pip3 install opencv-contrib-python
$ sudo apt install -y libhdf5-dev libatlas-base-dev libjasper-dev libqtgui4 libqt4-test
```
# Truoble Shouting

처음에 가이드를 따라서 pip를 잘 설치한 후 아래 명령어를 입력해서 opencv를 잘 설치했나 싶더니 에러가 발생

```bash
$ sudo pip install opencv-contrib-python
```

<p align='center'>
    <img src='https://drive.google.com/uc?id=1xxI8ARZPx6V4IN3Yki_iURQFkL1W8Imn'>
</p>

그래서 python site-packages에 문제가 있나 확인도 해보고 module 경로가 문제가 있나 확인도 해봄

module이 설치된 경로를 확인할 수 있음

```bash
$ python -m site

sys.path = [
    '/home/jaehyuk',
    '/home/jaehyuk/anaconda3/lib/python37.zip',
    '/home/jaehyuk/anaconda3/lib/python3.7',
    '/home/jaehyuk/anaconda3/lib/python3.7/lib-dynload',
    '/home/jaehyuk/anaconda3/lib/python3.7/site-packages',
]
USER_BASE: '/home/jaehyuk/.local' (exists)
USER_SITE: '/home/jaehyuk/.local/lib/python3.7/site-packages' (doesn't exist)
ENABLE_USER_SITE: True
```

확인해보니 경로는 잘 잡혀있지만 site-packages에는 opencv가 설치된게 없었다.

```bash
$ ls /home/jaehyuk/anaconda3/lib/python3.7/site-packages | grep open
```

<p align='center'>
    <img src='https://drive.google.com/uc?id=1rlV4ae7CpwfgK_Ai0AuvPHio6UNp30cF'>
</p>

혹시나 경고 메세지가 sudo 때문일까 싶어서 sudo를 때고 실행해봤더니 설치 완료!

```bash
$ pip install opencv-contrib-python
```

<p align='center'>
    <img src='https://drive.google.com/uc?id=1kN2HTwB0s89kDjO9lpOF8TQvvPr_8owJ'>
</p>
