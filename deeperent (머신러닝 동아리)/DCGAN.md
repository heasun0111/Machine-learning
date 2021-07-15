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


<img src="/img/DCGAN_5.png" width="900" height="500"> <br>



<img src="/img/DCGAN_6.png" width="900" height="500"> <br>



<img src="/img/DCGAN_7.png" width="900" height="500"> <br>



<img src="/img/DCGAN_8.png" width="900" height="500"> <br>



<img src="/img/DCGAN_9.png" width="900" height="500"> <br>



<img src="/img/DCGAN_10.png" width="900" height="500"> <br>



<img src="/img/DCGAN_11.png" width="900" height="500"> <br>



<img src="/img/DCGAN_12.png" width="900" height="500"> <br>



<img src="/img/DCGAN_13.png" width="900" height="500"> <br>



<img src="/img/DCGAN_14.png" width="900" height="500"> <br>



<img src="/img/DCGAN_15.png" width="900" height="500"> <br>



<img src="/img/DCGAN_16.png" width="900" height="500"> <br>



<img src="/img/DCGAN_17.png" width="900" height="500"> <br>



<img src="/img/DCGAN_18.png" width="900" height="500"> <br>
