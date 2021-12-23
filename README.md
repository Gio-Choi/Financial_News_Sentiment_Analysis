# Data_Capstone_Design
## 딥러닝을 이용한 금융 뉴스 감성 분석(21.09~21.12)
### 요약
#### [1. 과제 선정 배경 및 필요성](#1-과제-선정-배경-및-필요성)
#### [2. 과제 주요 내용](#2-과제-주요내용)
#### [3. 성능 평가](#3-성능-평가-1)
#### [4. 기대 효과 및 활용 방안](#4-기대-효과-및-활용-방안-1)
#### [5. 결론 및 제언](#5-결론-및-제언-1)

## 수정을 더 하고 있습니다. 12/24까지 수정 완료하겠습니다.

## 1. 과제 선정 배경 및 필요성 
‘불확실성’이란 단어는 현실에서는 중립적 의미이지만, 주식에서는 ‘부정’적 의미로 사용된다. 따라서 주식, 경제의 감성 분석을 위해서는 이에 특화된 모델이 필요하다. 
이렇게 개발된 모델을 통해 특정 종목에 대한 뉴스를 입력하면 해당 종목에 대한 투자의견이 과도하게 긍정이 많은지, 즉 시장이 과열됐는지 판단할 수 있다. 
긍정적인 투자의견이 과도하게 우세하다면, ‘비이성적 과열’로 판단할 수 있는 것이다. 
가령, 특정 기업의 전망이 비교적 낙관적이더라도, 최근 주가가 급격하게 올랐으며 뉴스의 긍정 비중이 70%만 넘어도 ‘비이성적 과열’로 규정할 수 있다.

 ## 2. 과제 주요내용
 성능을 높이기 위해 차례로 LSTM, GRU, BERT, KoBERT 모델로 금융 문장의 '긍정','부정','중립'의 분류를 진행했다. 
 ### 개발 환경 및 언어 : 
 개발 환경 : Colab, Jupyter Notebook
 언어 : 파이썬
 ### 데이터 셋 :
Kaggle(캐글)에서 제공하는 sentiment-analyis-for-financial-news‘ 데이터 셋을 사용했다. 국내에는 공개된 금융 뉴스 자연어 데이터 셋이 존재하지 않으므로, 캐글에서 제공하는, ’긍정‘, ’중립‘, ’부정‘으로 분류된 4846개 문장 데이터 셋을 한국어로 번역하여 사용했다. 번역은 네이버 CLOVA API를 이용하며 금융 도메인의 특성상 어색하게 번역된 부분이 있는지 확인하며 어색한 부분을 수정했다. 
데이터 라벨링은 사이킷런을 활용해 긍정, 중립, 부정은 차례로 ’2‘, ’1‘, ’0‘으로 인코딩했다. 이러한 전처리를 거친 데이터 셋은 사이킷런의 model_selection 모듈을 통해 train, val, test 데이터 셋으로 분리했다.  
### 토큰화:
LSTM과 GRU를 이용한 분석의 경우, 토큰화를 위한 형태소 분석기는 KoNLPy의 Mecab을 사용했다. Mecab의 경우 성능이 우수하며, 다른 분석기에 비해 분석에 소요되는 시간이 상대적으로 적어 이를 형태소 분석기로 선택했다. 형태소 분석기로 토큰화한 토큰들은 기계가 숫자로 처리할 수 있게 정수 인코딩을 진행했다. 이와 달리, BERT와 KoBERT를 이용한 분석은 BERT와 KoBERT 패키지의 tokenizer로 토큰화를 진행했다.  
### 패딩 : 
모델에 인풋하기 이전에 패딩을 통해 서로 다른 길이의 샘플들의 길이를 동일하게 맞춰줬다. 패딩은 데이터 셋의 99.9%를 포함하는 최대 문장 길이를 정하고, 데이터 셋의 각 샘플들은 문장의 길이가 최대 문장 길이보다 작다면 빈 공간들은 0으로 채워주는 과정을 거쳤다.

