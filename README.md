# Data_Capstone_Design
## 딥러닝을 이용한 주식 금융 뉴스 감성 분석(21.09~)

캐글에서 제공하는 금융 뉴스 자연어 데이터셋을 활용해 GRU를 이용한 RNN 모델을 시도해보았습니다.
캐글 제공 자연어 데이터 셋은 금융 뉴스를 긍정, 부정, 중립, 총 3가지 class로 분류한 데이터 셋입니다. 
하지만 정확도가 60% 정도밖에 안나왔습니다.

작은 데이터 셋의 한계를 극복해보기 위해 라벨링을 직접해 데이터 셋을 추가해보았습니다. 
요약용으로 배포된 경제 뉴스 자연어 데이터 셋을 AI HUB에서 다운 받아 직접 1026개 문장을 긍정, 부정, 중립을 라벨링해보았고
RNN 모델을 돌려보았으나 성능이 더 낮게 나왔습니다. 라벨링 과정에서는 긍정, 중립, 부정을 분류하기 애매한 경우가 많았습니다. 
예를 들어, 철강값 상승은 중공업 관련주에는 부정적일 수 있으나, 철강 산업에는 긍정적이기 때문입니다. 

선행연구에서 주로 사용하는 1)감성사전 2)전이학습 방식을 시도해보고 있습니다.

우선, 감성사전 제작을 시도해보고자, 금융뉴스 약 33만개의 문장에서 명사만을 추출해 빈도수를 카운팅했습니다.
최다 빈출 명사를 내림차순으로 정렬해봤고,긍/부정을 분류하기 어려운 단어, 또는 중립 의미를 가진 단어가 대다수를 차지했습니다.
이는 당연한 결과로 볼 수 있는데, 이 문제를 해결하기 위해 선행연구 '기업 재무분석을 위한 한국어 감성사전 구축'에서는 본인들의 주관적 판단과 기존 감성사전을 기준으로 긍/부정으로 분류했다고 합니다.
하지만 전 제 개인적 주관으로 긍/부정을 분류하는 것은 머신러닝, 딥러닝 공부 목적과 맞지 않기에 기존에 진행하던 딥러닝 모델을 더 개선하려 합니다.

현재는 Bert-base-multilingual-cased 모델을 3class 캐글 데이터 셋으로 fine-tuning 하였습니다.
Bert 모델을 돌려본 결과, 테스트 셋 대상 정확도가 0.79가 나왔습니다. GRU모델에서 정확도가 0.59였는데 소폭 상승했습니다.  

(2021.12.10)
SKT가 공개한 KoBert모델을 전이학습한 결과, 테스트 셋 대상 정확도가 0.839가 나왔습니다.

