## DCGAN
<br>

<img src="/img/DCGAN_0.png" width="900" height="500"> <br>
GAN 논문이 수식을 통해 이론제시를 했다면 DCGAN 논문은 복잡한 수식보단 효과적인 학습모델구조를 소개하는 형식의 논문인 것 같다.<br>
초록 1 .기존 컨볼루션 네트워크의 지도학습은 컴퓨터 비전 응용에 효과적이었으며 여러 성과를 통해 입증되었다. 하지만 비지도학습에선 뚜렷한 성과로 주목받지 못했다.<br>
해당 논문에선 DCGAN을 통해서 비지도학습에서의 컨볼루션 네트워크의 효과를 소개하며 다양한 데이터셋을 통한 증거들을 함께 소개한다.<br>
초록 2. 증거에는 deep convolutional adversarial pair가 generator와 discriminator에서 객체와 배경을 계측적으로 표현하도록 학습을 잘한다는 것을 소개한다.
<br>

<img src="/img/DCGAN_1.png" width="900" height="500"> <br>
introduction에선 이 논문에서 GAN을 이용한 이미지 표현(비지도학습)을 소개하고, generator와 discriminator를 지도학습에서 특징추출기로 사용할 수 있도록 한다는 내용<br>
(이는 GAN이 최대우도법에 대한 대안적인 기술이 되며 학습과정과 huristic(어림직작)한 손실함수의 부족에도 학습이 가능하도록 해준다.)<br>
또한 마지막으로 GAN의 학습과정을 시각화 하여 내부 학습 과정을 보여준다.<br>
본 논문이 기여한 바는 GAN의 안정화된 학습을 위한 arichitecture 제안, 다른 비지도 학습 알고리즘과 견줄만한 discriminator 학습결과, 
필터의 시각화와 특징추출 능력제시 (특정 객체들을 학습한 필터들), 생성자의 벡터 산술 특성 확인 들이 있다.<br>
특히 벡터 산술 특성을 통해 생성된 샘플들에 대한 시맨틱 퀄리티(의미적 특성) 조작이 가능하도록 해준다.<br>
<br>
기존 GAN의 문제점 : Minimax 혹은 saddle problem 으로 인한 GAN 학습의 불안정성 -> 이론과 달리 실제에선 수렴이 잘 안되는 불안정한 구조적 단점이 존재
<br>


<img src="/img/DCGAN_2.png" width="900" height="500"> <br>
관련 연구들<br>
1.비지도 표현 학습의 전통적인 방식은 데이터 클러스터링이며 클러스트링을 바탕으로 분류 스코어를 향상시켜왔다.(k-means와 같은 클러스터링 기법)<br>
또 다른 방식으론 이미지를 컴팩트 코드로 인코딩하고 최대한 정확하게 재구성하기위해 코드를 디코딩하는 auto-encoder기법이 있다.<br>
<br>
2.이미지 생성모델은Non-parametric과 parametric 카테고리로 구분되어진다.<br>
Non-parametric 모델은 이미 존재하는 이미지의 데이터들 이며 texture synthesis, super-resolution, in-painting 같은 분야에서 사용된다.<br>
(Texture synthesis - 구조적 콘텐츠를 활용하여 작은 디지털 샘플 이미지에서 큰 디지털 이미지를 알고리즘 적으로 구성하는 프로세스<br>
Super resolution - 이미징 시스템의 해상도를 향상시키는 기술<br>
Inpainting - 인 페인팅은 아트 워크의 손상, 품질 저하 또는 누락 된 부분을 채워 완전한 이미지를 제공하는 보존 프로세스)<br>
Parametric 모델은 mnist또는 texture synthesis 같은 분야에서 사용되었지만 자연적인 이미지 생성과 관련된 연구는 흔치 않다.<br>
접근법엔 variational sampling , iterative forward diffusion process와 GAN이 있지만 blur가 발생하거나 noise가 섞이고 이해못할 결과를 내곤한다. 또한 해당 연구들은 generator를 지도학습에는 사용하지 않고 있다.<br>
<br>
3.CNN의 부족한 점 중 하나는 학습의 과정이 보이지 않는 black box 방식이라는 것이다.<br>
네트워크가 어떻게 학습을 하는지 이해하기 위해 deconvolution과 ,활성화 함수의 최대값 필터링 방법들이 사용되며 입력 값에 gradient descent를 적용하여서 이미지를 검사하기도 한다.<br>

