###################################################
BIT 2d
###################################################

.. sidebar:: Tags

    - ``tag_bit``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
程式碼
************************

.. code-block:: cpp
    :linenos:

    const int MAX_N, MAX_M;
    int N, M;
    ll bit[MAX_N + 1][MAX_M + 1]; // index start from 1

    void init() {
        for (int i = 1; i <= N; i++)
            fill(bit[i] + 1, bit[i] + M + 1, 0);
    }

    ll sum(int a, int b) {
        ll s = 0;
        for (int i = a; i > 0; i -= (i & -i))
            for (int j = b; j > 0; j -= (j & -j))
                s += bit[i][j];
        return s;
    }

    void add(int a, int b, ll x) {
        for (int i = a; i <= N; i += (i & -i))
            for (int j = b; j <= M; j += (j & -j))
                bit[i][j] += x;
    }

************************
模板驗證
************************

`poj2155 <https://ideone.com/5onr93>`_
