---
title: "HMMを理解する"
emoji: "🎤"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["AI", "音声", "HMM"]
published: false
published_at: 2024-05-01 20:00
---

# はじめに

HMM(Hidden Markov Model)は、音声研究の初期に提案されたモデルで、音声合成や音声認識の分野で使われていました（が、最近は使われない傾向にあります）。
この記事では、HMMの基本的な概念を説明します。
注意：筆者が音声合成専門なので、偏った観点になる可能性があります。

# 用途

音響特徴量系列と音素系列との**アライメント**を取るのにつかわれます。
アライメントを知らない方向けに説明すると、
音声分野では、音響特徴量系列と音素系列の対応付けを行うために使われます。
音響特徴量系列はフレーム単位での系列、音素系列は音素単位での系列です。
そのため、一音素に対してどのフレームが対応するかの対応関係をつかむ必要があります。
アライメントを取ることに成功すれば、テキスト⇔音声の単位の変換が可能になります。
![alignment](/images/articles/hmm_understanding/alignment.png)



# モデル構造

音声分野でのHMMではスキップなしleft-to-rightのモデルが使われます。
![HMM](/images/articles/hmm_understanding/hmm.png)

HMMは以下の要素から構成されます。
ただし、$N$は状態数、$T$は系列長です。
- $Q = \{q_1, q_2, \cdots, q_T\}$状態系列
- $O = \{o_1, o_2, \cdots, o_T\}$観測系列
- $A = \{a_{ij}\} \in \mathbb{R}^{N \times N}$状態遷移確率行列
- $B = \{b_i(o_t) = \mathcal{N}(o_t|\mu_i,\Sigma_i)\}$出力確率行列
- $\pi$: 初期確率ベクトル

しかし今回は、$\pi=1$として、初期確率ベクトルの最適化を省略します。

# トレリス線図

HMMの状態遷移を図示すると、以下のようなトレリス線図と呼ばれる図が得られます。
系列長$T$の観測系列$O$が与えられたときに、状態数$N$のHMMのユニットをどのような道筋で通るかを表すのに便利です。
HMMでは自己遷移が可能なので、同じ状態に戻ることができますが、以下のような図を使えば、左下から右上まで一本のパスで表現できます。
![trellis](/images/articles/hmm_understanding/trellis.png)


# 学習

