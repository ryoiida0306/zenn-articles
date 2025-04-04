---
title: "「フーリエ変換は関数の内積」に気づき、その周辺の理解が深まったこと"
emoji: "🔍"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["フーリエ変換", "数学"]
published: false
---

# はじめに

**「フーリエ変換は、関数の内積だ！！」**
ということに気づいてから、式がすんなり理解できるようになりました。
結局今回はそれだけなのですが、
それに伴って、今まであやふやにしていた部分の理解を深めたので、記録に残しておこうと思います。


今回解決できた疑問と結論を先に書きます。

----
**【疑問】**
1. フーリエ変換は、関数の内積だ！けど、もう少し理解を深めたい。
    - 共役のこと
2. フーリエ変換は広義積分で、計算の値が発散するのでは？
3. $e^{i\omega t}$成分とは？
    - 偶関数・奇関数のこと
    - 三角関数の積の積分の理解を深める。

**【結論】**
1.→ $f(t)$の$e^{i\omega t}$成分をの抽出には、逆位相の$e^{-i\omega t} = \overline{e^{i \omega t}}$を掛けて打ち消して取り出す。
2.→ 発散するけど、$\delta(\omega)$で考えられる。実用上では窓関数などを使って近似する。
3.→ $e^{i\omega t} = \cos(\omega t) + i \sin(\omega t)$成分は、周波数$\omega$の成分を表す。


---
# フーリエ変換は関数の内積
フーリエ変換は、次のように定義されます。

$$
F(\omega) = \int_{-\infty}^{\infty} f(t) e^{-i \omega t} dt
$$

これを関数の内積として考えると、次のように書き換えられます。

$$
F(\omega) = \langle f(t), e^{i \omega t} \rangle
$$

このことから、「ある$\omega$に対して、$f(t)$の$e^{i\omega t}$成分を抽出する」操作に相当する
と言い換えられます。

# 関数の内積を思い出す


$L^2$空間における内積は、次のように定義されます。

$$
\langle f, g \rangle = \int_{-\infty}^{\infty} f(x) g(x) dx
$$

複素数空間も含む、$L^2$空間における内積は、次のように定義されます。

$$
\langle f, g \rangle = \int_{-\infty}^{\infty} f(x) \overline{g(x)} dx
$$

$g(t)$に共役になっています。「共役？そもそもなんでついているんだっけ？」
となったので深堀します。

## 虚数ベクトルの内積を思い出す
:::message
このセッションは、結果的にフーリエ変換の共役についてあまり理解を深められなかったので、忙しい人は次セッションの「フーリエ変換の式から共役の恩恵を考える」に飛んでも大丈夫です。
:::

内積とはなんでしょうか。実数範囲では類似度を計算するものでした。

:::details 高校範囲での内積
高校の範囲では2次元のベクトルの内積を考えていました。

$$
\langle \bm{a}, \bm{b} \rangle = |\bm{a}||\bm{b}| \cos \theta\\
= a_1 b_1 + a_2 b_2
$$

$\cos \theta$の表し方では、$\bm{a}$を$\bm{b}$の方向に投影した長さを考えたりしていました。
:::

実数範囲では内積は以下のように定義されていました。

$$
\langle \bm{a}, \bm{b} \rangle = \sum_{i=1}^{n} a_i b_i
$$

また自己内積は次のようにあらわされ、ノルムを表すのでした。

$$
\langle \bm{a}, \bm{a} \rangle = \sum_{i=1}^{n} a_i^2 = |\bm{a}|^2
$$

虚数範囲ではどうでしょうか。
内積とノルムは以下のように定義されています。

$$
\langle \bm{a}, \bm{b} \rangle = \sum_{i=1}^{n} a_i \overline{b_i} \\
\langle \bm{a}, \bm{a} \rangle = \sum_{i=1}^{n} a_i \overline{a_i} = \sum_{i=1}^{n} |a_i|^2
$$

共役の原因はここからきていそうです。なぜ共役がついているのでしょうか。