### 위와 같은 전처리 과정을 거친 후, 차례로 1)LSTM, 2)GRU, 3)감성사전, 4)BERT & KoBERT를 이용한 분석을 진행했다. 
### 1) LSTM, GRU를 이용한 감성 분석. 
과적합을 막기 위해 검증 데이터 손실이 3회 증가하면 학습을 조기 종료했으며,또한 keras의 ModelCheckpoint를 사용해 검증 데이터의 정확도가 이전보다 좋아질 경우에만 모델을 저장하게 했다. 에포크를 지정한 후 훈련 과정에서 검증 데이터의 정확도가 가장 높은 모델로 test 데이터를 예측해봤다. 
### 2) 직접 데이터 라벨링 
#### 캐글 데이터 셋이 4846개 뿐이란 한계 때문인지 정확도가 낮게 나왔고, 이를 극복하기 위해 직접 경제 뉴스 문장 1026개를 라벨링해 GRU분석을 진행해보았다. 
라벨링 과정에서 애매한 부분이 많이 존재했는데, 가령 금리가 인상했다는 뉴스는 은행주 입장에서는 긍정이고, 소비재주 입장에서는 부정이었다. 또한, 뉴스 내에 다수 존재하는 따옴표로 직접 인용된 표현들은 상당히 주관적이었다. 또한, 긍정과 부정이 섞인 문장이 다수 존재했는데, 가령, 현대차 관련 문장의 경우, 소형, SUV 부문에서는 매출이 증가했지만, 중형 세단 부문에서 매출이 감소했다는 문장은 긍/부정 모두를 내포하고 있었다. 따라서, 명백하게 모든 기업에 공통적으로 적용될 수 있는 문장을 선별해 직접 라벨링을 진행했다. 구체적으로, ’매출 원가와 재고가 증가했다‘와 같이 어떤 기업에도 부정적으로 적용될 수 있는 문장들은 부정 라벨링을 진행했고, 영업이익률의 증가, 판매량 증가, 매출액 증가, PER증가와 같은 문장은 긍정 라벨링을 진행했다. 하지만 데이터 셋을 1026개 추가했음에도 유의미한 성능 향상을 일어나지 않았다. 
#### 데이터 셋을 추가했음에도 성능 향상은 일어나지 않았기에 선행연구에서 주로 사용한 ’감성 사전‘ 방식과 ’전이학습‘방식을 새로 시도해보았다. 
### 3)감성사전
우선, 감성사전 제작을 위해 금융 뉴스 약 33 만 개의 문장에서 명사만을 추출해 빈도수를 카운팅했다. 최다 빈출 명사를 내림차순으로 정렬해봤고, 긍/부정의 경계가 모호한 단어, 또는 중립 의미를 가진 단어가 대다수를 차지했다. 이는 카운팅 기반 자연어 분석에서 당연한 결과로 볼 수 있는데, 이 문제를 해결하기 위해 선행연구 '기업 재무 분석을 위한 한국어 감성사전 구축' 에서는 본인들의 주관적 판단과 기존 감성 사전을 기준으로 긍/부정으로 분류했다고 한다. 하지만 개인적 주관으로 긍/부정을 분류하는 것은 머신러닝, 딥러닝 공부 목적과 맞지 않기에 ’전이학습‘ 방식을 남은 기간 시도해봤다. 
### 4) 전이학습 - BERT와 KoBERT
최종적으로 최근 자연어처리에서 가장 많이 활용되고 있는 pre-trained된 모델을 전이학습해 fine-tuning하는 방식을 시도해보았다. BERT의 다국어 버전 모델인 ’bert-base-multilingual-cased‘를 우선 사용해보았다. ’bert-base-multilingual-cased‘은 Google에서 발표한 BERT (Devlin et al., 2018)의 다국어 버전으로 104개 언어의 위키피디아 코퍼스를 모두 사용하여 pre-training을 진행한 모델인데, 한국어를 토큰화하는 과정에 한계를 보이는 모델이다. 따라서, BERT 분석 이후 SKT가 공개한 범용 자연어 모델인 KoBERT를 전이학습해 분석해보았다. 한편, 전이학습은 크게 ’네트워크 설계‘와 ’하이퍼 파라미터 조정‘을 통해 이루어진다. 선행 연구를 찾아본 결과, pre-trained된 BERT모델을 사용해 감성 분석을 할 경우 bert 분석 이전에 LSTM층을 추가하더라도 성능의 향상은 일어날 수 없음을 알게 됐고 따라서 하이퍼 파라미터 조정만을 했다. 최종적으로, 정확도는 0.839가 최대로 나왔다. 

## 3. 성능 평가
### 모델 별 테스트 셋 정확도
|Model|Test Accuracy|
|---|---|
|LSTM|0.58|
|GRU|0.59|
|BERT|0.80|
|KoBERT| 0.839|

