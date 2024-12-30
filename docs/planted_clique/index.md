---
title: 埋め込みクリーク問題
nav_order: 5
---
# 埋め込みクリーク問題

グラフ$G=(V,E)$に対し, 頂点部分集合$C\subseteq V$は$\binom{C}{2}\subseteq E$を満たすとき**クリーク**と呼び, 特に頂点数$k$のクリークを$k$-クリークと呼びます.
入力として与えられたグラフ$G=(V,E)$のクリークのうち頂点数最大のものを求める問題を**最大クリーク問題**と呼び, 古典的なNP困難問題の一つです.

**埋め込みクリーク問題 (Planted Clique Problem)**とは, 最大クリーク問題の平均時計算量解析の文脈で現れる問題で, 端的に言うとランダムな場所に大きいクリーク$C$を埋め込まれたランダムグラフが入力として与えられたときに, クリーク$C$を求めよという問題です.
この問題の計算量的困難性は, 高次元統計学など幅広い分野の問題の計算量的下界を導くのみならず, 暗号学的プリミティブの構成(一方向性関数)などにも応用されています.
暗号学的プリミティブの安全性は素因数分解や[Learning with Error](../Learning%20with%20Error/)やその他の代数的な問題の困難性に依拠していますが,
一方で最大クリークといった組み合わせ的な問題の困難性に依拠したプリミティブ(特に公開鍵暗号方式)の構成は知られておらず, 重要な研究テーマと認識されています.

# 背景

最大クリーク問題はNP完全なので最悪時の意味では広くその困難性が信じられています.
実はそれだけではなく, 平均時計算量の意味でも最大クリークは多項式時間で解けないであろうと予想されています.
具体的にはグラフ$G$がErdős--Rényiグラフからサンプリングされた時に最大クリークを見つけられるかを議論します.
Erdős--Rényiグラフとは, $n$個の頂点を持ち, 各頂点ペアを独立に同じ確率$p$で辺で結んで得られるランダムなグラフです.
そのようなランダムグラフの分布を$\mathcal{G}(n,p)$と表します.
定義より

$$
  \begin{align*}
    \Pr_{G\sim \mathcal{G}(n,p)}[G=H] = p^{|E(H)|}\cdot (1-p)^{\binom{n}{2} - |E(H)|}
  \end{align*}
$$

が成り立ちます. 特に$p=1/2$のとき, 上式の右辺は$H$に依存しない値になるので, $G(n,1/2)$は$n$頂点グラフ全体の集合から一様ランダムに選ばれたグラフとなります.
本ノートでは$p=1/2$のケースを主に考えます (他の$p$でも似たような議論は適用できます).

$p=1/2$の場合, $G\sim \mathcal{G}(n,1/2)$は確率$1-o(1)$で最大クリークのサイズが$\approx 2\log_2 n$となることが知られています.
一方, 適当な単一頂点からスタートして一つずつ頂点を追加していくと極大クリークが得られますが,
このようにして得られる極大クリークの頂点数は高確率で$\approx \log_2 n$となることが知られています (ここでの「高確率」とは$G\sim \mathcal{G}(n,1/2)$の選択に関する確率).
したがって単純な貪欲法は$\mathcal{G}(n,1/2)$上では最大クリーク問題を近似率$1/2$で解くことになりますが, 実は近似率$0.501$達成する多項式時間アルゴリズムは知られていません.

# 問題の定義
$\mathcal{G}(n,1/2)$上の最大クリーク問題では本質的に, サイズ$2\log_2 n$のクリークが含まれていることが**わかっている**グラフが与えられたときにそのクリークを見つけ出せるかが問われていました.
そこで, より一般にサイズ$k \gg \log n$のクリークが含まれていることがわかっているグラフが与えられたときにその$k$-クリークを見つけ出せるかという問題を考えます.

{: .definition }
> パラメータ$n,k\in \mathbb{N}$, $p\in [0,1]$ (ただし$k\le n$) に対し, 
> $\mathsf{PC}(n,p,k)$を以下の手続きによって定まる分布とする.
> 1. $G \sim \mathcal{G}(n,p)$を生成する. 頂点集合を$V(G)=[n]$とする.
> 2. 一様ランダムな$k$-頂点部分集合$C\sim \binom{[n]}{k}$を選ぶ.
> 3. グラフ$G'=\left([n],E(G)\cup \binom{C}{2}\right)$に対し, $(G',C)$を$\mathsf{PC}(n,p,k)$のサンプルとして出力する.
>
> 特に$p=1/2$の時は$\mathsf{PC}(n,p,k)$を省略して$\mathsf{PC}(n,k)$で表す.

[LWE問題](../Learning%20with%20Error/)と同様に, 埋め込みクリーク問題においても探索や判定などの問題設定が考えられています. 

## 埋め込みクリーク探索問題
探索問題では埋め込まれたクリークを復元せよという問題を考えます.

{: .definition }
> パラメータ$n,k\in \mathbb{N}$ (ただし$k\le n$) に対し, 分布$\mathsf{PC}(n,k)$に従って選ばれた$(G',C)$を考える. 入力として$G'$を受け取り, $C$を出力せよという問題を**埋め込みクリーク探索問題**という.
>
> アルゴリズム$\mathcal{A}$は
>
> $$
  \begin{align*}
    \Pr_{(G',C) \sim \mathsf{PC}(n,k)}\left[ \mathcal{A}(G') = C \right] \ge \gamma
  \end{align*}
> $$
>
> を満たすとき, **埋め込みクリーク探索問題を成功確率$\gamma$で解く**という (もしくは単に成功確率$\gamma$で埋め込みクリークを探すという).

ここでは考えるランダムグラフの辺密度を$1/2$に固定していますが, より一般に$p \in [0,1]$でパラメタライズされた設定も考えることができます.

## 埋め込みクリーク判定問題
判定問題ではクリークが埋め込まれたグラフ$G'$をErdős--Rényiグラフ$G(n,1/2)$と識別する問題を考えます.
もう少し詳しくいうと, 埋め込みクリーク判定問題ではアルゴリズムの入力として一つのグラフが与えられます.
ただしこのグラフは次のどちらかの分布に従って生成されるとします:
1. $(G,C) \sim \mathsf{PC}(n,k)$に対して$G$.
2. $G \sim \mathcal{G}(n,1/2)$.
このとき, アルゴリズムの入力はどちらの分布から生成されたかを当てる問題が判定問題です.

{: .definition }
> 出力値が0または1のアルゴリズム$\mathcal{A}$は
> 
> $$
  \begin{align*}
    \left| \Pr_{(G,C) \sim \mathsf{PC}(n,k)}[\mathcal{A}(G) = 1] - \Pr_{G\sim \mathcal{G}(n,1/2)}[\mathcal{A}(G) = 1]  \right| \ge \gamma
  \end{align*}
> $$
> 
> を満たすとき, **埋め込みクリーク判定問題をアドバンテージ$\gamma$で解く**という.