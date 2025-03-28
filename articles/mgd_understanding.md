---
title: "多次元ガウス分布の導出"
emoji: "📊"
type: "tech"
topics: ["数学", "統計"]
published: true
published_at: 2024-04-20 20:00
---

# はじめに

多次元ガウス分布（Multivariate Gaussian Distribution）は、多次元の確率分布を表現するための確率分布の一つです。多次元ガウス分布は、多変量正規分布とも呼ばれます。多次元ガウス分布は、多次元の確率変数に対する確率密度関数を表現するために使用されます。多次元ガウス分布は、多変量のデータをモデル化するために広く使用されています。
今回は、多次元ガウス分布を導出する方法について説明します。

# 多次元ガウス分布の定義

**評価データ** $x'$を代入し、その確率が計算できます。
$\mu$と$\Sigma$は、それぞれ**学習データ** $x$の平均ベクトルと共分散行列を表します。

$$
x, \mu \in \mathbb{R}^{D \times 1}, \Sigma \in \mathbb{R}^{D \times D}
$$

$$
p(x') = \frac{1}{\sqrt{(2\pi)^{D}|\Sigma|}}exp(-\frac{1}{2}(x'-\mu)^T\Sigma^{-1}(x'-\mu))
$$

# 学習データを主成分分析する

多次元ガウス分布を導出するために、学習データを主成分分析します。
主成分分析は、多次元データを低次元のデータに変換するための手法です。主に、データの次元削減やデータの可視化に使用されます。
多次元ガウス分布では、データを特徴ベクトルに射影し、射影されたデータの分散が最大となるような特徴ベクトル、特徴軸を見つけ出します。

学習データを$X$、特徴ベクトルを$V$とします。ここで、$D$はデータの次元数、$N$はデータの個数を表します。
ただし、$V$は正規直交基底を持たせます。

$$
x_n \in \mathbb{R}^{D \times 1}, v_d \in \mathbb{R}^{D \times 1},
X \in \mathbb{R}^{D \times N}, V \in \mathbb{R}^{D \times D}\\
$$

$$
X = [x_1, x_2, ..., x_N]\\
V = [v_1, v_2, ..., v_D]
$$

学習データを平均0に平均移動し、特徴ベクトルに射影し、その分散を求めます。
あるベクトルへの射影は、そのベクトルとデータの内積で求まります。
最初に、一つの特徴ベクトル$v_d$に対する分散を考えます。

$$
(射影されたベクトル) = v_d ^\top (X - \mu) \in \mathbb{R}^{1 \times N}
$$

$$
Var(v_d) = \frac{1}{N} (v_d^\top (X - \mu)) (v_d^\top (X - \mu)) ^\top\\
= \frac{1}{N} v_d^\top (X - \mu)(X - \mu)^\top v_d
= v_d^\top \Sigma v_d
$$

また、特徴ベクトルは正規直交基底を持たせるため、

$$
v_d^\top v_d = 1
$$

よって、ラグランジュの未定乗数法を用いて、

$$
J(v_d) = Var(v_d) - \lambda(v_d^\top v_d - 1) \\
= v_d^\top \Sigma v_d - \lambda(v_d^\top v_d - 1)
$$

$$
\frac{\partial J(v_d)}{\partial v_d} = 2\Sigma v_d - 2\lambda v_d = 0 \\
\Rightarrow \Sigma v_d = \lambda v_d
$$

よって、特徴ベクトル$v_d$は共分散行列の固有ベクトルであることがわかります。
ここから、$V$に拡張して考えます。
固有値$\lambda_d$は、特徴ベクトル$v_d$に対応する固有値となります。

$$
\Sigma V = [\Sigma v_1, \Sigma v_2, ..., \Sigma v_D]\\
= [\lambda_1 v_1, \lambda_2 v_2, ..., \lambda_D v_D] \\
= [v_1, v_2, ..., v_D] diag(\lambda_1, \lambda_2, ..., \lambda_D)\\
= V \Lambda
$$

ここで、$\Lambda$は固有値を対角成分に持つ行列です。

この式は重要なので一度整理します。

$$
\Sigma V = V \Lambda
$$

$\Sigma$ : 学習データの分散共分散行列
$V$ : 特徴ベクトル
$\Lambda$ : 固有値を対角成分に持つ行列

またこの式は後ほど使うので、いろいろ導出しておきます。
$V$は正規直交基底を満たすため、直行行列という点に注意してください。

$$
\Sigma v_d = \lambda_d v_d \\
\Leftrightarrow \lambda_d = v_d^\top \Sigma v_d \\
= Var(v_d)
= \sigma _d ^ 2
$$

$$
\Sigma = V \Lambda V^{-1} = V \Lambda V^\top
$$

$$
| \Sigma | = | V \Lambda V^\top | = | V | | \Lambda | | V^\top | = | \Lambda |
$$

$$
\Sigma^{-1}\Sigma = I \\
\Leftrightarrow \Sigma^{-1} V \Lambda V^\top = I \\
\Leftrightarrow \Sigma^{-1} V \Lambda V^\top \cdot (V \Lambda ^{-1} V^\top) = I \cdot (V \Lambda ^{-1} V^\top) \\
\Leftrightarrow \Sigma^{-1} V \Lambda (V^\top V) \Lambda ^{-1} V^\top = V \Lambda ^{-1} V^\top \\
\Leftrightarrow \cdots \\
\Leftrightarrow \Sigma^{-1} = V \Lambda ^{-1} V^\top
$$

$$
diag(\lambda_1, \lambda_2, ..., \lambda_D) diag(\frac{1}{\lambda_1}, \frac{1}{\lambda_2}, ..., \frac{1}{\lambda_D}) = I\\
\Leftrightarrow \Lambda diag(\frac{1}{\lambda_1}, \frac{1}{\lambda_2}, ..., \frac{1}{\lambda_D}) = I\\
\Leftrightarrow \Lambda ^{-1} = diag(\frac{1}{\lambda_1}, \frac{1}{\lambda_2}, ..., \frac{1}{\lambda_D})
$$

結果は以下の通りです。

$$
\sigma _d ^ 2 = \lambda_d \\
\Sigma = V \Lambda V^\top \\
| \Sigma | = | \Lambda | \\
\Sigma^{-1} = V \Lambda ^{-1} V^\top \\
\Lambda ^{-1} = diag\left(\frac{1}{\lambda_1}, \frac{1}{\lambda_2}, ..., \frac{1}{\lambda_D}\right)
$$

# 多次元ガウス分布の導出

以前の式を使って、多次元ガウス分布の導出を行います。
特徴軸に沿って射影した上で、ガウス分布を構成して、それらの積を取ります。
評価データは$x'\in \mathbb{R}^{D \times 1}$です。
少し丁寧に行います。


$$
p(x') = \prod_{d=1}^{D} \frac{1}{\sqrt{2\pi \sigma_d ^2}}exp\left(-\frac{1}{2}\frac{(v_d ^\top (x'-\mu)) ^2} {\sigma _d ^2} \right)\\
= \frac{1}{(2\pi)^{D/2} \prod_{d=1}^{D} \sqrt{\sigma_d ^2}} exp \left( - \frac 1 2 \sum _{d=1} ^D \frac{(v_d ^\top (x'-\mu)) ^2} {\sigma _d ^2}  \right) \\
= \frac{1}{(2\pi)^{D/2} \sqrt{\prod_{d=1}^{D} \lambda _d}} exp \left( - \frac 1 2 \sum _{d=1} ^D \frac{1} {\lambda _d} (v_d ^\top (x'-\mu)) ^2  \right) \\
= \frac{1}{(2\pi)^{D/2} \sqrt{|\Lambda|}} exp \left( - \frac 1 2 (V ^\top (x'-\mu)) ^\top diag\left(\frac{1}{\lambda_1}, \frac{1}{\lambda_2}, ..., \frac{1}{\lambda_D}\right) (V ^\top (x'-\mu))  \right) \\
= \frac{1}{(2\pi)^{D/2} \sqrt{|\Sigma|}} exp \left( - \frac 1 2 (x'-\mu) ^\top V \Lambda ^{-1} V^\top (x'-\mu)  \right) \\
= \frac{1}{\sqrt{(2\pi)^{D} |\Sigma| }} exp \left( - \frac 1 2 (x'-\mu) ^\top \Sigma^{-1} (x'-\mu)  \right) \\
$$

> 3行目から4行目の変形は、二次形式で考えます。


以上で、多次元ガウス分布の導出が完了しました。

# まとめ

多次元ガウス分布は、多次元の確率分布を表現するための確率分布の一つです。多次元ガウス分布は、多変量のデータをモデル化するために広く使用されています。多次元ガウス分布を導出するために、学習データを主成分分析し、特徴ベクトルを求めました。そして、特徴軸に沿って射影した上で、ガウス分布を構成しました。