高校範囲では1次元の時に限って虚数のノルムを考えたことがありました。
ある虚数$z$のノルムは、$|z|^2 = z \overline{z}$と教えられました。
高校から習っていると自明のような気がしますが、考えたり調べたりすると、
以下の理由が出てきました。

- 実際に計算が合致する。
- 実数を拡張して定義されている。

:::details 詳細
- 実際に計算が合致する。
$z = re^{i\theta}$とすると、$|z|^2 = z \overline{z} = r^2 e^{i\theta} e^{-i\theta} = r^2$となる。
虚数を掛けると絶対に偏角を打ち消しあい、複素平面における$z$の半径$r$も出てくる。

- 実数を拡張して定義されている。
実数の公理では、

$$
\forall \bm{a}, \langle \bm{a}, \bm{a} \rangle \geq 0 \\
等号成立は、\bm{a} = 0の時に限る
$$

があった。複素数においてもこれを満たすように定義したい。

虚数をつけない定義にすると、
例えば$\bm{a} = (1, i)$の時に、$\langle \bm{a}, \bm{a} \rangle = 1 + i^2 = 0$となり、$\bm{a} = 0$の時に限らない。
なので、共役をつけて定義して、実数の拡張になるように定義されている。
:::

今ノルムを考えていましたが、内積の定義では、ノルムを表すために必要な条件を満たすように定義されています。
ざっくりいうと滑らかに拡張できるように定義されているということです。
なので、共役で定義されているということになります。





## フーリエ変換の式から共役の恩恵を考える

「うーん、フーリエ変換の式から共役の恩恵が直接感じられないなぁ」

フーリエ変換の式からもなぜ共役にするか考えました。

$sin(\omega t),cos(\omega t)$などは$y=0$を中心として振動します。
また、$e^{i\omega t} = cos(\omega t) + i sin(\omega t)$も、振動します。
このような関数の積分は、$(-\infty, \infty)$の積分は正と負の面積で打ち消しあうため$0$になります。

$$
\int_{-\infty}^{\infty} (対称に振動する関数) dt = 0
$$

逆に、振動しない関数は、$(-\infty, \infty)$の積分は値を持つことになります。

$$
\int_{-\infty}^{\infty} (振動しない関数、定数など) dt > 0
$$

以上のことから、**$f(t)$の$e^{i\omega t}$成分を抽出するためには、$e^{i\omega t}$の逆位相成分の$e^{-i\omega t} = \overline{e^{i \omega t}}$を掛けて打ち消せば取り出せそう**です。

具体例を示します。
$f(t) = a e^{i\omega t} + b e^{i (3\omega) t} + c e^{i (8\omega) t}$とします。
この時、$F(\omega)$は次のようになります。

$$
F(\omega) = \int_{-\infty}^{\infty} f(t) e^{-i \omega t} dt \\
= \int_{-\infty}^{\infty} (a e^{i\omega t} + b e^{i (3\omega) t} + c e^{i (8\omega) t}) e^{-i \omega t} dt \\
= \int_{-\infty}^{\infty} (a + b e^{i (2\omega) t} + c e^{i (7\omega) t}) dt \\
= a \int_{-\infty}^{\infty} dt
$$

よって、$f(t)$の$e^{i\omega t}$成分である$a$を取り出せることがわかります。

$\omega$は変数で、各$\omega$に対して、$f(t)$の$e^{i\omega t}$成分を取り出し関数にしたものが$F(\omega)$です。
これを考えれば、フーリエ変換の式がすんなり理解できませんか？

# フーリエ変換は広義積分

フーリエ変換は$(\infty, \infty)$の積分を考えています。
なので、値を抽出しても、その値自体発散してしまうのでは？と疑問に思いました。

結論はデルタ関数$\delta(\omega)$で考えられるということです。
デルタ関数は、$\omega = 0$の時に$\infty$、それ以外の時は$0$になる関数です。

