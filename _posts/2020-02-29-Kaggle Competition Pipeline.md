---
layout: post
title:  "Kaggle Competition Pipeline 따라잡기"
date:   2020-02-29 05:06:00 +0800
categories: Setting
--- 

# Kaggler TV

국대 Kaggle 최초 Grand Master인 이정윤님이 유튜브에서 Kaggler TV라는 채널을 운영하고 있다. Kaggle을 시작하는 입문자들을 위한 여러가지 운영 팁과 관련된 영상들을 업로드하고 있다. 

[이정윤님의 Kaggle Profile](https://www.kaggle.com/jeongyoonlee)

기존에 kaggle을 몇 번 해보면서 kaggle에 있는 kernel을 사용하는게 그렇게 편하지도 않고 코드관리나 실험적인 면에 있어서 오히려 비효율적인 면이 많았다. 평소에 Pipeline을 어떻게 만드는게 kaggle이나 분석할때 더 유용할까 고민을 많이하는데 정말 좋은 소스라고 생각해서 따라해보기로 했다.

한 달 전에 끝난 대회에 대해 진행한 파이프 라인이고 대회는 [여기](https://www.kaggle.com/c/cat-in-the-dat-ii/leaderboard)서 확인 할 수 있다.

<p align='center'>
    <img src="https://drive.google.com/uc?id=1qeVgkRX1tkdPJHzOobqlt37HP7myjsJb"><br>
    <i>categorical이 cat이라서 배경도 고양이사진임 졸귀</i>
</p>


영상에서 사용된 코드는 [여기](https://github.com/kaggler-tv/cat-in-the-dat-ii)서 확인할 수 있다. 단, Mac이나 Linux 환경이 따라하기 좋음. 

# 따라하며 몰랐던 것들

사용환경인 CLI이기 때문에 모르는 명령어가 정말 많았다. 이번 기회에 따라해보면서 알게된 점 중 가장 좋은 것은 크게 세 가지였다.

- Kaggle API
- Makefile
- 몇가지 bash command

## pip install kaggle

**In Ubuntu**

1. Kaggle 설치

    kaggle 설치 시 .local/bin으로 설치가 되면 환경변수를 추가해준다. **추가한 후에는 재부팅해야 적용된다.**
    ```bash
    $ export KAGGLE_PATH=/home/username/.local/bin
    ```

2. kaggle.json 

    kaggle 설치 후 [Kaggle.com](https://www.kaggle.com/) > My Account 에서 Create New API Token 클릭 후 다운받은 kaggle.json을 ~/.kaggle로 옮긴다.

## Makefile?

**정의**

- linux상에서 반복 적으로 발생하는 컴파일을 쉽게하기위해서 사용하는 make 프로그램의 설정 파일이다.
- Makefile을 통하여 library 및 컴파일 환경을 관리 할수 있다.

**Makefile 사용 예시**

[What is a Makefile and how does it work?](https://opensource.com/article/18/8/what-how-makefile)

## Command

### WC

wc 명령어는 주어진 파일 또는 표준 입력의 바이트, 문자, 단어 그리고 줄(라인) 수를 출력해주는 명령어이다. 여기서 wc는 word count를 의미한다고 한다.

    $ wc -l input/*.csv
    
     400001 input/sample_submission.csv
     400001 input/test.csv
     600001 input/train.csv
    1400003 합계

### head

head는 주어진 파일을 위에서부터 10개의 라인을 읽어온다. 라인 수를 정하기 위해서는 `-` 와 함께 라인 수를 입력하면된다.

    $ head -1 input/train.csv
    
    id,bin_0,bin_1,bin_2,bin_3,bin_4,nom_0,nom_1,nom_2,nom_3,nom_4,nom_5,nom_6,nom_7,nom_8,nom_9,ord_0,ord_1,ord_2,ord_3,ord_4,ord_5,day,month,target

### awk

다음은 feature 수가 몇개인지 확인하기 위한 명령어이다. awk에 대한 자세한 설명은 [변성윤님의 블로그 글](https://zzsza.github.io/development/2017/12/20/linux-6/)을 참고하도록 하자. 여기서 NF는 Number of Field라는 뜻이다. 예시는 여기 [블로그 글](https://happyoutlet.tistory.com/entry/awk-NF-Number-Of-Fields-unix-linux)을 참고하자

    $ awk -F, '{print NF}' input/train.csv |head -1
    
    25

# 맺음말

이후 kaggle pipeline이 조금 정리되고 수월해지면 천천히 시작해볼까한다. 최근 열리는 대회 중 Deepfake 관련된 대회가 있는데 상금이 무려 $1,000,000 이다! 

<p align='center'>
    <img src="https://drive.google.com/uc?id=1iaiwAmyBXlERATfJlDBzBxhclosKy3Jv"><br>
    <i>한 달 남았다... </i>
</p>

[Deepfake Detection Challenge](https://www.kaggle.com/c/deepfake-detection-challenge)

Deepfake라길래 이전 GAN 대회처럼 Deepfake 모델을 잘 만드는건가 싶었는데 실제 영상과 Deepfake로 만든 영상 중 진짜인지 가짜인지를 분류하는 문제였다. 최근 Deepfake가 점점 잘되면서 오히려 윤리적으로 문제가 되는 일이 많이 생기지 않을까 싶었는데 좋은 취지의 대회인거같다.