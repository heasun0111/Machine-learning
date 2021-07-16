## styleGAN
<br>

<img src="/img/styleGAN_1.png" width="900" height="500"> <br>
오늘은 이미지 생성 네트워크에서 뛰어난 성능을 보인 Style GAN에 대해 발표하겠습니다.
<br> <br>

<img src="/img/styleGAN_2.png" width="900" height="500"> <br>
styleGAN은 PGGAN 구조에서 Style transfer 개념을 적용하여 generator 아키텍쳐를 재구성 한 논문입니다. 그로 인하여 PGGAN에서 불가능 했던 style을 scale-specific control이 가능하게 되었습니다.<br>
<br>
기존에 알려진 style transfer라는 이미 알려진 분야의 아이디어를 이용해서 이미지를 구성하는 다양한 특징들에 대해서 컨트롤 할 수 있는 방향으로 이미지를 생성하는 방법을 제안합니다.<br>
<br>
예를들면 1번 스타일 -> 인종, 2번 스타일 -> 포즈, 3번 스타일->얼굴 등 <br>
각기 분리된 특징들을 컨트롤할 수 있도록 수행하는데<br>
우리가 원하는 세부적인 특징들만 바꾸는 방식으로 다양한 이미지를 만드는 생성자로써 더욱 이상적인 속성을 가질 수 있도록 합니다.<br>
<br>

<img src="/img/styleGAN_3.png" width="900" height="500"> <br>
stochastic variation은 확률적인 다양성을 의미하는데 어떤 사람(모델)을 구성하는 다양한 주요 피쳐들을 구성한 상태에서 확률적인 다양성이 매번 바뀔 수 있습니다.<br>
실제로 논문에서는 컨볼루션 레이어를 거치면서 노이즈 벡터를 입력해서 하나의 이미지가 확률적으로 다양한 방식으로 바뀔 수 있도록 합니다.<br>
<br>

<img src="/img/styleGAN_4.png" width="900" height="500"> <br>
본 네트워크에서는 학습된 하나의 상수로부터 출발해서 여러 개의 블록들을 거쳐가면서 해상도가 점점 커지게 되고 채널은 감소하게 되고 마지막 rgb 칼라로 바뀌면서 결과 이미지를 만듭니다.<br>
이때, 각각 블락 안에 포함되고 있는 컨볼루션 레이어를 거칠 때 마다 스타일 정보를 입력 받도록 만들어서 많은 스타일 정보가 반영될 수 있도록 만듭니다.<br>
또한 stochastic variation도 컨트롤이 가능하고, 이러한 결과는 Discriminator가 아닌 채널을 바꿔서 얻어낸 결과입니다.<br>
<br>

<img src="/img/styleGAN_5.png" width="900" height="500"> <br>
논문에서 제안한 기존의 생성자 아키텍쳐와 다른 점에 대해서 설명하겠습니다.<br>
<br>
본 네트워크에서는 별도로 스타일 정보를 레이어로 거칠 때마다 따로 넣어주기 때문에, 맨 처음부터 latent code로부터 출발하지 않아도 됩니다.<br>
<br>
따라서 본 메소드에서는 하나의 학습된 상수를 그 자체로서 생성자에 입력으로 넣어줄 수 있도록하고 그러한 학습된 상수가 컨볼루션 레이어를 거치는 과정에서 스타일에 대한 정보를 입력 받아 다양한 이미지가 생성되도록 만들었습니다.<br>
<br>
일반적인 네트워크와 다른 점은 생성자의 입력으로 들어가는 내용이 일반적인 laten벡터가 아니라 학습된 하나의 상수 이미지로 입력됩니다.<br> <br>


