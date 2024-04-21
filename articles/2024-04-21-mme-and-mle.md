---
title: "モーメント法と最尤推定法の関連性についてメモ"
emoji: "📔"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["計量経済学", "統計学"]
published: true
published_at: 2024-04-23 07:00:00
---

計量経済学の本を読んでいて「へー」と思ったのでメモ。

モーメント法は計量経済学だと教科書に載っていることが多い有名なトピックですが、他の分野出身の方からするとマニアックな方法だと思うので前提となる知識も最初に書いておきます。不要な方は前提知識のパートは読み飛ばしてください。


# 前提知識１：モーメント法

**モーメント法** （method of moment）は統計的推定における方法の 1 つで、母集団におけるモーメント（母平均など）を標本におけるモーメント（標本平均など）で置き換えて推定を行う方法です。

確率変数$X$の分布が$k$次元の未知のパラメータ$\theta = (\theta_1, \cdots, \theta_k)$をもつとします。このとき、

$$
E[m(X, \theta)]
= E
\begin{bmatrix}
m_1(X, \theta)\\
\vdots\\
m_k(X, \theta)\\
\end{bmatrix}
= 0
$$

という条件（モーメント条件）を満たすように$k$個の関数（スコア関数と呼ばれる）$m_1(\cdot, \cdot), \cdots, m_k(\cdot, \cdot)$を考えて、標本モーメントに置き換えてパラメータについて解く、という方法です。

## 例：正規分布の平均と分散

正規分布$N(\mu, \sigma^2)$から無作為抽出された$n$個のサンプル$X_1,X_2,\dots,X_n$があるとします。

原点のまわりの 1 次と 2 次のモーメントは

$$
\begin{aligned}
E[X] &= \mu\\
E[X^2] &= \sigma^2 + \mu^2
\end{aligned}
$$

であるため、モーメント条件を

$$
\begin{aligned}
E[m_1(X_i; \mu, \sigma^2)] &= E[X_i - \mu] = 0\\
E[m_2(X_i; \mu, \sigma^2)] &= E[X_i^2 - (\sigma^2 + \mu^2)]=0
\end{aligned}
$$

とします。この条件の標本対応

$$
\begin{aligned}
& \frac{1}{n} \sum_{i=1}^n (X_i - \hat{\mu}) = 0\\
& \frac{1}{n} \sum_{i=1}^n [X_i^2 - (\hat{\sigma}^2 + \hat{\mu}^2)] = 0
\end{aligned}
$$

を解くと

$$
\begin{aligned}
\hat{\mu} &= \frac{1}{n} \sum_{i=1}^n X_i\\
\hat{\sigma}^2 &= \frac{1}{n} \sum_{i=1}^n X_i^2 - \hat{\mu}^2\\
\end{aligned}
$$

という推定量が得られます。

## 例：線形回帰モデル

被説明変数$Y$と、$k$個の説明変数$X = (X_1, X_2, \dots, X_k)$について$n$​ 個のサンプルが独立に得られたとして、線形回帰モデル

$$
Y_i = X_i^\top \beta + u_i\\
E[X_i u_i] = 0
$$

のパラメータ$\beta$の推定をしたいとします。ここで$X_i$は$i$番目のサンプルについての説明変数のベクトルで、$X_i, \beta \in \mathbb{R}^k$です。$X_i$は確率変数であり、誤差項と無相関$E[X_i u_i] = 0$であると仮定します。

誤差項についての仮定をそのままモーメント条件として、$k$個の回帰係数について

$$
E[X_i u_i] = E[X_i (Y_i - X_i^\top \beta)] = 0
$$

という$k$​ 本の条件を作ります。これに対応する標本での条件は

$$
\frac{1}{n} \sum^n_{i=1} X_i u_i
= \frac{1}{n} \sum^n_{i=1} X_i (Y_i - X_i^\top \beta)
= 0
$$

であり、$y, u \in \mathbb{R}^n, X \in \mathbb{R}^{n\times k}$として行列表記にすると

$$
\frac{1}{n} X^\top u
= \frac{1}{n} X^\top (Y - X\beta) = 0
$$

です。これを解くと

$$
\frac{1}{n} X^\top Y = \frac{1}{n} X^\top X\beta\\
\to \beta = (X^\top X)^{-1}X^\top Y
$$

と、最小二乗推定量と同じ推定量が得られます。


# 前提知識２：線形回帰モデルの最尤推定量

線形回帰モデルの最尤推定量の話もややマニアックな気がするので書いておきます。

被説明変数$Y$と、$k$個の説明変数$X = (X_1, X_2, \dots, X_k)$について$n$個のサンプルが独立に得られたとして、線形回帰モデル

$$
Y_i = X_i^\top \beta + u_i\\
u_i \sim \mathcal{N}(0, \sigma^2 I)
$$

を考えます。説明変数$X$​​ は確率変数ではなく、誤差項が正規分布に従うという仮定を置いた **古典的正規線形回帰モデル** （classical normal linear regression model）と呼ばれるモデルです。

