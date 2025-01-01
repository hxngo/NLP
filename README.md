# NLP

-참고-

LSTM과 GRU:
순환 신경망(RNN: Recurrent Neural Network)의 일종으로 주로 순차적인 데이터를 학습할 때 사용됨.
기본 RNN은 시간이 지남에 따라 과거 정보를 잊어버리는 문제가 있는데, 이를 해결하기 위해 장기 의존성 문제를 해결하는 구조로 만들어짐

1. LSTM(Long Short-Term Memory)
LSTM은 셀(Cell), 입력 게이트(Input Gate)를 사용하는 구조로, 이 구조를 통해 중요한 정보는 유지하고, 불필요한 정보는 잊어버리도록 설계되어 있다.
- 셀(Cell): 정보를 저장하는 역할. 중요한 정보는 셀 상태로 저장되고, 다음 단계로 전달됨.
- 입력 게이트: 새로운 정보를 얼마나 반영할지 결정
- 망각 게이트: 이전의 정보를 얼마나 잊을지 결정
- 출력 게이트: 다음 단계로 얼마나 많은 정보를 보낼지 결정

이 세 가지 게이트로 인해 LSTM은 중요한 정보는 기억하고, 덜 중요한 정보는 잊어버릴 수 있어 긴 문장에서도 의미를 잃지 않고 정보를 유지 가능

1. GRU(Gated Recurrent Unit)
GRU는 LSTM의 단순화된 버전으로, 업데이트 게이트(Update Gate)와 리셋 게이트(reset Gate) 두 가지 게이트만 사용
- 업데이트 게이트: LSTM의 입력 게이트와 망각 게이트를 합친 역할을 한다. 이 게이트를 통해 정보를 얼마나 기억할지 결정
- 리셋 게이트: 과거의 정보를 얼마나 반영할지 결정

GRU는 LSTM보다 구조가 간단해 계산속도가 빠르고, 비슷한 성능을 내기 때문에 많은 경우에 LSTM 대신 GRU가 사용된다.

Transformer Original

트랜스포머의 생성된 이유 - Sequence to Sequence Learning을 위해 생성됨

Seq2Seq 모델:
모델은 연속적인 데이터를 입력받아 또 다른 연속적인 데이터를 출력하는 모델.

인코더와 디코더 두 부분으로 구성.

1. 인코더(Encoder): 입력된 데이터를 받아서 이를 고정된 길이의 벡터로 압축.
ex) 영어 문장을 번역하려고 할 때, 인코더는 영어 문장을 의미가 압축된 벡터로 변환
2. 디코더(Decoder): 인코더가 만든 벡터를 바탕으로 원하는 출력 시퀀스를 생성.
ex) 영어 문장을 한국어로 번역한다면, 이 벡터를 바탕으로 한국어 문장 생성

![Seq2Seq Model](/assets/1.png)

위 사진을 참고하여 인코더는 ‘I am a student’라는 입력 문장을 받아 Context 벡터를 만든다. Context 벡터는 ‘I am a student’에 대한 정보를 압축하고 있으며 컴퓨터가 이해할 수 있는 숫자로 이루어진 벡터이다. Context 벡터는 다시 디코더로 전달되며 디코더는 이를 활용하여 최종적으로 'je suis étudiant'라는 불어 문장을 생성한다.
즉, 인코더는 문장을 가지고 Context 벡터를 만들어주는데, 이 Context 벡터에는 문장에 대한 정보가 응축되어 있다. 반면 디코더는 정보가 응축되어 있는 Context 벡터로부터 다른 문장을 생성해준다. 참고로, 간단한 seq2seq 모델에서 Context 벡터는 인코더의 마지막 스텝이 출력한 은닉(hidden) 상태와 같다.(RNN 모델에서의 hidden state와 같다.)

인코더와 디코더를 한층 더들여다 보면 다음과 같다.

![Encoder Decoder](/assets/2.png)

인코더나 디코더는 RNN으로 구성되어 있다. 그 중 LSTM이나 GRU가 사용되는데 위 예시에서는 LSTM을 사용했다. 인코더의 LSTM은 단어 순으로 입력을 받는다. 아까 언급했던 것처럼 인코더 LSTM의 마지막 은닉 상태가 바로 Context 벡터이다.

인코더의 LSTM 계층은 오른쪽으로도 은닉 상태를 출력하고 위 쪽으로도 은닉 상태를 출력한다. 이. 구성에서 더 위에는 다른 계층이 없으니 LSTM 계층의 위쪽 출력은 폐기된다. 위 그림과 같이 인코더에서는 마지막 문자를 처리한 후 LSTM 계층의 은닉 상태 층인 Context 벡터를 디코더로 전달한다.

디코더는 입력 문장을 통해 출력 문장을 예측하는 언어모델 형식이다. 위 그림에서 <sos>는 문장의 시작(start of string)을 뜻하고 <eos>는 문장의 끝(end of string)을 뜻한다. 인코더로부터 전달받은 Context 벡터와 <sos>가 입력되면 그 다음에 등장할 확률이 가장 높은 단어 (’je’)를 예측한다. 다음 스텝에서는 이전 스텝의 예측값인 ‘je’가 입력되고 ‘je’ 다음에 등장할 확률이 가장 높은 단어(’suis’)를 예측한다. 이런 식으로 문장 내 모든 단어에 대해 반복한다. 하지만 이는 모델의 학습 후 Test 단계에서의 디코더 작동 원리이다.

Training 단계에서는 교사 강요(Teacher Forcing) 방식으로 디코더 모델을 훈련시킨다.

