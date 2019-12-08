---
layout: post
title:  "딥러닝의 한계? 딥러닝의 겨울이 다시 올까?"
date:   2019-12-07 20:42:00 +0800
categories: Seminar
tags: DeepLearning
---

본 글은 2019년 모두의 연구소에서 주최한 모두콘 키노트를 맡으셨던 보이저엑스 남세동 대표님의 연사 내용을 기반으로 작성한 글입니다.

# 시작말

이번 작년에 못갔던 모두콘을 올해 드디어 가게 됐다. 올해 키노트는 보이저엑스의 남세동 대표님께서 연사를 맡아주셨고 딥러닝에 대하 얘기를 해주셨다. 단순히 기술에 대한 내용이나 활용에 대한 내용이 아닌 딥러닝의 근본적인 이야기와 앞으로의 미래를 얘기하는 시간이였다. 

내가 정리한 내용은 크게 두 가지였다. 

1. *딥러닝의 한계는 어디일까?*
2. *우리는 딥러닝을 잘 알고 있는가?*

# 딥러닝의 한계는 어디일까?

인공신경망은 최근에나 생겨난것이 아니다. 그러나 옛날에는 풀리지 않은 문제들과 컴퓨터자원에 대한 문제로인해서 흔히 말하는 딥러닝의 겨울이라는 시기가 있었다. 그러나 이후 역전파나 ReLU 같은 방법으로 인해 딥러닝은 급속도로 성장하기 시작했고 점점 더 많은 성과를 내고있고 많은 일을 자동화해주고있다. 이제는 네트워크조차 스스로 구조화하여 사용한다. 단순한 0과 1의 비트연산만으로 가상현실을 만들 수 있고 불가능하다고 생각했던 일을 현실로 이뤄내기도 한다. 평생을 바둑에 몰두한 사람도 이길 수 있는 알파고를 구현할 수 있다. 

한편으로는 '딥러닝은 연금술이다' 라고하는 얘기도 있다. 그러나 이 말은 반은 맞고 반을 틀리다. 딥러닝이 계산되는 과정은 볼 수 있지만 우리는 그게 어떻게 되고있는건지는 알 수 없다. 

# 우리는 딥러닝을 잘 알고 있는가?

딥러닝은 많은 사람들이 사용하고 있지만 사실 제대로 모르고 사용하는 경우가 많고 알고싶다고해도 아직까지 왜그런지 알 수 없는 것들이 너무나 많다. 그래서 우리가 딥러닝을 모르는 이유에 대해 크게 6가지로 요약하자면 아래와 같은 부분들이 있다.

1. *Activation Function*
2. *Number of Layers*
3. *Batch Normalization*
4. *Batch Size*
5. *Generalization*
6. *Convolution*

## Activation Function

Sigmoid는 왜 사용하게된 걸까? 힌튼도 얘기했었다. 시그모이드 덕분에 30년을 고생했다고. 그렇다면 시그모이드는 왜 나왔고 왜 저런식이 생기게 된걸까? 단지 0~1사이로 변환한다는 것뿐 그 이외에 대답할 수 있는 사람은 없었다. 

<p align="center">
    <img src='https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F275BAD4F577B669920'/>
</p>

이후에 딥러닝의 시대를 열어준 큰 역할을 한것이 바로 ReLU(Rectified Linear Unit)이다. 하지만 ReLU는 큰 단점이 있다. 바로 gradient가 0이 되는 부분이 있다는 것이다. Sigmoid는 banishing gradient 문제가 있지만 ReLU는 dead ReLU 문제가있다. 그러나 잘된다. 이게 왜 잘되는지 또한 설명할 수 있는 사람이 없다.

<p align="center">
    <img src='http://img.thothchildren.com/ec6ef79b-788a-4e69-bffc-8fca03b38ed9.png' width='300'/>
</p>

## Number of Layers

Layer의 개수 또한 정해져있는게 아니다. 대부분 실험적으로 증명되고 2015년 이미지넷 대회에서 우승했던 ResNet에서 그 내용이 있다. 아래는 해당 논문의 실험 결과 테이블이다. Layer가 110개인 경우 error rate가 가장 작았다고 하지만 논문 어디에도 왜 그런지에 대한 내용이 없다.

