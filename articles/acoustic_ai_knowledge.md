---
title: "音声関連研究の基礎知識をまとめる"
emoji: "🎤"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["AI", "音声"]
published: true
---


# はじめに

名古屋工業大学に所属し、音声合成の研究を行っている学生です。
この記事では、大学の研究活動や学会に参加して得られた知識を辞書のようにまとめます。
随時更新予定です。
それぞれ簡単に説明しますが、詳細に書けるものは別途リンク先に記載します。

# モデル構造

- DNN (Deep Neural Network)
全ての根源。
- CNN (Convolutional Neural Network)
画像分野発端？だが、音声分野では、1次元畳み込みで使われる。
RNNの記憶保持能力がないため、時間軸で近い情報同士を圧縮表現するのに使われる。硬いモデル。
- RNN (Recurrent Neural Network)
記憶保持能力があり、時系列データに強い。音声合成や音声認識で使われる。
- LSTM (Long Short-Term Memory)
RNNの一種。勾配消失問題を解決するために開発された。
言語分野では、通常のRNNより長い記憶を保持できるため、長い文章を生成するのに使われる。
単方向LSTMでは、過去の情報しか保持できないが、双方向LSTM(Bidirectional LSTM)では過去と未来の情報を保持できる。
双方向LSTMは、1次元CNNの柔らかいモデル、と考えることもできる。
- ResNet (Residual Network)
残差ブロックを用いたモデル。勾配消失問題を解決するために開発された。
DNNでは層を積み重ねれば積み重ねるほど性能が向上するわけではなく、ある一定の層数を超えると性能が低下する。
ResNetは、層を積み重ねても性能が向上するように設計されている。
- AR/NAR (Auto Regressive/Non-Auto Regressive)
自己回帰/非自己回帰。
自己回帰モデルは、過去の出力を次の入力として使うモデル。
非自己回帰モデルは、過去の出力を使わず、一度に全ての出力を生成するモデル。並列。
- AE (Auto Encoder)
エンコーダとデコーダからなるモデル。入力データを圧縮表現に変換し、それを元のデータに復元する。
自己教師あり学習が利点。
- VAE (Variational Auto Encoder)
AEでは、圧縮表現が離散的であるが、VAEでは連続的な表現の確率分布を学習する。
こちらも自己教師あり学習が利点。
- GAN (Generative Adversarial Network)
生成モデルと識別モデルが互いに競い合うモデル。
生成モデルは、本物のデータと区別できないデータを生成することを目指し、識別モデルは生成モデルが生成したデータを本物か偽物か判別することを目指す。
音声合成分野では、ボコーダに使われる。
- Transformer
"Attention is all you need"
Attention機構を用いたモデル。RNNのような明示的な記憶を持たないが、Attention機構により暗黙的に前後の情報を保持できる性質を持つ。
RNNと比較して並列生成できるため、学習が高速である。
双方向LSTMよりもさらに柔らかいモデルともいえる。
- BERT/HuBERT (Bidirectional Encoder Representations from Transformers)
Transformerを用いた言語モデル。双方向LSTMのように過去と未来の情報を保持できる。
音声分野では、音声認識に使われる傾向にある。
- flow
確率密度関数を変換するモデル。ベクトル空間によって、流れるように分布変換を行う。
- DDPM
画像分野が発端？
学習時に画像にノイズを加え、逆にノイズの取り除き方を学習し、生成時にノイズを取り除くことで画像を生成する。
音声分野では、メルスペクトログラムの画像に対して使われる。
- MoA(Mixture of Adapters)
複数のアダプタを用意して学習し、生成時に選択的に適切なアダプタを使うモデル。
- U-Net 
エンコーダとデコーダがU字型に結合されたモデル。画像分野で使われることが多いが、音声分野でも使われる。
- VQ-VAE (Vector Quantized Variational Auto Encoder)
VAEのエンコーダが離散的な表現を学習するモデル。
離散表現にするベクトル量子化では、コードブックのうち最も近いベクトルを選び、それを階層的に行う。
離散的表現にする意味は、離散的なカテゴリを学習できたり、離散トークンベースモデルとの組み合わせに有効。
- Manba (Masked Non-Local Block Attention)
状態空間モデルが核。
非局所的な情報を取り込むためのブロック。音声合成分野で使われる。
- DALL-E
画像生成モデル。画像とテキストの関連性を学習し、テキストから画像を生成する。
音声分野では、音声とテキストの関連性を学習し、テキストから音声を生成するモデルが考えられる。
- LLaMA
- LoRA

