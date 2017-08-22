###################################################
Lucas's Theorem
###################################################

.. sidebar:: Tags

    - ``tag_lucas``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
程式碼
************************

求取 :math:`\binom{n}{m} \pmod p`

.. math::

    &\binom{n}{m} \equiv \prod_{i=0}^k \binom{n_i}{m_i} \pmod p \\
    \text{where}& \\
    &n = n_kp^k+n_{k-1}p^{k-1}+\cdots +n_1p+n_0, \\
    &m = m_kp^k+m_{k-1}p^{k-1}+\cdots +m_1p+m_0 \\
    &p \text{ is prime}

等式右方的 :math:`n_i, m_i` 必小於 p，所以可以直接 factorial 建表或直接爆開。

.. code-block:: cpp
    :linenos:

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

************************
模板驗證
************************

`hdu5446 <../../other/nctu_2017_winter/pj.html>`_
