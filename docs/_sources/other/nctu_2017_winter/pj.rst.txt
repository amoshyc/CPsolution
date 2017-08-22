#####################################
[hdu5446] J. Unknown Treasure
#####################################

.. sidebar:: Tags

    - ``tag_lucas``
    - ``tag_mod_inv``
    - ``tag_crt``

.. contents:: TOC
    :depth: 2

*************************************************************
`題目 <https://vjudge.net/contest/147988>`_
*************************************************************

求 C(n, m) mod M，其中 M 是 k 個質數 p[i] 的積。

************************
Specification
************************

::

    1 <= m <= n <= 10^18
    1 <= k <= 10
    1 <= M <= 10^18
    1 <= p[i] <= 10^5

************************
分析
************************

.. note:: Lucas's theorem + CRT

直接求肯定 TLE，用 factorial 預建表 + 模反元素也會 TLE，得想個好方法。

說到求組合數，Lucas's theorem 說：

.. math::

    &\binom{n}{m} \equiv \prod_{i=0}^k \binom{n_i}{m_i} \pmod p \\
    \text{where}& \\
    &n = n_kp^k+n_{k-1}p^{k-1}+\cdots +n_1p+n_0, \\
    &m = m_kp^k+m_{k-1}p^{k-1}+\cdots +m_1p+m_0 \\
    &p \text{ is prime}

等式右方的 :math:`n_i, m_i` 必小於 p，所以可以直接 factorial 建表或直接爆開。

利用這個公式，我們可以在 :math:`O((\log_p n) \cdot p)` 計算出 :math:`\binom{n}{m} \pmod p`，注意 p 是質數，所以這題不適用。但我們知道 M 是許多質數的積，這讓我們想到了中國剩餘定理 `CRT <../../template/math/crt.html>`_。於是我們可以設計出一個演算法：

1. 用 lucas 求出:

    .. math::
        \binom{n}{m} &\pmod {p_1}, \\
        \binom{n}{m} &\pmod {p_2}, \\
        &\vdots \\
        \binom{n}{m} &\pmod {p_k}

   假設答案分別為 :math:`r_1, r_2, \cdots, r_k`

2. 我們得到聯立同餘方程:

    .. math::

        \begin{cases}
        \binom{n}{m} &\equiv r_1 \pmod {p_1} \\
        \binom{n}{m} &\equiv r_2 \pmod {p_2} \\
        &\vdots \\
        \binom{n}{m} &\equiv r_k \pmod {p_k}
        \end{cases}

   用 CRT 求解即可得到 :math:`\binom{n}{m} \pmod M`


************************
AC Code
************************

.. code-block:: cpp
    :linenos:

    #include <bits/stdc++.h>
    using namespace std;

    typedef long long ll;

    ll fast_pow(ll a, ll b, ll p) {
        ll ans = 1;
        ll base = a % p;
        b = b % (p - 1); // Fermat's little theorem
        while (b) {
            if (b & 1) {
                ans = (ans * base) % p;
            }
            base = (base * base) % p;
            b >>= 1;
        }
        return ans;
    }

    ll inv(ll a, ll p) {
        return fast_pow(a, p - 2, p);
    }

    ll C(ll n, ll m, ll p) {
        if (n < m) return 0;
        m = min(m, n - m);
        ll nom = 1, den = 1;
        for (ll i = 1; i <= m; i++) {
            nom = (nom * (n - i + 1)) % p;
            den = (den * i) % p;
        }
        return (nom * inv(den, p)) % p;
    }

    // To make C(n, m) % p computed in O(log(p, n) * p) instead of O(m)
    // https://en.wikipedia.org/wiki/Lucas's_theorem
    ll lucas(ll n, ll m, ll p) {
        if (m == 0) return 1;
        return C(n % p, m % p, p) * lucas(n / p, m / p, p) % p;
    }

    // Template of crt
    // https://amoshyc.github.io/CPsolution/template/math/crt.html

    struct Item {
        ll m, r;
    };

    ll extgcd(ll a, ll b, ll& x, ll&y) {
        if (b == 0) {
            x = 1;
            y = 0;
            return a;
        }
        else {
            ll d = extgcd(b, a % b, y, x);
            y -= (a / b) * x;
            return d;
        }
    }

    Item extcrt(const vector<Item>& v) {
        ll m1 = v[0].m, r1 = v[0].r, x, y;

        for (int i = 1; i < int(v.size()); i++) {
            ll m2 = v[i].m, r2 = v[i].r;
            ll g = extgcd(m1, m2, x, y); // now x = (m/g)^(-1)

            if ((r2 - r1) % g != 0)
                return {-1, -1};

            ll k = (r2 - r1) / g * x % (m2 / g);
            k = (k + m2 / g) % (m2 / g); // for the case k is negative

            ll m = m1 * m2 / g;
            ll r = (m1 * k + r1) % m;

            m1 = m;
            r1 = (r + m) % m; // for the case r is negative
        }

        return (Item) {m1, r1};
    }

    int main() {
        // printf("%lld\n", lucas(9, 5, 3));
        // printf("%lld\n", lucas(9, 5, 5));
        int TC;
        scanf("%d", &TC);
        while (TC--) {
            ll N, M, K;
            scanf("%lld %lld %lld", &N, &M, &K);
            vector<Item> items;
            for (int i = 0; i < K; i++) {
                ll p; scanf("%lld", &p);
                items.push_back((Item) {p, lucas(N, M, p)});
            }
            // for (auto x : items) {
            //     printf("C(%lld, %lld) mod %lld = %lld\n", N, M, x.m, x.r);
            // }
            printf("%lld\n", extcrt(items).r);
        }

        return 0;
    }