<img src="/img/DCGAN_3.png" width="900" height="500"> <br>
DCGAN 설계 가이드라인<br>
1.Pooling layer와 strided convolution(stride = 2) 둘다 이미지를 downsampling하는데 사용 될 수 있다.<br>
차이는 풀링레이어는 학습할 파라미터가 없는 대신 strided convoulution 방식의 downsampling엔 학습 파라미터가 있다는 점이다.<br>

2.convolutional feature의 최상단 전결합층을 제거한다. 논문에 따르면 이게 하나의 추세라고 한다.<br>
어떤 이미지 분류의 sota급 성능의 모델역시 해당방식이 적용됨을 확인 할 수 있다고 논문에서 언급..<br>
(여기에 가장 강력한 예시로는 global average pooling을 이용한 Mordvintsev et al. 논문의 sota급 image classification 모델이 있다.)<br>

3.학습 안정화를 위한 batch normalization의 사용<br>
(잘못된 파라미터 초기화에 따른 문제를 완화하고 깊은 모델들의 gradient 플로우에 효과가 있다. 실제로 generator가 모든 샘플들이 한 지점으로 무너지는 것을 막아주는 효과에 중요하다.<br>
하지만 batch normalization를 모든 layer에 직접적으로 적용하면 샘플진동과 모델 불안정성을 초래 할 수 있기에 generator의 output layer와 discriminator의 input layer엔 적용해주지 않는다.)<br>
((이유를 추측해보자면 아무래도 Generator가 생성하는 이미지가 BN을 지나면서 다시 normalize 되면 아무래도 실제 이미지와는 값의 범위가 다를 수 밖에 없으니 그런 문제가 생기지 않을까 싶습니다.<br>
저자들은 BN을 넣으면 GAN의 고질적 문제 중 하나인 Mode collapsing 문제를 어느 정도 완화해준다고 하는데 이 부분은 사실 아직 해결되었다고 보기는 어렵습니다. NIPS 2016 Tutorial에서도 언급되었습니다만 BN이 거꾸로 특정 문제를 일으키기도 하는 경우가 있습니다.))
<br>
<br>
4.Relu의 사용. Generator에선 output을 제외하고 모두 relu를 사용하한다. outpu엔 tanh를 사용한다.<br>
Discriminator에선 모든 층에 leaky relu를 사용한다.<br> <br>