$f(t)=1$の時の具体例で示します。
この$f(t)$の周波数をフーリエ変換で抽出できると仮定すると、この関数は偏角が$0$の成分を持つので、
$\omega = 0$の時に何か値を持ち、それ以外は$0$になりそうだと考えられます。
実際に計算すると、$\mathcal{F}(1) = \delta(\omega)$となります。

:::details 計算過程

$$
\mathcal{F}(1) = \int_{-\infty}^{\infty} 1 \cdot e^{-i \omega t} dt \\
= \int_{-\infty}^{\infty} e^{-i \omega t} dt \\ 
= \lim_{T \to \infty} \int_{-T}^{T} e^{-i \omega t} dt \\
= \lim_{T \to \infty} \left[ \frac{e^{-i \omega t}}{-i\omega} \right]_{-T}^{T} \\
= \lim_{T \to \infty} \frac 1 {-i\omega} \left( e^{-i \omega T} - e^{i \omega T} \right) \\
= \lim_{T \to \infty} \frac 2 {\omega} \left( \frac {e^{i \omega T} - e^{-i \omega T}}{2i} \right) \\
= \lim_{T \to \infty} \frac 2 {\omega} \sin(\omega T) \\
= 
\begin{cases}
\infty & (\omega = 0) \\
0 & (\omega \neq 0)
\end{cases}
\\
= \delta(\omega)
$$

:::

よって値は発散するけど、$\delta(\omega)$で考えられるということがわかりました。

しかしこれでは実用上は使えません。実用上ではどうするのでしょうか。
音声信号などは、実際には有限の時間で観測されるので、窓関数を使って近似することが多いです。
窓関数とは、信号の一部を切り取る関数のことです。
ハミング窓と呼ばれる$w(t) = \frac{1}{2} (1 - \cos(\frac{2\pi t}{T}))$は、元の関数$f(t)$を$w(t)$で掛け算して、$f(t)w(t)$とすることで、信号の一部を切り取ることができます。
この切り取られた関数の両端は$0$に漸近するので、フーリエ変換を計算しても発散しません。
ただしあくまでも近似であるので、窓の形によっては大きな誤差が出ます。
良い窓では、主要な周波数成分が高い値になり、そこが頂点となるような山の形を示します。
窓関数に関してはここでは論点から外れすぎるため、詳しくは触れませんが、
興味がある人は、調べてみてください。
メインローブ、サイドローブ、ブラックマン窓、ハミング窓、ハン窓


# $e^{i\omega t}$成分とは？

先ほどはさらっと流しましたが、$e^{i\omega t}$成分とはなんでしょうか。
複素平面での半径1の円上を回る点ということはわかりますが、
実数関数$f(t)$の、複素関数$e^{i\omega t}$成分とはなんでしょうか。

結論を先に述べると、$e^{i\omega t}$成分は、周波数$\omega$の成分を表すものです。
これもなんとなくわかりますが、式で考えるとどういうことでしょうか。

一度実数で考えてみましょう。
フーリエ変換の元となる複素フーリエ級数展開の式の導出の過程では、$f(t)$は三角関数の和で表されると仮定して、
$cos(n t)$と$sin(n t)$の成分を取り出すこと考え、それぞれ$f(t)$と掛け合わせて積分することで、取り出していました。
同じ角周波数の積では値が残り、違う各周波数同士だと$0$になるということを利用していました。

$$
f(t) = \sum_{n=0}^{\infty} a_n cos(n t) + b_n sin(n t) \\
a_n = \frac{1}{T} \int_{0}^{T} f(t) cos(n t) dt \\
b_n = \frac{1}{T} \int_{0}^{T} f(t) sin(n t) dt\\
\left(
    \because
    \int_{0}^{T} cos(n t) cos(m t) dt = \frac{T}{2} \delta_{mn}, 
    \int_{0}^{T} sin(n t) sin(m t) dt = \frac{T}{2} \delta_{mn} 
\right)
$$

ここで一つ疑問として思ったのが、
$cos(n t) = sin(n t + \frac{\pi}{2})$なので、各周波数$n$の成分を取り出すだけなら、
$sin(n t)$だけでよくない？ということです。

