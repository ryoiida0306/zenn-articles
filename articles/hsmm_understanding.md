---
title: "HSMMを理解する"
emoji: "🎤"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["AI", "音声", "HMM", "HSMM"]
published: true
published_at: 2024-05-04 20:00
---

# はじめに
HSMM(Hidden Semi-Markov Model)は、HMM(Hidden Markov Model)の発展形であるため、
先に[HMM]{https://zenn.dev/ryoiida0306/articles/hmm_understanding}を理解しておくことをお勧めします。
HMMでは、合成時に自己遷移を極端に嫌う性質があるため、極端に短い系列が生成される傾向にあります。
HSMMは、この問題を解決するために提案されたモデルです。
本記事では、HSMMの基本的な考え方について解説します。

# HSMMとは
HSMMは、HMMの状態遷移が時間に依存するモデルです。
HMMでは、合成時に自己遷移を極端に嫌う性質があり、最終的な尤度を最大化することを考えると、確率は確実に1以下であるため、掛け合わせるとそれが起こる確率は小さくなります。
具体的には、HMMにおける状態$S_i$で長さ$d$だけ続く継続長確率$P_i(d|\lambda)$は次のような幾何分布に従います。

$$
P_i(d|\lambda) = P(q_{t-1} = S_{i-1}, q_t = S_i, q_{t+d-1} = S_i, q_{t+d} = S_{i+1}| \lambda)\\
= a_{i-1,i}\cdot a_{i,i} ^{d-1} \cdot a_{i,i+1}\\
= a_{i-1,i}\cdot (1-a_{i,i+1})^{d-1} \cdot a_{i,i+1}
$$

ここからわかる通り、継続長が長くなるほど、確率は指数関数的に減少します。
このため、HMMは短い系列を生成する傾向があります。
HSMMは、この問題を解決するために、$P_i(d|\lambda)$をガウス分布などの他の分布に置き換えます。

# HSMMのモデル

HSMMは、HMMと同様に、状態遷移確率行列$A$、観測確率行列$B$、初期確率$\pi$を持ちますが、
状態遷移確率行列$A$は、状態$i$から状態$j$に遷移する確率が時間$t$に依存するようになります。
HMMでは$A$を$N \times N$の行列として定義しましたが、
今回ガウス分布を想定すると、以下のように定義されます。

$$
A = \{ a_i(d) = \mathcal{N}(d | \eta_i, \tau_i) \}
$$

これをすることで、前節で述べた$P_i(d|\lambda)$はガウス分布に従うようになり、
継続長が長くなっても確率が急激に減少することがなくなります。

また学習においては、基本的な考え方はHMMと同様ですが、
forward-backwardアルゴリズムの式だけ少し変わります。

# foward-backwardアルゴリズム

HMMと同様に漸化式を用いて、前向き確率$\alpha$、後ろ向き確率$\beta$を計算します。

## forwardアルゴリズム

時刻$t$で状態$S_i$にいるときの観測系列$O$の前向き確率$\alpha(t,i)$は次のように定義されます。

$$
\alpha(t,i) =P(o_1, o_2, \ldots, o_t ,  q_{t} = S_{i} |q_{t-1} = S_{i-1}, \lambda)\\
\alpha(0,0) = \pi
$$

この時漸化式は以下のようになります。

$$
\alpha(t,i) =P(o_1, o_2, \ldots, o_t ,  q_{t} = S_{i} |q_{t-1} = S_{i-1}, \lambda)\\
= \sum_{d=1}^t \alpha(t-d, i-1) a_{i-1}(d) \prod_{t'=t-d+1}^t b_{i}(o_{t'})
$$

![forward](/images/articles/hsmm_understanding/forward.png)

## backwardアルゴリズム

時刻$t$で状態$S_i$にいるときの観測系列$O$の後ろ向き確率$\beta(t,i)$は次のように定義されます。

$$
\beta(t,i) =P(o_{t+1}, o_{t+2}, \ldots, o_T  | q_{t} = S_{i}, q_{t+1} = S_{i+1}, \lambda)\\
\beta(T,N) = 1
$$

この時漸化式は以下のようになります。

$$
\beta(t,i) =P(o_{t+1}, o_{t+2}, \ldots, o_T  | q_{t} = S_{i}, q_{t+1} = S_{i+1}, \lambda)\\
= \sum_{d=1}^{T-t} \beta(t+d, i+1) a_{i}(d) \prod_{t'=t+1}^{t+d} b_{i}(o_{t'})
$$

![backward](/images/articles/hsmm_understanding/backward.png)


# Aの更新

ガウス分布に従うようにしたため、パラメータ$\eta_i, \tau_i$を更新式が必要になります。

$$
\eta_i = \frac{\sum_{t=1}^{T-1} \sum_{d=1}^{T-t} \alpha(t,i) a_i(d) \beta(t+d, i) \prod_{t'=t}^{t+d-1} b_i(o_{t'})}{\sum_{t=1}^{T-1} \sum_{d=1}^{T-t} \alpha(t,i) a_i(d) \beta(t+d, i)}\\
\tau_i = \frac{\sum_{t=1}^{T-1} \sum_{d=1}^{T-t} \alpha(t,i) a_i(d) \beta(t+d, i) \prod_{t'=t}^{t+d-1} b_i(o_{t'}) (d - \eta_i)^2}{\sum_{t=1}^{T-1} \sum_{d=1}^{T-t} \alpha(t,i) a_i(d) \beta(t+d, i)}
$$

# まとめ

HSMMは、HMMの状態遷移が時間に依存するモデルです。
HMMでは、合成時に自己遷移を極端に嫌う性質があるため、極端に短い系列が生成される傾向にあります。
HSMMは、この問題を解決するために提案されたモデルであり、状態遷移確率行列$A$をガウス分布などの他の分布に置き換えることで、継続長が長くなっても確率が急激に減少することがなくなります。
HSMMは、状態遷移が時間に依存することで、より自然な系列を生成することができます。




