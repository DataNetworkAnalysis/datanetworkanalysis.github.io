---
layout: post
title:  "pip package 만들기!"
date:   2020-02-06 17:29:00 +0800
categories: Setting
author: 허재혁
--- 

# package 만들게된 동기

이번에 computer vision에서 사용되는 explainable AI에 대한 연구를 하면서 이것저것 pytorch로 구현했었습니다. tensorflow는 saliency라는 패키지로 이미 잘 구현이 되어있어서 저는 pytorch로 만들어서 패키지를 올려야겠다라구 생각했습니다. 

그런데 다 만들고나서 ICCV 2019에 나온 논문들을 다시 보다보니... 

<p align="center">
    <img src='https://drive.google.com/uc?id=1Wfn597R6qw9cuU3fb4UIGhY1a_V7IDDq' /><br>
</p>


음?? 잘못봤나? 싶어서 다시 봤더니... 아하... 역시 페이스북에서도 안만들리가 없었지.... 심지어 repository 만든날짜도 나보다 한 달 더 빠르다.. 

<p align="center">
    <img src='https://drive.google.com/uc?id=1k_O8muZBEb_5YEkJod2ds4FReqcqPNAZ' /><br>
</p>

torcyray github : [https://github.com/facebookresearch/TorchRay](https://github.com/facebookresearch/TorchRay)

GradCAM example이라는데 내가 구현한거랑 방식이 굉장히 유사하다. 

```python
    from torchray.attribution.grad_cam import grad_cam
    from torchray.benchmark import get_example_data, plot_example
    
    # Obtain example data.
    model, x, category_id, _ = get_example_data()
    
    # Grad-CAM backprop.
    saliency = grad_cam(model, x, category_id, saliency_layer='features.29')
    
    # Plots.
    plot_example(x, saliency, 'grad-cam backprop', category_id)
```

그래도 만들어 놓은게 있는데 이후에도 나는 계속 구현할 것이기 때문에 이것저걱 다 올려버리겠다는 각오로 우선 패키지를 만들고보자라는 생각으로 만들고 봤다.

# 준비

우선 pip 패키지를 만들기 위해서는 아래와 같이 먼저 각 파일을 만들어야한다.

- Repository (패키지로 올릴 레포)
- setup.py
- setup.cfg
- Release 파일
- [PyPI 회원가입](https://pypi.org/)

**Intall packages**

    pip install wheel
    pip install twine

# Repository

Repo는 github에서 만들면된다. 만드는 부분은 여기서 생략하겠다. 

나는 내 닉네임이 TooTouch기 때문에 이를 본따서 **tootorch**로 지었다. [[Github](https://github.com/TooTouch/tootorch)]

<p align="center">
    <img src='https://drive.google.com/uc?id=1ByprtTC8WFJtSHjVhidyzTDrtc9LGcVj' /><br>
</p>

# setup.py

우선 setup.py를 통해서 기본 설정을 정해야한다. 아래 내용을 그대로 가져다 붙이고 각 옵션들을 본인에 코멘트로 달아놓은 설명에 맞게 본인 내용으로 작성하면된다. 첫 release라면 version은 0.1로 하는게 좋다.

```python
    from setuptools import setup, find_packages
    
    with open('README.md', encoding='utf-8') as f: # README.md 내용 읽어오기
        long_description = f.read()
    
    setup(
        name                = 'tootorch',
        version             = '0.1', # PyPI에 올릴 version 
        long_description    = long_description, # README.md 내용을 PyPI project Description에 넣기
        long_description_content_type = 'text/markdown', # 형식은 markdown으로 지정
        description         = 'Implemetation XAI in Computer Vision (Pytorch)', # 짦은 소개
        author              = 'Jaehuck Heo', # 이름
        author_email        = 'wogur379@gmail.com', # 메일 
        url                 = 'https://github.com/TooTouch/tootorch', # github url
        download_url        = 'https://github.com/TooTouch/tootorch/archive/v0.1.tar.gz', # release 이름
        install_requires    =  ["torch","torchvision","h5py","tqdm","pillow","opencv-python"], # 패키지 사용시 필요한 모듈
        packages            = find_packages(exclude = []),
        keywords            = ['tootorch','XAI'], # 키워드
        python_requires     = '>=3.6', # python 필요 버전
        package_data        = {}, 
        zip_safe            = False,
        classifiers         = [   
            'Programming Language :: Python :: 3',
            'Programming Language :: Python :: 3.6',
            'Programming Language :: Python :: 3.7',
            "Topic :: Scientific/Engineering :: Artificial Intelligence",
            "Topic :: Software Development :: Libraries :: Python Modules",
        ],
    )
```

만약 아래와 같이 PyPI project description에 내용을 달고 싶다면 [README.md](http://readme.md)에 작성한 후 아래 long_description을 꼭 작성해야한다. 

<p align="center">
    <img src='https://drive.google.com/uc?id=1CwUEY7BIrFLkgmPoPDMNuknSizU5ZTP_' width='600'/><br>
</p>

# setup.ckg

이 부분 그냥 아래내용을 그대로 붙여넣기하면 된다. Description 파일을 어떤걸로 쓸것인지 물어보는 파일이다.

    [metadata]
    description-file = README.md

# Release 파일 만들기

Release 파일을 만드는 방법은 간단하다 github repo에 들어가서 화면과 같이 release라고 써있는 곳을 들어간다.

<p align="center">
    <img src='https://drive.google.com/uc?id=1E1GC3lvoJMm7lVEZPSumv8XwmHcg004S' /><br>
</p>

그리고나면 아래 화면에서 Create a new release 버튼을 누르고

<p align="center">
    <img src='https://drive.google.com/uc?id=12QvaQTAQN3lH7Lvo6v2bA0kha6T5hMLM' /><br>
</p>

빈칸에 내용을 입력하면 된다. 버전을 어떤식으로 써야할지 모르겠다면 오른쪽 suggestion을 참고해도 좋다. 참고로 첫 release라면 v0.1로 하는 것이 좋다.

<p align="center">
    <img src='https://drive.google.com/uc?id=1WYyeWyPaMclgMtif0WE-VOVjpLG8_--f' /><br>
</p>

파일을 만들면 아래와 같이 zip이나 tar.gz로 받을 수 있게 된다. 

<p align="center">
    <img src='https://drive.google.com/uc?id=1wtKNUdpNNsO_gufxukWaIoPGjEy_D_jN' /><br>
</p>

# Project Upload

이제 준비가 다 끝났으니 Project를 올리기만 하면된다. 아래와 같이 명령어를 입력하면 dist라는 폴더가 생기고 안에 **~~.whl** 이라는 파일이 생성된다.

```bash
    python setup.py bdist_wheel
```

그리고나서 아래 명령어를 통해 PyPI에 업로드해주면 된다! 뒤에 .whl 파일은 **앞서 생성된 이름으로 바꿔넣도록 하자**

```bash
    twine upload dist/tootorch-1.1-py3-none-any.whl
```

아래와 같이 username과 password를 입력하라고 나오는데 **PyPI 홈페이지**에서 회원가입하고 가입한 내용을 적으면 된다.

```bash
    D:\bllfpc_github\tootorch>twine upload dist/tootorch-1.1-py3-none-any.whl
    Uploading distributions to https://upload.pypi.org/legacy/
    Enter your username: tootouch
    Enter your password:
    Uploading tootorch-1.1-py3-none-any.whl
    100%|█████████████████████████████████████████████████████████████| 39.5k/39.5k [00:02<00:00, 15.8kB/s]
    
    View at:
    https://pypi.org/project/tootorch/0.1/
```

# Import tootorch

이제 python에서 내가 올린 패키지를 import해보면 끝이다. 완성!

<p align="center">
    <img src='https://drive.google.com/uc?id=11neJaRsZsYQ6suuGH4ebyE_cJ0RQr8lF' /><br>
</p>

# Trouble Shooting

중간에 PyPI에 업로드 했다가 내용이 잘못돼서 PyPI에 있는 release 파일을 삭제한 후 다시 twine으로 업로드하려고 하니 **HTTPError**가 발생한다. 이는 이전에 내가 같은 이름으로 whl 파일을 업로드했어서 그렇다. 

근데 PyPI에 있는 release파일을 삭제했는데도 그래서 이상하다하고 찾아봤더니 삭제했어도 같은이름은 안된다고한다. 그러면 방법은 단순하다. 

```bash
    D:\bllfpc_github\tootorch>twine upload dist/tootorch-0.1-py3-none-any.whl
    Uploading distributions to https://upload.pypi.org/legacy/
    Enter your username: tootouch
    Enter your password:
    Uploading tootorch-0.1-py3-none-any.whl
    100%|█████████████████████████████████████████████████████████████| 33.5k/33.5k [00:01<00:00, 23.8kB/s]
    NOTE: Try --verbose to see response content.
    HTTPError: 400 Client Error: This filename has already been used, use a different version. See https://pypi.org/help/#file-name-reuse for url: https://upload.pypi.org/legacy/
```

그냥 whl 파일 이름을 다르게 변경하고 업로드하면 해결된다.

```bash
    twine upload dist/tootorch-0.1-py3-none-any.whl # 이전이름
    twine upload dist/tootorch-1.1-py3-none-any.whl # 바꾼이름
```