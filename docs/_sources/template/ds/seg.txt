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

    typedef long long ll;
    const int MAX_N = 100000;
    const int MAX_NN = (1 << 20); // bigger than MAX_N

    struct SegTree {
        int NN; // size of tree
        ll dflt; // default val
        ll seg[2 * MAX_NN]; // 0-based index, 2 * MAX_NN - 1 in fact
        ll lazy[2 * MAX_NN]; // 0-based index, 2 * MAX_NN - 1 in fact
        // lazy[u] != 0 <->
        // substree of u (u not inclued) is not up-to-date (it's dirty)

        void init(int n, ll val) {
            dflt = val;
            NN = 1; while (NN < n) NN <<= 1;
            fill(seg, seg + 2 * NN, dflt);
            fill(lazy, lazy + 2 * NN, dflt);
        }

        void gather(int u, int l, int r) {
            seg[u] = seg[u * 2 + 1] + seg[u * 2 + 2];
        }

        void push(int u, int l, int r) {
            if (lazy[u] != 0) {
                int m = (l + r) / 2;

                seg[u * 2 + 1] += (m - l) * lazy[u];
                seg[u * 2 + 2] += (r - m) * lazy[u];

                lazy[u * 2 + 1] += lazy[u];
                lazy[u * 2 + 2] += lazy[u];
                lazy[u] = 0;
            }
        }

        void build(int u, int l, int r) {
            if (r - l == 1) return;
            int m = (l + r) / 2;
            build(u * 2 + 1, l, m);
            build(u * 2 + 2, m, r);
            gather(u, l, r);
        }

        ll query(int a, int b, int u, int l, int r) {
            if (l >= b || r <= a) return dflt;
            if (l >= a && r <= b) return seg[u];
            int m = (l + r) / 2;
            push(u, l, r);
            ll res1 = query(a, b, u * 2 + 1, l, m);
            ll res2 = query(a, b, u * 2 + 2, m, r);
            gather(u, l, r); // data is dirty since previous push
            return res1 + res2;
        }

        void update(int a, int b, int x, int u, int l, int r) {
            if (l >= b || r <= a) return;
            if (l >= a && r <= b) {
                seg[u] += (r - l) * x; // update u and
                lazy[u] += x; // set subtree u is not up-to-date
                return;
            }
            int m = (l + r) / 2;
            push(u, l, r);
            update(a, b, x, u * 2 + 1, l, m);
            update(a, b, x, u * 2 + 2, m, r);
            gather(u, l, r); // remember this
        }
    };

    SegTree seg;

    int main() {

        seg.init(N, 0);
        copy(A, A + N, seg.seg + seg.NN - 1);
        seg.build(0, 0, seg.NN);
        
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
        copy(A, A + N, seg.seg + seg.NN - 1);
        seg.build(0, 0, seg.NN);

    }


************************
模板驗證
************************

 - [單點更新] `poj1769 <http://codepad.org/cQItxKrb>`_
 - [延遲標記] `poj3468 <http://codepad.org/pLLfo3d6>`_
