###################################################
排列組合
###################################################

.. sidebar:: Tags

    - ``tag_combination``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
公式
************************

==============
排列 P
==============

.. math::

    P^n_k = \frac{n!}{(n-k)!}

* 從 :math:`n` 個不同球中取 :math:`k` 個，**不同取出順序視為不同**，方法數為 :math:`P^n_k`
* :math:`P^4_2 = 12`

==============
圓排列 Q
==============

.. math::

    Q^n_k = \frac{n!}{k (n-k)!} = \frac{P^n_k}{k}

* 從 :math:`n` 個不同球中取 :math:`k` 個，**排成一個圓**，圓只考慮相對位置，方法數為 :math:`Q^n_k`
* :math:`Q^4_2 = 6`


==============
組合 C
==============

.. math::

    C^n_n &= C^n_0 = 1 \\
    C^n_k &= C^n_{n-k} = \frac{n!}{(n-k)!k!} = \frac{P^n_k}{k!} \\
    C^n_k &= C^{n-1}_{k-1} + C^{n-1}_{k}

* 從 :math:`n` 個不同球中取 :math:`k` 個， **取出順序不影響** ，方法數為 :math:`C^n_k`
* 考慮第 :math:`k` 顆球取或不取：取的話轉移成從 :math:`n-1` 個不同球中取出 :math:`k-1` 個；不取的話，轉移成從 :math:`n-1` 個球中取 :math:`k` 個，即 :math:`C^n_k = C^{n-1}_{k-1} + C^{n-1}_{k}`
* :math:`C^4_2 = 6`


==============
重複組合 H
==============

.. math::

    H^n_k = C^{n+k-1}_k = C^{n+k-1}_{n-1}

* 將 :math:`n` 顆 **相同的球** 丟進 :math:`k` 個 **可空、不同** 箱子，方法數為 :math:`H^n_k`
* :math:`x_1 + x_2 + \dots + x_n = k`，:math:`(x_1, x_2, \dots, x_n)` 非負整數解的個數為 :math:`H^n_k`
* :math:`x_1 + x_2 + \dots + x_n = k`，:math:`(x_1, x_2, \dots, x_n)` 正整數解的個數為 :math:`H^n_{k - n}`
* :math:`H^4_2 = 10`

===========================
Stirling Number（Type I)
===========================

.. math::

    S^n_0 &=0 \\
    S^n_1 &=1 \\
    S^n_k &= S^{n-1}_{k-1} + (n-1) S^{n-1}_{k}

* 將 :math:`n` 顆 **不同的球** 丟進 :math:`k` 個 **非空、相同** 箱子，每個箱子為一 **圓排列** ，方法數為 :math:`S^n_k`
* 考慮第 :math:`n` 顆球：可以自己一個箱子，即其他 :math:`n-1` 個球丟進 :math:`k-1` 個箱子；也可以 :math:`n-1` 個球丟進 :math:`k` 個箱子，這顆球插到任一顆球旁邊。所以得 :math:`S^n_k = S^{n-1}_{k-1} + (n-1) S^{n-1}_{k}`
* :math:`S^4_2 = 11`

===========================
Stirling Number（Type II)
===========================

.. math::

    S^n_n &= S^n_1 = 1 \\
    S^n_k &= S^{n-1}_{k-1} + k S^{n-1}_{k}

* 將 :math:`n` 顆 **不同的球** 丟進 :math:`k` 個 **非空、相同** 箱子，每一個箱子為一 **集合**，方法數為 :math:`S^n_k`
* 考慮第 :math:`n` 顆球：可以自己一個箱子，即其他 :math:`n-1` 個球丟進 :math:`k-1` 個箱子；也可以 :math:`n-1` 個球丟進 :math:`k` 個箱子，這顆球丟進其中一個箱子。所以得 :math:`S^n_k = S^{n-1}_{k-1} + k S^{n-1}_{k}`
* :math:`S^4_2 = 7`

************************
程式碼
************************

.. code-block:: cpp
    :linenos:

    const int MAX_N = 1e6;
    const ll M = 1e9 + 7;
    ll fac[MAX_N + 1];

    ll fast_pow(ll a, ll b) {
        ll ans = 1;
        ll base = a % M;
        while (b) {
            if (b & 1)
                ans = ans * base % M;
            base = base * base % M;
            b >>= 1;
        }
        return ans;
    }

    void fac_init() {
        fac[0] = 1;
        for (int i = 1; i <= N; i++)
            fac[i] = fac[i - 1] * i % M;
    }

    ll inv(ll n) {
        return fast_pow(n, M - 2) % M;
    }

    ll C(ll a, ll b) {
        // a! / (b! * (a-b)!)
        return fac[a] * inv(fac[b] * fac[a - b]) % M;
    }

************************
模板驗證
************************

`cf181C <../../other/nctu_sc_enum/pg.html>`_
