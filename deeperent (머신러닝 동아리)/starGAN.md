## starGAN
<br>

<img src="/img/starGAN_1.png" width="900" height="500"> <br>

<img src="/img/starGAN_2.png" width="900" height="500"> <br>
이미지 변환이란 어떤 이미지를 특정한 특징을 가진 이미지로 변환하는 것을 말한다. 예를 들어 무표정인 사람의 모습을 웃는 모습으로 바꾼다거나 혹은 성별을 바꾸는 등 
다양한 특징을 가지게 변환시키는 작업을 “이미지 변환", 변환할 때 적용하는 특징을 “도메인" 이라고 부른다.
딥러닝 분야에서 이미지 변환기의 대표적인 예로 Cycle-GAN 이 있다.

<img src="/img/starGAN_4.png" width="900" height="500"> <br>
Stargan은 단일 뉴럴 네트워크로 학습된 모델을 통해 여러 개의 도메인을 가진 이미지를 변환 하는 방법을 제시합니다.<br>
아래 사진에서 다중도메인 변환의 예시입니다.<br>
왼쪽과 오른쪽은 학습된 데이터셋만 다르고 단일 뉴런네트워크로 여러 개의 도메인을 변환한 결과입니다.<br>
왼쪽 input의 이미지에 대해 금발, 성별, 나이, 피부색이 한 도메인입니다.<br>
오른쪽은 분노, 행복, 공포에 해당하는 도메인으로 input의 이미지가 변환된 모습입니다.<br>
<br>
해당 도메인들을 한 뉴런 네트워크로 학습하여 원본이미지를 원하는 도메인 옵션을 통해 변환 할 수 있습니다.<br>
<br>
뒤에 다시 기술하겠지만 위의 결과는 단일 모델에서 2개의 데이터 셋을 동시에 학습하여 출력한 결과이다.<br>
<br> <br>

<img src="/img/starGAN_3.png" width="900" height="500"> <br>
StarGAN 논문에서는 해결하고자 한 문제와 기여는 다음과 같습니다.<br>
<br>

<img src="/img/starGAN_5.png" width="900" height="500"> <br>
기존 연구[CycleGAN, DiscoGAN, pix2pix, cGAN 등]에서는 한 개의 특징만을학습해서 변환하는 방법을 제시한다.
 이 방법으로 구현된 신경망은 웃는 얼굴을 우는 얼굴로 바꾸는 작업밖에 하지 못한다.
 이런 환경에서 다양한 도메인으로 변환하려면 도메인 K당 K(K-1)개의 네트워크가 필요하다.
 또한 각 데이터셋이 부분적으로 라벨링되어 있기 때문에, jointly training이 불가능하다.
<br><br>
그림의 왼쪽에 있는 그림이 기존 모델인 크로스 도메인 모델인데 이 모델에서 숫자가 적힌 원은 하나의 도메인을 나타내고
G_ij 는 도메인 i에서 도메인 j로 변환하는 하나의 신경망을 나타낸다.
이런 모델에서 각각의 생성망 G는 전체 데이터 셋을 학습하기는 하지만 특정 도메인에 대해서만 학습하기 때문에 연산력 측면에서 낭비가 발생한다.
그리고 특정 도메인만 특화해서 학습하기 때문에 일반적인 정보를 얻지 못해서 생성하는 이미지의 질적 차이가 존재한다.
<br><br>
[그림2]의 오른쪽 모델인 StarGAN은 하나의 신경망을 이용해서 많은 도메인으로 변환하기 때문에 일반적인 지식을 학습하여 더 높은 퀄리티의 이미지를 생성한다.
또한, 연산력을 비용으로 생각한다면 매우 경제적인 모델이다. 지금부터는 StarGAN의 아키텍처와 손실 함수에 대해 알아 볼 것이다.
<br><br>

<img src="/img/starGAN_6.png" width="900" height="500"> <br>
 먼저 기존 GAN에서는 잠재 변수 z를 입력값으로 받는 반면, StarGAN에서는 변환하고자 하는 도메인 정보(c)와 원본 이미지(x)를 입력값으로 받는다.<br>
 원본 이미지를 입력값으로 받는 건 변분 오토 인코더 (VAE)가 사용된 UNIT [3]모델에서 아이디어를 차용했다.<br>
 그리고 판별기는 원본 이미지의 Real/Fake여부에 더해서 특정 도메인 정보까지 맞추는 걸 목표로 한다.<br>
<br>

