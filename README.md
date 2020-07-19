# GANの評価指標についてまとめてみる

## FID (Frechet Inception Distance)

FID(Frechet Inception Distance)は、生成された画像の分布と元の画像の距離を図る指標。  

<img width="625" alt="distillation" src="https://user-images.githubusercontent.com/39772824/87878662-af7a1800-ca20-11ea-8114-e4ad295d7922.png">

しかし、画像は高次元空間なので、簡単に分布の距離を計測できない。  
そこで、近年は人間を超える画像認識精度をもつモデルを使って、**画像を低次元空間に適用して、その空間で分布の距離を測ろう**と言うのがFIDのコンセプト

以下は定義式。

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{150}&space;\|&space;m&space;-&space;m_w&space;\|^2_2&space;&plus;&space;Tr(C&space;&plus;&space;C_w&space;-&space;2(CC_w)^{\frac{1}{2}})" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{150}&space;\|&space;m&space;-&space;m_w&space;\|^2_2&space;&plus;&space;Tr(C&space;&plus;&space;C_w&space;-&space;2(CC_w)^{\frac{1}{2}})" title="\| m - m_w \|^2_2 + Tr(C + C_w - 2(CC_w)^{\frac{1}{2}})" /></a>

Inception V3というネットワークで低次元空間に埋め込まれたベクトルでWasserstein-2距離を算出している。  
m, cは埋め込み空間上で平均ベクトル、共分散行列で、添字wがついているものが生成画像、ついていないものはオリジナル画像。  
分布の距離なので、値が小さいほど実画像っぽい画像が生成できている事になる。