디코더의 훈련 단계에서는 필요한 데이터가 Context 벡터와 <sos>, je, suis, étudiant이다. 하지만 테스트 단계에서는 Context 벡터와 <sos>만 필요하다. 훈련 단계에서는 교사 강요를 하기 위해 <sos>뿐만 아니라 je, suis, étudiant 모두가 필요한 것이다. 하지만 테스트 단계에서는 Context 벡터와 <sos>만으로 첫 단어를 예측하고, 그 단어를 다음 스텝의 입력으로 넣는다.

---

교사 강요란 ?
교사 강요(Teacher Forcing)는 RNN 모델을 훈련할 때 예측 오류가 쌓이는 것을 방지하기 위해 실제값을 사용하는 기법입니다. 예를 들어 번역 모델을 훈련한다고 가정하고, 영어 문장 “How are you?“를 한국어로 번역하는 RNN을 생각해봅시다.

**일반적인 RNN 예측 방식**

일반적인 RNN 모델에서는 예측된 출력을 다음 스텝의 입력으로 사용합니다. 예를 들어:

1. 첫 단어를 예측할 때, “어떻게”를 예측했다고 가정합니다.
2. 다음 단어를 예측할 때, “어떻게”를 입력값으로 넣고 예측을 이어갑니다.

이 방식으로 예측을 이어가면 이전 예측이 틀렸을 경우 그 다음 예측도 틀릴 가능성이 높아져, 예측 오류가 연속적으로 쌓이는 문제가 발생할 수 있습니다.

**교사 강요 방식**

교사 강요에서는 예측한 단어가 아니라 **실제 정답 단어**를 다음 스텝의 입력으로 사용합니다. 예를 들어:

1. 첫 단어를 예측할 때, “어떻게”를 예측했다고 해도, 실제 정답 단어인 “어떻게” 를 다음 입력값으로 넣습니다.
2. 다음 단어를 예측할 때도 마찬가지로 예측한 단어가 아닌 **실제 정답 단어**를 입력값으로 넣습니다.

이렇게 하면 모델이 잘못 예측 했더라도 이전 예측의 오류가 다음 예측에 영향을 미치지 않도록 막을 수 있습니다. 결국 훈련 중에는 모델이 항상 올바른 정답을 기반으로 학습할 수 있게 됩니다.

**예시로 요약**

•	**일반 RNN 방식**: “어떻게”(예측) → “잘못된 단어” (틀린 예측이 다음에 영향을 줌)

•	**교사 강요 방식**: “어떻게”(정답) → “지내”(정답) → “세요”(정답)

---

이제 단어 입력 부분을 더 세분화하여 살펴보겠다.

![Word Embedding](/assets/3.png)

딥러닝 모델은 문자보다 숫자에 대한 성능이 더 좋다. 따라서 모든 문자는 숫자화해야 하며, 이를 워드 임베딩이라고 한다. i, am, a, student라는 문자도 모두 숫자로 즉, 벡터로 표현해야 한다.
아래는 i, am, a, student에 대해 워드 임베딩을 한 결과인 벡터를 나타낸다. 각 단어에 대해 4차원으로 표현했지만 실제는 몇백 차원으로도 표현한다고 한다.

![Word Vector Example](/assets/4.png)

---

RNN이란?
RNN(Recurrent Neural Network)은 순차적인 데이터를 다루기 위해 고안된 인공 신경망이다. 이전 스텝의 정보를 다음 스텝에 전달하면서 연속적인 데이터를 학습하게 된다.

RNN의 입력과 출력 구조

1. 현재 스텝의 입력값: 예를 들어, 시퀀스의 첫 번째 단어 “안녕”.
2. 이전 스텝의 출력값: 이전에 RNN이 계산한 출력값이 다음 스텝에 다시 입력으로 들어간다.

이렇게 해서, 첫 번째 스텝의 출력은 두 번째 스텝의 입력으로 사용되고, 두 번째 스텝의 출력은 세 번째 스텝의 입력으로 사용된다.
즉, RNN은 매 스탭마다 이전 스탭의 정보를 참고하면서 새로운 입력을 연속으로 처리한다. 이렇게 n번째 스탭에서는 이전의 모든 스탭들로부터 축적된 정보를 가지고 있어서, 연속된 데이터의 흐름을 학습할 수 있게 된다.

**디코더의 출력층**

![Transformer Model](/assets/5.png)

디코더 RNN 셀의 출력으로 다양한 단어에 대한 벡터 값이 나올 것이다. 그 중 확률이 가장 높은 단어를 선택하기 위해 softmax를 취해준다. 이를 통해 최종 예측 단어를 생성한다.

seq2seq는 인코더와 디코더로 구성되어 있으며, 인코더는 입력 문장의 정보를 압축하는 기능을 한다. 압축된 정보는 Context 벡터라는 형식으로 디코더에 전달된다. 디코더는 훈련 단계에 교사 방식(teaching force)으로 훈련되며, 테스트 단계에서는 인코더가 전달해준 Context 벡터를 입력값으로 하여 단어를 예측하는 것을 반복하며 문장을 생성한다.

---

기술적으로는 RNN + Attention 에 기반한 인코더 + 디코더 구조를 대체하기 위해 만들어 졌다. Sequence to Sequence Learning 관점에서 볼 때 입력 시퀀스는 인코더가, 출력 시퀀스는 디코더가 처리하는 구조를 기본적으로 지향한다.

![Pretraining Pattern](/assets/6.png)

Original Transformer 는 본직적으로 자연어처리 측면에서 보게 되면 Symbol 입력에서 Symbol 출력으로 나가는 형태이다. 애초에 Transformer가 번역기를 위해 탄생했다는 관점에서 보면 이는 매우 자연스러운 구조이다.
(여기서 말하는 symbol은 단어 또는 토큰을 의미)

