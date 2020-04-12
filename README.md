# CycleGAN
CycleGAN으로 여자얼굴&lt;->남자얼굴로 바꾸기
### GAN이란?
&nbsp;GAN에는 Generator와 Discriminator가 있는데 Generator는 이미지를 생성하고 Discriminator는 이미지가 진짜 이미지인지 Generator가 생성한 이미지인지 판별한다. 결국 Generator는 Discriminator를 속이기 위해 점점 더 그럴듯 한 이미지를 생성하고 Discriminator는 점점 더 진짜 이미지와 가짜 이미지를 잘 판별한다.
### CycleGAN이란?
&nbsp;CycleGAN은 하나의 이미지 도메인을 다른 이미지의 도메인으로 바꾸는 모델이다. Pix2Pix와 CycleGAN의 차이점은 Pix2Pix는<br>
<img src="https://cdn.discordapp.com/attachments/698770951071989841/698770965986803733/unknown.png" width="40%" height="30%" title="px(픽셀) 크기 설정" alt="Pix2Pix Dataset"></img> <br>
위와 같이 Input Image와 Target Image가 페어를 이루는 데이터 셋이 필요하지만 CycleGAN은 위와 같이 페어를 이룰 필요가 없다. 이게 무슨 뜻이냐 하면, 만약 오렌지를 사과로 바꾸는 모델을 만들고 싶다면, 사과와 오렌지의 이미지가 1대 1을 이룰 필요가 없이 사과 이미지셋과 오렌지 이미지셋이 있으면 된다는 뜻이다.<br>
&nbsp;CycleGAN의 Loss는 Cycle consistency를 고려한다.이는 하나의 도메인에서 다른 도메인으로 바꾼 이미지를 다시 원래 이미지로 되돌렸을 때 진짜 이미지와 얼마나 차이나는지 계산하게 된다. 즉 Generator를 각 도메인마다 G()과 F()로, Discriminator를 D_G()과 D_F()로 표현했을 때, 아래의 그림과 같이 표현할 수 있다.<br>
<img src="https://cdn.discordapp.com/attachments/698770951071989841/698774474794860604/cycle_loss.png" width="100%" height="100%" title="px(픽셀) 크기 설정" alt="Cycle Consistency"></img><br>
또 다른 로스는 Identity Loss가 있는데, 이는 바꿔야 할 도메인이 아닌 자신의 도메인의 Generator를 넣었을 때 원래 이미지와 생성될 이미지가 얼마나 차이나는지 계산하는 것이다. 이는 다음과 같이 나타낸다. Identity loss = |G(Y) - Y|+|F(X)-X)| <br>
