#####################################
[hdu5441] E. Travel
#####################################

.. sidebar:: Tags

    - ``tag_offline``
    - ``tag_dsu``

.. contents:: TOC
    :depth: 2

*************************************************************
`題目 <https://vjudge.net/contest/147988>`_
*************************************************************

多筆測資。給定一個無向圖 G = (V, E)，每條邊有權重 d[i]。Q 筆詢問，每筆調問給定一個值 x，請問有多少點對 (a, b)，a, b 之間存在一條路徑連通，且路徑上所有邊的權重都 <= x。

※ (a, b), (b, a) 視為不同的點對。

************************
Specification
************************

::

    1 <= V <= 20000
    1 <= E <= 10^5
    1 <= Q <= 5000
    1 <= d[i] <= 10^5

************************
分析
************************

.. note:: 模擬

原來 hdu 可以用 c++11，爽~
這題想了一段時間才想出來~

----------------

明顯地，針對某個 x，我們可以把圖中所有權重 > k 的刪掉，剩下的邊分成一個或多個 components，每個 compoent 的 size 假設為 sz_comp[i]，那加總所有 sz_comp[i] * (sz_comp[i] - 1) 就是答案，即組合數 C(sz_comp[i], 2) 再乘 2 嘛~

但也明顯地，我們不可能針對每個 query 就做上述事情，一定 TLE。一個重要觀察是::

    假設我們求出 x = 6000 的解時，我們要求 x = 7000，
    我們只需將權重在 60001~7000 之間的邊加進圖，再分解成 components 來求解。

這可以設計出一個演算法::

    將 queryies 由小到大排序，將邊由權重小到大排序。用 dsu 維護 components。
    當一個 query 求完解，要求下一個 query 時，只需將權重在他們之間的邊加進圖（dsu）。

當將一個邊 (u, v) 加進 dsu 時，有 2 種情況：

1. u, v 已經在相同的 component 中，那對解沒影響

2. u, v 在不同 components 中，將他們合併，點對數會增加 2 * u * v 個。

注意，一開始沒加任何邊時，每個點自己都是一個 component。實作就直接下模板吧~

************************
AC Code
************************

.. code-block:: cpp
    :linenos:

    #include <bits/stdc++.h>
    using namespace std;

    const int MAX_N = 20000;

    struct Dsu {
        int par[MAX_N];

        void init(int V) {
            fill(par, par + V, -1);
        }

        int root(int a) {
            if (par[a] < 0) return a;
            return (par[a] = root(par[a]));
        }

        void unite(int a, int b) {
            a = root(a);
            b = root(b);
            if (a == b) return; // already in same set
            if (-par[a] > -par[b]) swap(a, b); // if (size[a] > size[b])
            par[b] += par[a]; // size[b] += size[a]
            par[a] = b;
        }

        bool same(int a, int b) {
            return root(a) == root(b);
        }

        int size(int a) { // correct if a is root
            return -par[a];
        }
    };

    struct Edge {
        int u, v, w;
        bool operator < (const Edge& e) const {
            return w < e.w;
        }
    };

    struct Query {
        int id, w, ans;
    };

    bool cmp_by_w(const Query& q1, const Query& q2) {
        return q1.w < q2.w;
    }
    bool cmp_by_id(const Query& q1, const Query& q2) {
        return q1.id < q2.id;
    }

    int N, M, Q;
    vector<Edge> edges;
    vector<Query> query;
    Dsu dsu;

    int main() {
        ios::sync_with_stdio(false);
        cin.tie(0);

        int TC;
        cin >> TC;

        while (TC--) {
            cin >> N >> M >> Q;

            edges.clear();
            query.clear();
            dsu.init(N);

            for (int i = 0; i < M; i++) {
                int u, v, w; cin >> u >> v >> w; u--; v--;
                edges.push_back((Edge) {u, v, w});
            }
            sort(edges.begin(), edges.end());

            for (int i = 0; i < Q; i++) {
                int w; cin >> w;
                query.push_back((Query) {i, w, -1});
            }
            sort(query.begin(), query.end(), cmp_by_w);

            int cnt = 0;
            int ptr = 0;
            for (Query& q : query) {
                while (ptr < M && edges[ptr].w <= q.w) {
                    Edge e = edges[ptr];
                    ptr++;

                    if (dsu.same(e.u, e.v)) {
                        continue;
                    }

                    int ru = dsu.root(e.u);
                    int rv = dsu.root(e.v);
                    int su = dsu.size(ru);
                    int sv = dsu.size(rv);

                    cnt += 2 * su * sv;
                    dsu.unite(e.u, e.v);
                }
                q.ans = cnt;
            }

            sort(query.begin(), query.end(), cmp_by_id);
            for (int i = 0; i < Q; i++) {
                cout << query[i].ans << endl;
            }
        }

        return 0;
    }
