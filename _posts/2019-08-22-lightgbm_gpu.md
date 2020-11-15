---
layout: post
title:  "Python LightGBM GPU 버전 설치"
date:   2019-11-13 11:47:00 +0800
categories: Setting
image: images/default.jpg
link: https://tootouch.github.io/setting/lightgbm_gpu/
author: 허재혁
---

LightGBM은 요즘 안쓸래야 안쓸수가 없는 부스팅 모델 중 하나입니다. 그러나 GPU버전을 사용하지 않는다면 아무리 빨라도 제대로 사용하지 않는것이죠. 

GPU모델을 설치하기 위해서는 여러가지 과정이 있어서 어려움이 많습니다. 이 포스팅을 통해 하나씩 따라가 보시죠!

# 1. 설치 환경

- Windows 10
- python 3.7

# 2. 필수 프로그램 설치 5(?)가지

- [Cmake](https://cmake.org/download/) 에 들어가서 아래 프로그램 설치!

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1iwN43hDemnzGeNKjLV049R8Jk43qYJwo' /><br>
</p>

설치과정에서 Path에 추가를 꼭 체크 하셔야합니다. 체크가 안되셔도 이후에 추가가 가능합니다. 그러나 미리 해놓으면 더 편하다는 사실!

- [CUDA](https://developer.nvidia.com/cuda-downloads)에 들어가셔서 Windows버전 설치!

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1OB9K61z3blgCg_HMvWQKIvQbQvUkrrF2' /><br>
</p>

GPU 사용을 위해서는 CUDA 설치가 필수입니다.

- [MinGW](http://iweb.dl.sourceforge.net/project/mingw-w64/Toolchains%20targetting%20Win32/Personal%20Builds/mingw-builds/installer/mingw-w64-install.exe) 를 클릭해서 설치!

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1tDvwhHCeMjOsu7j8gTazCPHNB9ZjPSXz' /><br>
</p>

버전은 더 높을 수 있으나 웬만하면 따라서 해주세요. 

설치가 끝났으면 아래 경로를 환경 변수에 추가! 아래 Path라고 파란줄이 쳐져있는곳에 추가해주세요. 주석(#으로 설명을 써놓은것)도 같이 추가하시면 안됩니다. 

    # MinGW Path
    C:\Program Files\mingw-w64\x86_64-5.3.0-posix-seh-rt_v4-rev0\mingw64\bin
    
    # CUDA Path
    C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0\include
    C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0\bin
    C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v10.0\libnvvp
    
    # cmake는 설치시 추가된 경우 이미 추가가 되어있습니다.
    C:\Users\wogur\Downloads\cmake-3.14.1-win64-x64\cmake-3.14.1-win64-x64\bin

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1ftZrSy2q7Vi8CpZmLvAB5eDDHzW7Nd9e' /><br>
</p>

- [boost](https://sourceforge.net/projects/boost/files/boost-binaries/1.69.0/) 파일 설치 후 C:/local 에 이동! `boost_1_69_0-msvc-14.1-64.exe` 파일 설치!

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1RXq89SiTGMyZFY57RBA-0F7s_SP6VbP2' /><br>
</p>

사진과 같이 설치된 파일이 있으면 됩니다. 파일이 설치되었다면 이번엔 환경변수에 `BOOST_ROOT` 라는 변수를 만들고 안에  C:\local\boost_1_69_0\ 추가!

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1BJoZP_7sDFoKvxaJysyQ0TWlM0y-JMU4' /><br>
</p>

모두 설치가 되었다면 이제 **명령프롬프트**에서 확인! 아래 명령어를 치고 잘 나오는지 확인합니다.

무엇이 나와야하느냐면, 위에서 추가했던 경로들이 출력값에 포함되어야합니다.

    echo %BOOST_ROOT%
    echo %path%

자 이제 끝

이 아닙니다. Visual Studio가 설치되어 있는지 확인해 보세요. 만약 설치가 안되어있다면 [Visual Studio Download](https://visualstudio.microsoft.com/ko/downloads/?rr=https%3A%2F%2Fwww.google.com%2F) 에서 Community 설치 후 `C++빌드 도구`를 설치 해야합니다. Visual C++ 빌드 도구를 클릭한 후 **오른쪽 옵션 3개**로 체크해주세요

- Windows 10 SDK(10.0.17763.0)
- CMake용 Visual C++ 도구
- 테스트 도구 핵심 기능 - 빌드 도구

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1ES-dE1Xnb0mlYLlb4lpXbW8ZsPQbUdOv' width='800'/><br>
    <i>화질구지 죄송합니다.</i>
</p>



# 3. Installation

전부 설치가 되었다면 아나콘다 프롬프트로 가즈아

아래 명령어를 입력합니다.

    pip install lightgbm --install-option=--gpu

설치 끝!

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1WY-HL8ExvHAG_Jjgqxn6M6aeiw6q2k9I' /><br>
</p>

ipython 에 들어가서 import 해봅시다.

<p align="center">
    <img src='http://drive.google.com/uc?export=view&id=1vu-VkoHuexAh4_247HpXEvp_bD-P8121' /><br>
</p>

끝!

**Light GBM 돌리러 가즈아**