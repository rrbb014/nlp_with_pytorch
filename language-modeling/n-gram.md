# N-gram

## Introduction

이전 섹션에서 언어모델에 대해 소개를 간략히 했습니다. 하지만 만약 그 수식대로라면 우리는 확률들을 거의 구할 수 없을 것 입니다. 왜냐하면 비록 우리가 수천만~수억 문장을 인터넷에서 긁어 모았다고 하더라도, 애초에 출현할 수 있는 단어의 조합의 경우의 수는 무한대이기 때문입니다. 문장이 조금만 길어지더라도 Count를 구할 수 없어 분자가 0이 되어 버리거나, 심지어 분모가 0이 되어 버릴 것이기 때문입니다.

## Markov Assumption

따라서 이러한 문제를 해결하기 위해서 Markov Assumption을 도입합니다.

$$
P(x_i|x_1,x_2,\cdots,x_{i-1}) \approx P(x_i|x_{i-k},\cdots,x_{i-1})
$$

한마디로 이전에 출현한 모든 단어를 볼 필요 없이 앞에 $$ k $$ 개의 단어만 조건으로써 본다는 것입니다. 이렇게 simplify 한 표현을 통해서 확률을 approximation 하겠다는 것 입니다. 보통 $$ k $$는 0에서 3의 값을 갖게 됩니다. 즉, $$ k = 2 $$ 일 경우에는 앞 단어 2개를 참조하여 $$ x_i $$ 단어의 확률을 근사하여 나타내게 됩니다.

$$
P(x_i|x_{i-2},x_{i-1}) 
$$

이를 응용하여 문장에 대한 확률도 다음과 같이 표현 할 수 있습니다.

$$
P(x_1,x_2,\cdots,x_i)=\prod_{j}{P(x_j|x_{j-k},\cdots,x_{j-1})}
$$

이때, 문장이 길어지게 된다면 당연히 확률곱이 많아지면서 확률이 매우 작아지게 되어 표현이 힘들어지게 됩니다. 따라서 우리는 log를 취하여 덧셈으로 바꿀 수 있습니다.

$$
\log{P(x_1,x_2,\cdots,x_i)}=\sum_{j}{log{P(x_j|x_{j-k},\cdots,x_{j-1})}}
$$

우리는 이 방법을 $$ n=k+1 $$으로 ***n-gram***이라고 부릅니다.

| k | n-gram | 명칭 |
|-|-|-|
| 0 | 1-gram | uni-gram |
| 1 | 2-gram | bi-gram |
| 2 | 3-gram | tri-gram |

위 테이블과 같이 3-gram 까지는 tri-gram이라고 읽지만 4-gram 부터는 그냥 four-gram 이라고 읽습니다. 앞서 설명 하였듯이, ***n***이 커질수록 corpus내에 존재하지 않을 가능성이 많기 때문에, 오히려 확률이 정확하게 표현되는데에 어려움이 있을 수도 있습니다. (corpus에 존재 하지 않는다고 세상에서 쓰이지 않는 문장 표현은 아니니까요.) 따라서 당연히 corpus의 양이 적을수록 ***n***의 크기도 줄어듭니다. 보통은 3-gram을 가장 많이 사용하고, corpus(말뭉치)의 양이 많을 때는 4-gram을 사용하기도 합니다. 하지만 언어모델의 성능은 크게 오르지 않는데 반해, 단어 조합의 경우의 수는 exponential하게 증가하기 때문에, 크게 효율성이 없습니다.

$$
P(x_i|x_{i-2},x_{i-1})=\frac{COUNT(x_{i-2},x_{i-1},x_i)}{COUNT(x_{i-2},x_{i-1})}
$$

이제 위와 같이 3개 단어의 출현 빈도와, 앞 2개 단어의 출현 빈도만 구하면 $$ x_i $$의 확률을 근사할 수 있습니다.

## Smoothing

위의 방식에는 문제점이 있습니다. 바로 corpus에 출현하지 않는 단어 조합에 대한 대처 방법 입니다. 

## Back-off

## Interpolation

## Limits

n-gram 방식은 굉장히 쉽고 간편합니다. 대신에 단점도 명확합니다. Corpus에 등장하지 않은 단어 조합은 확률을 정확하게 알 수 없습니다. 따라서 Smoothing과 Back-off 방식을 통해서 그 단점을 보완하려 했습니다만, 이 또한 근본적인 해결책은 아니므로 실제로 음성인식이나 통계기반 기계번역에서 쓰이는 언어모델 어플리케이션 적용에 있어서 큰 난관으로 작용하였습니다. 