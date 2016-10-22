###################################################
Segment Tree
###################################################

.. sidebar:: Tags

    - ``tag_segment_tree``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
程式碼（延遲標記）
************************

.. code-block:: cpp
    :linenos:

    const int MAX_N = 100000;
    const int MAX_NN = (1 << 20); // should be bigger than MAX_N

    int N;
    ll inp[MAX_N];

    int NN;
    ll seg[2 * MAX_NN - 1];
    ll lazy[2 * MAX_NN - 1]; // lazy[u] != 0 : the subtree of u (u not included) is not up-to-date

    void seg_gather(int u) {
        seg[u] = seg[u * 2 + 1] + seg[u * 2 + 2];
    }

    void seg_push(int u, int l, int m, int r) {
        if (lazy[u] != 0) {
            seg[u * 2 + 1] += (m - l) * lazy[u];
            seg[u * 2 + 2] += (r - m) * lazy[u];

            lazy[u * 2 + 1] += lazy[u];
            lazy[u * 2 + 2] += lazy[u];
            lazy[u] = 0;
        }
    }

    void seg_init() {
        NN = 1;
        while (NN < N)
            NN *= 2;

        memset(seg, 0, sizeof(seg));    // val that won't affect result
        memset(lazy, 0, sizeof(lazy));  // val that won't affect result
        memcpy(seg + NN - 1, inp, sizeof(ll) * N); // fill in leaves
    }

    void seg_build(int u) {
        if (u >= NN - 1) { // leaf
            return;
        }

        seg_build(u * 2 + 1);
        seg_build(u * 2 + 2);
        seg_gather(u);
    }

    void seg_update(int a, int b, int delta, int u, int l, int r) {
        if (l >= b || r <= a) {
            return;
        }

        if (a <= l && r <= b) {
            seg[u] += (r - l) * delta;
            lazy[u] += delta;
            return;
        }

        int m = (l + r) / 2;
        seg_push(u, l, m, r);
        seg_update(a, b, delta, u * 2 + 1, l, m);
        seg_update(a, b, delta, u * 2 + 2, m, r);
        seg_gather(u);
    }

    ll seg_query(int a, int b, int u, int l, int r) {
        if (l >= b || r <= a) {
            return 0;
        }

        if (a <= l && r <= b) {
            return seg[u];
        }

        int m = (l + r) / 2;
        seg_push(u, l, m, r);
        ll ans = 0;
        ans += seg_query(a, b, u * 2 + 1, l, m);
        ans += seg_query(a, b, u * 2 + 2, m, r);
        seg_gather(u);

        return ans;
    }

************************
程式碼（單點更新）
************************

.. code-block:: cpp
    :linenos:

    const int INF = 0x3f3f3f3f;
    const int MAX_N = 50000;
    const int MAX_NN = (1 << 16); // bigger than MAX_N

    struct SegTree {
        int NN; // size of tree
        int seg[2 * MAX_NN]; // 0-based index, 2 * MAX_NN - 1 in fact
        int dflt; // default val

        void init(int n, int val) {
            dflt = val;
            NN = 1; while (NN < n) NN <<= 1;
            fill(seg, seg + 2 * NN, dflt);
        }

        void gather(int u, int l, int r) {
            seg[u] = min(seg[u * 2 + 1], seg[u * 2 + 2]);
        }

        void build(int u, int l, int r) {
            if (r - l == 1) return;
            int m = (l + r) / 2;
            build(u * 2 + 1, l, m);
            build(u * 2 + 2, m, r);
            gather(u, l, r);
        }

        int query(int a, int b, int u, int l, int r) {
            if (l >= b || r <= a) return dflt;
            if (l >= a && r <= b) return seg[u];
            int m = (l + r) / 2;
            int res1 = query(a, b, u * 2 + 1, l, m);
            int res2 = query(a, b, u * 2 + 2, m, r);
            return min(res1, res2);
        }

        void update(int idx, int x, int u, int l, int r) {
            if (idx < l || idx >= r) return;
            if (idx == l && r - l == 1) { seg[u] = x; return; }
            int m = (l + r) / 2;
            update(idx, x, u * 2 + 1, l, m);
            update(idx, x, u * 2 + 2, m, r);
            gather(u, l, r); // remember this
        }
    };

    SegTree seg;

    int main() {

        seg.init(N, INF);
        copy(A, A + N, seg + seg.NN - 1);
        seg.build(0, 0, seg.NN);

    }


************************
模板驗證
************************

 - [單點更新] `poj1769 <http://codepad.org/cQItxKrb>`_
 - [延遲標記] `poj3468 <http://codepad.org/ITp1iOPE>`_
