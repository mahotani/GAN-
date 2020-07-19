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

## PPL (Perceptual Path Length)

PPL(Perceptual Path Length)は、**知覚**的に、つまり人間の感覚で見て潜在空間上で画像が滑らかに変化するか、という指標。  

画像を生成する元になる潜在空間上で、画像の変化は知覚的に短距離で変化しているかの指標。  

<img width="625" alt="distillation" src="https://user-images.githubusercontent.com/39772824/87879825-06372000-ca28-11ea-8605-13279d17559b.png">

犬を生成するのだったら、白い犬を生成する潜在変数z1と黒い犬を生成する潜在変数z2があったとする。  
この時、色以外の要素は変化させないでその中間データをとると、黒と白の間、つまり灰色の犬が出てくることが理想。  
つまり、色のみを変化させるような上の図の青い経路が知覚的に最短経路になる。  
逆に、犬とはかけ離れたものを経由してしまう緑の経路は知覚的に長い距離になってしまう。  

これを数値化したのがPPLで、定義式は次の式。

<a href="https://www.codecogs.com/eqnedit.php?latex=\dpi{150}&space;l_w&space;=&space;E\[&space;\frac{1}{\epsilon&space;^2}&space;d&space;(g(lerp(f(z_1),&space;f(z_2)&space;;&space;t)),&space;g(lerp(f(z_1),&space;f(z_2)&space;;&space;t&space;&plus;&space;\epsilon)))&space;\]" target="_blank"><img src="https://latex.codecogs.com/gif.latex?\dpi{150}&space;l_w&space;=&space;E\[&space;\frac{1}{\epsilon&space;^2}&space;d&space;(g(lerp(f(z_1),&space;f(z_2)&space;;&space;t)),&space;g(lerp(f(z_1),&space;f(z_2)&space;;&space;t&space;&plus;&space;\epsilon)))&space;\]" title="l_w = E\[ \frac{1}{\epsilon ^2} d (g(lerp(f(z_1), f(z_2) ; t)), g(lerp(f(z_1), f(z_2) ; t + \epsilon))) \]" /></a>

2つの潜在変数z1とz2を比率tで混ぜ合わせた潜在変数で生成した画像と、比率t+εで混ぜ合わせた潜在変数で生成した画像の距離の期待値となっている。  
つまり、tで混ぜたデータと、t+εで混ぜたデータが知覚的に近ければ、小さな距離をとる。  

学習済のVGGという画像認識ネットワークを使用し、それが算出する特徴量ベクトルの距離を知覚的な距離にしている。  
多くの画像でこの計算を行って期待値をとった結果がPPLの値になる。  
この値が小さいほど潜在空間が知覚的に滑らかだということになる。  
