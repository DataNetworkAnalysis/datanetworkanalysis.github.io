---
layout: post
title:  "이상 탐지 넌 무엇이냐? #2"
date:   2020-03-03 16:59:00 +0800
categories: Paper
tags: WorkSpace
--- 

본 내용은 2018년 한국보건사회연구원 에서 출간한 '기계학습(Machine Learning)기반 이상 탐지(Anomaly Detection)기법 연구- 보건사회 분야를 중심으로' 연구보고서를 정리한 내용입니다.  원문은 [여기서](https://www.kihasa.re.kr/common/filedown.do?seq=41721) 확인할 수 있습니다.

# 기계학습 기반 이상 탐지 기법

## 1. 분류 기반 이상 탐지 기법

분류는 각 관측값이 어느 클래스에 속하는지에 대한 라벨링이 붙어있는 데이터로 분류기를 학습한 뒤, 학습된 모델로 새로운 관측값에 대해 각 클래스에 속할 확률을 예측하는 방법이다. 분류 기반 이상 탐지 기법도 거의 비슷한 과정을 거친다. 데이터의 라벨 수에 따라 Multi Class와 One Class로 나눌 수 있다.

<p align='center'>
    <img width='600' src='https://www.researchgate.net/profile/Madhav_Mishra/publication/281650757/figure/fig14/AS:613948625809418@1523388167282/Using-classification-for-anomaly-detection-Chandola-et-al-2009.png'><br>분류 기반 이상 탐지 예시
</p>

Multi Class 문제는 데이터가 여러 클래스로 이루어져 있다고 가정한다. 이상 탐지를 위해서 모델이 각 정상 클래스와 나머지를 구분하도록 훈련시키고, 어느 클래스에도 포함되지 않는 개체를 이상값으로 처리한다.

One Class 문제는 훈련 데이터가 모두 정상이라고 가정한다. One Class Support Vector Machine, One Class Kernel Fisher Discriminant 등의 단일 집단 분류 알고리즘을 이용해 정상 관측값들을 두르는 결정 경계를 학습한다. 테스트 데이터의 관측값이 학습된 경계 밖에 존재하면 이상값으로 처리한다.

### 1.1 신경망(Neural Network) 기반 이상 탐지 기법

신경망 모형은 Multi Class와 One Class에서 모두 적용할 수 있다. Multi Class 문제인 경우, 기본 과정은 신경망이 여러 정상 클래스를 학습한 후에 테스트 데이터를 그 신경망의 입력값으로 넣는 방식이다. 테스트 결과는 신경망의 출력 결과값으로 이상 여부를 판단할 수 있다. 

One Class 문제에서는 AutoEncoder와 같은 복제 신경망을 사용해왔다. 

<p align='center'>
    <img width='500' src='https://i0.wp.com/hugrypiggykim.com/wp-content/uploads/2018/01/1_HIBRgSV2ePFtCSOF_lgPCQ.png?resize=878%2C341'><br>
</p>

AutoEncoder 예시. 출처: [https://hugrypiggykim.com/2018/01/16/fds-fraud-detection-system-with-autoencoder/](https://hugrypiggykim.com/2018/01/16/fds-fraud-detection-system-with-autoencoder/)

One Class 기반 이상 탐지를 위해서는 정상 데이터를 사용하여 AutoEncoder를 훈련시킨다. 그 후, 훈련시킨 모델에 **테스트 데이터를 입력값으로 넣어 발생한 오차가 클수록 이상 개체라고 판단한다.** 이 값을 그대로 이상 점수로도 사용할 수 있다.

<p align='center'>
    <img width='600' src='https://user-images.githubusercontent.com/37654013/89288860-471e6e00-d691-11ea-87ec-5469fb864d4e.png'><br>
</p>

<p align='center'>
    <img width='400' src='https://user-images.githubusercontent.com/37654013/89288883-51d90300-d691-11ea-812d-db41dac62214.png'><br>100개의 피쳐의 1000 시점동안 값의 변화를 시각화. 900 시점부터 분포가 바뀐다는 것을 알 수 있다.
</p>


<p align='center'>
    <img width='400' src='https://user-images.githubusercontent.com/37654013/89288946-687f5a00-d691-11ea-9d36-fcac6a04c4f1.png'><br>900시점 까지는 Anomaly Score의 변동이 거의 없다는 것을 알 수 있다. 
</p>

### 1.2 베이지안 네트워크 기반

Multi Class 문제에 이용된다. 분류 문제일 때 나이브 베이지안 네트워크로 테스트 데이터 내 관측값의 각 정상 클래스와 이상에 대한 사후 확률을 추정해 가장 높은 확률에 해당하는 클래스로 지정한다. 이 때, 각 클래스의 사전확률과 조건부확률은 훈련 데이터를 사용하여 추정해야한다. 만약, 확률이 0으로 나오는 경우, Laplace Smoothing으로 0 대신 1 또는 적절한 양수 값을 부여한다.

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89288980-77660c80-d691-11ea-934e-c29c5996714e.png'><br>
</p>

베이지안 네트워크 예제  [출처: Wikipedia]

### 1.3 Support Vector Machine 기반

One Class 이상 탐지 문제에서 사용할 수 있다. **One Class Support Vector Machine이라고도 불리우며, 훈련 데이터를 포함하는 영역을 학습한다.** 영역 기반 학습 방법은 결정 경계(Decision Boundary)에만 초점을 맞추고 경계의 내·외부에서의 분포에는 관심을 갖지 않는다. 따라서 분포에 둔감하고 데이터의 샘플링이 어떻게 이루어졌는지와 무관한 결과를 도출할 수 있다. 일반적으로 Linear Support Vector Machine을 이용하지만, 영역의 구분이 복잡해지면 RBF(Radial Basis Function)와 같은 커널 함수를 사용하기도 한다. 테스트 데이터가 학습된 영역 내에 존재하면 정상으로, 아니면 이상으로 판단한다. 

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89288997-8056de00-d691-11ea-891e-a4219beb0cb8.png'><br>One-Class Support Vector Machine 예시 with Pyod
</p>


### 1.4 결정 규칙(Decision Rule) 기반

결정 규칙 기반 기법은 시스템에서 정상 자료를 판단하는 규칙들을 학습하고, 어떤 규칙에도 해당하지 않는 관측값을 이상으로 취급한다. Multi Class, One Class 문제 모두 적용할 수 있다. 기본적인 훈련 방법은 다음과 같다. 먼저 훈련 데이터에 RIPPLE, Decision Tree 등의 결정 규칙 학습 알고리즘을 이용해 규칙을 학습한다. 

연관 규칙 마이닝(Association Rule Mining)이 One Class 이상 탐지 문제에 대한 Unsupervised Learning에 사용되어 왔다. 결정 규칙은 범주형 데이터에서 생성되며, 규칙들이 강한 패턴만을 보여주게 하도록 지지도(Support)가 일정 수준을 넘지 못하는 규칙은 제거한다. 

### 1.5 계산 복잡도 및 장단점 비교

- 계산 복잡도

    분류 기반 기법의 계산 복잡도는 사용 알고리즘에 따라 다르다. 일반적으로 의사결정 나무 학습이 빠른 편이고 2차 최적화가 필요한 SVM이 느린 편이라고 알려져있다. 학습 과정에서 이미 학습된 모델을 테스트 과정에 사용하기 때문에 테스트 과정은 매우 빠르다.

- 분류 기반 이상 탐지 기법의 장단점
    - 기존의 여러 강력한 알고리즘들을 이용할 수 있다.
    - 이미 학습된 모형에 대해 예측만 하면 되기 때문에 테스트 과정이 매우 빠르다.
    - Multi Classification 문제에서 각 정상 관측값 종류에 대한 라벨을 구하기 어려울 수 있다.
    - 신경망 모형의 경우 훈련을 위해 뛰어난 성능의 하드웨어가 필요하다는 점이 문제가 될 수 있다. 또한 SVM을 사용할 때 어떠한 커널을 사용해야할지 결정해야 한다.
    - 각 관측값에 대한 이상 점수가 필요한 경우에는 라벨만 지정하는 기법들을 활용하기 어렵다. 이를 해결하기 위해 몇 기법은 분류기의 출력에서 확률적인 예측 점수를 얻어낸다.

## 2. Nearest Neighbor 기반 이상 탐지 기법

이 방법은 **'정상값들은 어떤 근방(Neighbor)에 밀집되어 있고, 이상값들은 각 근방에서 멀리 떨어져있다'**고 가정한다. NN 기법을 이용하기 위해선, 관측값 간의 거리 개념이 정의되어야 한다. 거리는 여러 가지 방법으로 정의할 수 있다. 연속형 변수에 대해서는 유클리드 거리가 일반적인 선택이다. 범주형 변수에 대해서는 단순 일치 계수(Simple Matching Coefficient)가 자주 쓰인다.  데이터가 다변량이라면 변수에 대한 거리를 결합해야 한다는 특징이 존재한다. NN 기반 기법에서 이상 점수를 구하는 방법은 k 번째로 가까운 개체와의 거리를 이용하는 k-NN 방법과 상대 밀도(Relative Density)를 이용하는 DBSCAN방법이 존재한다. 

### 2.1 k-NN 기반

**이상 점수를 k번째로 가까운 개체와의 거리로 정의하는 방법이다.** 일반적으로 이상 점수의 경계값(threshold)을 설정하지만, 이상 점수를 기준으로 정렬시킨 뒤 이상 점수가 가장 큰 m개를 이상값으로 보는 방법도 존재한다. 여기서 m을 결정하는 것은 분석가의 영역이다. 

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289020-88168280-d691-11ea-9133-283cc0bee9f4.png'><br>k-NN 예시 with Pyod
</p>

기본적인 방법에서 다음과 같이 세 가지 방향으로 확장할 수 있다. 첫 번째는 이상 점수의 정의를 바꾸었고, 두 번째는 연속형이 아닌 데이터 처리를 위해 다른 거리 척도를 도입했고, 세 번째는 가까운 개체를 얻기 위해 데이터의 수의 제곱 시간이 소요되는 기본 기법의 계산 효율을 늘렸다.

**가장 가까운 k개 관측값과의 거리 합을 이상 점수로 부여하는 방법**이 있고, **한 관측값에서 일정 거리 이내에 있는 개체의 수를 세는 방법**도 존재한다.

위에 언급한 내용은 대부분 연속형 변수에 관한 기법이다. 범주형 변수의 데이터를 다루는 방법도 존재한다.  HOT라 불리는 초그래프(Hypergraph) 기반 기법은 범주형 값들을 초그래프로 모형화한 뒤 그 그래프에서의 연결 관계를 통해 거리를 계산한다. 

<p align='center'>
    <img width='500' src='https://upload.wikimedia.org/wikipedia/commons/thumb/5/57/Hypergraph-wikipedia.svg/262px-Hypergraph-wikipedia.svg.png'><br>HyperGraph 예시
</p>

범주형, 연속형 변수가 모두 포함되어 있는 데이터에 대한 거리 척도도 제안되었다. **범주형, 연속형에 대한 거리를 따로 구한 후에 더하는 방식**을 사용한다. 이 때, 범주형 변수에 대해서는 두 관측값이 일치하는 값의 개수를, 연속형 변수에 대해서는 종속성(Dependency)를 찾기 위해 공분산 행렬을 이용한다.

효율성 개선을 위해 다양한 변형 방법도 제시되었다. 그 중 몇 가지는 **이상값이 될 수 없는 관측값을 무시**하거나, **가장 이상값이 될 만한 개체에만 집중해 탐색 공간을 축소하는 방법**이 존재한다. 개체에 대해 가까운 이웃들을 구했으면 알고리즘은 지금까지 찾아낸 이상값의 이상 점수 중 최소를 경계값으로 정한다.

분할 기반 기법은 데이터를 분할적으로 군집화(Partitional Clustering)한 뒤, 각 분할에 대해 그 안에서 각 개체의 k번째로 가까운 이웃과의 거리를 구한다.  거리의 최소, 최대값을 이용하여 가장 이상한 k개의 개체를 포함하지 않을 만한 군집은 제거하고 나머지에서만 이상값을 찾아낸다.

### 2.2 상대 밀도 기반

밀도 기반 기법은 각 관측값 근방의 밀도를 추정한다. **근방의 밀도가 낮은 관측값은 이상값이라 판단한다.** 이러한 기법은 영역에 따라 밀도가 다를 때 취약하다. 즉, 밀도가 상이한 클러스터들이 존재할 때 문제가 발생한다. 기존의 밀도 기반 방법론들은 '밀도'라는 개념을 정의하기 위해, 특정 Window Size 및 최소 개수 등을 이용했다. 예를 들면, '**거리가 c 이하인 Window 내 존재하는 데이터가 k 개 이상인가?**' 를 밀도로 지정하였다. 그러나, 클러스터별 밀도가 상이한 경우, 기존의 방법론처럼 밀도에 대한 절대적인 기준을 지정할 수 없어진다. k-NN 방법론 역시, 각 클러스터에 대한 Outlier를 찾기 위한 적절한 kNN Distance가 달라진다. 다음 그림을 보면 이해가 빠르다. O3는 두 집단의 밀도가 다르기 때문에 상대적으로 걸러내기 쉽다. 그러나, O4는 걸러내기 어렵다. 좌측 클러스터와 우측 클러스터의 밀도가에만 특화된 Outlier Detection을 수행하게 된다.

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289042-92388100-d691-11ea-8e81-a0cd64727d31.png'><br>
</p>

이러한 문제를 통해, Local의 상대적인 밀도를  비교하여 Outlier를 정하는 **LOF(Local Outlier Factor)** 기법이 등장했다. 가장 가까운 k 개 점들의 local density의 평균과 자기 자신의 local density 밀도의 비율로 정의된다.

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289062-982e6200-d691-11ea-886d-3c03052f1bf8.png'><br>LOF with Pyod
</p>

### Local Outlier Factor 개념

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289088-a1b7ca00-d691-11ea-8672-0adb38c18ef6.png'><br>
</p>

위 그림에서 O1이 이상치라는 것은 대부분 공감할 수 있을 것이다. 그러나 "O2도 이상치인가?" 라는 질문에 명확하게 대답하기는 어려울것이다. 얼핏 보기엔 이상치가 아닌 것 같지만 C2라는 큰 밀집도를 가진 군집이 존재하므로 이상치라고 볼 수도 있을 것이다. 여기서 LOF는 O2도 이상치로 분류한다. 밀집 지역에서 밀도가 급격하게 감소되기 때문이다. 즉, **LOF는 데이터가 가지는 상대적인 밀도를 고려한 이상치 탐지 기법이다.**

1. **k-distance**

    LOF의 개념을 이해하기 위해서는 k-distance라는 개념을 이해해야한다. k-distance(A)는 A로부터 k번 째 근접 이웃까지의 거리를 의미한다. 또한, k-distance안에 들어오는 오브젝트의 집합을 N_k(A)라고 정의한다. 쉽게 말하면, k-distance보다 작거나 같은 거리를 가지는 수이다. 표로 정리하면 다음과 같다. 숫자는 현재 오브젝트에서 다른 오브젝트까지의 거리이다. **오브젝트들의 밀도가 높을수록 k-distance는 낮아진다는 것을 알 수 있다.**  
      
    <br>
    **k-distance**

    예시|1st|2nd|3rd|2-distance|N_2(A)
    ---|---|---|---|---|---
    예시A	| 1	| 1	| 2	| 1	| 2
    예시B	| 1	| 2	| 3	| 2	| 2
    예시C	| 1	| 3	| 3	| 3	| 3

2. **Reachability Distance**

    Reachability Distance의 수식은 다음과 같다. 단순히 수식을 보면 복잡하게 보인다. 간단히 살펴보자면, p와 o까지의 거리 vs k-distance 중 큰 값을 사용하면 된다는 것을 의미한다. **k-distance 안에 들어오는 오브젝트들은 전부 원 위로 밀어내고 원 밖은 그대로 거리 값을 사용**한다는 개념이라고 이해하면 된다.

    $$reach-distance(p,o)=max{(k-distance(o),dist(p,o)})$$

    <p align='center'>
        <img width='500' src='https://user-images.githubusercontent.com/37654013/89289106-a9776e80-d691-11ea-9e67-705d8ccbda69.png'><br>
    </p>

    rechability → reachability

    위 그림에서도 A를 기준으로 봤을 때, B와 C까지의 Reachability Distance는 원 밖으로 밀어내서 3-distance(A)와 같아진다. D의 경우 원 밖에 존재하기 때문에 그대로 거리값을 사용하게 된다.

3. **Local Reachability Density**

오브젝트 A에 대한 Local Reachability Density는 다음과 같이 구할 수 있다. 

$$lrd_{ k }(A)=\frac { |{ N }_{ k }(p)| }{ \sum _{ O\in { N }_{ k }(A) } reachability-distance_{ k }(A,B) }$$

분자는 k-distance 안의 관측값 수이고 분모는 A에서 다른 오브젝트들까지의 reachability distance이다.  간단하게 A로부터 다른 오브젝트들까지의 Reachability Distance들의 평균을 역수로 취한 것과 같다.

- Case1: A가 밀도가 높은 Dense Area에 위치한다면 분모가 작아지고 Local Reachability Density값은 커지게 된다.
- Case2: A가 밀도가 낮은 Sparse Area에 위치한다면 분모가 커지게되고 Local Reachability Density는 낮아지게 된다.

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289147-b5fbc700-d691-11ea-8e22-92d249011e05.png'><br>
</p>

 1. **Local Outlier Factor**

$${ LOF }_{ k }(A)=\frac { \sum _{ B\in { N }_{ k }(A) }^{  }{ \frac { lrd_{ k }(B) }{ lrd_{ k }(A) }  }  }{ |{ N }_{ k }(A)| } =\frac { \frac { 1 }{ lrd_{ k }(A) } \sum _{ B\in { N }_{ k }(A) }^{  }{ lrd_{ k }(B) }  }{ |{ N }_{ k }(A)| }$$

위 식에서 분자를 보면 A에 속한 B의 Local Reachability Density의 평균을 |N_k|로 나눴다는 것을 알 수 있다. 따라서 **LOF Score는 A가 얼마나 이상치인가를 나타내는 정도라고 볼 수 있을 것이다.**그림을 통해 좀 더 쉽게 이해해보겠다. 

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289164-bbf1a800-d691-11ea-8176-1341f9468fa5.png'><br>
</p>
<br>

**Case별 Score**

Case|ldr(A)|ldr(B)|LOF(A)
---|---|---|---
Case 1	| Large	| Large	| Small
Case 2	| Small	| Large	| Large
Case 3	| Samll	| Small	| Small

위 그림에서 파란점은 A이고 초록점은 B라고 가정한다. LOF(A)값이 크다는 것은 lrd(b)가 높고 lrd(A)가 낮다는 의미이다. 즉, **초록색 점들이 밀도가 높은 지역에, 파란색 점은 밀도가 낮은 지역에 위치한다는 것이다.** 다시 말해, 파란점이 밀도가 낮은 지역에 있을수록, 초록점들의 밀도가 높은 지역에 위치할수록 파란색 점의 LOF값은 커지게 된다.

LOF의 장점은, 밀집된 클러스터에서 조금만 떨어져있어도 이상치로 탐지해준다는 점이다. 그러나, 이상치라고 판단하는 기준을 어디에 잡아야할지 정해줘야 한다는 단점이 존재한다. 특히, 차원이 낮은 데이터에서는 시각적인 도움을 통해 어느 정도 보완할 수 있지만 차원이 커지게 되면 판단하기 어려워진다. 따라서 도메인 지식을 이용한 Threshold 결정이 중요한 요인 중 하나이다.

### 2.3 계산 복잡도 및 장단점 비교

- 계산 복잡도

    기본 NN 기법과 LOF 기법의 단점은 **데이터의 수에 대해 제곱 시간이 소요되는 알고리즘**이라는 점이다. 해당 기법들이 각 관측값에 대해 근방을 찾는다는 특징을 고려하면, [k-d Tree](https://ko.wikipedia.org/wiki/K-d_%ED%8A%B8%EB%A6%AC)나 [R-Tree](https://ko.wikipedia.org/wiki/R_%ED%8A%B8%EB%A6%AC)를 활용해 효율을 높일 수 있다.  그러나, 변수의 수가 늘어나면 효율성이 떨어진다.

- NN 기반 이상 탐지 기법의 장단점
    - 비지도 학습 기반이며, **어떠한 가정도 필요하지 않다.**
    - **이상값이 훈련 데이터에서 가까운 근방을 형성할 확률이 매우 낮아** 준지도 기법이 비지도 기법보다 이상값 감지 면에서 더 좋은 성능을 보인다.
    - 거리만 잘 정의되어 있다면 데이터의 형태에 구애받지 않는다.
    - 정상값이 가까운 이웃이 없거나, 이상값이 가까운 이웃이 있는 상황에서 비지도 기법은 이상을 정상으로 탐지할 수 있다.
    - 준지도 기법을 적용할 때, 훈련 데이터에서 볼 수 없었던 패턴의 정상값이 테스트 데이터에서 등장한다면, 이상으로 처리할 가능성이 높다.
    - 테스트 과정에서 각각의 근방을 계산해야 하므로 오랜 시간이 소요된다.
    - 모델 성능이 거리 척도의 영향을 많이 받는다.
        - 일반적인 유클리드 거리를 사용할 경우, 각 변수별로 척도의 차이가 존재하기 때문에 결과 왜곡이 발생할 수 있다. 따라서 **표준화를 통해 척도의 차이를 해결**하고 **범주형 변수가 존재할 경우 더미 변수를 생성**하는 등 다른 방식으로 문제를 해결해야 한다.
    - 데이터의 차원이 커질수록 거리를 계산하는 데 있어 어려움이 발생한다.

## 3. 군집화(Clustering)기반 이상 탐지 기법

**Clustering은 비슷한 관측값들을 군집으로 형성하여 탐색적 자료 분석과 시각화를 위해 사용된다.** 군집화와 이상 탐지는 완전히 다른 것처럼 보이지만, 지금까지 많은 군집화 기반 이상 탐지 기법이 개발되었다. 이 분야의 기법들은 근본 가정에 따라 세 종류로 나누어 진다.

### 3.1 근본 가정에 따른 군집화 기반 이상 탐지 기법

첫 번째 종류는 **'정상값들은 하나 또는 몇 개의 군집에 모여 있고, 이상값은 군집에 속하지 않는다'**고 가정한다. 이 가정을 바탕으로 한 기법은 **모든 관측값을 군집에 넣지 않아도 되는 DBSCAN, ROCK, SNN 군집화 등의 알고리즘을 사용**한다. FindOut 알고리즘은 WaveCluster 알고리즘의 확장으로, 데이터에서 군집을 찾아낸 뒤 제거하고 나머지를 이상값으로 처리한다. 그러나, 이 기법들은 군집을 찾아내는 것이 주된 목적이기 때문에 이상 탐지에 최적화되어 있지 않다는 단점이 존재한다. 

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289400-2571b680-d692-11ea-8414-59b941bd1968.png'><br>DBSCAN 예시
</p>



두 번째 종류는 **'군집의 중심 중 가장 가까운 것과 거리가 짧으면 정상값, 길면 이상값이다.'** 라고 가정한다. 군집화를 수행하고 관측값이 포함된 **군집의 중심과 관측값 사이의 거리를 이상 점수**로 놓는 것이 기본 과정이다. **Self-Organizing map(SOM), k-Means Clustering, EM algorithm** 등이 존재하며 SOM은 준지도 학습 방법으로 침입 탐지, 오작동 탐지, 사기 탐지 분야에 사용되었다.  이 종류의 기법들은 훈련 데이터를 군집화하고 테스트 데이터를 군집과 비교해 이상 점수를 얻는 방식으로 준지도 학습 방법이 될 수 있다. 이와 같은 기법은 **이상값들이 군집을 이룰 때 성능이 떨어진다는 단점이 존재한다.** 

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289415-2c98c480-d692-11ea-8ffc-5300e1ff80a1.png'><br>k-Means 예시
</p>


세 번째 종류는  **'정상값은 크거나 조밀한 군집에, 이상값은 작거나 희소한 군집에 속한다'**라고 가정한다. 관측값이 속한 군집의 크기나 밀도가 이상 여부를 판단하는 기준이 된다. FindCBLOF(Find Clustering-Based Local Outlier Factor)에서는 관측값이 속한 군집의 크기 및 관측값과 그 군집의 중심 사이의 거리를 반영하는 방법이다.

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289431-33bfd280-d692-11ea-89c6-f8ed865440a2.png'><br>CBLOF 예시 with Pyod
</p>

여러 군집화 기반 이상 탐지 기법은 한 쌍의 관측치들 간의 거리 계산이 필요하다. 거리 계산이 필요하다는 점에서 NN 기반 이상 탐지 기법과 유사하다. 거리 척도 선택은 기법의 성능에 매우 영향을 미치기 때문에 군집화 기반 이상 탐지 기법에도 적용된다. 그러나 두 기법 간의 차이점은 **군집화 기반 이상 탐지 기법이 자신이 속한 군집과 관련하여 각 관측치를 평가**하는 반면, **NN 기반 이상 탐지 기법은 가까이 존재하는 로컬 이웃에 대해 각 관측치를 분석**한다는 것이다.

### 3.2 계산 복잡도 및 장단점 비교

- 계산 복잡도

    학습 과정의 복잡도는 사용하는 알고리즘에 따라 다르다. 관측값에 대한 거리를 모두 구해야하는 제곱 시간 알고리즘도 존재하는 반면, k-Means 처럼 경험적 기법은 비교적 빠르다. 테스트 과정은 적은 수의 군집의 중심들과 비교하면 충분하기 때문이다.

- 군집화 기반 이상 탐지 기법의 장단점
    - 비지도 학습 방법이다.
    - 적당한 군집화 알고리즘만 있다면 복잡한 자료도 다룰 수 있다.
    - 테스트 과정이 빠르다.
    - 모델 성능은 군집화 알고리즘이 정상 관측값의 군집을 얼마나 잘 잡아내는지에 달렸다.
    - 대부분의 알고리즘 목적은 군집화이다. 이상 탐지를 위해 개발된 알고리즘이 아니다.
        - 이상값이 군집을 이루는 경우 극도로 취약해지는 기법들이 존재한다.
    - 다수의 군집화 알고리즘이 모든 관측값의 군집을 지정하기 때문에 이상값이 큰 군집에 들어가 정상값으로 판단될 수 있다.

## 4. 이상 탐지의 통계적 기법

통계적 기법을 이용한 이상 탐지의 근본적 원칙은 '**이상값은 가정된 확률 분포에서 생성되지 않아 부분적으로, 또는 완전히 동떨어졌다고 여겨지는 관측값**'이라는 것이다. 또한 '**이상값은 확률 분포에서 낮은 영역에 나타난다**'고 가정한다.

통계적 기법은 주어진 데이터로 모델링을 수행한 뒤 통계적 추론을 통해 새로운 관측값이 그 모형을 따르는지 판단한다. 검정 통계량을 바탕으로 학습된 모형으로부터 생성되었을 확률이 낮은 관측값을 이상값으로 본다. 이 때, 모수적, 비모수적 기법 모두 적용할 수 있다. 모수적 기법은 분포의 형태를 미리 알고 있다고 가정하고 모수를 추정한다. 반면, 비모수적 기법은 일반적으로 분포에 대한 가정이 없다.

### 4.1  모수적 기법

모수적 기법은 분포의 종류에 따라 나뉜다.

1. **정규분포 기반**

    데이터가 정규분포를 따른다는 것으로 가정하고, 모수는 최대가능도 추정량(Maximum Likelihood Estimator, MLE)를 사용한다. **각 관측값과 추정된 평균 사이의 거리가 이상 점수가 되고, 이상 점수의 경계를 정하고 이상값 여부를 판단한다**. 거리의 정의와 경계에 대해 다양한 방법들이 제안되었다.  대표적으로 가장 간단한 방법은 Box Plot을 이용한 방법(box plot rule)이다.  Box plot은 최소 정상값(min), 1사분위수(Q1), 중앙값, 3사분위수(Q3), 최대 정상값(max)를 나타내는 시각화 방법이다. **3사분위수와 1사분위수의 차(Q3 - Q1)를 사분위수 범위(Inter Quantile Range, IQR)이라 하는데, 이 범위 밖에 위치한 관측값들을 이상값으로 판단한다.**

    <p align='center'>
        <img width='500' src='https://user-images.githubusercontent.com/37654013/89289449-3b7f7700-d692-11ea-96ca-468308d1441c.png'><br>
    </p>

    Grubbs Test는 정규분포를 가정한 일변량 데이터의 이상 탐지에 이용된다. 표본의 평균과 표준편차를  Y_bar, s라 할때, 테스트 관측값 Y의 G점수는 다음과 같이 주어진다.

<p align='center'>
    <img width='300' src='https://user-images.githubusercontent.com/37654013/89289476-476b3900-d692-11ea-9ffe-a8e542a35cf2.png'><br>
</p>

G 값이 다음과 같으면 이상값으로 처리한다.

<p align='center'>
    <img width='300' src='https://user-images.githubusercontent.com/37654013/89289493-4d611a00-d692-11ea-8fe6-7ab527c36427.png'><br>
</p>

여기에서, N은 데이터의 크기, t는 자유도 n인 t분포 분위수이다. alpha는 신뢰 수준을 나타내며, 이상값의 빈도를 간접적으로 조정하는 역할을 한다.

다변량 데이터에 Grubbs Test를 적용하는 방법도 제시되었다. **테스트 관측값 Y의 표본 평균 Y_bar에 대한 Mahalanobis거리로 데이터를 일변량으로 축소하는 방법이다.** 즉, 표본분산행렬 S가 역행렬이 존재할 때 다음을 구하고 y에 실시한 Grubbs Test 결과를 Y에 그대로 가져오는 방식이다.

$$y^2 = (Y-\bar{Y})'S^{-1}(Y-\bar{Y})$$

1. **회귀모형 기반**

회귀모형 기반 이상 탐지는 시계열 데이터에 대해 널리 연구되었다. 기본적 원리는 데이터에 회귀 모형을 적합한 후 그 모형에 대한 테스트 관측값의 잔차로 이상 점수를 산출한다. 잔차란 회귀모형으로 설명되지 않는 부분을 뜻한다. 여기서 잔차의 크기를 그대로 이상 점수로 활용할 수 있지만, 신뢰도로 이상값을 정하는 여러 통계적 검정들이 존재한다.

하지만 **훈련 데이터에 이상값이 존재하면 회귀 모수 및 회귀모형에 영향을 줄 수 있다.** 이러한 문제에서 주로 쓰이는 방안이  Robust 회귀이고, 이상값이 Robust한 적합에 대해 큰 잔차를 가지는 경향이 있어, Robust 회귀를 통해 이상값을 가려냄과 동시에 발견할 수 있다. 

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289514-52be6480-d692-11ea-82ec-1ab430f57bef.png'><br>
</p>

1. **혼합 모수적 모형 기반**

해당 기법은 데이터에 모수적 분포들을 혼합한 모형을 이용한다. 여기서 두 부분으로 나눌 수 있는데, 하나는 **정상값과 이상값에서 서로 다른 분포를 부여**하는 방법이고, 다른 하나는 **정상값에만 혼합 분포를 주는 방법**이다.

첫 번째 기법들은 **테스트 과정에서 관측값이 정상값 분포와 이상값 분포 중 어디에 속하는지를 결정한다**.  두 번째 기법들은 **정상값의 혼합 모형을 구하고 어떠한 모형도 따르지 않는 관측값을 이상으로  판단한다**.

### 4.2 비모수적 기법

비모수적 기법은 모델의 구저를 사전에 결정하지 않고 데이터를 통해 구한다. 모수적 방법보다는 일반적으로 가정이 적다는 것이 특징이다.

1. 히스토그램 기반

    히스토그램은 정상 데이터의 분포를 살피는 가장 간단한 기법이라 할 수 있다. 특히, 침입 탐지 및 사기 탐지와 같이 프로필이 데이터의 행동적 속성을 제한하는 분야에서 유용하다고 알려져있다. 

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289529-58b44580-d692-11ea-87ac-7866f71326be.png'><br>
</p>


훈련 데이터로 히스토그램을 생성한 뒤 테스트 관측값이 유의미한 구간(bin)에 포함되면 정상, 그렇지 않으면 이상으로 판단하는 것이 일반적 방법이다. 이 때 관측값이 포함되는 구간의 빈도를 바탕으로 이상 점수(Anomaly Score)를 구하기도 한다.

여기서 **구간의 폭(Size)를 적절하게 결정하는 것이 매우 중요하다.** 너무 작으면 정상값이 빈 구간에 들어가 이상으로 판단되어 False Alarm Rate가 높아지고, 너무 크면 이상값도 의미 있는 구간에 포함되어 False Negative Rate가 높아진다. 

데이터가 다변량이면 각 변수에 대한 히스토그램으로 이상 점수를 구한 뒤 합산하여 총 이상 점수(Total Anomaly Score)를 구하는 방법이 일반적이다. 

1. 커널 함수 기반

비모수적 밀도함수 추정 기법으로 커널 함수를 이용한 Parzen Window방법이 존재한다. 커널 함수 기반 기법은 모수적 기법과 매우 유사하고, 유일한 차이는 사용하는 밀도함수 추정 기법에 있다.


<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289545-5f42bd00-d692-11ea-830d-456b23414a23.png'><br>
</p>

### 4.3 계산 복잡도 및 장단점 비교

- 계산 복잡도

    사용하고자 하는 모델의 종류에 따라 복잡도가 달라진다. 정규, 포아송, 다항처럼 간단한 분포를 사용하면 보통 데이터의 크기에 따라 선형적으로 증가하며, 혼합 모델이나 은닉 마코프 모델같은 복잡한 모형은 추정에 반복적인 계산을 요구해 수렴 속도와 기준에 따라 오랜 시간이 소요될 수 있다. 커널 기반 기법은 데이터의 크기에 대해 잠재적으로 제곱 시간 알고리즘이다.

- 이상 탐지의 통계적 기법의 장단점
    - 분포에 대한 가정이 맞다면 통계적으로 적합한 모델을 확보할 수 있다.
    - 이상 점수를 구하는 과정에서 신뢰 구간과 같이 의사 결정에 도움이 될 추가 정보를 얻는다.
    - 분포 추정이 이상값에 대해 Robust하면 비지도 학습이 가능하다.
    - 데이터가 특정 분포를 따른다는 가정이 성립하지 않을 때가 많고, 데이터가 고차원일 때 더욱 심해질 수 있다.
    - 적당한 가설 검정 통계량을 결정하는 일이 까다롭고 분포가 복잡하다면 가설을 세우기 어렵다.
    - 히스토그램으로는 변수 사이의 교호작용을 확인할 수 없다.
        - 어떤 관측값이 두 변수에서 전체 데이터에 대해 흔한 값을 가지더라도, 조합을 하게되면 매우 드문 값을 보일 경우, 히스토그램 기반 기법은 이를 감지하지 못한다.

## 5. 이상 탐지 기법들의 상대적 장단점

지금까지 소개한 기법은 각각 특수한 장단점을 가지고 있다. 주어진 문제에 어떤 기법이 가장 적합한지 아는 것은 중요하지만, 문제 공간이 아주 복잡함을 고려하면 분석 기법들을 일일이 살펴보기는 불가능하다. 따라서 몇 가지의 케이스를 통해 각 분야의 장단점을 살펴본다.

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289557-6669cb00-d692-11ea-8b09-4a8fec236a48.png'><br>
</p>


(a)의 데이터에서 정상값은 정규분포로부터 생성되었고 조밀한 군집을 이루는 한편 매우 적은 이상값이 정상값 분포의 평균에서 아주 먼 곳에 존재한다. 따라서 **정상값들을 포함하는 전형적인 훈련 집합을 구할 수 있다.** 대부분 기법의 가정들이 성립해, 이 데이터에는 **어느 기법이든 이상값을 잘 탐지**할 것이다.

(b)는 평균들이 원을 이루고 분산이 매우 작은 여러 정규분포에서 생성된 값들이 정상 데이터를 구성한다.  일집단 분류 기반 기법(One Class SVM  등)은 전체 데이터를 포함하는 원형 경계를 설정해 이상값을 찾아내지 못할 수도 있다. **각 군집이 하나의 클래스가 되면 다집단 분류 기반 기법으로 각 군집을 둘러싸는 경계를 학습해 가운데의 이상값들을 감지할 것이다.** 이상값이 다른 개체와 충분히 떨어져 있어 클러스터링 기법 및 NN 기반 기법도 사용할 수 있다.

(c)는 이상값들이 군집화를 이룬 케이스이다. NN 기반 기법은 이상값을 정상값으로 처리해 안 좋은 성능을 보일 것이다. 고차원 데이터일 경우, 더 많은 문제가 발생한다. **군집화 및 NN 기반 기법은 데이터의 차원이 커질수록 악영향을 받는다.** 거리 측도가 정상, 이상값을 구분하기 힘들기 때문이다.

이때 분류 기반 기법이 좋은 대안이 될 수 있다. 하지만 **정상, 이상에대한 라벨링과 데이터의 불균형(imblance)에 대한 문제가 존재**한다. 따라서 정상값 라벨만 사용하는(Novelty Detection) NN, 군집화 기법이 더 효율적일 수 있다. 통계적 기법은 비지도 학습 방법이지만, 데이터가 저차원이고 통계적 가정이 성립해야한다는 가정이 필요하다. 

NN과 군집화 기반 기법들은 관측값 사이의 거리를 정의할 수 있어야 하며, 그 거리를 이용해 정상값과 이상값을 구분 가능하다고 가정한다. 이러한 거리를 결정하기 어렵다면 분류 기반이나 통계적 기법이 더 나은 선택이다. 

이상 탐지에서 계산 복잡도는 중요한 요소이다. 이상을 감지한 후, 상황에 맞게 빠른 대처를 하기 위함이다. 분류, 군집화, 통계적 기법은 훈련 과정에 많은 시간을 소요하지만, 테스트는 빠르게 진행된다. 반대로 NN 기법은 훈련 과정이 없음에도 테스트가 오래 걸려 활용 범위에 제한이 있다.

## 마치며

이 글을 정리하며 느낀점은 분석 기법을 사용하기 전 해당 분석 기법을 통한 모델링이 어떤 가정을 바탕에 두고 이뤄져야하는지 정확하게 알 필요가 있다는 것을 느꼈다. 이는 이상 탐지 기법 뿐만아니라, 예측 모델링에서도 중요한 요인 중 하나이다. 가령, k-Means는 클러스터들의 분산과 공분산이 동일하다는 가정을 밑에 깔고 있고, 선형 회귀 분석은 변수들이 서로 독립적이고 잔차가 Independent and identically distributed 특성을 갖고 있어야 한다는 가정이 필요하다. 이러한 가정을 바탕으로 모델링을 수행하지 않으면, 모델의 성능이 좋게 나오더라도 우연이라고 볼 수 밖에 없을 것이다. 

<p align='center'>
    <img width='500' src='https://user-images.githubusercontent.com/37654013/89289580-6f5a9c80-d692-11ea-89ed-f47d32256847.png'><br>
</p>

앞으로, 지금까지 정리한 내용을 토대로 이제 데이터를 이용하여 직접 구현해보는 내용을 정리해보려한다. 사실 정리하면서도 이게 무슨 말인지 이해가 안갈 때가 많았다.. 관련 분석 기법을 이용하기에 앞서 다시 한 번 내용을 확인봐야 할 필요가 있을 듯싶다.

---

## Reference

[Jaeyun's Blog - 로컬 아웃라이어 팩터(Local Outlier Factor)](https://jayhey.github.io/novelty%20detection/2017/11/10/Novelty_detection_LOF/)