**Symbol 생성을 위해 인코더가 꼭 필요한가?**

자연어 처리의 다양한 작업들을 살펴보면, 번역, 요약, 질의응답(Q/A) 등 많은 과제들이 기본적으로 심볼 시퀀스의 입력(seq_input)에서 출력(seq_output) 으로의 변환 과정을 모델링하고 있다. 이러한 변환 과정은 전통적으로 입력 시퀀스를 이해하는 인코더와 출력 시퀀스를 생성하는 디코더 두 부분으로 구성된 모델을 사용하여 처리되어 왔다. 그러나 이러한 과정을 다르게 해설해 볼 수 있다.

**새로운 해석: 입력과 출력을 하나의 전체 시퀀스로 묶기**

입력(seq_input) 과 출력(seq_output) 을 분리하지 않고 하나의 연속된 전체 시퀀스로 간주해보면 어떨까? 이렇게 하면 seq_input과 seq_output은 seq=[seq_input; seq_output]의 형태로 하나의 시퀀스가 된다. 이 전체 시퀀스를 자연스럽게 생성하도록 하는 모델링 방식을 생각해 볼 수 있다.

예를 들어, 한국어 문장 ”오늘 날씨가 어때?”를 영어로 번역할 때 “How is the weather today?” 라는 결과를 얻는 상황을 생각해 보자. 과거에는 “오늘 날씨가 어때?” 라는 부분을 인코더가 처리하고, “How is the weather today?”라는 부분을 디코더가 처리하는 방식이었다. 그러나 이를 하나의 문제로 재구성하여 ”Translate Korean to English : 오늘 날씨가 어때? ⇒ How is the weather today?”라는 하나의 긴 문장을 만들고, 이 문장이 자연스럽게 생성될 확률 P(How is the weather today? | Translate Korean to English : 오늘 날씨가 어때?)을 모델링 하는 것.

**Autoregressive 문제로의 접근**

이 접근 방식은 Autoregressive, 즉 자동회귀 모델링 문제로 해석될 수 있다. 자동회귀 모델은 이전의 데이터 포인트들을 기반으로 다음 데이터 포인트를 예측하는 방식으로, 이 경우에는 주어진 번역 문제에서 이전의 한국어 입력과 추가적인 문맥을 통해 영어 번역 결과를 예측하게 된다.

Autoregressive:
앞의 정보(토큰)를 이용해 다음 정보를 예측하는 방식. 언어 모델에서는 이전 단어들을 하나씩 참고하여 다음 단어를 순차적으로 생성하는 방식으로, 단어를 하나씩 만들어 나가며 문장을 완성.
예를 들어, “저는 제주도에 가고싶어요”라는 문장을 만들 때, “저는”을 먼저 입력하면 모델이 다음 단어로 “제주도에”를 예측하고, 그 다음에는 “가고”, 그 다음에는 “싶어요”를 예측하는 식으로 하나씩 이어서 생성하는 방식

**디코더만으로의 모델링**

이러한 접근 방식은 인코더 없이 디코더만을 사용하여 구현할 수 있다.
이는 디코더가 주어진 입력 시퀀스의 문맥을 충분히 활용하여 필요한 출력 시퀀스를 직접 생성할 수 있기 때문이다. 디코더는 각 시점에서 모든 이전 시점의 정보를 통합하여 다음 심볼을 예측하므로, 전체 시퀀스를 통한 문맥의 흐름을 유지하면서 자연스러운 출력을 생성할 수 있다. 즉 이렇게 진행하게 되면 더 이상 인코더용 어휘셋 따로 디코더용 어휘셋 따로 관리하는게 아니라, 아예 하나의 긴 시퀀스를 모델링 하는 방식으로 처리하게 된다.

이후 다루겠지만, 이 방식을 적극적으로 채용한 것이 바로 GPT이다. 간단히 소개하면 GPT(Generative Pre-trained Transformer)는 Transformer 아키텍처를 기반으로 하는 언어 모델로, 전통적인 인코더-디코더 구조를 사용하지 않고, 모든 텍스트 생성 작업을 단일 디코더로 처리한다. GPT는 각 입력 단어의 위치 정보를 활용하여 문맥을 파악하고, 이를 기반으로 다음 단어를 예측한다. 이러한 모델은 특히 다양한 자연어 처리 작업에서 뛰어난 성능을 보이며, 번역, 요약, 질문 응답 등 복잡한 텍스트 기반 작업에 맞게 적응력을 개선할 수 있는 유연성을 제공하여, 이로 인해 매우 다양한 언어 관련 응용 분야에 적용 가능하다.

‘Data to Numbers’만 수행하고 싶은데 꼭 디코더가 필요한가?

자연어 처리나 이미지 인식과 같은 많은 AI 응용 분야에서는 복잡한 데이터를 효과적으로 숫자로 변환하는 과정이 중요하다. 이러한 과정은 특히 분류나 회귀 문제에서 중요한데, 예를 들어 감정 분석, 주가 예측, 의료 진단과 같은 작업에서 이런 변환이 필수적이다.

디코더의 필요성 재고

분류나 회귀 같은 작업에서는 데이터를 숫자로만 변환하는 것이 목표이기 때문에, 전통적인 인코더-디코더 구조에서 디코더를 생략할 수 있다.
이는 처리해야 할 작업이 출력을 생성하는 것이 아니라 특정한 숫자 값이나 범주를 예측하는 것이기 때문이다. 따라서, 이러한 유형의 문제에서는 데이터를 숫자로 매핑하는 인코더만이 필요하며, 이는 계산 자원을 절약하고 모델의 효율성을 높일 수 있는 방법이다.