정확도가 가장 높게 나온 KoBERT 모델의 경우 정확도가 0.839가 나왔다. 금융 자연어 감성 분석의 벤치마크는 공개된 것이 없지만, 가장 유사한 최신 연구인, KB-ALBERT의 ‘네이버 영화 리뷰 데이터’ 대상 정확도(accuracy)가 89.62를 나타냈기 때문에 이에 준하는 성능을 냈다 평가했다. 한편, KB-ALBERT는 KB국민은행이 공개한 모델인데 국내에 공개된 금융 자연어 감성분석 모델 중 가장 정확한 모델로 여겨진다. 

## 4. 기대 효과 및 활용 방안
### 1) 기대 효과
본 모델로 특정 종목의 긍/부정 비중을 파악해 해당 종목의 과열 여부를 확인할 수 있다. 또한 주가의 추세와 함께 분석한다면 종목의 매수, 매도 타이밍을 잡을 수 있다. 예를 들어, 특정 종목 관련 뉴스를 크롤링하여 모델로 분석해본 결과 긍정의 비중이 과도하게 높고, 지난 5일간 상승 폭이 과도하게 크다면 해당 종목이 과열됐다 판단하고 매도하여 이익을 볼 수 있다. 반대로, 주가가 하락세 임에도 모델 분석 결과 투자 의견이 과도하게 부정 비중이 높다면, 매수하여 이익을 볼 수 있다.
### 2) 활용 방안
긍/부정 비중과 주가 추세를 활용해 실제 투자에 활용할 수 있다. 본 연구로 도출할 수 있는 긍/부정 비중에 더해 활용할 주가의 추세는 증권사 API를 통해 가져올 수 있다. 이 두 가지로 국내에 상장한 코스피, 코스닥 약 2000개 종목을 전부 스크리닝하여 과도하게 상승 중이며 뉴스에서 과도하게 긍정적으로 평가하는 종목, 혹은 과도하게 하락 중이며 뉴스에서 과도하게 부정적으로 평가하는 종목을 선별해 매수/매도 전략을 짤 수 있다. 또한 꼭 상장된 모든 종목을 선별하지 않더라도 본인이 보유한 종목의 긍/부정 비중을 daily로 업데이트해주어서 본인 보유 종목의 매수, 매도의 지표가 될 수 있다. 
   
## 5. 결론 및 제언
본 연구는 국내 금융 뉴스의 감성분석을 통해 해당 종목에 대한 뉴스의 긍/부정 비중을 알아보고자 했다. LSTM, GRU 모델부터, BERT와 KoBERT 전이학습까지 하나씩 시도해나가며 점차 정확도를 높여나갔다. 연구하는 내내 데이터 셋을 얻기가 어려웠는데, KoBERT모델을 파인튜닝하는데 사용할 데이터 셋에 본 연구에서 사용한 뉴스뿐만 아니라 커뮤니티 내 댓글, 영상을 STT로 변환한 텍스트, 증권사 리포트와 같은 다양한 데이터를 포함시킨다면 금융 자연어 모델은 더욱 범용성을 띌 수 있을 것이다. 

## appendix 
### 감성 분석을 기반으로 주가를 예측하지 않은 이유
추가적으로 국내 선행 연구가 주로 주가 예측을 하려 시도했음에도 하지 않은 이유를 말하자면, 시계열 데이터로 주가를 예측하는 것은 현업에서도 논란이 많은 영역이다. 딥러닝의 다른 task에 비해서 주가 예측은 주가 데이터가 정상성(stationarity)를 만족하지 않기에 데이터의 양을 늘린다고 예측을 잘되는 분야도 아니다. 따라서 현재 미국 헤지펀드들은 초단기적인 수준의 트레이딩(scalping)에서만 딥러닝을 주로 활용하고 있는데, 코드를 최대한 효율적으로 짜고 망속도를 올리는 방식으로 거래가 1 밀리세컨초라도 빨리 체결하려는 쪽으로 모델이 개발되고 있다. 즉, 금융 뉴스가 아무리 최신이라 해도 나온지 몇 시간에서 며칠이 지난 데이터이기에 이미 모든 정보가 주가에 반영됐다 가정했고, 본 연구는 주가 예측에 금융 뉴스 감성분석을 이용하지 않기로 결정했다. 다만, 본 연구는 금융 뉴스의 중단기적 긍/부정 비중을 보임으로써 대중이 특정 종목에 관해 비이성적으로 열광하는지 여부를 긴 호흡에서 점검할 수 있도록 했다. 