HMMの学習パラメータは、状態遷移確率行列$A$、出力確率行列$B$、初期確率ベクトル$\pi$です。
学習にはEMアルゴリズム^[zuka 2023年9月18日【徹底解説】EMアルゴリズムをはじめからていねいに academaid https://academ-aid.com/ml/em]（個人的にお気に入りの解説）を使います。

## 道筋

1. 学習パラメータ$\lambda = (A, B, \pi)$を初期化
2. 学習データとしての観測系列$O$が与えられる
3. 尤度$P(O|\lambda)$の補助関数$\mathcal{Q}(\lambda, \lambda^{old})$を定義
4. $Q(\lambda, \lambda^{old})$を最大化するような$\lambda^*$に更新
5. $\lambda$が収束するまで3, 4を繰り返す

## 尤度最大化

尤度$P(O|\lambda)$を最大化するようなパラメータ$\lambda$を求めます。

$$
P(O|\lambda) = \sum_{Q} P(O, Q|\lambda)
=\sum_{q_1} \sum_{q_2} \cdots \sum_{q_T} P(O, Q|\lambda)
$$

このように素直に求めると、計算が膨大になり、現実的ではありません。$O(T^N)$の計算量がかかります。
そこで、補助関数$\mathcal{Q}(\lambda, \lambda^{old})$の導入を考えます。

## 補助関数$\mathcal{Q}(\lambda, \lambda^{old})$

EMアルゴリズムの説明と被りますが、今一度説明します。

尤度最大化の目的のため、対数尤度を式変形します。
$\lambda$は変数という点に注意してください。

$$
\log P(O|\lambda) = \log \sum_{Q} P(O, Q|\lambda)\\
=\log \sum_{Q} X(Q|\lambda^{old}) \frac{P(O, Q|\lambda)}{X(Q|\lambda^{old})}\\
\geq \sum_{Q} X(Q|\lambda^{old}) \log \frac{P(O, Q|\lambda)}{X(Q|\lambda^{old})}\\
=\mathcal{L}_{ML}(\lambda)
$$

不等式はJensenの不等式を使っています。
ここで、$X(Q|\lambda^{old})$は任意の確率分布で、$\lambda^{old}$は現在のパラメータです。
この時、

$$
\log P(O|\lambda) - \mathcal{L}_{ML}(\lambda) \\
\sum_{Q} X(Q|\lambda^{old}) \log P(O|\lambda) - \sum_{Q} X(Q|\lambda^{old}) \log \frac{P(O, Q|\lambda)}{X(Q|\lambda^{old})} \\
= \sum_{Q} X(Q|\lambda^{old}) \log P(O|\lambda) - \sum_{Q} X(Q|\lambda^{old}) \log \frac {P(Q|O,\lambda)P(O|\lambda)}{X(Q|\lambda^{old})} \\
= \sum_{Q} X(Q|\lambda^{old}) \log \frac{X(Q|\lambda^{old})}{P(Q|O,\lambda)}\\
= KL[X(Q|\lambda^{old})||P(Q|O,\lambda)]
$$

よって、

$$
\log P(O|\lambda) = \mathcal{L}_{ML}(\lambda) + KL[X(Q|\lambda^{old})||P(Q|O,\lambda)]
$$

が成り立ちます。
この時、$\log P(O|\lambda)$を最大化するような$\lambda$を求めたいのですが、
KLダイバージェンスは常に非負なので、

$$
\mathcal{L}_{ML}(\lambda) \geq \log P(O|\lambda)
$$

が成り立ちます。
この下限としての$\mathcal{L}_{ML}(\lambda)$を最大化することで、$\log P(O|\lambda)$を下から押し上げることができます。
ここで、$X(Q|\lambda^{old}) = P(Q|O,\lambda^{old})$(現在のパラメータを使った事後分布)と設定すると、
$\lambda = \lambda^{old}$の時、KLダイバージェンスを0にできます。
（KLを0にする理由は、$\mathcal{L}_{ML}(\lambda)$を最大化できそうだからです。のちにわかります。）
すると、

$$
\log P(O|\lambda^{old}) = \mathcal{L}_{ML}(\lambda^{old})
$$

となります。
この時、$\mathcal{L}_{ML}(\lambda)$を最大化する$\lambda^{new}$を求めると次が成り立ちます。

$$
\mathcal{L}_{ML}(\lambda ^{old}) \leq \mathcal{L}_{ML}(\lambda^{new})
$$

この時、次は依然として成り立ちます。

$$
\log P(O|\lambda^{new}) \geq \mathcal{L}_{ML}(\lambda^{new})
$$

これらの不等式をつなぎ合わせると、

$$
\log P(O|\lambda^{old}) \leq \mathcal{L}_{ML}(\lambda^{old}) \leq \mathcal{L}_{ML}(\lambda^{new}) \leq \log P(O|\lambda^{new})
$$

となるため、$\lambda^{old}$から$\lambda^{new}$に更新することで、尤度を向上させることができました。
つまり、$\lambda^*= \lambda^{new}$としてよいです。

この$\lambda^{new}$は、$X(Q|\lambda^{old}) = P(Q|O,\lambda^{old})$という制約の下で、$\mathcal{L}_{ML}(\lambda)$を最大化するような$\lambda$でした。
ここで、$\mathcal{L}_{ML}(\lambda)$をさらに式整理してみると、

$$
\mathcal{L}_{ML}(\lambda)\ \ \ ( where \ \ \ X(Q|\lambda^{old}) = P(Q|O,\lambda^{old}))\\
=\sum_{Q} P(Q|O,\lambda^{old}) \log \frac{P(O, Q|\lambda)}{P(Q|O,\lambda^{old})}\\
=\sum_{Q} P(Q|O,\lambda^{old}) \log P(O, Q|\lambda) - \sum_{Q} P(Q|O,\lambda^{old}) \log P(Q|O,\lambda^{old})\\
$$

ここで、第二項のエントロピーは$\lambda$に依存しないため、定数です。
よって、$\mathcal{L}_{ML}(\lambda)$を最大化することは、$\sum_{Q} P(Q|O,\lambda^{old}) \log P(O, Q|\lambda)$を最大化することと同じです。
なので、$\mathcal{Q}(\lambda, \lambda^{old}) = \sum_{Q} P(Q|O,\lambda^{old}) \log P(O, Q|\lambda)$とおき直します。

よって最終的には、
E-stepで$\mathcal{Q}(\lambda, \lambda^{old})$を最大化するような$\lambda$を求め、
M-stepで$\lambda$を更新します。

この手続きで、補助関数$\mathcal{Q}(\lambda, \lambda^{old})$に基づく更新で、尤度を最大化することができます。

# 補助関数の計算


補助関数$\mathcal{Q}(\lambda, \lambda^{old}) = \sum_{Q} P(Q|O,\lambda^{old}) \log P(O, Q|\lambda)$を計算するためには、
$P(Q|O,\lambda^{old}), P(O, Q|\lambda)$を計算する必要があります。
この計算には、素直に計算するのは全通りの状態系列を考慮する必要があり、$O(N^T)$かかるため、現実的ではありませんので、
forward-backwardアルゴリズムが使われます。

## forward-backwardアルゴリズム

forwardアルゴリズム、backwardアルゴリズムは、尤度や補助関数の計算に使うことができます。
前時刻の情報から次の時刻の情報を計算するという漸化式でDP(Dynamic Programming)を行うことで、計算量を$O(TN^2)$に抑えることができます。

### forwardアルゴリズム

次のような$\alpha_i(t)$を定義します。

$$
\alpha(t, i) = P(o_1, o_2, \cdots, o_t, q_t = S_i|\lambda)\\
\alpha(0, 0) = \pi
$$

この時、再帰的に以下のように計算できます。

$$
\alpha(t+1, i) = P(o_1, o_2, \cdots, o_{t+1}, q_{t+1} = S_i|\lambda)\\
=\sum_{j=1}^{N} P(o_1, o_2, \cdots, o_t, q_t = S_j, o_{t+1}, q_{t+1} = S_i|\lambda)\\
=\sum_{j=1}^{N} P(o_1, o_2, \cdots, o_t, q_t = S_j|\lambda)P(o_{t+1} | q_{t+1} = S_i, \lambda)P(q_{t+1} = S_i | q_t = S_j, \lambda)\\
=\sum_{j=1}^{N} \alpha(t, j) a_{ji} b_i(o_{t+1}) \\
\left(=\alpha(t, i) a_{ii} b_i(o_{t+1}) + \alpha(t, i-1) a_{i-1, i} b_i(o_{t+1}) \right)
$$

この時、$P(O|\lambda) = \alpha(T, N)$です。
ひとつ前の時刻における全状態から次の時刻の1状態へ、と各々計算していくことで、$\alpha(t, i)$を使って尤度を効率よく$O(TN^2)$で計算できます。

![forward](/images/articles/hmm_understanding/forward.png)

### backwardアルゴリズム

forwardアルゴリズムと同様に今度は逆向きに計算していきます。

次のような$\beta_(t, i)$を定義します。

$$
\beta(t, i) = P(o_{t+1}, o_{t+2}, \cdots, o_T|q_t = S_i, \lambda)\\
\beta(T, N) = 1
$$

この時、再帰的に以下のように計算できます。

$$
\beta(t-1, i) = P(o_t, o_{t+1}, o_{t+2}, \cdots, o_T|q_{t-1} = S_i, \lambda)\\
=\sum_{j=1}^{N} P(o_t, o_{t+1}, o_{t+2}, \cdots, o_T, q_t = S_j|q_{t-1} = S_i, \lambda)\\
=\sum_{j=1}^{N} P(o_{t+1}, o_{t+2}, \cdots, o_T|q_t = S_j, \lambda)P(o_t|q_t = S_j, \lambda)P(q_t = S_j|q_{t-1} = S_i, \lambda)\\
=\sum_{j=1}^{N} \beta(t, j) a_{ij} b_j(o_t)\\
\left(=\beta(t, i) a_{ii} b_i(o_t) + \beta(t, i+1) a_{i, i+1} b_i(o_t) \right)
$$

この時、$P(O|\lambda) = \beta(0, 0)$です。

![backward](/images/articles/hmm_understanding/backward.png)

## 実際に補助関数を計算する

補助関数の式をパラメータ別に分解すると、以下のようになります。

$$
\mathcal{Q}(\lambda, \lambda^{old}) = \sum_{Q} P(Q|O,\lambda^{old}) \log P(O, Q|\lambda)\\
=\sum_{Q} P(Q|O,\lambda^{old}) \log \left( P(Q|\lambda_A)P(O|Q, \lambda_B) \right)\\
=\sum_{Q} P(Q|O,\lambda^{old}) \log P(Q|\lambda_A) + \sum_{Q} P(Q|O,\lambda^{old}) \log P(O|Q, \lambda_B)\\
=\mathcal{Q}_A(\lambda, \lambda^{old}) + \mathcal{Q}_B(\lambda, \lambda^{old})
$$

これによって、パラメータ$A, B$ごとに最適化を考えることができます。

### 事前準備

補助関数を計算するにあたって必要な計算を先に示しておきます。

$$
P(O|\lambda) = \alpha(T, N) = \beta(0, 0)\\
= \sum_{i=1}^{N} P(o_1, o_2, \cdots, o_T, q_t = S_i|\lambda)\\
= \sum_{i=1}^{N} P(o_1, o_2, \cdots, o_t, q_t = S_i|\lambda)P(o_{t+1}, o_{t+2}, \cdots, o_T|q_t = S_i, \lambda)\\
= \sum_{i=1}^{N} \alpha(t, i) \beta(t, i)
$$

これはつまり、1からTまで行くとき、tでは必ずどこかの状態を通るので、その行き方を全て足し合わせるというイメージです。

$$
P(q_t = S_i | O, \lambda) = \frac{P(q_t = S_i, O|\lambda)}{P(O|\lambda)}\\
=\frac{P(o_1, o_2, \cdots, o_t, q_t = S_i, o_{t+1}, o_{t+2}, \cdots, o_T|\lambda)}{P(O|\lambda)}\\
=\frac{P(o_1, o_2, \cdots, o_t, q_t = S_i|\lambda)P(o_{t+1}, o_{t+2}, \cdots, o_T|q_t = S_i, \lambda)}{P(O|\lambda)}\\
=\frac{\alpha(t, i) \beta(t, i)}{P(O|\lambda)}\\
=\frac{\alpha(t, i) \beta(t, i)}{\sum_{i'=1}^{N} \alpha(t, i') \beta(t, i')}
$$

$$
P(q_{t-1} = S_i, q_t = S_j | O, \lambda) = \frac{P(q_{t-1} = S_i, q_t = S_j, O|\lambda)}{P(O|\lambda)}\\
=\frac{P(o_1, o_2, \cdots, o_{t-1}, q_{t-1} = S_i, q_t = S_j, o_t, o_{t+1}, o_{t+2}, \cdots, o_T|\lambda)}{P(O|\lambda)}\\
=\frac{P(o_1, o_2, \cdots, o_{t-1}, q_{t-1} = S_i|\lambda)P(q_t = S_j|q_{t-1} = S_i, \lambda)P(o_t|q_t = S_j, \lambda)P(o_{t+1}, o_{t+2}, \cdots, o_T|q_t = S_j, \lambda)}{P(O|\lambda)}\\
=\frac{\alpha(t-1, i) a_{ij} b_j(o_t) \beta(t, j)}{P(O|\lambda)}\\
=\frac{\alpha(t-1, i) a_{ij} b_j(o_t) \beta(t, j)}{\sum_{i'=1}^{N} \alpha(t, i') \beta(t, i')}
$$

### 状態遷移に対する補助関数

$$
\mathcal{Q}_A(\lambda, \lambda^{old}) = \sum_{Q} P(Q|O,\lambda^{old}) \log P(Q|\lambda_A)\\
=\sum_{Q} P(Q|O,\lambda^{old}) \log \prod ^T _{t=1}  P(q_t| q_{t-1} \lambda_A) \\
=\sum_{Q} P(Q|O,\lambda^{old}) \sum ^T _{t=1} \log P(q_t| q_{t-1} \lambda_A) \\
=\sum_{Q} P(Q|O,\lambda^{old}) \sum ^T _{t=1} \sum _{i,j} \delta(q_{t-1} = S_i) \delta(q_t = S_j) \log P(q_t|q_{t-1}, \lambda_A) \\
=\sum ^T _{t=1} \sum _{i,j} \left[\sum_{Q} P(Q|O,\lambda^{old}) \delta(q_{t-1} = S_i) \delta(q_t = S_j) \right] \log P(q_t|q_{t-1}, \lambda_A) \\
=\sum ^T _{t=1} \sum _{i,j} P(q_{t-1} = S_i, q_t = S_j | O, \lambda^{old}) \log a_{ij} \\
=\sum ^T _{t=1} \sum _{i,j} \frac{\alpha(t-1, i) a_{ij} b_j(o_t) \beta(t, j)}{\sum_{i'=1}^{N} \alpha(t, i') \beta(t, i')} \log a_{ij} \\
$$

ただし、$\log$の前についている項は全て現在のパラメータ$\lambda^{old}$に依存しているため、定数ということに注意してください。

### 出力確率に対する補助関数

$$
\mathcal{Q}_B(\lambda, \lambda^{old}) = \sum_{Q} P(Q|O,\lambda^{old}) \log P(O|Q, \lambda_B)\\
=\sum_{Q} P(Q|O,\lambda^{old}) \log \prod ^T _{t=1}  P(o_t| q_t, \lambda_B) \\
=\sum_{Q} P(Q|O,\lambda^{old}) \sum ^T _{t=1} \log P(o_t| q_t, \lambda_B) \\
=\sum_{Q} P(Q|O,\lambda^{old}) \sum ^T _{t=1} \sum _{i} \delta(q_t = S_i) \log P(o_t| q_t, \lambda_B) \\
=\sum ^T _{t=1} \sum _{i} \left[\sum_{Q} P(Q|O,\lambda^{old}) \delta(q_t = S_i) \right] \log P(o_t| q_t, \lambda_B) \\
=\sum ^T _{t=1} \sum _{i} P(q_t = S_i | O, \lambda^{old}) \log b_i(o_t) \\
=\sum ^T _{t=1} \sum _{i} \frac{\alpha(t, i) \beta(t, i)}{\sum_{i'=1}^{N} \alpha(t, i') \beta(t, i')} \log b_i(o_t) \\
$$

こちらも同様に、$\log$の前についている項は全て現在のパラメータ$\lambda^{old}$に依存しているため、定数ということに注意してください。

### 補助関数の最大化

やっと準備が整いました。それではパラメータを更新しましょう。

状態遷移確率に対する補助関数を最大化します。

$$
\lambda ^* _A  = argmax_{\lambda_A} \mathcal{Q}_A(\lambda, \lambda^{old}) \ s.t. \sum _{j} a_{ij} = 1, i = 1, \cdots, N\\
\mathcal{Q}_A  = \sum ^T _{t=1} \sum _{i,j} \frac{\alpha(t-1, i) a_{ij} b_j(o_t) \beta(t, j)}{\sum_{i'=1}^{N} \alpha(t, i') \beta(t, i')} \log a_{ij} \\
J(a_i) = \mathcal{Q}_A + \sum _{i=1}^{N} \lambda_i \left(1 - \sum _{j} a_{ij} \right)\\
a_{ij} = \frac{\sum _{t=1} ^T P(q_{t-1} = S_i, q_t = S_j | O, \lambda^{old})}{\sum _{t=1} ^T \sum_{i',j'} P(q_{t-1} = S_{i'}, q_t = S_{j'} | O, \lambda^{old})}\\
$$

$J(a_i)$はラグランジュ関数です。これを最大化するような$a_{ij}$を求めました。

同様に、出力確率に対する補助関数も最大化します。

$$
\lambda ^* _B  = argmax_{\lambda_B} \mathcal{Q}_B(\lambda, \lambda^{old}) \\
\mathcal{Q}_B  = \sum ^T _{t=1} \sum _{i} P(q_t = S_i | O, \lambda^{old}) \log b_i(o_t) \\
b_i(o_t) = \mathcal{N}(o_t|\mu_i, \Sigma_i)\\
\lambda_B = \{ \mu_i, \Sigma_i | i = 1 , \cdots , N\}\\
\mu_i = \frac{\sum _{t=1} ^T P(q_t = S_i | O, \lambda^{old}) o_t}{\sum _{t=1} ^T P(q_t = S_i | O, \lambda^{old})}\\
\Sigma_i = \frac{\sum _{t=1} ^T P(q_t = S_i | O, \lambda^{old}) (o_t - \mu_i)(o_t - \mu_i)^\top}{\sum _{t=1} ^T P(q_t = S_i | O, \lambda^{old})}\\
$$

これで、補助関数を最大化するようなパラメータを求めることができました。

# まとめ

HMMの基本的な概念と、EMアルゴリズムを使った学習方法を説明しました。
HMMは音声合成や音声認識の分野で使われていましたが、最近は使われない傾向にあります。
ただし、HMMの基本的な概念は、他のモデルにも応用できるため、理解しておくとよいでしょう。







