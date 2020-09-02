---
layout: post
title:  "The problem of concept drift: Deﬁnitions and related work Korean Version (한국어버전)"
date:   2019-12-05 1:37:00 +0800
categories: Paper
image: images/default.jpg
---

본 논문은 2004년에 발표되었으며, 'concept drift'의 정의와 관련연구들에 대한 비평을 하는 논문이다. 원문은 [여기](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.58.9085&rep=rep1&type=pdf)서 확인할 수 있다.

**Authors**: Alexey, Tsymbal    
**Conference**: Computer Science Department Trinity College Dublin  
**Paper**: [The problem of concept drift: Deﬁnitions and related work](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.58.9085&rep=rep1&type=pdf)  
**Year**: 2004   

# 1. Definitions and peculiarities of the problem

Real world를 학습하기 어려운 이유는 바로 the concept of interest가 예측하기위해 사용된 변수가 아닌 어떤 `hidden context` 에 따라 달라질 수 있기 때문이다. 대표적으로는 날씨에 대한 예측이나 고객들의 선호도를 예로 들 수 있다. 

concept drift의 또다른 문제는 noise와 착각하기 쉽다는 점이다. 때문에 noise와 concept drift 모두 잘 잘을 수 있도록 학습해야한다.

수 많은 도메인에서 concept drift가 발생하며 그 주기가 빠른경우도 있다. 이러한 경우 다시 조사해야하는 경우도 많지만 이를 처리할 수 있는 여건이 좋지 않은 경우가 대부분이다. 

concept drift를 다루기위한 이상적인 방법은 크게 3가지가 있다.

1. *빠르게 concept drift를 처리하는 것*
2. *concept drift와 noise를 따로 구분하고 noise에 robust할 것*
3. *반복되는 context를 확인하고 처리하는 것*

# 2. Types of concept drift

일반적으로 concept drift는 크게 두 가지 종류가 있다. 갑자기 생기거나 어느새 생기거나. 예를 들면 대학을 졸업하고 갑자기 금전적인 걱정이 생기게 되거나, 공장의 장비가 서서히 마모되어 품질에 영향이 생기는 경우이다. 

어떤 context의 hidden change는 목표변수의 변화 때문일수도 있지만 데이터의 분포가 바뀌어서 일수도있다. 목표변수가 변하지 않더라도 데이터의 분포가 변하면 모델도 더이상 사용할 수가 없다. 이러한 분포 변화에 따른 모델 수정의 필요성을 `virtual concept drift` 라고 한다. Virtual concept drift와 real concept drift는 종종 함께 발생한다. 그러나 결론적으로 이 두 가지를 구분하는것은 사실 그렇게 중요하지 않다. 중요한 것은 concept drift가 발생했다면 모델은 수정되어야 한다는 것이다.

# 3. Systems for handling concept drift

처음으로 concept drift를 시도한 것은 **STAGGER**, **FLORA** 그리고 **IB3** 일것이다. 이 세가지 방법은 사용할 수 있는 시스템이 서로 다르다. 

1. *Instance selection*
2. *Instance weighting*
3. *Ensemble learning*

**Instance selection**은 가장 일반적으로 사용되는 방법이다. 바로 가장 가까운 미래만 예측하는 것이다. 정해진 윈도우 사이즈를 가지고 현재 concept를 학습하고 바로 직후의 미래를 예측하는 것이다. **FLORA**, **FRANN** 그리고 **Time-Windowed Forgetting**이 대표적이다. 

**Instance weighting**은 Support Vector Machine 같은 학습 알고리즘에 가중치를 더하는 것이다. 예를 들면 현재 concept를 고려하여 나이와 같은 변수에 가중치를 부여하는 것이다. 그러나 Klinkenberg는 그의 연구에서 instance weight는 selection weight보다 더 결과가 안좋았음을 보였다. 왜냐하면 instance weight는 과적합되기 쉽기때문이다. 

