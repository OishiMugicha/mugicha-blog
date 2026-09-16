---
title: 半単純表現の部分表現を図式で整理する
description: 定理の主張で詰まったので、包含写像と直和の図式に書き直してみる。
date: 2026-09-16
---

表現論の勉強ノートです。

半単純表現の部分表現についての命題を読んでいたのですが、そもそも何を主張しているのか掴めず、詰まってしまいました。そこで、主張を写像と図式の言葉で整理しておきます。

## 各成分の図式で書く

代数閉体 $k$ 上の $k$ 代数 $A$ と、有限次元の半単純な $A$ 表現

$$
V\cong\bigoplus_{i=1}^{m}n_iV_i
$$

を考えます。$V_1,\ldots,V_m$ は互いに非同型な既約表現で、$n_iV_i$ は $V_i$ を $n_i$ 個直和したものです。

この分解を固定し、各成分からの埋め込みを $\iota_i:n_iV_i\hookrightarrow V$ とします。また、任意の部分表現 $W\subseteq V$ を取り、その包含写像を $\phi:W\hookrightarrow V$ と書きます。

すると、ある整数 $0\le r_i\le n_i$ と $A$ 準同型

$$
\sigma_i:r_iV_i\longrightarrow W,
\qquad
\phi_i:r_iV_i\longrightarrow n_iV_i
$$

が存在して、次の二つを満たす、というのが主張です。

**1. 任意の $i$ について、次の図式が可換になる。**

$$
\begin{array}{ccc}
r_iV_i & \xrightarrow{\quad\phi_i\quad} & n_iV_i \\
{\scriptstyle\sigma_i}\,\Big\downarrow
&&
\Big\downarrow\,{\scriptstyle\iota_i} \\[4pt]
W & \xhookrightarrow{\qquad\phi\qquad} & V
\end{array}
\qquad (1\le i\le m)
$$

つまり、すべての $i$ について

$$
\phi\circ\sigma_i=\iota_i\circ\phi_i
$$

が成り立ちます。$r_iV_i$ から $W$ を通って $V$ に入れても、$n_iV_i$ を通って入れても、同じ写像になるということです。

**2. $\sigma_i$ たちが、$W$ の直和分解を与える。**

「$\sigma_i$ たちは直和」というのを式で書くと、これらから誘導される写像

$$
\begin{aligned}
\sigma:\bigoplus_{i=1}^{m}r_iV_i&\longrightarrow W,\\
(w_1,\ldots,w_m)&\longmapsto\sum_{i=1}^{m}\sigma_i(w_i)
\end{aligned}
$$

が同型になる、ということです。したがって、$W$ の元は $\sigma_i(w_i)$ たちの和として一意に書けます。

この条件から各 $\sigma_i$ は単射です。また、1の可換性と $\phi$ の単射性から、各 $\phi_i$ も単射になります。

要するに、各成分の図式を任意の $i$ について可換にする $\sigma_i,\phi_i$ たちが存在し、しかも $\sigma_i$ たちで $W$ 全体が直和に分かれる。この二つを合わせて、$W$ から $V$ への包含写像が、各 $r_iV_i$ から $n_iV_i$ への埋め込みの直和に分解する、という主張になるわけです。