인코더만으로의 모델링

이러한 접근의 대표적인 예가 바로 BERT(Bidirectional Encoder Representations)입니다. BERT는 Transformer 아키텍처의 인코더 구조를 활용하여, 입력 데이터의 양방향 컨텍스트를 이해한다. BERT는 특히 문맥에 민감한 특성을 파악하는 데 강점을 보이며, 다양한 자연어 처리 작업에 대해 뛰어난 성능을 제공한다.

BERT와 GPT의 핵심 요소와 성공요인

시기적으로 GPT가 2018년 초에 먼저 발표되고 같은 해 겨울 즈음에 BERT가 발표되었다. 두 구조는 각각의 뚜렷한 목적을 가지고 구현 되었으나 공통적으로 2가지에 기반하여 큰 성공을 거두었다고 볼 수 있다.

1. Self-Supervised Learning에 기반한 Pretraining + Fine-tuning 패턴 적용
2. Downstream task를 위한 처리 방법 고민

각각을 살펴보고 BERT와 GPT에 대해 상세히 다뤄보겠다.

Self-Supervised Learning에 기반한 Pretraining + Fine-tuing

Self-Suervised Learning (SSL)은 레이블이 명시적으로 제공되지 않는 데이터로부터 학습하는 기법. 이 방법은 비지도 학습과 유사하지만, 데이터 자체에서 학습할 작업을 생성하여 모델이 스스로 감독 신호를 생성하게 한다.
여기서는 단순하게 “Self-Supervised Learning” 은 인터넷에 있는 수많은 태깅이 되어있지 않은 RAW 데이터들을 기계학습에 적용할 수 있게 해주는 학습 방법 정도로 이해해도 좋다.
GPT와 BERT는 이 접근 방식을 통해 기존의 연구들이 소규모의 Tagged Dataset에 기반한 신경망 학습에 머무르고 있을 때, 대규모의 데이터에 대한 사전 학습을 수행하여, 언어의 복잡한 패턴과 구조를 미리 학습해 두는 방식을 취하였다.

![GPT Downstream Tasks](/assets/7.png)

Pretraining and Fine-tuning Pattern

위 그림에 대한 설명:
위 이미지는 대규모 데이터와 소규모 태그 데이터로 AI 모델을 학습시키는 과정에 대한 흐름을 설명. 이 과정은 일반적으로 다음과 같다.

1. 대규모(RAW) 데이터: 비 정제된 대량의 데이터를 사용하여 모델을 학습시킨다. 이 데이터는 다양한 원천에서 수집되며, 모델의 기초적인 이해와 학습에 사용된다.
2. 사전 학습 모델(Pretraining Model): 초기 모델은 대규모 데이터를 사용하여 비교적 간단한 작업을 수행하며 학습된다. 이 단계는 모델이 기본적인 패턴과 구조를 학습하도록 도와준다
3. 모델(Model): 사전 학습된 모델이 생성된다. 이 모델은 특정 작업에 맞춰 초기 훈련된 상태이다.
4. 세부 조정(Finetuning Model): 소규모의 태그가 된 데이터(정제된 데이터)를 사용하여 모델을 특정 작업에 맞게 미세 조정한다. 이 단계는 모델의 성능을 높이기 위해 중요한 역할을 한다.
5. 최종 모델(Final Model): 미세 조정 후 완성된 최종 모델이 출력된다. 이 모델은 주어진 작업에 대해 높은 정확도와 성능을 보장한다.

*Downstream task를 위한 처리 방법 고민

GPT 와 BERT는 사전 학습된 언어 모델을 다양한 NLP 작업에 적용하는 방법을 모색했다. 이러한 Downstream task는 기계 번역, 질문 응답, 감정 분석 등과 같은 특정 목적의 작업을 포함한다.

*Downstream task는 사전 학습된 모델을 다양한 특정 목적의 자연어 처리(NLP) 작업에 적용하는 것을 의미. 예를 들어, GPT와 BERT와 같은 사전 학습된 언어 모델은 일반적으로 대규모 텍스트 데이터로부터 일반적인 언어 패턴을 학습한다. 이후 이러한 사전 학습된 모델을 Downstream task에 맞춰 Finetuning하거나 직접 활용하여 특정 NLP 작업을 수행하게 된다.

GPT같은 경우는 기본적으로 생성형 모델이기 때문에 번역, 요약과 같은 생성형 작업(task)은 바로 적용이 가능하다. 하지만, GPT는 여기서 그치지 않고 분류, 유사도 계산 등의 다른 테스크에도 GPT 모델을 활용하는 방법을 몇 가지 제시하였다.

![How to apply GPT for downstream tasks (Figure from the GPT1 paper)](/assets/8.png)

How to apply GPT for downstream tasks (Figure from the GPT1 paper)