<img src="/img/starGAN_7.png" width="900" height="500"> <br>
Stargan 손실함수의 목적은 3가지가 있다.<br>
하나의 손실 함수를 통해 많은 도메인을 학습할 수 있어야 한다.<br>
도메인을 학습하면서도 이미지의 퀄리티는 잃지 않아야 한다.<br>
원본 이미지와 타겟 도메인이 주어지면 원본 이미지를 타겟 도메인으로 변환 할 수 있어야 한다.<br>
<br>
오른쪽 적대적 신경망의 판별자 손실함수(L_d)와 생성자 손실함수(L_g)를 정의하기에 앞서 4개의 손실 함수를 정의할 필요가 있다.<br>
<br>

StarGAN의 손실 함수는 기존 적대적 생성망의 손실 함수를 그대로 사용하고 몇 가지 계산을 추가하였다.<br>
StarGAN에서는 아래 네 가지 함수를 먼저 정의하고 이를 조합하여 L_D와 L_G 함수를 정의한다.<br>
Adversarial Loss GAN의 MinMax-Game과 동일하게 정의한다. 한 가지 다른 점은 G의 입력 값으로 latent space를 이용하지 않고 원본 이미지인 x를 그대로 사용한다.<br>
Domain Classification Loss at Discriminator Discriminator는 주어진 이미지가 원본인지 가짜인지 구분하는데 더해 원본 이미지의 도메인까지 예측한다.<br>
Domain Classification Loss at Generator Generator 또한 도메인을 학습해야 한다.<br>
2번은 원본 이미지가 도메인 c’로 잘 분류하게끔 학습 시킨다면, 3번은 생성기가 생성된 이미지가 타겟 도메인 c로 잘 분류되게끔 학습 시키는데 목적을 둔다.<br>
Reconstruction Loss 이미지의 퀄리티를 지키기 위해 사용한다.<br><br>


<img src="/img/starGAN_8.png" width="900" height="400"> <br>
StarGAN의 판별기/생성기의 최종 손실 함수<br>
λ값은 하이퍼 파라미터 이다. 논문에서는 λ_cls = 1, λ_rec = 10으로 해서 Reconstruction Loss에 더 높은 패널티를 부여해서 사용한다.<br>
위의 손실함수들을 조합하면 전 페이지의 손실함수와 생성자 손실함수가 만들어집니다.<br>
(람다는 하이퍼파라미터 :  λ_cls = 1, λ_rec = 10으로 해서 Reconstruction Loss에 더 높은 패널티를 부여해서 사용한다.)<br>
<br>

<img src="/img/starGAN_9.png" width="900" height="350"> <br>
StarGAN 논문에서는 CelebA[5]와 RaFD[6] 데이터 셋을 하나의 모델에서 학습한 결과를 보여준다.
CelebA는 사람 이미지를 헤어 스타일이나, 얼굴형 등 40여개의 특징으로 분류하였고, RaFD는 8개의 표정으로 사람 사진을 분류한 데이터 셋이다.
이 두개의 데이터 셋은 각각 다루는 도메인이 다루기 때문에 이를 통합해서 학습하려면 몇 가지 전략이 필요하다.
<br><br>

Mask Vector사용 : 두 개의 데이터 셋의 Class를 통합하여 학습하기 위해 두 가지 클래스를 모두 만족하는 one-hot vector를 만들어서 사용하는데 이를 Mask Vector 라고 부른다.
<br><br>

에폭수 조정 학습 : CelebA는 데이터 셋이 200,000개 이상인 반면 RaFD는 500여개 밖에 안되기 때문에 그냥 배치로 학습하면 CelebA 데이터에 크게 편향될 수 있다.
그래서 여기에서는 CelebA는 10 epoch로 학습할 때 RaFD는 100 epoch로 학습하는 방법으로 두 데이터 셋을 균등하게 학습한다.
<br><br>

<img src="/img/starGAN_10.png" width="900" height="500"> <br>
DIAT 모델인 경우에는 3개의 도메인으로 변환할 때 이미지가 뭉개지는 것이보인다.<br>
StarGAN 논문의 novelty를 다시 한 번 떠올릴 필요가 있다.<br>
다른 모델은 k(k-1)개의 신경망을 사용하지만, StarGAN은 단일 신경망(Unified Model)으로 기존 모델보다 더 나은 결과를 보여준다.<br>
<br>


<img src="/img/starGAN_11.png" width="900" height="550"> <br>
Facial expression에서 단일 label을 구현할 때도, realistic한 결과를 낼 수 있다는 것을 보여준다.<br>
또한 StarGAN으로 생성된 이미지의 classification loss는 기존의 모델(DIAT, cycleGAN, IcGAN)보다 classification error가 더 적을 뿐만 아니라<br>
parameter의 수도 적다는 것을 보여주었다.<br>