**Ensenble learning**은 여러 예측값을 투표하거나 가장 연관이 있어보이는 것을 선택하는 방식으로 해서 concept description을 유지한다. **STAGGER**가 이러한 방식을 사용한다. Harries와 Sammut의 **conceptual clustering**은 같은 concept으로 분류되는 정도에 따라 유사도를 반영하여 instance를 군집하여 stable hidden context를 확인한다. Street과 Kim의 연구에서는 일정한 크기의 시퀀셜 데이터로 나누고 이 데이터들로 효율적인 concept drift를 다룰 수 있도록 enesemble하였다. Stanley와 Kolter 그리고 Maloof의 연구에서는 가장 최근의 instance를 구분할수 있도록 서로 다른 나이로 나누어 ensemble을 하였다. 

# 4. Base learning algorithms for handling concept drift

많은 학습 알고리즘들이 concept drift를 다루기위해 사용된다. 예를 들면 규칙 기반의 학습방법, 의사결정나무, SVM, 나이브베이즈, Radial Basis Functions-Networks 그리고 instance-based learning같이 것들이 있다. 

많은 eager learners가 가진 문제는 (부분적으로 수정이 필요할때가 있는데 업데이트할 수 없는 경우) 부분적으로 일어나는 concept drift를 처리할 수 없다는 것이다. 현실에서는 대부분 concept drift는 지역적으로 일어난다. 예를 들면 특정스팸의 경우만 시간에 따라 변하고 다른 것들은 변하지 않는 것이 대부분이다. 이런 경우 많은 global models들이 concept drift가 일어나지 않은 부분에 대해서는 좋은 모델임에도 불구하고 수정할 수 없기 때문에 버려진다. 반면 [lazy learning](https://en.wikipedia.org/wiki/Lazy_learning) (쿼리가 발생해야지만 학습데이터가 생성되는 학습방법, 예를들면 고객의 구매, 조회 등과 같은 이벤트) 같은 경우 이러한 지역적인 concept drift를 잘 처리할 수 있다.

**Lazy learning**은 concept drift를 다루기 유리한 이점이 크게 세 가지가 있다. 첫 번째로 여러 하위유형으로 분류되어있는 스팸과같은 명확하지 않은 concept에 잘 적용될 수 있다. 두 번째로는 lazy learning에서 case-base는 업데이트하기가 쉽다는 점이다(여기서 case-base란 lazy learning의 특징을 말하는것같다. 앞서 언급한 예시 참고). 예를 들면 새로운 스팸이 등장하는 경우를 말한다. 세 번째는 특정 유형의 문제에 대한 지식을 공유하는 것이 쉽기때문에 다양한 가능성을 가진 case-base들 더 쉽게 유지할 수 있다는 것이다. 이러한 instance-based learning은 비모수적인 학습 방법으로 정확도를 높이기위해서는 더많은 instance를 필요하다라고 저평가되었지만 instance가 많으면 이러한 문제는 해결된다. 

Concept drift를 다루기위한 첫 instance-based learning 방법은 **IB3**이다. IB3는 정확히 분류된 것이 얼마나 있는지 확인하고 해당 클래스의 빈도수를 비교하여 noise가 있거나 오래된 케이스는 버리면서 케이스를 유지한다. IB3는 점진적인(gradual) concept drift에 대해서만 사용될 수 있고 적용이 상대적으로 느리다는 점에서 비판을 받았다. Salganicoff의 **Local Weighted Forgetting (LWF)**는 오래된 instance는 비활성화하지만 이것도 단지 유사한 새 instance가 나타났을때만 해당한다. **Prediction Error Context Switching (PECS)**는 **LWF**와 비슷하지만 instance에 대한 정확도를 고려한다. 그리고 추가적으로 재활성화에 필요한 instance를 저장할 수 있다. PECS와 LWF는 단순 윈도우 기반인 TWF보다 더 성능이 좋고 PECS가 지금까지 가장 성능이 좋다.  

# 5. Datasets for testing systems handling concept drift

concept drift를 평가하기 위한 가장 유명한 benchmark data로는 세 가지 특징값을 갖는 세 가지 Boolean concept를 포함한 STAGGER concept가 대표적이다. 또다른 유명한 benchmark는 hyperplane을 이동하는것이 대표적이다. STAGGER와 Hyperplane 문제는 concept drift, context recurrence, noise의 존재 그리고 부적절한 속성의 유형과 비율을 조정하는 것이 가능하다. Concept drift를 다루는 시스템을 평가하기 위해 몇몇 현실문제도 사용되었다. 예를 들면 filght simulator data, Web page access data the Text Retrieval Conference data, credit card fraud data, breast cancer, anonymous Web browing, US Census Bureau data, e-mail data들이 있다. 이런 현실문제들이 가진 중요한 문제점은 concept drift가 거의 없다는 것이다. 그리고 있다고 해서 의도적으로 만들어진경우이다. 예를 들면 TREC 데이터에서는 각 특정기간 동안 관련된 주제들에 대해서 하위 집합을 제한하였다. 

# 6. Incremental (online) learning versus batch learning

Concept drift를 다루기위한 대부분의 알고리즘은 batch learing과 반대로 **incremental learning**를 고려한다. **batch learning** 많은 instance를 모아서 한번에 학습하고 하나의 모델을 만든다. 반면에 incremental learning은 계속해서 새로운 instance에 대해서 update를 한다. Incremental learning은 실제 생활에서는 온라인에서 매번 새로운 데이터를 처리하기 때문에 더 적합하다고 볼 수 있다. 

Batch concept drift learning은 (Harries et al., 1998; Klinkenberg, 2004)의 연구에서 단순성을 위해 고려되었다. Klinkenberg(2004)에서는 어떻게 이러한 SVM같은 알고리즘이 inclemental learning으로 바꿀 수 있을지 논의했었다.

# 7. Criteria for updating the current model

Concept drift를 다루기위한 많은 알고리즘들은 새로운 데이터가 왔을때 일반적으로 사용하는 모델 업데이트 방식을 사용한다. 그러나 이러한 방법은 새로운 데이터의 양이 많은때 과도한 비용이 들게된다. 그리고 spam 분류같은 경우는 각 데이터에 대해서 정답이 달려야하기 때문에 많은 시간과 자원이 들게된다. 이러한 문제를 극복하기위한 방법으로는 필요한부분만 탐지하고 수정하여 반영하는것이다.  

몇 가지 "**triggers**"라 불리는 기준이 제안된 논문이 있다. **Lanquillon (1999)**의 연구에서는 유저의 피드백없이 변화를 탐지할 수 있는 두 가지 기준을 언급했다. 

1. 새로운 instance에 대한 모델의 정확도의 평균적인 신뢰수준에 근거하는 방법 
2. 주어진 임계값(threshold)를 기준으로 보다 작은 같에 대하여 instance의 부분을 확인하는 방법

그러나 Lanquillon은 이러한 방법도 현실에서는 반영되기 어렵다고 결론지었다. 

**Leake and Wilson (1999)**은 case-based reasoning(사례 기반 추론)에 특정한 두 가지 유사한 기준을 제안했다. 

1. problem-solution regularity
2. problem-distribution regularity (cases(problem)의 유사성을 반영하는 해결책에 대해 얼마나 잘 유사성을 표현할 수 있는지)

이 기준들이 case base의 질을 높일 수 있는 좋은 기준이라고 하지만 역시나 현실에서는 시간에 따라 drift의 비율이나 noise의 수준이 급격하게 변하기때문에 적용하기는 힘들거같다고한다.

# 8. Conclusions

Concept drift는 기계학습 분야에서 앞으로도 더 중요하게 다뤄야할 문제가 될 것이다. 무엇보다 concept drift를 연구하는데 중요한것은 꼭 업데이트가 필요한 부분만 탐지할 수 있는 기준을 만드는 것이다. 현재까지 이 기준은 현실에 적용하기 힘들었으며 더 많은 연구가 필요한 부분이다.