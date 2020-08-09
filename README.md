# CycleGAN
CycleGAN으로 여자얼굴&lt;->남자얼굴로 바꾸기
## GAN이란?
&nbsp;GAN에는 Generator와 Discriminator가 있는데 Generator는 이미지를 생성하고 Discriminator는 이미지가 진짜 이미지인지 Generator가 생성한 이미지인지 판별한다. 결국 Generator는 Discriminator를 속이기 위해 점점 더 그럴듯 한 이미지를 생성하고 Discriminator는 점점 더 진짜 이미지와 가짜 이미지를 잘 판별한다.
## CycleGAN이란?
&nbsp;CycleGAN은 하나의 이미지 도메인을 다른 이미지의 도메인으로 바꾸는 모델이다. Pix2Pix와 CycleGAN의 차이점은 Pix2Pix는<br>
<img src="https://cdn.discordapp.com/attachments/698770951071989841/698770965986803733/unknown.png" width="40%" height="30%" title="이미지 출처: https://www.tensorflow.org/tutorials/generative/cyclegan" alt="Pix2Pix Dataset"></img> <br>
위와 같이 Input Image와 Target Image가 페어를 이루는 데이터 셋이 필요하지만 CycleGAN은 위와 같이 페어를 이룰 필요가 없다. 이게 무슨 뜻이냐 하면, 만약 오렌지를 사과로 바꾸는 모델을 만들고 싶다면, 사과와 오렌지의 이미지가 1대 1을 이룰 필요가 없이 사과 이미지셋과 오렌지 이미지셋이 있으면 된다는 뜻이다.<br>
### Loss
&nbsp;CycleGAN의 Loss는 Cycle consistency를 고려한다.이는 하나의 도메인에서 다른 도메인으로 바꾼 이미지를 다시 원래 이미지로 되돌렸을 때 진짜 이미지와 얼마나 차이나는지 계산하게 된다. 즉 Generator를 각 도메인마다 G()과 F()로, Discriminator를 D_X()과 D_Y()로 표현했을 때, 아래의 그림과 같이 표현할 수 있다.<br>
<img src="https://cdn.discordapp.com/attachments/698770951071989841/698774474794860604/cycle_loss.png" width="100%" height="100%" title="이미지 출처: https://www.tensorflow.org/tutorials/generative/cyclegan" alt="Cycle Consistency"></img><br>
또 다른 로스는 Identity Loss가 있는데, 이는 바꿔야 할 도메인이 아닌 자신의 도메인의 Generator를 넣었을 때 원래 이미지와 생성될 이미지가 얼마나 차이나는지 계산하는 것이다. 이는 다음과 같이 나타낸다. Identity loss = |G(Y) - Y|+|F(X)-X)| <br>
### Generator
&nbsp;CycleGAN의 Generator는 Resnet이나 U-Net을 이용한다. 이 모델에서는 Resnet을 사용하였다. Resnet을 이용한 Generator는 아래와 같은 그림으로 나타낼 수 있다.
<img src="https://media.discordapp.net/attachments/698770951071989841/698834734796505158/Generator.png?width=1442&height=354" title="이미지 출처: https://hardikbansal.github.io/CycleGANBlog/" alt="resnet generator"></img><br>
Resnet에 대한것은 http://blog.naver.com/laonple/220761052425 를 참고하였다.
### Discriminator
&nbsp;CycleGAN의 Discriminator는 PatchGAN을 이용한다. PatchGAN은 이미지의 전체 영역이 아닌 특정 크기의 patch 단위로 Generator가 생성한 이미지가 진짜인지 가짜인지 판단한다. 이미지로 표현하면 다음과 같다.
<img src="https://cdn.discordapp.com/attachments/698770951071989841/698839530005987359/2.png" title="이미지 출처: https://brstar96.github.io/mldlstudy/what-is-patchgan-D/" alt="PatchGAN" width="50%" height="50%"></img><br>
PatchGAN에 관해 더 자세한 정보를 보고싶다면 https://brstar96.github.io/mldlstudy/what-is-patchgan-D/ 를 참고하면 좋을 것 같다.
### 학습 과정
#### 정의
G: 남자 이미지를 여자 이미지로 변조시키는 Generator<br>
F: 여자 이미지를 남자 이미지로 변조시키는 Generator<br>
D_man: 이미지가 남자인지 판단<br>
D_woman: 이미지가 여자인지 판단<br>
#### 학습 과정
1. G와 F로 남자와 여자 이미지를 변조시킨다.
2. 각 변조된 이미지를 Binary Cross Entropy로 Generator Loss를 구한다.
2. D_man과 D_woman을 진짜 이미지와 가짜 이미지를 각각 판별해 Discriminator의 Loss를 구한다. (즉 D_man(남자 이미지), D_woman(여자 이미지), D_man(G(남자 이미지)), D_woman(F(여자 이미지)) 이다.)
3. 여기서 구한 진짜 이미지와 가짜 이미지의 PatchGAN 행렬을 Binary Cross Entropy로 계산해 Discriminator의 Loss를 구한다.
4. G로 변조시킨 이미지를 F로, F로 변조시킨 이미지를 G로 변조시켜 다른 도메인으로 바꾼 이미지를 다시 원래 도메인의 이미지로 바꾼다.
5. 남자 이미지를 G(F(남자 이미지))와, 여자 이미지를 F(G(여자 이미지))를 MAE로 계산해 Cycle consistency Loss를 구한다.
6. 남자 이미지를 F를, 여자 이미지를 G를 통과시켜 바꾼다.
7. 같은 도메인의 Generator를 통과시켰던 이미지를 진짜 이미지와 MAE로 계산해 Identity Loss를 구한다.
8. Generator Loss와 Cycle consistency Loss, Identity Loss를 더해 Total Generator Loss를 구한다.
9. 구한 Loss들을 바탕으로 G, F, D_man, D_woman을 Optimizer로 학습시킨다.
### 학습 결과
&nbsp;위와 같은 과정으로 200에폭을 학습시킨 결과 다음과 같은 결과물을 얻었다.
<img src="https://cdn.discordapp.com/attachments/698770951071989841/698842619941224448/EvgHyzrup9ru6zrsq77uK6llIWOYsGCBQsWXH2aaMGCBQsW7AEWZ7BgwYIFCxZnsGDBggULFmewYMGCBQtYnMGCBQsWLGBxBgsWL.png" title="여자2남자 학습 결과" alt="여자2남자 학습 결과" width="75%" height="75%"></img><br>
<img src="https://cdn.discordapp.com/attachments/698770951071989841/698843436404703283/mg5StwW2ckAAAAASUVORK5CYII.png" title="남자2여자 학습 결과" alt="남자2여자 학습 결과" width="75%" height="75%"></img><br>
