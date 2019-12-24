---
layout: post
title:  "Jupyter notebook 확장팩 사용하기"
date:   2019-09-30 17:20:00 +0800
categories: Setting
tags: Jupyter
images: images/default.jpg
---

**jupyter notebook nbextensions**을 설치하면 기존 jupyter notebook에서 사용하지 못했던 다양한 기능들을 사용할 수 있습니다. **jupyter notebook 확장팩!**

# jupyter nbextensions 설치

---


**아나콘다** 프롬프트에서 명령어 입력하면 설치 끝

    pip install jupyter_nbextensions_configurator jupyter_contrib_nbextensions
    jupyter contrib nbextension install --user
    jupyter nbextensions_configurator enable --user

설치 후 jupyter notebook 접속 시 Nbextensions 메뉴가 생깁니다.

만약 설치 전 jupyter notebook이 켜져있다면 반드시 jupyter notebook을 재실행 해주셔야합니다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1R0O5LU9d9WmPQALQgM_v6kkFZsGbymWS'/>
</p>

만약 아래와같이 아무것도 나와있지 않다면 

disable configuration for nbextensions without ~ 의 체크를 풀어주세요

# 유용한 기능

---

제가 주로 많이 사용하는 기능은 크게 4개입니다.

1. Execute Time (강추)
2. Hide Input & Hide Input all 
3. Table of Contents (강추)
4. Variable Inspector

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1ExLUMvCIk2nm6NLVVPIX3hWQFa7p2BBm'/>
</p>