でも実際には、取り出せない成分があります。それは、偶関数、奇関数に関係してきます。

## 偶関数・奇関数

任意の関数$f(t)$は、偶関数成分と奇関数成分の和で表現されます。

$$
f(t) = f_{\text{even}}(t) + f_{\text{odd}}(t) \\
f_{\text{even}}(t) = \frac{f(t) + f(-t)}{2} \\
f_{\text{odd}}(t) = \frac{f(t) - f(-t)}{2}
$$

この視点から考えると、$sin(\omega t)$で取り出せるものはなんでしょうか？

$$
\int_{-\infty}^{\infty} f(t) sin(\omega t) dt \\
= \int_{-\infty}^{\infty} f_{\text{odd}}(t) sin(\omega t) dt + \int_{-\infty}^{\infty} f_{\text{even}}(t) sin(\omega t) dt \\
= \int_{-\infty}^{\infty} f_{\text{odd}}(t) sin(\omega t) dt + 0 \\
$$

ここからわかる通り、$sin(\omega t)$は、$f(t)$の偶関数成分を取り出せていないことがわかります。
なので、例えば$f(t)$に$cos$成分しかない場合、$sin(\omega t)$では0になってしまい、何も取り出せないことがわかります。

## ただの三角関数では位相によって取り出せる成分が変わる

もう少し考察してみましょう。
$f(t)=sin(at+b)$として、$a,b$を変化させ、何が取り出せるかを見てみます。

$$
\int_{-\infty}^{\infty} f(t) sin(\omega t) dt \\
= \int_{-\infty}^{\infty} sin(at+b) sin(\omega t) dt \\
= \int_{-\infty}^{\infty} \frac{1}{2} \left( cos((a-\omega)t+b) - cos((a+\omega)t+b) \right) dt \\
=
\frac{1}{2} cos(b) \delta(a - \omega) - \frac{1}{2} cos(b) \delta(a + \omega) \\
$$

よって、同周波数$\omega$の成分に対して、成分を取り出せていますが、
我々が本来取り出したい値は、$cos(b)$以外の$\frac 1 2$の部分です。
位相$b$の値が変わることで、本来取り出したい成分を$cos(b)$が邪魔していることがわかります。
$b=0$では、正確に取り出せますが、$b=\frac{\pi}{4}$では、本来の値に$\frac 1 {\sqrt{2}}$がかかってしまったり、$b=\frac{\pi}{2}$では、$0$になってしまいます。

ここから$\pi$の周期で取り出せたり出せたり出せなかったりしています。ということは、$f(t)$自体が位相が変わることで偶関数と奇関数に変化している？と気づきます。
実際に、分解するとそうであることが分かります。

$$
sin(at+b) = cos(b) \underbrace{sin(at)}_{\text{偶関数}} + sin(b) \underbrace{cos(at)}_{\text{奇関数}}
$$

なるほどなるほど、なら$sin(\omega t)$で積分してから、$cos(b)$で割り算して、本来の値を取り出せる？
これは$cos(b) = 0$の時に場合分けが発生したりと、面倒くさいし、美しくありませんね。

ここで$e^{i\omega t}$の登場です。

$e^{i\omega t}$を考えると、超美しく整理できるじゃないか！
本来取り出したい成分$|F(\omega)|$は、$F(\omega)$のノルムだから、$F(\omega)$の共役を掛け算するだけで取り出せる！

$$
F(\omega) = |F(\omega)| e^{i (arg(F(\omega)))} \\
|F(\omega)|^2 = F(\omega) \overline{F(\omega)} \\
$$

しかもさっきまで考えていた三角関数で取り出すことと実質やっていることは同じで、
$sin$と$cos$での取り出しを同時に行っている！
$arg(F(\omega))$で偏角も表現して保持できる！（先ほどの三角関数で行っていた偏角$b$に相当する）

$f(t) = cos(\omega_0 t + \phi_1) + sin(\omega_0 t+\phi_2)$の時、

