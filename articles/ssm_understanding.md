---
title: "状態空間モデルの式を理解する"
emoji: "📊"
type: "tech"
topics: ["AI", "Manba", "状態空間モデル", "数学"]
published: false
---

# はじめに

近年出たManbaでは、状態空間モデルが使われています。
私自身、状態空間モデルの式の、特に状態方程式の意味を理解していなかったので、
かみ砕いて考えてみました。

# モデル式

状態空間モデルは、観測値と隠れ状態の関係を表すために、以下のような式で表されます。

隠れ状態: $\bm{s}(t)$, 観測値: $\bm{y}(t)$ 外力: $\bm{u}(t)$

$$
\dot{\bm{s}}(t) = \bm{A} \bm{s}(t) + \bm{B} \bm{u}(t) \\
\bm{y}(t) = \bm{C} \bm{s}(t) + \bm{D} \bm{u}(t)
$$

一つ目が状態方程式、二つ目が観測方程式です。

# 状態方程式の意味

$\bm{s}(t)$は、現在の位置と、速度を含むベクトルです。（そう考えます。）
実際に、$\bm{s}(t) = [(位置ベクトル), (速度ベクトル)]^\top$としましょう。

位置ベクトル: $\bm{x}(t)$
速度ベクトル: $\bm{v}(t)$

$$
\bm{s}(t) = [\bm{x}(t), \bm{v}(t)]^\top
$$

外力と速度を結びつける運動方程式$F=ma$を考えます。
今回に当てはめると、加速度は速度の微分なので、

$$
\dot{\bm{v}}(t) = \frac {F}{m} = \frac{1}{m} \bm{u}(t)
$$

よって

$$
\dot{\bm{s}}(t) = \left(
\begin{matrix}
\dot{\bm{x}}(t) \\
\dot{\bm{v}}(t)
\end{matrix}
\right)
=
\begin{pmatrix}
    0 & 1 \\
    0 & 0
\end{pmatrix}
\begin{pmatrix}
    \bm{x}(t) \\
    \bm{v}(t)
\end{pmatrix}
+ \begin{pmatrix}
    0 \\
    \frac{1}{m}
    \end{pmatrix}
\bm{u}(t)\\
$$

$$
\dot{\bm{s}}(t) = \bm{A} \bm{s}(t) + \bm{B} \bm{u}(t)
$$

# おわりに

状態空間モデルの式を展開してみました。
今回は運動方程式を用いましたが、