$u_i$が正規分布に従うため$Y_i$も正規分布$\mathcal{N}(Y_i| X_i^\top \beta, \sigma^2)$に従います。分布型を仮定しているため尤度関数$L(Y| X, \beta, \sigma)$が構築でき、その対数をとったものは

$$
\begin{aligned}
\ln L(Y| X, \beta, \sigma)
&= \sum^N_{i=1} \ln \mathcal{N}(Y_i| X_i^\top \beta, \sigma^2)
\\
&= - \frac{N}{2} \ln (2\pi)
    - \frac{N}{2} \ln \sigma^2
    - \frac{1}{2\sigma^2} \sum^N_{i=1} (Y_i - X_i^\top \beta)^2
\end{aligned}
$$

となります。これを最大化する$\beta$が知りたいので、対数尤度$\ln L(Y| X, \beta, \sigma)$を$\beta$で微分してゼロとおいた

$$
\begin{aligned}
\nabla \ln L(Y| X, \beta, \sigma)
&= \frac{1}{\sigma^2} \sum^N_{i=1} (Y_i - X_i^\top \beta)X_i^\top\\
&= \frac{1}{\sigma^2} (\sum^N_{i=1} Y_i X_i^\top)
 - \beta^\top \frac{1}{\sigma^2} (\sum^N_{i=1} X_i X_i^\top)\\
&= \frac{1}{\sigma^2} (X^\top Y)^\top
 - \beta^\top \frac{1}{\sigma^2} X^\top X\\
&= 0\\
\end{aligned}
$$

を$\beta$について解けば、最尤推定量は

$$
\beta = (X^\top X)^{-1} X^\top Y
$$

となります。

# モーメント法と最尤推定法

やっと本題です。

最尤推定量は対数尤度の導関数をスコア関数とした

$$
E\left[
    \frac{\partial \log f(Y|X; \theta)}{\partial \theta}
\right]
= 0
$$

という条件の下でのモーメント推定量と考えることができる。
という話です。

## 確率密度によるモーメント条件

$Y_i$の条件付き確率密度関数を$f(Y_i | X_i; \theta)$とします。確率密度関数の性質から、積分すると 1 になります。

$$
\int f(Y_i|X_i; \theta) dy_i = 1
$$

両辺を$\theta$について微分すると

$$
\int \frac{\partial f(Y_i|X_i; \theta)}{\partial \theta} dy_i = 0
$$

になります。ここで

$$
\begin{aligned}
\frac{\partial f(Y_i|X_i; \theta)}{\partial \theta}
&= \frac{\partial f(Y_i|X_i; \theta)}{\partial \theta} \frac{1}{f(Y_i|X_i; \theta)} f(Y_i|X_i; \theta)\\
&= \frac{\partial \log f(Y_i|X_i; \theta)}{\partial \theta} f(Y_i|X_i; \theta)
\end{aligned}
$$

という関係を用いると

$$
\int \frac{\partial \log f(Y_i|X_i; \theta)}{\partial \theta}
f(Y_i|X_i; \theta) dy_i = 0
$$

と書き換えることができます。

スコア関数を

$$
m(X_i, \theta) = \frac{\partial \log f(Y_i|X_i; \theta)}{\partial \theta}
$$

とおけば、

$$
\int m(X_i, \theta) f(Y_i|X_i; \theta) dy_i = 0
$$

となり、条件付き確率$f(Y_i|X_i; \theta)$による条件付き期待値

$$
E[m(X_i, \theta)\mid X_i] = 0
$$

となっています。繰り返し期待値の法則により

$$
E[m(X_i, \theta)] = 0
$$

なので、この式をモーメント条件として用いることができます。

## モーメント推定量と最尤推定量

モーメント法では標本モーメント条件

$$
\frac{1}{n}\sum^n_{i=1}
\frac{\partial \log f(Y_i|X_i; \theta)}{\partial \theta}
= 0
$$

を解いて推定量を得ます。

最尤推定法では対数尤度関数の最大化において

$$
\sum^n_{i=1} \frac{\partial \log f(Y_i|X_i; \theta)}{\partial \theta}
= 0
$$

を解くため、得られる推定量は同じものになります。


最尤推定法は「微分してゼロとおく」というような解析的な解き方ではなく数値的に解くこともあるので「最尤推定法を一般化したものがモーメント法」とまで強く言えるのかはわかりませんが、少なくとも「解析的に解いて求めるタイプの最尤推定量は対数尤度の導関数をスコア関数としたモーメント法である」と捉えることができそうです。


# 参考文献

- Hamilton, J. D. (1994). _Time series analysis_. Princeton university press.
- 難波明生. (2015). 計量経済学講義. 日本評論社.
- 西山慶彦, 新谷元嗣, 川口大司, & 奥井亮. (2019). 計量計済学. 有斐閣.