<img src="/img/styleGAN_6.png" width="900" height="500"> <br>
StyleGAN구조를 설명하기 앞서 Progressive Growing GAN구조에 대해서 설명하겠습니다.<br>
다음 사진은 Progressive Growing GAN에서 제안된 고행도 이미지를 생성하는 방법입니다. 간단하게 이야기 하자면 '저해상도 이미지의 생성으로부터 시작해 점차 고해상도용의 Generator, Discriminator을 추가하여 나감으로써 고해상도 이미지를 생성하는 방법’이다.<br>
4x4의 이미지를 생성한 것으로 부터 시작하여 조금씩 해상도를 높여 최종적으로 1024x1024의 고행도 이미지를 생성해내고 있다. 해상도를 높이는 네트워크를 추가하여도, 저해상도 이미지를 생성하는 G와 판별하는 D는 파라미터가 고정하지 않고 학습을 계속하는 것이 특징이다.<br>
그러나 PGGAN에서 style들을 변형시킬 때 Generator에 latent vector z가 바로 입력되기 때문에 entangle(서로 얽혀 있는 상태여서 특징 구분이 어려운 상태. 즉, 각 특징들이 서로 얽혀 있어서 구분이 안됨)하게 되어서 불가능 하다는 단점이 있었습니다.<br>
이때, Style Gan은 각각 다른 style을 여러 scale에 넣어서 학습 시키는 방법을 고안했습니다.<br> <br>


<img src="/img/styleGAN_7.png" width="900" height="500"> <br>
AdaIN 에서 평균값을 빼고 표준편차로 나누어주는 것은 일반적인 정규화고,(컨텐드 이미지) 추가적으로 스케일링과 바이어스를 적용함으로써 피쳐 스케일링에서의 statistic을 변경할 수 있다.(y는 스타일 이미지)<br>
X는 고정된 상수값에서 출발한 아다 레이어를 거치기 전 값, 아다레이어를 거쳐서 스타일 정보가 바뀌는 내용이 y에 의해서 컨트롤 된다.<br>


<img src="/img/styleGAN_8.png" width="900" height="500"> <br>
왼쪽 일반적인 제네레이터로 레이턴트 벡터 Z는 특정한 디스트리뷰션에서 바로 샘플링을 해서 바로 입력으로 들어가지만<br>
오른쪽 style-based generator은 레이턴트 벡터 Z를 뽑아내서 하나의 매핑 네트워크를 거쳐서 w벡터로 바꾼 뒤에 w벡터가 입력으로 들어갈 수 있는 형태로 바꾼다. 이러한 w벡터는 각각의 블록마다 두 번씩 별도의 transformation을 거친 뒤에 입력되도록 만든다.<br>
latent variable 기반의 생성 모델은 가우시안 분포 형태의 random noise을 입력으로 넣어주는 특징을 갖고 있어 StyleGAN은 학습 데이터셋이 어떤 분포를 갖고 있을지 모르니 GAN에 z을 바로 넣어주지 말고 학습 데이터셋과 비슷한 확률 분포를 갖도록 non-linear하게 mapping을 우선적으로 하고 mapping된 z을 이용하면 좀 더 학습하기에 쉽지 않을까? 하는 아이디어로 그림 3처럼 Mapping Network을 사용해 mapping된 W을 각 scale에 입력으로 넣어서 학습을 시키게 됩니다.<br><br>
또한 스토캐스틱 베리에이션을 처리할 수 있도록 별도의 노이즈 벡터 또한 입력으로 받는다.<br>
그리고 여러 개의 블록을 거쳐서 점차 고해상도 이미지를 가지도록 한다.<br> <br>


