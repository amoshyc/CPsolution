###################################################
BIT
###################################################

.. sidebar:: Tags

    - ``tag_bit``
    - ``tag_template``

.. contents:: TOC
    :depth: 3

************************
程式碼
************************

.. code-block:: cpp
    :linenos:

    const int MAX_N = ...;
    int N;
    int bit[MAX_N + 1]; // 1-based index

    void init() {
        fill(bit + 1, bit + N + 1, 0);
    }

    int sum(int idx) {
        int s = 0;
        for (int i = idx; i > 0; i -= (i & -i))
            s += bit[i];
        return s;
    }

    void add(int idx, int x) {
        for (int i = idx; i <= N; i += (i & -i))
            bit[i] += x;
    }

************************
模板驗證
************************

`poj 3321 <../../p3321.html>`_