$$
F(\omega) = \frac 1 2 (e^{i\phi_1} + ie^{i\phi_2}) \delta(\omega - \omega _0) + \frac 1 2 (e^{-i\phi_1} - ie^{-i\phi_2}) \delta(\omega + \omega _0)
$$

::: details 計算過程

三角関数を経由

$$
\int _{-\infty}^{\infty} f(t) cos(\omega t) dt\\
= \frac 1 2 (cos(\phi_1) + sin(\phi_2)) \delta(\omega + \omega _0) + \frac 1 2 (cos(\phi_1) + sin(\phi_2)) \delta(\omega - \omega _0)\\
$$

$$
\int _{-\infty}^{\infty} f(t) sin(\omega t) dt \\
= \frac 1 2 (sin(\phi_1) - cos(\phi_2)) \delta(\omega + \omega _0) + \frac 1 2 (-sin(\phi_1) + cos(\phi_2)) \delta(\omega - \omega _0)\\
$$

$$
F(\omega) = \int_{-\infty}^{\infty} f(t) e^{-i \omega t} dt \\
= \int_{-\infty}^{\infty} f(t) (cos(\omega t) - i sin(\omega t)) dt \\
= \int_{-\infty}^{\infty} f(t) cos(\omega t) dt - i \int_{-\infty}^{\infty} f(t) sin(\omega t) dt \\
= \frac 1 2 (cos(\phi_1) - i sin(\phi_1) + sin(\phi_2) + i cos(\phi_2)) \delta(\omega + \omega _0) \\
+ \frac 1 2 (cos(\phi_1) + i sin(\phi_1) + sin(\phi_2) - i cos(\phi_2)) \delta(\omega - \omega _0) \\
= \frac 1 2 (e^{-i\phi_1} + ie^{-i\phi_2}) \delta(\omega + \omega _0) + \frac 1 2 (e^{i\phi_1} - ie^{i\phi_2}) \delta(\omega - \omega _0)\\
$$

$e^{-i\omega t}$を経由

$$
f(t) = cos(\omega t + \phi_1) + sin(\omega t+\phi_2)\\
= \frac 1 2 \left( e^{i(\omega t + \phi_1)} + e^{-i(\omega t + \phi_1)} \right) + \frac 1 {2i} \left( e^{i(\omega t + \phi_2)} - e^{-i(\omega t + \phi_2)} \right)\\
$$

$$
F(\omega) = \int_{-\infty}^{\infty} f(t) e^{-i \omega t} dt \\
= \int_{-\infty}^{\infty} \left\{ \frac 1 2 \left( e^{i(\omega t + \phi_1)} + e^{-i(\omega t + \phi_1)} \right) + \frac 1 {2i} \left( e^{i(\omega t + \phi_2)} - e^{-i(\omega t + \phi_2)} \right) \right\} e^{-i \omega t} dt\\
= \frac 1 2 \left( \int_{-\infty}^{\infty} e^{i(\omega t + \phi_1 - \omega t)} dt + \int_{-\infty}^{\infty} e^{-i(\omega t + \phi_1 + \omega t)} dt \right) \\
+ \frac 1 {2i} \left( \int_{-\infty}^{\infty} e^{i(\omega t + \phi_2 - \omega t)} dt - \int_{-\infty}^{\infty} e^{-i(\omega t + \phi_2 + \omega t)} dt \right)\\
= \frac 1 2 e^{i\phi_1} \delta(\omega - \omega _0) + \frac 1 2 e^{-i\phi_1} \delta(\omega + \omega _0) \\
+ \frac 1 {2i} e^{i\phi_2} \delta(\omega - \omega _0) - \frac 1 {2i} e^{-i\phi_2} \delta(\omega + \omega _0) \\
= \frac 1 2 (e^{i\phi_1} + ie^{i\phi_2}) \delta(\omega - \omega _0) + \frac 1 2 (e^{-i\phi_1} - ie^{-i\phi_2}) \delta(\omega + \omega _0)\\
$$

:::

# まとめ

フーリエ変換は、すごい