[참고 출처](http://jaejunyoo.blogspot.com/2017/02/deep-convolutional-gan-dcgan-1.html)


<img src="/img/DCGAN_4.png" width="900" height="500"> <br>
뒤에 나올 LSUN 데이터셋에 적용된 DCGAN의 구조. 가이드라인에 맞춰 전결합층과 풀링층은 사용되지 않았다.<br>
100 차원 uniform 분포를 입력으로 하는 Z가 convolution의 feature map들에 투영된다. 64x64 픽셀 이미지로 변형될때까지 4개의 fractionally – strided convolution을 통과된다.<br>
Fractionally –strided convolution은 transpose convolution이라고도 불리며 일반적으로 kernel들의 연산과 달리 upsampling이 일어나 압축된 정보를 복원하는 곳에 사용 되기도 합니다. Stride가 분수형태라는 특징 또한 가지고 있다고 합니다.<br> <br>

[참고 출처](https://eehoeskrap.tistory.com/431)


<img src="/img/DCGAN_5.png" width="900" height="500"> <br>
4.DETAILS OF ADVERSARIAL TRAINING에서는 세 가지 데이터 세트에 대해 DCGAN을 교육했습니다. 적용한 데이터 셋에 대해서 설명하자면,<br>
4.1 LSUN은 침실 사진들이 모여있는 데이터셋이고,.<br>
4.1.1 DEDUPLICATION은 memorization 문제를 피하기 위해서 이미지들을 Autoencoder를 이용하여 짧은 code로 변환할 수 있도록 학습한 후 이 code들에서 가까운 값들을 제거했다.<br>
4.2 FACES는 web에서 random image들을 받아와서 근대에 태어난 사람 얼굴 이미지 데이터고,<br>
4.3 IMAGENET-1K 는 DCGAN으로 학습시킨 후 disriminator의 feature를 가져다가 K-means classification을 하니 state-of-art 결과와 비등한 정도의 정확도를 보였습니다.<br>
이는  특징 추출(Feature Extractor)로써의 역할을 DCGAN이 잘 해줄 수 있다는 점을 의미한다.<br>
<br>


<img src="/img/DCGAN_6.png" width="900" height="500">
4.1 LSUN에서는 과적합 및 메모라이제이션 문제에 대한 증명을 했습니다.<br>
한번의 에폭을 학습해서 나온 샘플들이 실시간 학습을 모방한다는 것을 보였고, 게다가 수렴 이후의 샘플들은 간단한 오버피팅/메모라이징 학습 예시들을 통해서 샘플을 단순히 교육 예제를 과적합/암기하여 모델이 고품질 샘플을 생산하지 않음을 입증하였습니다.<br> <br> <br>


<img src="/img/DCGAN_7.png" width="900" height="500"> <br>
앞서 얘기했던 1번 문제(Memorization)가 일어나지 않았다는 것을 검증하기 위해서 네트워크에 학습 데이터(training data)를 한 번만 보여준 결과를 확인해줍니다.<br>
한 번 밖에 안 보여줬기 때문에 학습이 아무리 빠르게 되었더라도 과학습(overfitting)이 일어났을리는 없을테죠, Random initialization 된 filter들이 조금 조정될 수는 있지만 과학습이 일어나기엔 너무 이릅니다.<br>
아래 그림은 DCGAN이 이런 경우에도 첫 epoch만에 문제가 없이(이미지를 외워서 그대로 내보내는 것이 아닌) 이미지를 스스로 잘 만들어내었다는 것을 보여줍니다.<br> <br>


<img src="/img/DCGAN_8.png" width="900" height="500"> <br>
underfitting이 일어났다고 말하면서<br>
그 증거로 침대의 머리 부분에 약간의 노이즈 질감이 반복되는 것을 볼 수 있다.<br>
-> 과적합에 대한 문제 검증 <br> <br>


<img src="/img/DCGAN_9.png" width="900" height="500">
5. EMPIRICAL에서는 EMPIRICAL VALIDATION OF DCGANS CAPABILITIES 성능 입증하기위한 시도에 대한 내용입니다.<br> <br>


<img src="/img/DCGAN_10.png" width="900" height="500"> <br>
비지도 표현 학습 알고리즘의 퀄리티를 평가하는 하나의 일반적인 방법은<br>
지도 학습 데이터셋의 특징 추출기로써 적용하고 이러한 특징들에 맞는 선형 모델의 성능을 평가하는 것입니다.<br><br>

이 도표를 통해서 DCGAN은 82.8% 정확도를 보였고, 모든 K-mean 기반의 방법의 성능을 넘었다.<br>

판별자는 K-means 기반의 방법과 비교하여 더 적은 피쳐 맵을 갖고 있지만, 4x4 spatial location의 많은 레이어들 때문에 총 피쳐 벡터 사이즈는 더 큰 결과를 얻었다.<br>
DCGAN의 성능은 여전히 Prespar CNN(Dosovitskiy et al., 2015)보다 낮으며, 이는 비지도 방식으로 정상적인 차별적 CNN을 훈련시켜 소스 데이터 세트에서 특별히 선택되고 공격적으로 증강된 예시 샘플을 구별하는 기술입니다.<br> 판별자의 표현을 미세 조정함으로써 더 많은 개선이 이루어질 수 있지만, 아직 시도하지 않았습니다.<br>


<img src="/img/DCGAN_11.png" width="900" height="500"> <br>
1000개의 uniform 한 클래스 분포의 트레이닝 예시들은 랜덤하게 선택됬고 CIFAR-10에서 사용한 파이프라인과 같은 feature extraction에 regularized linear L2-SVM 분류기를 사용하여 학습하는데 사용되었다.<br>
이것은 22.48% 테스트 오류를 보였고, 레이블이 없는 데이터를 이점으로 사용하도록 설계된 CNN의 변형들보다 성능이 더 높다.<br>
추가적으로, 우리는 DCGAN에 사용된 CNN 아키텍쳐가 같은 구조에 같은 데이터에 64번의 하이퍼 파라미터 랜덤 탐색 시토를 통해 모델을 최적화하면서 순수하게 지도학습 CNN에 학습되는데 사용된다면 모델의 성능에 중요한 기여 요소가 아니라는 것을 검증했다. 이것은 28.87%로 더 높은 테스트 에러를 달성했다.
<br><br>


<img src="/img/DCGAN_12.png" width="900" height="500"> <br>
학습된 매니폴드를 걸으면 일반적으로 암기 징후(급격한 전환이 있는 경우)와 공간이 계층적으로 붕괴되는 방식에 대해 알 수 있습니다. 이 잠재 공간에서 걸으면 이미지 생성(예: 추가 및 제거되는 개체)에 대한 의미적 변화가 발생할 경우 모델이 관련되고 흥미로운 표현을 학습했다고 추론할 수 있습니다.<br> <br>

제안한 대로 안내된 역전파를 사용하여, 우리는 판별기에 의해 학습된 기능이 침실이나 창문과 같은 침실의 일반적인 부분에서 활성화된다는 것을 그림 5에 보여줍니다. 비교를 위해, 동일한 그림에서, 우리는 의미론적으로 관련되거나 흥미로운 어떤 것에서도 활성화되지 않는 랜덤하게 초기화된 특징에 대한 기준을 제공합니다. <br> <br>


<img src="/img/DCGAN_13.png" width="900" height="500"> <br>
아주 부드럽게 그림이 바뀌는 것을 확인하실 수 있습니다. 특히 중간에 여섯 번째 줄의 그림들을 보면 벽이었던 곳에서 서서히 커다란 창문이 있는 방으로 바뀌어가는 것을 보실 수 있습니다.
<br> <br>

<img src="/img/DCGAN_14.png" width="900" height="500"> <br>
CNN이 black-box 모델이라 안이 어떻게 돌아가는지 모르고 쓸 뿐이라는 지적을 받았는데,<br>
DCGAN 논문에서는 이 부분을 조금이나마 보여주고자 input까지 backpropagation을 하여 어떤 input이 discriminator가 학습한 feature의 어떤 부분을 active하게 하는지를 보여주고 있습니다.<br><br>

왼쪽 그림과 같이 filter가 무작위 값을 가지고 있는 경우는 딱히 구조를 특정할 수 없지만 학습된 filter들은 나름 DCGAN이 구조의 특징들을 이해하고 구별하여 침대나 창문과 같은 구조 등으로 나누어 역할을 담당하고 있는 것을 확인할 수 있습니다. <br><br>

<img src="/img/DCGAN_15.png" width="900" height="500"> <br>
판별자에 의해 학습된 표현 외에도, 생성자가 학습하는 표현은 무엇인지 의문이 있다.<br>
샘플의 퀄리티는 생성자가 주요한 장면 요소를 위한 침대, 창문, 등불, 문, 그리고 여러가지 잡다한 가구 같은 특정한 오브젝트 표현을 학습한다는 것을 제안한다. 이러한 표현들이 가지는 형태를 살펴보기 위해서, 우리는 생성자로부터 생성된 창문들을 완전하게 제거하는 시도를 하는 실험을 수행했다.<br>
결과는 침실 안에서 창문을 그리는 것을 대부분 하지 않았고, 이것을 다른 오브젝트로 대체한 것을 확인할 수 있다.<br><br>


<img src="/img/DCGAN_16.png" width="900" height="500"> <br>
단어의 표현을 학습하는 것을 평가하는 부분에서는 간단한 산술 연산이 표현 공간 안에서 풍부한 선형 구조를 드러낸다는 것을 증명했다.<br>
하나의 예시는 vector(“King”) - vector(“Man”) + vector(“Woman”) 의 결과는 가장 근접한 이웃의 벡터인 Queen의 결과를 보이는 것을 증명했다.<br> <br>



<img src="/img/DCGAN_17.png" width="900" height="500"> <br>
 우리의 생성자의 Z 표현에도 비슷한 구조를 가지는지 조사했다. 우리는 시각적 컨셉의 예시 샘플의 집합인 Z vector에 대한 비슷한 산술 연산을 수행했다.<br>
 컨셉 당 오직 하나의 샘플을 사용하는 것은 안정적이지 않지만, 3개의 예시에 대한 Z 벡터의 평균은 산술 연산을 의미적으로 따르면서 지속적이고 안정적인 생성을 보여주었다.<br>
 Object manipulation에 대해서, 우리는 얼굴 포즈 또한 Z 공간 안에서 선형적으로 모델 될 수 있다는 것을 증명했다.<br> <br>


<img src="/img/DCGAN_18.png" width="900" height="500"> <br>
해당 프레임워크를 영상과 오디오로 확장시킬 수 있으며 laten space의 특성에 대한 연구를 할 수 도 있을 것이다.


