# Productization of Machine Translation

이제 기계번역은 2014년도의 deep impact로부터 수많은 variation들이 쏟아져나왔고, 어느정도 standard가 정립되어 가고 있는 시점입니다. 따라서 많은 회사들이 자신들의 상용화 경험을 토대로 성과를 자랑하는 논문을 써 내고 있습니다. 이번 section에서는 이러한 각 회사 별 기계번역 시스템을 살펴보도록 하겠습니다.

사실, 요즈음과 같이 새로운 기술에 대한 논문 및 소스코드 공개가 당연시되는 현상 아래에서는 각 기계번역 시스템마다의 수준의 편차가 그렇게 크지 않습니다. 따라서 누가 최신 기술을 구현했느냐보다, 상용화에 대한 경험과 연륜, 데이터의 양과 품질(정제 이슈)에 따라 성능이 결정될 수 있습니다.

아래는 현재 대표적인 기계번역 시스템의 현 주소를 살펴볼 수 있는 간단한 샘플 문장 입니다. (2018년 04월 기준)

> 차를 마시러 공원에 가던 차 안에서 나는 그녀에게 차였다.
- Google: I was kicking her in the car that went to the park for tea.
- Microsoft: I was a car to her, in the car I had a car and went to the park.
- Naver: I got dumped by her on the way to the park for tea.
- Kakao: I was in the car going to the park for tea and I was in her car.
- SK: I got dumped by her in the car that was going to the park for a cup of tea.

위와 같이 어려운 문장(문장구조는 쉬우나 중의성에 대해서 높은 난이도)에 대해서 어찌보면 갈 길이 멀기도 합니다. 따라서, 위와 같이 누군가 저런 어려운 문제에 대한 한단계 더 높은 기술력을 가졌다기보단, 기술의 수준은 비슷하다고 볼 수 있습니다.

## Pipeline for Machine Translation

이번 섹션에서는 실제 Machine Translation 시스템을 구축하기 위한 절차와 시스템 Pipeline이 어떻게 구성되는지 살펴보도록 하겠습니다. 아울러 그 다음에는 실제 Google 등에서 발표한 논문을 통해서 그들의 상용 번역 시스템의 실제 구성에 대해서 살펴보도록 하겠습니다.

통상적으로 번역시스템을 구축하면 아래와 같은 흐름을 가지게 됩니다.

1. Corpus 수집, 정제
    - Parallel corpus (병렬 말뭉치)를 다양한 소스에서 수집합니다. WMT등 번역 시스템 평가를 위해 학술적으로 공개 된 데이터 셋도 있을 뿐더러, 뉴스 기사, 드라마/영화 자막, 위키피디아 등을 수집하여 번역 시스템에 사용 할 수 있습니다.
    - 수집된 데이터는 정제 과정을 거쳐야 합니다. 정제 과정에는 양 언어의 말뭉치에 대해서 문장 단위로 정렬을 시켜주는 작업부터, 특수문자 등의 noise를 제거해 주는 작업도 포함 됩니다.
1. Tokenization
    - 각 언어 별 POS tagger 또는 segmenter를 사용하여 띄어쓰기를 normalization 시켜 줍니다. 영어의 경우에는 대소문자 등의 normalization issue가 있을 수도 있습니다. 한국어의 경우에는 한국어의 특성 상, 인터넷에 공개 되어 있는 corpus 들은 띄어쓰기가 제멋대로일 수 있습니다.
    - 한국어의 경우에는 Mecab과 같은 open 되어 있는 parser들이 있습니다.
    - 띄어쓰기가 정제 된 이후에는 Byte Pair Encoding(BPE by Subword or Wordpiece)를 통해 어휘 목록을 구성합니다.
1. Batchfy
    - 전처리 작업이 끝난 corpus에 대해서 훈련을 시작하기 위해서 mini-batch로 만드는 작업이 필요합니다.
    - 여기서 중요한 점은 mini-batch 내의 문장들의 길이를 최대한 통일시켜 주는 것 입니다. 이렇게 하면 문장 길이가 다름으로 인해서 발생하는 훈련 시간 낭비를 최소화 할 수 있습니다. 
    - 예를 들어 mini-batch 내에서 5단어 짜리 문장과 70단어 짜리 문장이 공존할 경우, 5단어 짜리 문장에 대해서는 불필요하게 65 time-step을 더 진행해야 하기 때문입니다. 따라서 5단어 짜리 문장끼리 모아서 mini-batch를 구성하면 해당 batch에 대해서는 훨씬 수행 시간을 줄일 수 있습니다.
    - 실제 훈련 할 때에는 이렇게 구성된 mini-batch들의 순서를 shuffling 하여 훈련하게 됩니다.
1. Training
    - 준비된 데이터셋을 사용하여 seq2seq 모델을 훈련 합니다.
1. Inference
    - Evaluation (성능 평가) 과정도 inference에 속합니다. 이때에는 준비된 테스트셋(훈련 데이터셋에 포함되어 있지 않은)을 사용하여 beam-search를 사용하여 inference를 수행합니다.
1. Tokenization 복원
    - inference 과정이 끝나더라도 BPE를 통한 tokenization이 더해져 있기 때문에 아직 사람이 실제 사용하는 문장 구성과 형태가 다릅니다. 따라서 BPE에 의한 tokenization을 복원 하면 프로세스가 종료됩니다.

