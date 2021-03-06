# Neural Network Language Model

## Introduction

앞서 설명한 것과 같이 기존의 n-gram 기반의 언어모델은 간편하지만 훈련 데이터에서 보지 못한 단어의 조합에 대해서 상당히 취약한 부분이 있었습니다. 그것의 근본적인 원인은 n-gram 기반의 언어모델은 단어간의 유사도를 알 지 못하기 때문입니다. 예를 들어 우리에게 아래와 같은 문장이 주어졌다고 했을 때,

* 고양이는 좋은 반려동물 입니다.

사람은 단어간의 유사도를 알기 때문에 다음 중 어떤 확률이 더 큰지 알 수 있습니다.

* $$P(\text{반려동물}|\text{강아지는}, \text{좋은})$$
* $$P(\text{반려동물}|\text{자동차는}, \text{좋은})$$

비록 강아지가 개의 새끼이고 포유류에 속하는 가축에 해당한다는 지식이 없을지라도, 강아지와 고양이의 유사도를 알기 때문에 자동차 보다는 강아지에 대한 반려동물의 확률이 더 높음을 유추할 수 있습니다. 하지만 n-gram 방식의 언어모델은 단어간의 유사도를 알 수 없기 때문에, 이와 같이 보지 못한 단어의 조합에 대해서 효과적으로 대처할 수 없습니다.

하지만 Neural Network LM은 word embedding을 사용하여 단어를 vectorize 함으로써, 고양이와 강아지를 비슷한 vector로 만들어 자동차보다 훨씬 높은 유사도를 가지게 합니다. 따라서 NNLM이 훈련 데이터에서 보지 못한 단어의 조합을 보더라도, 최대한 비슷한 훈련 데이터에 대한 기억을 찾아내어 대처할 수 있도록 합니다.

Neural Network LM은 많은 형태를 가질 수 있지만 우리는 가장 효율적이고 흔한 형태인 RNN을 활용한 방식에 대해서 짚고 넘어가도록 하겠습니다.

## Recurrent Neural Network LM

![](/assets/rnn_lm_architecture.png)

Recurrent Neural Network Lauguage Model \(RNNLM\)은 위와 같은 구조를 지니고 있습니다. 기존의 n-gram은 각각의 단어를 descrete한 존재로써 처리하였기 때문에, $$ n $$이 커져서 word sequence가 길어지면 어려운 부분이 있었습니다. 따라서, $$ n-1 $$ 이전까지의 단어만 조건부로 잡아 확률을 approximation하였습니다. 하지만, RNNLM은 단어를 embedding하여 vectorize함으로써, sparsity를 해소하였기 때문에, 문장의 첫 단어부터 모두 조건부에 넣어 확률을 계산할 수 있습니다.

$$
P(w_1,w_2,\cdots,w_k) = \prod_{i=1}^{k}{P(w_i|w_{<i})}
$$

로그를 취하여 표현 해보면 아래와 같습니다.

$$
\log{P(w_1, w_2, \cdots, w_k)} = \sum_{i = 1}^{k}{\log{P(w_i|w_{<i})}}
$$

### Implementation

## Drawbacks

NNLM은 word embedding vector를 사용하여 sparseness를 해결하여 큰 효과를 보았습니다. 따라서 훈련 데이터셋에서 보지 못한 단어의 조합에 대해서도 대처가 가능합니다. 하지만 그만큼 연산량에 있어서 n-gram에 비해서 매우 많은 대가를 치루어야 합니다. 단순히 table look-up 수준의 연산량을 필요로 하는 n-gram방식에 비해서 NNLM은 다수의 matrix 연산등이 포함된 feed forward 연산을 수행해야 하기 때문입니다. 그럼에도 불구하고 점점 빨라지는 H\W 사이에서 NNLM의 중요성은 커지고 있습니다.