위 그림은 트랜스포머 모델이 다양한 *downstream task를 어떻게 처리하는지를 시각화 한 것이다. 각기 다른 작업 유형에 대해 트랜스포머 모델이 어떻게 입력을 받고 처리하는지 설명한다.
*downstream task: [https://chan-lab.tistory.com/31](https://chan-lab.tistory.com/31)

1. 트랜스포머 블록:
- **Text & Position Embed**: 입력 문장과 위치 정보를 임베딩 하여 모델의 첫 입력으로 사용. 이는 모델이 입력 문장의 각 단어와 그 순서에 대한 정보를 학습할 수 있도록 도와준다.
- **Masked Multi Self Attention**: 입력 문장의 각 단어가 문장 내 다른 단어들과 어떻게 연관되는지를 학습한다. Masked는 특정 단어가 자신보다 이후의 단어와는 보지 않도록 제한하는 것을 의미하며, 특히 언어 모델의 학습시 중요하다.
- **Layer Norm**: 입력값을 정규화하여 각 층에서 안정된 학습을 보장
- Feed Forward Layer: 각 단어의 특징 벡터를 독립적으로 비선형 변환하는 층. 이는 복잡한 패턴을 학습할 수 있도록 돕는다

위 블록을 총 12번 반복하여 복잡한 관계와 패턴을 더 깊이 학습할 수 있게 한다.

1. 분류(Classification):
- 입력 형식: Start, Text, Extract는 텍스트의 시작점, 분문 텍스트, 그리고 추출을 나타낸다
- 처리: 트랜스포머 블록을 통해 각 입력이 분석되면, 마지막으로 Linear 레이어를 통해 주어진 텍스트가 어떤 분류에 속하는지를 예측한다.
- 예시: 감정 분석이나 문장 분류 작업이 해당된다.
2. 귀속(Entailment):
- 입력 형식: Start, Premise, Delim, Hypothesis, Extract로 구성됨. Premise는 주어진 전제, Hypothesis는 검증할 가설, Delim은 두 부분을 구분하는 구분자이다.
- 처리: 모델은 전제와 가설 사이의 논리적 관계(예: 참, 거짓, 중립)를 학습한다. 트랜스포머 블록을 통과한 후 Linear레이어에서 출력된다.
- 예시: 자연어 추론(NLI) 작업에 사용된다.
3. 유사성(Similarity):
- 입력 형식: Start, Text 1, Delim, Text 2. Extract로 구성되며, 두 개의 텍스트 간 유사성을 비교한다.
- 처리: 트랜스포머는 두 텍스트의 특징을 각각 분석하여 벡터로 출력하고, 이를 + 연산으로 결합하여 최종 Linear 레이어에서 결과를 도출한다.
- 예시: 두 분장의 의미적 유사성을 평가하는 작업.
4. 다중 선택(Multiple Choice)
- 입력 형식: Start, Context, Delim, Answer 1, Extract 부터 Answer N까지 다양한 선택지와 문맥이 결합된 형식이다.
- 처리: 트래스포머 블록은 문맥과 각 선택지를 개별적으로 처리하며, 각 선택지가 Linear 레이어를 통해 분석된다. 모든 선택시의 점수가 비교되어 최종 선택이 이루어진다.
- 예시: 다지선다형 질문 응답 작업(예: 시험 문제 풀이)

BERT는 Text 입력을 숫자(vextor)로 바꾸는 것에 매우 특화되어 있기 때문에 흔히 말하는 분류형 테스크 들에는 매우 탁월하게 적용된다. 미리 학습된 BERT모델을 불러와 미세 조정을 통해 입력된 문제의 맥락을 더욱 정확하게 파악하고, 특정 작업에 필요한 결과를 얻어낼 수 있다. 특히 자연어 이해(Natural Language Understanding, NLU) 작업에서 강력한 성능을 보여주며, 여러 NLP 벤치마크에서 최고 성능을 보여주었다.

![How to apply BERT for downstream task(Figure from the BERT paper)](/assets/9.png)

How to apply BERT for downstream task(Figure from the BERT paper)

위 그림은 BERT 모델의 사전 학습(pre-training)과 파인튜닝(fine-tuning)과정을 시각적으로 설명한 것이다. BERT는 다양한 자연어 처리(NLP) 작업에서 사용되는 강력한 언어 모델로, 사전 학습과 파인 튜닝 단계로 구성된다.

1. 사전 학습(Pre-training)
- Masked Language Model (Mask LM): BERT는 입력 문장 중 일부 단어를 마스킹([MASK])하고, 모델이 이 마스킹된 단어를 예측하도록 학습한다. 이를 통해 문맥을 이해하고 단어 간의 관계를 학습한다.
- Next Sentence Prediction (NSP): 두 개의 문장을 입력받아 두 문장이 연속된 문장인지 아닌지를 예측하도록 학습한다. 이 과정은 문장 간의 관계를 이해하는 데 도움을 준다.
- 입력 포맷:

- [CLS] (Special Classification token): 입력의 시작을 나타내는 특수 토큰이다. 이 토큰은 전체 문장의 표현을 학습하는 데 사용된다.
- [SEP] (Special Separator token): 두 문장을 구분하는 토큰이다. BERT는 두 문장을 함께 입력 받고 [SEP]을 사용해 나눈다.
- 토큰 임베딩: 입력 문장의 각 단어를 임베딩 벡터로 변환하여 BERT 모델에 전달된다.

- 출력: 사전 학습 과정에서 BERT는 마스킹된 단어와 문장 관계를 예측하도록 학습되며, 이 과정은 대규모 비지도 학습 데이터로 수행된다.

1. 파인 튜닝(Fine-Tuning)
- 특정 NLP 작업 적용: 사전 학습된 BERT는 다양한 Downstream 작업에 맞게 조정된다. 예를 들어, 그림에서는 세 가지 작업이 나타나 있다.

- MNLI(Multi-Genre Natural Language Inference): 자연어 추론 작업으로, 두 문장 간의 논리적 관계(참, 거짓, 중립)를 예측하는 작업이다.
- NER(Named Entity Recognition): 문장에서 사람, 장소, 조직과 같은 고유 명사를 인식하는 작업이다.
- SQuAD(Stanfort Question Answering Dataset): 질문에 대한 답을 주어진 문서에서 찾아내는 작업이다. 모델은 질문과 문단을 입력받고, 정답의 시작과 끝 위치를 예측한다.
- 입력 포맷:
- [CLS]와 문장, [SEP]는 파인 튜닝 작업에서도 동일하게 사용된다.
- 출력:
- 각 작업에 맞게 모델의 최종 레이어 출력이 조정된다. 예를 들어, SQuAD의 경우는 질문에 대한 답의 시작과 끝 위치를 예측한다.
- Start/End Span: 질문에 대한 답의 범위를 나타내는 출력이다.

전체적인 배경을 이해했다면, BERT와 GPT에 대해 상세히 살펴보겠다. 먼저 출판된 GPT에 대해 살펴보고 그 이후에 BERT에 대해 살펴보겠다.

GPT(Generative Pre-trained Transformer)

GPT는 OpenAI에 의해 개발된 모델로, 트랜스포머의 디코더 구조만을 활용하여 텍스트를 생성한다. GPT는 처음에는 다양한 텍스트에서 대규모로 사전 학습을 거친 후, 특정 생성 작업에 적합하도록 미세 조정된다. GPT의 주요 특징 등에 대해 상세하게 살펴보자.

RAW Data로부터 언어 학습

인터넷 세계는 이미 수많은 고품질 텍스트 자원으로 가득 차 있다. 책, 위키피디아, 공식 문서 등을 포함한 이 풍부한 데이터 소스는 양질의 학습 자료를 제공한다. 중요한 질문은, 이러한 데이터를 어떻게 활용하여 기계가 ‘언어’를 배울 수 있게 할까이다.

GPT는 이 문제에 접근하는 데 있어 전통적인 언어 모델(Language Model)의 방식을 채택했다. 전통적인 확률기반 언어 모델은 주로 다음 단어의 확률을 주어진 단어들의 시퀀스에 조건부로 계산하는 방식으로 표현된다. 이를 수식으로 나타내면 다음과 같다.

- *P*( next word ∣ given words )

예를 들어, 문장 “The quick brown fox” 다음에 오는 단어를 예측하려면, 모델은 “jumps”라는 단어가 올 확률을 계산한다.

- *P*( “jumps” ∣ “The quick brown fox” )

자, 위 확률 모델링에 있어서 사람이 구체적인 레이블링을 달아야 할까? 아니다. 이미 기록되어 있는 텍스트 데이터 그 자체가 일종의 교사학습 데이터처럼 작동될 수 있는 것이다.

이러한 방식으로 GPT는 인터넷에서 쉽게 접할 수 있는 방대한 양의 텍스트 데이터를 활용하여, 문맥에 따른 다음 단어의 적합성을 학습한다. 궁극적으로 고도화된 GPT모델은 첫 단어만 주어지면, 그 이후에 오게 될 시퀀스를 생성해 낼 수 있고, 이 시퀀스는 ”사람이 썼을 법한” 그럴듯한(Likelihood) 시퀀스를 생성해내는 것을 배우게 된다. 단순히 문법적으로 정확한 문장을 생성하는 것을 넘어, 주어진 문맥에 맞는 의미론적으로도 적합한 문장을 생성할 수 있는 능력을 가지게 되는 것이다.

Autoregressive Models

Autoregressive(AR) 모델은 이전의 정보를 바탕으로 다음 데이터 포인트를 예측하는 방식을 말한다. 위에서 살펴봤던 언어 모델링 패턴, 즉 뒤의 단어열을 기반으로 다음 단어를 예측하고, 예측된 단어를 포함한 기존 단어열을 바탕으로 다시 또 다음 단어를 예측해 나가는 방식이다.

![Autoregressive Language modeling](/assets/10.png)

Autoregressive Language modeling

이미지 설명:

- X 부분(하단의 녹색 박스들): 입력 시퀀스를 나타내며, 보통 인코더가 처리하는 입력 단어들이나 토큰들
- Y 부분(상단의 분홍색 박스들): 출력 시퀀스를 나타내며, 디코더가 생성해내는 결과 단어들이나 토큰들
- [bos](Begin Of Sequence”), [eos](End Of Sequence): 각각 시퀀스의 시작과 끝을 나타내는 특별한 토근. 디코더는 [bos]로부터 출발하여 y1, y2, y3 등을 생성하며, [eos] 토큰을 생성할 때 까지 시퀀스를 이어간다.

간단하게 Autoregressive LM의 장단점을 정리하면 아래와 같다.

장점

1. 자연스러운 텍스트 생성: 문맥에 순차적으로 텍스트를 생성한다.
2. 강력한 문맥 이해: 이전 모든 단어를 고려하여 다음 단어를 예측한다.
3. 유연한 언어 사용: 다양한 스타일과 주제에 적응력이 뛰어나다.
4. 데이터 효율성: 방대한 언어 데이터로부터 효과적으로 학습한다.

단점

1. 생성 속도 제한: 단어를 하나씩 생성하기 때문에 속도가 느릴 수 있다.
2. 오류 전파 문제: 초기 오류가 나머지 텍스트 생성에 영향을 준다.
3. 학습 난이도: 모든 이전 단어 정보를 처리해야 해 학습 자원이 많이 필요하다.
4. 긴 의존성 문제: 긴 문장에서 시작 부분의 정보가 끝 부분에 미치는 영향이 제한된다.

Transformer Decoder Only 구조

전통적인 Transformer 모델은 인코더와 디코더 두 부분으로 구성된다. 그러나 GPT는 디코더 구성 요소만을 사용하여 전체 모델을 구성하는 ’decoder only’ 접근 방식을 채택한다. 이 구조는 입력 시퀀스를 받아 각 시점에서 이전의 모든 출력을 참고하여 다음 출력을 생성하는 방식으로 작동한다. 이 단일 디코더 구조는 모델의 효율성을 높이며, 계산 자원을 더욱 집중적으로 사용할 수 있게 한다.

![GPT1 Architecture form the Original Transformer](/assets/11.png)

GPT1 Architecture form the Original Transformer

GPT의 진화: GPT-2, GPT-3

GPT 모델은 GPT1을 시작으로 자연어처리의 모든 분야에 획기적인 공헌을 하였다. GPT 시리즈는 GPT-2 GPT-3 그리고 최근에는 GPT-4로 진화하면서, 모델의 크기와 복잡성이 대폭 증가하였으며, 각각의 버전은 더 많은 데이터, 더 큰 모델 사이즈, 더 복잡한 언어 이해 능력을 제공하며, 이는 AI 분야에 큰 영향을 미쳤다. 특히 GPT-3와 GPT-4는 수고 개의 매개변수를 가지고 학습되며, 이는 AI가 인간의 언어를 처리하는 능력을 크게 향상시켰다.

여기서는 각 논문이 AI분야에 던져준 주요 메시지를 간단하게 정리하고 넘어가도록 하겠다.

**GPT-1**

- 논문 제목: “[Improving Language Understanding by Generative Pre-training](https://s3-us-west-2.amazonaws.com/openai-assets/research-covers/language-unsupervised/language_understanding_paper.pdf)”
- 메시지: Decoder-only 모델로 효과적인 언어 학습 가능. 다양한 NLP 작업 수행 가능.
- 핵심: Transformer 아키텍처를 기반으로 한 첫 번째 대규모 언어 모델, 전이 학습의 가능성 보여줌.

**GPT-2**

- 논문 제목: “[Language Models are Unsupervised Multitask Learners](https://d4mucfpksywv.cloudfront.net/better-language-models/language_models_are_unsupervised_multitask_learners.pdf)”
- 메시지: 큰 모델과 많은 데이터로 자연스러운 텍스트 생성 가능.
- 핵심: 모델 확장과 데이터 양 증가로 향상된 텍스트 질과 다양성. Zero-shot 학습 능력 강조. 다양한 테스크를 하나의 모델로 다룰 수 있음.

**GPT-3**

- 논문 제목: “[Language Models are Few-Shot Learners](http://arxiv.org/abs/2005.14165)”
- 메시지: 거대한 모델로 추가 튜닝 없이도 뛰어난 성능 가능.
- 핵심: 1750억 파라미터 사용, 복잡한 언어 이해 및 생성 능력 강화. Few-shot 및 zero-shot 학습 능력 크게 향상. 커다란 모델은 그 자체로 Reasoning 능력을 가짐.

초거대 언어모델의 초석

GPT 시리즈의 발전은 초거대 언어모델이라는 새로운 시대의 문을 열었다. 이러한 모델은 기계 번역, 요약, 문서 생성 등 다양한 NLP 태스크에 광범위하게 적용되고 있다. 또한, 이 모델들은 AI가 인간의 언어를 더 깊이 이해하고 자연스럽게 생성할 수 있는 능력을 제공함으로써, AI의 사용 가능성을 크게 확장하고 있다.

---

BERT**(Bidirectional Encoder Representations from Transformers)**

BERT는 Google AI에 의해 개발된 모델로, 트랜스포머의 인코더 구조만을 활용하여 언어를 이해한다. BERT는 특히 양방향 문맥 이해에 중점을 둔 사전 학습을 통해 자연어 처리의 다양한 작업에서 높은 성능을 달성한다.

새로운 언어 모델링의 형태

BERT는 전통적인 언어 모델과는 다른 접근 방식을 채택한다. BERT의 핵심 특징 중 하나는 Masked Language Model (MLM)이다. 이 방식에서 일부 단어는 ‘마스크’ 처리되며, 모델은 주어진 문맥 안에서 이 마스크된 단어를 예측해야한다. 예를 들어, 문장 “The [MASK] brown fox jumps”에서 BERT는 [MASK] 위치에 들어갈 적합한 단어를 예측한다. 즉 original 텍스트인 “The quick brown fox jumps”라는 문장에 적절히 mask를 씌워서 수 많은 문제-답 쌍을 만들어 낸다. 만약, 언어를 충분히 배워낸 BERT라면 [MASK]에 해당하는 어휘를 주변 문맥으로 부터 배워낼 수 있을 것이다.

![Masked Language Model in BERT](/assets/12.png)

Masked Language Model in BERT

또 다른 언어 모델링 방식은 Next Sentence Prediction (NSP) 라는 형태의 언어이해 문제이다. 이 문제는 두 개의 문징이 서로 이어질 문장인지 그렇지 않은 문장인지 언어 모델에게 물어보는 형태로 학습된다.

예를 들어,

- 문장 A: “고양이가 매트 위에 앉아 있다.”
- 문장 B: “고양이가 주인을 쳐다보고 있다.”

이 두 문장은 실제로 연결되어 있는 문장이다. 따라서 BERT는 저 두 문장에 대해서 ‘이어질 수 있는 문장인가’ 라는 질문에 “yes”를 예측해야 한다. 
반대로

- 문장 A: “그는 수영복을 입었다.”
- 문장 B: “그 테이블은 둥글다.”

이 두 문장에 대해서는 ‘이어질 수 있는 문장인가’ 라는 질문에 BERT는 “no”를 예측할 수 있도록 학습된다. 실제로 문장 A와 문장 B는 서로 이어지지 않는 문장을 서로 다른 문서로부터 랜덤하게 가져왔기 때문이다.

![Next Sentence prediction Model in BERT](/assets/13.png)

Next Sentence prediction Model in BERT

만약 제대로 학습된 언어모델이라면 언어의 맥락을 이해할 것이고, 이 맥락에 기반해 다음에 올 문장이 올법한 문장인가 아닌가를 알 수 있다. 이러한 맥락에 대한 이해는 질문 응답 및 문서 요약과 같은 작업에 언어모델이 잘 작동하도록 한다.

Transformer Encoder Only구조

BERT는 전통적인 트랜스포머의 인코더 구성 요소만을 사용한다. 하지만 단순하게 그대로 가져다 쓰는 형태는 아니다. 내부 트랜스포머 인코더 블록은 원형 그대로 유지되나, 입력되는 임베딩 레이어에 몇 가지 변화가 추가되었고, 인코더 블록의 최상단에서 결과를 받아 예측 및 결과를 내놓기 위한 부분이 추가 되었다. 
하나씩 살펴보자면

![BERT Architecture form Transformer](/assets/14.png)

BERT Architecture form Transformer

Segmert 정보

Segment Embedding은 BERT 모델이 두 개의 문장을 구분할 수 있도록 도와주는 정보이다. 예를 들어, BERT가 “문장 A”와 “문장 B”를 함계 처리할 때 각 문장이 어디까지인지 알아야 한다. 이 때, Segment Embedding은 “문장 A는 0번”, “문장 B는 1번” 처럼 표시해 BERT 각 문장을 구분할 수 있게 해준다.
이 과정에서 [SEP] 토큰은 두 문장의 경계를 명확히 표시해준다. 이렇게 하면 BERT는 두 문장을 한 번에 입력 받아도 문장들이 어디서 시작되고 끝나는지를 이해할 수 있다.

![Embedding in BERT](/assets/15.png)

Embedding in BERT

[CLS] (Special Classification token) 토큰

또한 BERT는 문장 전체의 의미를 얻어내기 위해 [CLS]라는 특별한 토큰을 마련하였다. Transformer의 self-attention 덕분에 [CLS]는 모든 단어들의 정보를 집약하여 문장 전체의 블렌딩된 정보를 담게 되고, 이로부터 sequence classification을 수행할 수 있다.

학습형 Positional 임베딩

Positional Embedding은 각 단어의 위치를 나타내기 위해 사인, 코사인을 사용하지 않고, BERT가 학습하면서 위치 정보를 스스로 조정할 수 있는 방법이다. 즉, BERT는 훈련 중에 데이터에 맞춰 위치 정보를 자동으로 최적화한다. 이렇게 하면 모델이 더 유연하게 각 단어의 위치를 인식하고, 데이터의 특성에 맞게 잘 작동할 수 있다.

위 그림에서 보다시피 BERT의 임베딩 부분은 토큰 임베딩, 세그먼트 임베딩, 포지셔널 임베딩을 모두 더하여 전체 임베딩 벡터를 계산해 낸다.

BERT Pooler

Pooler는 BERT가 문장의 전체 의미를 잘 요약할 수 있도록 돕는 부분이다. BERT는 각 단어에 대한 정보(임베딩)를 처리하지만, 문장의 전체적인 의미를 표현할 때는 특정한 요약이 필요하다.
Pooler는 입력된 문장의 첫 번째 토큰은 [CLS]의 출력 벡터를 가져와 이 벡터를 문장 전체 표현으로 사용한다. 이 벡터는 Pooler를 통해 한번 더 처리되며, 문장 분류와 같은 작업에 적합하게 만들어진다. 쉽게 말해, Pooler는 문장의 대표적인 의미를 뽑아내며 BERT가 문장 수준의 작업을 더 잘할 수 있도록 도와준다.
 최종 출력 벡터를 추가가공하여 다양한 다운스트림 작업에서 활용할 수 있는 것이다. 특히 분류, 회귀 문제 등에서 이를 바로 활용할 수 있다.

![BERT Pooler](/assets/16.png)




BERT Pooler

결론

이 글을 통해 우리는 Transformer의 전통적인 인코더-디코더 구조를 벗어나 특정 목적에 맞게 아키텍처를 수정했을 때, 매우 효율적인 결과를 얻을 수 있다는 것을 살펴보았다. BERT와 GPT 모델은 이러한 점을 명확하게 보여주며, 각각의 모델이 어떻게 언어를 이해하고 모델링 하는지에 대해 새로운 방식을 제안하였다.

BERT는 특히 문맥을 전방향으로 파악하는 능력을 강화하여, 문장 간의 관계를 파악하는 데 강점을 보였다. 반면, GPT는 연속적인 텍스트 생성에 초점을 맞추어, 주어진 텍스트에 이어질 내용을 자연스럽게 예측하는 능력을 발휘했다. 두 모델 모두 인터넷에서 접할 수 있는 방대한 양의 데이터를 활용하는 self-supervised learning 기법을 적용함으로써, 레이블이 지정되지 않은 데이터로부터 학습할 수 있게 되어 높은 성능을 보여주었다.

이러한 혁신적인 접근 방식은 NLP 분야에서만큼이나 다른 분야에도 깊은 영향을 미쳤다. 특히 GPT시리즈는 다양한 언어 처리 작업에 대한 새로운 가능성을 열었으며, ChatGPT와 같은 대화형 AI 시스템의 기반 기술로 자리 잡게 되었다.

-----------
Downstream Task 개념 설명
[https://chan-lab.tistory.com/31](https://chan-lab.tistory.com/31)

BERT 설명
[https://hwiyong.tistory.com/392](https://hwiyong.tistory.com/392)

**Sequence to Sequence (시퀀스 투 시퀀스), Attention(어텐션) 개념** [https://acdongpgm.tistory.com/216](https://acdongpgm.tistory.com/216)