<img src="/img/styleGAN_9.png" width="900" height="500"> <br>
표는 논문에서 제안한 메소드를 적용해서 얼마나 성능이 좋아졌는지를 보여주는데, 전통적인 생성자 퀄리티를 측정하는 FID방식을 사용하였습니다.<br>
<br>
여기서 두 데이터 셋에 대해서 측정했는데<br>
CelebA-HQ는 얼굴 데이터 셋의 고해상도 버전, FFHQ는 논문에서 새롭게 배포한 데이터 셋을 의미합니다.<br>
<br>
베이스라인 프로그래시브 GAN에서 점점 기능을 추가하였는데,<br>
A는 베이스라인 프로그래시브 GAN을 적용한 결과, B는 튜닝만 적용, C는 매핑과 스타일을 추가하고<br>
D는 트래디셔널한 인풋을 제거하고, E는 노이즈 인풋을 추가하고, F는 정규화를 믹싱 했습니다.<br>
여기서 F는 논문에서 제안한 모든 기법을 다 적용한 것입니다.<br>
<br>
이 기능을 추가할 수록 두 데이터 셋에 대해서 성능이 많이 개선되는 것을 확인할 수 있었습니다.<br>
<br>

<img src="/img/styleGAN_10.png" width="900" height="500"> <br>
Figure2는 styleGan을 이용해서 만들어낸 다양한 이미지이다. 여기서 높은 퀄리티의 이미지를 만들기 위해서 truncation trick을 사용했는데, truncation trick란 하나의 GAN모델을 학습 완료한 후에 샘플링을 수행할 때 그럴싸한 이미지가 잘 나올 수 있도록 더욱 평균에 가까운 이미지가 나올 수 있게 w latent 벡터에 truncation을 적용한 것임을 설명했습니다.<br>
<br>
2.2. Prior art 에서는 이전까지의 연구 결과 중에서 본 논문과 유사한 테크닉을 적용한 것들이 있는지를 언급<br>
그 전 많은 연구들은 어떻게하면 판별자 파트를 더욱 개선할 수 있는지에 대해서 초점을 맞췄지만, style GAN은 더욱 좋은 제너레이터를 만드는데 초점을 맞췄다.<br>
<br>

