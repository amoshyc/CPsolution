###################################################
Second Best MST 次小生成樹
###################################################

.. sidebar:: Tags

    - ``tag_mst``
    - ``tag_lca``

.. contents:: TOC
    :depth: 2

************************
程式碼
************************

.. code-block:: cpp
    :linenos:

    const int INF = 0x3f3f3f3f;
    const int MAX_V = 100;
    const int MAX_LOG_V = 7;
    int V, E; // 記得初使化

    struct Dsu {
        int par[MAX_V];

        void init() {
            fill(par, par + V, -1);
        }

        int root(int a) {
            return ((par[a] < 0) ? a : (par[a] = root(par[a])));
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

        int size(int a) {
            return -par[root(a)];
        }
    };

    Dsu dsu;

    struct Edge {
        int u, v, w;
    };
    vector<Edge> edges;

    // btn[i][u] = u 前往它 2^i parent 的路上經過的最大權重
    // par[i][u] = u 的 2^i parent 是誰
    int dep[MAX_V]; // should be init to -1
    int btn[MAX_LOG_V][MAX_V];
    int par[MAX_LOG_V][MAX_V];

    // mst
    struct AdjE {
        int to, w;
    };
    vector<AdjE> g[MAX_V];

    void dfs(int u, int p, int d) {
        dep[u] = d;
        par[0][u] = p;
        for (auto e : g[u]) {
            if (e.to != p) {
                btn[0][e.to] = e.w;
                dfs(e.to, u, d + 1);
            }
        }
    }

    void build() {
        for (int u = 0; u < V; u++) {
            if (dep[u] == -1) {
                dfs(u, -1, 0);
            }
        }

        for (int i = 0; i + 1 < MAX_LOG_V; i++) {
            for (int u = 0; u < V; u++) {
                if (par[i][u] == -1 || par[i][par[i][u]] == -1) {
                    par[i + 1][u] = -1;
                    btn[i + 1][u] = 0;
                }
                else {
                    par[i + 1][u] = par[i][par[i][u]];
                    btn[i + 1][u] = max(btn[i][u], btn[i][par[i][u]]);
                }
            }
        }
    }

    int lca(int u, int v) { // 回傳 u, v 之間的最大權重
        int mx = -INF; // u, v 之間的最大權重

        if (dep[u] > dep[v]) swap(u, v);
        int diff = dep[v] - dep[u];
        for (int i = MAX_LOG_V - 1; i >= 0; i--) {
            if (diff & (1 << i)) {
                mx = max(mx, btn[i][v]);
                v = par[i][v];
            }
        }

        if (u == v) return mx;

        for (int i = MAX_LOG_V - 1; i >= 0; i--) {
            if (par[i][u] != par[i][v]) {
                mx = max(mx, btn[i][u]);
                mx = max(mx, btn[i][v]);
                u = par[i][u];
                v = par[i][v];
            }
        }
        // lca = par[0][u] = par[0][v];
        mx = max(mx, max(btn[0][u], btn[0][v]));

        return mx;
    }

    int main() {
        int TC; scanf("%d", &TC);
        for (int tc = 1; tc <= TC; tc++) {
            scanf("%d %d", &V, &E);
            printf("Case #%d : ", tc);

            dsu.init();
            edges.clear();
            fill(dep, dep + V, -1);
            for (int u = 0; u < V; u++) {
                g[u].clear();
            }

            for (int _ = 0; _ < E; _++) {
                int u, v, w; scanf("%d %d %d", &u, &v, &w); u--; v--;
                edges.push_back((Edge) {u, v, w});
            }

            // mst
            sort(edges.begin(), edges.end(), [](const Edge& e1, const Edge& e2) {
                return e1.w < e2.w;
            });
            int total_w = 0;
            vector<Edge> non_mst_edges;
            for (auto e : edges) {
                if (!dsu.same(e.u, e.v)) {
                    total_w += e.w;
                    dsu.unite(e.u, e.v);
                    g[e.u].push_back((AdjE) {e.v, e.w});
                    g[e.v].push_back((AdjE) {e.u, e.w});
                }
                else {
                    non_mst_edges.push_back(e);
                }
            }

            if (dsu.size(0) != V) {
                puts("No way");
                continue;
            }

            // second mst
            build();
            int ans = INF;
            for (auto e: non_mst_edges) {
                int mx_w = lca(e.u, e.v);
                ans = min(ans, (total_w + e.w - mx_w));
            }

            if (ans == INF) {
                puts("No second way");
                continue;
            }

            printf("%d\n", ans);
        }

        return 0;
    }


************************
原理
************************

=======================
引理
=======================

.. default-role:: literal

要求次小生成樹（second MST），我們需要用到一個引理::

    Second MST 可由 MST **替換** 一條邊得到。

替換的意思是將 MST 的一條 tree edge 移除，此時 MST 變成兩個連接單元 `A, B`，這時從 `A, B` 的 cut edges 中選一條邊，將 `A, B` 重新連起來，形成一個相異於 MST 的生成樹。

以下給出 Second MST 不可能由 MST 替換二條邊得到，可推廣到更多數量（不保證正確，這證明是我自己想的）：

::

    Assume that
    T = Second best MST by substituting 2 edges from MST
      = MST - w_1 - w_2 + w_3 + w_4
      = MST + (w_3 - w_1) + (w_4 - w_2)

我們知道 `w_3 >= w_1, w_4 >= w_2` 否則 MST 就不是 MST 了。此時我們可以建構出一棵只替換一條邊的生成樹，權重和比 `T` 更小：

::

    new T = MST + (w_3 - w_1) <= T

所以假設不成立。

=================
演算法
=================

有了這個性質，我們可以設計出演算法：

1. 求出 MST
2. 枚舉 MST 中的每一條邊，將之替換（刪除並禁用這條邊，枚舉所有 cut edges 看換成哪條能產生最小的權重和）。

時間是 `O(ElgE + VE)`，解這題是就足夠了，不過還存在更好的演算法。

---------------------------

同樣利用上述性質，這次換成枚舉 cut edges：

1. 求出 MST
2. 枚舉每一條不在 MST 中的邊（即 cut edges） ``(u, v)`` ，將之加入 MST，此時必形成一個環（這由 MST 的性質保證），刪除環上除 ``(u, v)`` 外權重最大的邊，此時得到生成樹 ``T``，最小的 ``T`` 即為答案。

.. image:: https://imgur.com/Trspttn.png
    :width: 700px
    :alt: none

求環上除 ``(u, v)`` 外的最大邊事實上就是求 MST 中，``u`` 到 ``v`` 的路徑上權重最大的邊。這是標準的 Minimum/Maximum Bottleneck 問題，可以用 LCA 的倍增法解決，細節請參 uva 11354 。

=============
實作
=============

流程很清晰：

1. 找出 MST，用 Kruskal's Algorithm。
2. 倍增法 LCA 預處理
3. 枚舉所有 non-mst edges，針對 ``(u, v)``，用該邊替換掉 MST 中的 ``max-edge(u, v)``，從而得到新的生成樹。

整體時間是 `O(ElgE + VlgV + ElgV)`。

至於如何判斷給定的圖存不存在 MST，因為是使用 Kruskal，可以在做完後，看 DSU 裡是不是所有節點都在同一個集合中，若不是則代表圖本身就不連通，當然沒有 MST。而 Second Best MST 則可以透過檢查是否有 non-mst edge，所有邊都在 MST 中那當然就沒有 Second Best MST，沒有邊可以換~

************************
模板驗證
************************

- `uva 10462 <../../uva/p10462.html>`_