[Deep Residual Learning for Image Recognition](https://arxiv.org/pdf/1512.03385.pdf)

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1L5hsUBAnt8P9fcKiJuzEzhnmglKn1kKN' width='400'/>
</p>

## Batch Normalization

Batch Normalization은 이제 모델에서 떼어놓기 힘들정도로 많은 도움을 주고 있다. 이 방법은 Covariate Shift를 잘 해결해주기 때문에 학습이 잘된다고 했었지만, 최근에는 이에 대한 반박이 나오기 시작했고 실제 covariate shift와는 상관이 없다는게 밝혀졌다. 이제는 Batch Normalization이 왜 잘되는지 설명할 수 있는 방법을 더 모르게된것이다. 아래는 해당 논문이다.

[How Does Batch Normalization Help Optimization?](https://arxiv.org/pdf/1805.11604.pdf)

## Batch Size

Batch size는 몇이 가장 좋을까요? 정해진게 있을까? 이 실험을 실제로 구글에서 해봤다. 올해 나온 논문이다. 아래 이미지를 보면 보통 사람들과 회사에서는 하기 힘들정도로 batch size을 키워서 실험을 진행했다. 여기서 신기하게도 batch size는 커지면 커질수록 학습이 더 빨리 된다는 것이다. 어느정도 기준이 있긴하지만 결과적으로는 batch size는 크게하면 할수록 좋다. 그러나 이것 역시 실험적으로 얻은 결론이지 왜 그런지는 모른다.

[Measuring the Effects of Data Parallelism on Neural Network Training](https://arxiv.org/pdf/1811.03600.pdf)

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1Nj42fByYoiTTir0N_3_XReIBAyo9FS1V' width='500'/>
</p>

## Generalization

모델 복잡도 커지면 과적합? 이건 그동안 우리가 알고 있던 사실이다. 딥러닝도 당연히 적용되는 얘기지만 한편으로는 찝찝하다. VGG만 봐도 파라미터가 수백만개인데 과적합되지 않고 잘 학습된다. 그래도 어느정도 기준을 넘어서면 과적합이 될 수는 있다. 그러나 이 상식을 깨는 실험이 12월 4일 바로 4일전에 Open AI를 통해서 나왔다. 

[DEEP DOUBLE DESCENT: WHERE BIGGER MODELS AND MORE DATA HURT](https://arxiv.org/pdf/1912.02292.pdf)

실제로는 아래 그림과 같이 어느정도 지점이 지나면 다시 test set에 대한 성능이 좋아질 수 있다는 것이다. 

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1B_wedqIJz9t5-cawpbcTq3WTCbmL3esk' width='700'/>
</p>

실제 논문에서 실험한 결과는 아래 그림을 보면 알 수 있다. 노란색에 가까울수록 error rate가 높아지는 것이다. y축을 기준으로보면 epoch에 따라 변하는 과정을 볼 수 있고 x축을 기준으로보면 parameter 수에 따라 변하는 것을 볼 수 있다. 두 가지 모두 test 데이터를 확인했을때 과적합이 되는 것 같았다가 다시 error rate가 낮아지는 부분을 확인할 수 있다. (중간에 노란띠가 생기는 것) 그러나 역시 이마저도 왜그런지에 대한 설명은 부족하다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=10fadnlgOYhurGn6QHqLPCH0fEAiI06Bs' width='700'/>
</p>

## Convolution

[Exploring Randomly Wired Neural Networks for Image Recognition](https://arxiv.org/pdf/1904.01569.pdf)에서는 random하게 node를 구성해도 결과가 잘 나오는 것에 대해서 실험을 했다. 결과적으로 큰 차이가 없음을 확인했다. 그러나 왜 잘나오는지에 대해서도 알 방법이 없다.

<p align="center">
    <img src='https://drive.google.com/uc?export=view&id=1y0xmIKZVydzH-Yrac9jeZ7ZOVuwNegCa' width='400'/>
</p>

딥러닝에 대한 설명은 아직까지도 '잘되는데 왜그런지는 알 수 없어요' 이다. 심지어 GAN을 처음으로 발표한 우리들의 좋은 친구 이안 굿펠로우조차 본인도 GAN이 왜 잘되는지는 모른다고한다. 

# 맺음말

화약이 처음 나왔을때 산소에 대한 지식은 없었다. 나침반이 처음 나왔을때 자기장에 대한 정보도 없었다. 증기기관이 생겼을때는 열역학에 대한 것을 몰랐지만 이 과정을 통해 1차 산업혁명이라는게 생겼다. 에디슨은 전기를 알고 전구를 만든것이 아니다. 그렇다면 인공지능은? 아직 잘모르지만 잘된다. 

대륙이동설을 통해 예를 들자면 신기하게도 일치하는 부분이 너무많아서 정말인가 싶었지만 그 당시에는 이를 진지하게 받아들이지 않았고 단지 우연일 뿐으로 넘겼다. 그러나 이후에 멘틀이라는 것을 발견했고 그때서야 대륙이동설에 대한 신뢰가 생기기 시작했다. 

딥러닝 또한 현재는 우연이라 생각할 수 있지만 우연이라기에는 잘되고 있는게 너무 많고 앞으로도 계속해서 잘될것이라고 생각한다. 더 이상 딥러닝의 겨울은 오지 않는다.

---

그 동안 남세동 대표님의 페이스북을 통해서 많은 글을 보고 진심으로 딥러닝에 발전을 기대하고 많은 기여를 하고계신다고 생각만 했었는데 실제 강연을 듣고 그 진심을 눈으로 볼 수 있었던 기회였다. 개인적으로 XAI를 공부하며 딥러닝의 Why를 연구한다고 하지만 근본적은 질문은 아직까지 알 수 없는것이 너무 많다는 것을 알았다. 한참 멀은 것 같다.