<img src="/img/styleGAN_11.png" width="900" height="500"> <br>
THE STYLE-BASED GENERATOR 의 특징은 제안한 generator architectur를 통해 스타일에 대한 Scale-specific modification)이 가능하다는 점입니다.<br>
다시말해  style의 특정 subset을 바꾸어 이미지의 특정 양상을 바꿀 수 있습니다.<br>
이러한 동작이 가능한 이유는 adain 연산을 통해서 가능하다고 합니다. 수식을 통해 표준편차로 나누고 평균을 뺀 과정을 통해 정규화가 이루어 짐을 알 수 있습니다.<br>
이는 instance에 대한 normalization이라고 볼 수 있다고 합니다. 때문에 이러한 adain연산을 각 layer마다 추가해 주면 정규화가 한번씩 진행되면서 normalization이 진행되고 이는 style이 각각의 scale에서만 영향을 끼칠 수 있도록 분리 해주는 효과를 갖는다고 합니다.
<br>
[참고 링크1](https://blog.promedius.ai/stylegan_2/)
[참고 링크2](https://www.youtube.com/watch?v=HXgfw3Z5zRo&t=3284s)<br> <br>


<img src="/img/styleGAN_12.png" width="900" height="500"> <br>
스타일에 대한 Scale-specific modification(스케일별 수정)이 가능하도록 해주는 이유엔 multi latent의 사용도 있다고 합니다.<br>
Multil atent를 사용하지 않고 singlelatent를 사용한다면 style correlation이 발생 할 수 있습니다. Style correlation은 학습 데이터셋에서 극히 드물게 대머리인 사람은 항상 선글라스를 착용하고 있는 데이터가 있을 때 .GAN은 학습 데이터의 분포와 비슷한 분포를 갖도록 학습하다보니 Generator에서 선글라스 == 대머리 라는 correlation이 발생하여 무조건 대머리인 사람은 선글라스를 착용한 상태로 생성하는 overfitting이 일어날 가능성이 많아집니다.<br>
<br>
style correlation이 발생하지 않고 학습을 진행하기 위해서는 합성네트워크(synthesis network)를 학습 시킬 때 하나의 Latent vector z에서 나온 W을 이용해서 학습하는 것이 아니라 latent space에서 뽑은 z1​, z2​, ... zn​을 mapping network f에 통과시켜서 w1​, w2​, ... wn​을 만듭니다. 이후 왼쪽 그림과 같이 50:50 비율로 나눠서 각 layer에 적용해도 되고 다른 비율로 상황에 따라 적용하면 됩니다.<br>
이런 style mixing 방법을 사용하면 다양한 style이 섞여서 합성네트워크(synthesis network)학습이 됩니다. 따라서 Generator을 이용해서 이미지 생성을 해보면 style correlation이 거의 없으며 입력할 때 AdaIN을 통해 입력을 하게 되므로 regularization 효과도 볼 수 있게 됩니다.<br>
<br>
오른쪽 그림은 Multi latent z 의 효과로 style correlate되는 현상을 방지해서 각 layer에 해당하는 style들이 잘 구분되면서 적용되는 것을 볼 수 있습니다. 두개의 latent 벡터 A와 B가 있을 때 각각의 layer에서 latent 벡터를 섞어서 출력한 결과입니다. 믹싱 방식은 소스 A의 정보를 소스 b에서 가져와 스타일 믹싱을 수행한 결과 입니다.<br>
<br>
가장 위쪽의 경우는 소스B의 coarse style에 해당하는 정보가 소스A와 섞여서 생성된 이미지입니다.<br>
Coarse style은세밀하진 않지만 매우 포괄적인 스타일 정보를 의미하며 synthesis network에서 앞쪽 부분에 해당하므로 이미지에 큰 변화를 일으킬 수 있는 정보들을 포함하고 있다고 합니다. 따라서 B의 포즈, 머리스타일, 얼굴모양, 안경유무가 소스 A와 섞인 이미지들이 생성됩니다.[머리, 눈, 조명등의 색깔은 (from source A)]
다음 5~8번째벡터에 해당하는 Middle style은 좀더 얼굴의 작은 특징과 머리스타일, 눈의 감고 뜬 상태들을 소스B에서 얻어와 나머지 소스A와 섞인 이미지들이 생성됩니다.[ 자세, 얼굴 모양, 안경(from source A) 보존]<br>
마지막으로 Fine 스타일은 synthesis networ에서 뒤쪽 부분에 해당하는 정보들이며 작은 정보들의 변형합니다. 이미지 색상, 미세구조등의 정보가 소스 B에서 얻어지고 소스A에 섞인는 것을 확인 할 수 있습니다.<br>
<br>


<img src="/img/styleGAN_13.png" width="900" height="500"> <br>
논문에서 소개한 generator의 또 다른 특징으로는 확률적 다양성이 표현 될 수 있다고 합니다.<br>
노이즈 벡터에 따라 머리카락, 주근깨, 여드름, 피부 모공 등의 정보가 다양하게 표현 될 수 있다고 합니다. 왼쪽 그림은 스타일은 유지한 상태로 노이즈 벡터만 바꾸어서 개별적인 부분을 만들어낸 결과입니다.<br>
두 사진의 (a)의 스타일이 유지된 상태로 (b) 사진에서 노이즈 벡터의 확률적 정보에 따라 각기 미세한 차이를 갖는 머리카락의 상태가 표현되는 것을 확인 할 수 있습니다. (c) 사진은 (b) 사진과 같이 노이즈 벡터의 확률적 정보에 따라 변화는 부분이 머리카락과 같은 미세한 부분이라는 것을 확인해주는 사진입니다.<br>
<br>
오른 쪽의 사진은 각 layer에 대해서 노이즈의 인풋을 넣는 방식에 따른 차이입니다. (a)는 기본 stylegan이고 (b)는 노이즈를 적용하지 않을 때의 사진으로 (a)에 비해 (b)의 머리카락의 detail이 떨어지는 것을 확인 할 수 있습니다. (c)는 fine layer에서(d)는 coarse layer에 노이즈가 적용된 예시 입니다. Fine layer에 적용된 경우와 coarse layer에 노이즈가 적용된 사진이 각각 다른 정도의 detail이 표현 됨을 확인 할 수 있습니다.<br>
<br>
대신 노이즈는 high-level aspect보단 보다 미세한 것에 대한 표현에 사용됩니다.<br>
<br>
마지막으로 generator의 특징 정리로 전역적 효과와 확률적 효과의 분리입니다. 얼굴 포즈, 조명 배경 등과 같은 전역적인 효과는 W 벡터를 통해 변형이 가능하고 머리카락, 수염과 같은 상대적으로 확률적 다양성 측면에 해당하는 영역은 노이즈를 통해 표현되기 때문에 노이즈로 전역적인 특징인 얼굴포즈, 조명,등과 같은 정보는 통제하기 어렵다고 합니다.<br>
<br>


<img src="/img/styleGAN_14.png" width="900" height="500"> <br>
다음으로 본 논문에선 disentanglement의 정도를 측정하는 방법을 소개하고 있습니다. <br>
먼저 일반적인 의미의 Disentanglement는 latent 공간이 여러 개의 선형적인 서브스페이스로 구성 되어있을 때를 의미합니다. 간단히 말해서 선형적인 서브스페이스로 구성되었기 때문에 서로 다른 두 벡터를 latent 공간에서 뽑아 interpolation 시킨다면 자연스러운 모습으로 이미지의 변화를 보여야 합니다. <br> <br>

논문에서 소개하는 방법은 perceptual path length와 linear separability 두가지 방법이 있습니다. perceptual path length는 앞서 말한대로 자연스럽게 이미지가 변화는 정도를 측정하기위해 왼쪽과 같은 수식을 이용하는 방식입니다. Z1과 z2가 interpolation되는 벡터이고 얼마만큼의 비율로 interpolation을 수행할지를 t로 설정하고(0부터 1사이의 값해당) 매우 작은 상수 입실론을 주어 작은 시간동안 변화된 결과사이의 perceptual 거리를 판단하여 feature사이의 변화를 파악 할 수 있습니다. 반면에 w벡터끼리의 interpolation을 수행할 땐 선형보간법을 사용한다고 합니다. 일반적으로 w벡터에서의 separability를 계산했을 때 낮은 값으로 좋은 성능의 interpolation을 보인다고 합니다. <br> <br>

두번째 방법은 선형적인 분리정도를 파악합니다. 선형 분류기를 학습한뒤 특징을 구분하는 정도의 엔트로피를 계산하여 latent 벡터의 선형적인 공간에서의 존재 정도를 파악하는 방식이라고 합니다. <br> <br>



<img src="/img/styleGAN_15.png" width="900" height="500"> <br>
다음 표를 통해서 앞서 말한 perceptual path length를 계산할 때 w공간에서 separability측정이 z공간에 비해 낮은 값으로 잘 나오는 것을 볼 수 있습니다. 낮은 separability값은 interpolation과정에서 좀더 자연스럽게 이미지가 변한다는 것을 의미합니다.<br>
마지막으로 결론을 정리하자면 High resolution Image Generation하는 GAN 논문과 비교했을 때 StyleGAN은 전통적인 방식과 다르게 각 style을 담당하는 layer에 style w를 AdaIN과 함께 입력해주는 방법으로 학습하고 원하는 style로 변형을 시켜줄 수 있는 모델이라고 할 수 있습니다. 특히 다른 모델에 비해 높은 성능 향상을 보여주었으며 각각의 high-level에 해당하는 특징들이 잘 분리되고 확률적 효과에 대한 표현이 가능하다는 장점이 있습니다. 각각의 벡터들이 선형적으로 latent 벡터상에서 분류되는 것 또한 측정방식을 제시하며 확인 할 수 있었습니다.<br>