# 概念
- end-to-end
入力から出力までの全ての処理が一つのパスでつながっており、全体最適化ができるモデル。
- zero-shot
学習時に使われていないデータに対しても性能が高いモデル。
zero-shot声質変換では、変換したい話者の音声データは生成時に初めて与えられるため、学習時に与えることはできない。
- one-shot
学習時に1つのデータだけを使っても性能が高いモデル。
- few-shot
学習時に少量のデータを使っても性能が高いモデル。
- sequence-to-sequence
系列データを系列データに変換するモデル。
系列長の異なるデータ間の変換に使われる。
- ハイパーパラメータ
人力で設定するパラメータ。学習率やバッチサイズなどが該当する。
学習されるパラメータではない。
- 対照学習
似たようなデータは近い表現、似ていないデータは遠い表現になるように学習する手法。
話者認識、変換などでは、話者の音声データを近い表現に、異なる話者の音声データを遠い表現にすることが目的。
- パラ言語・非パラ言語
パラ言語とは、言語の外側にある情報。音声合成では、話者の音声データや音声の感情表現などが該当する。
非パラ言語とは、言語の内側にある情報。音声合成では、音声の音響特徴や音声の言語表現などが該当する。
- 転移学習
学習済みのモデルを新しいデータやタスクに適用する手法の総称。初期の段階では出力層のみを再学習し、その後必要に応じて他の層も調整することが多い。
- ファインチューニング
学習済みのモデルの一部またはすべてのパラメータを再学習する手法。特に高次の層を中心に再学習を行うことが多い。破壊的な学習を防ぐため、低レイヤーの学習率を低くすることが一般的。
- プリトレーニング
大規模なデータで学習済みのモデルを作る初期段階の学習プロセス。一般に、ファインチューニングや転移学習の基盤となる。
- 蒸留 (Teacher-Student Learning)
大きなモデル（Teacher）の知識を小さなモデル（Student）に伝達する手法。通常、ソフトターゲット（出力確率分布）を学習に利用し、精度を維持しながらモデルを軽量化できる。自己蒸留などの派生手法もある。
- アンサンブル
複数のモデルを組み合わせて、個々のモデルよりも高い精度を達成する手法。
- ドロップアウト
学習時にランダムに一部のニューロンを無効化する手法。過学習を防ぐ効果がある。
アンサンブルな側面も見られる。
- データ拡張
学習データを増やす手法。音声合成では、音声データにノイズを加えたり、速度を変えたりすることがある。
- Teatcher Forcing/ Free running
教師によって強制される/自由に生成されるという意味合い。いずれも学習手法。
AR構造で、Teacher Forcing学習では、教師の音声データを次の入力として使うが、Free running学習では、生成した音声データを次の入力として使う。
Teacher Forcingでは誤差が拡散せずに学習がスムーズになる。推論時には正解データがないため、学習と推論のギャップが発生する。
Free runningではデータがずれると誤差がどんどん拡大するため、学習が難しくなる。しかし、推論時に学習時と同じ状態になるため、学習と推論のギャップが少ない。
- scheduled Sampling
学習時に一定の確率で教師強制を行う手法。初期は教師強制を行い、徐々に生成データを使うようにすることで、学習と推論のギャップを緩和する。
Scheduled Sampling という手法では、最初はTeacher Forcingを使いながら、徐々にFree Runningへ移行することで、学習の安定性を確保する。
- LM(Language Model)
言語モデル。言語の文法や意味を学習するモデル。
- Chat-GPT
対話システムなどでは、chatGPTに質問文のプロンプトを直接投げて、回答文を生成させる手法が取られる。
- 学習
    - 教師あり学習 (Supervised Learning)
    入力データと正解データのペアで学習を行う手法。
    - 自己教師あり学習 (Self-Supervised Learning; SSL)
    SSLとよく略される。(?)
    入力データのみで学習を行う手法。
    - 半教師あり学習 (Semi-Supervised Learning ; semi-SSL)
    一部のデータには正解データがあり、一部のデータには正解データがない状態で学習を行う手法。
    最初に教師あり学習を行い、その後に自己教師あり学習を行うことが多い。
    



# 音声合成(TTS;Text-to-Speech)

- [HMM](https://zenn.dev/ryoiida0306/articles/hsmm_understanding) (Hidden Markov Model)
アライナーとして機能する。状態単位でモデル化によって明示的なアライメントを持つことができる。現在はあまり使われていない。
    - [HSMM](https://zenn.dev/ryoiida0306/articles/hsmm_understanding) (Hidden Semi-Markov Model)
    HMMの拡張版。状態の持続時間を考慮したモデル。
    - GMM+HSMM
    GMMを用いたHSMM。分布がGMMになる。
    - DNN+HSMM
    DNNを用いたHSMM。出力確率、継続長確率のガウス分布パラメータをDNNに出力させる。

- zero-shot音声合成
参照音声以外の大規模な音声データを用いて学習したモデルを使って、少量の参照音声データから音声合成を行う手法。
- WORLD
- STRAIGHT
- SPTK
- Encodec
コードブックを用いて音声を圧縮する手法。音声合成や音声認識で使われる。
- WaveNet
- Tacotron1/2
自己回帰的生成モデル
アテンション機構(Bahdanau Attention)を用いたモデル。
2では、Location-Sensitive Attentionを用いて、アテンションを徐々にずらすようにすることでアライメントを改善している。
- FastSpeech1/2
非自己回帰的生成モデル、並列生成で早い
duration predictorで、Tacotronのアライメントを改善している。
- Glow-TTS
メルスペクトログラムの画像＋DDPMを用いて音声を生成する手法。
- YourTTS
- Vall-E
- HiFi-GAN
- ParallelWaveGAN
- MelGAN
- VITS
音声合成と話者変換が同時にでき、かつend-to-endなモデル。
VAE, flow, GANを組み合わせたモデル。

- 評価
    - MOS (Mean Opinion Score)

    - MCD
    - F0RMSE
    - V/UV error rate

 


#  音声認識(ASR;Automatic Speech Recognition)

- マルチスピーカー音声認識
- shallow fusion
    - deep fusion
    - cold fusion

- CTC
    - Conformer+CTC
    - HMTL
- Transformer
- RNN-T(RNN-Transducer)
- LAS
- wav2vec1/2
- wavLM
- whisper
- SepFormer


# 音声変換(VC;Voice Conversion)

- VAE-VC
    - C-VAE
    - Beta-VAE
    - Cycle-VAE
    - AC-VAE
- VITS




# 音声分離(SE;Source Separation)

- ARMA
- ILRMA
- ARIMA

# 話者認識(SR;Speaker Recognition)

- x-vector
- i-vector
- ECAPA-TDNN

# 音声データセット

- VoxCeleb 1/2
- LJSpeech
- LibriSpeech
- CHiME

# その他

## 敵対的音声
- FGSM/I-FGSM

## 顔画像→音声
- CLIP


