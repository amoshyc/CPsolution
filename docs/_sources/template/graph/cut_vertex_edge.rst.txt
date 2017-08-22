###################################################
Cut Vertex/Edge
###################################################

.. sidebar:: Tags

    - ``tag_cut_vertex``
    - ``tag_cut_edge``

.. contents:: TOC
    :depth: 3

********************
原理
********************

割點，割邊都使用 dfs tree + low 值 來找::

    dfn[u] = 展開 dfs tree 時，u 是第幾個被探索到的
    low[u] = u 與 u 的後代，只使用 back edge(s)，所能到達的祖先中的最小 dfn

可用下列程式碼在 dfs tree 上維護 dfn, low

.. code-block:: cpp

    void dfs(int u, int p) {
        dfn[u] = low[u] = dfn_idx++;

        for (int v : g[u]) {
            if (v == p) continue;

            if (dfn[v] == -1) { // v not visited
                dfs(v, u);
                low[u] = min(low[u], low[v]);
            }
            else {
                low[u] = min(low[u], dfn[v]);
            }
        }
    }

一個點 u 是割點若且唯若滿足下列條件之一::

    1. u 是 root 且 u 的子節點數 > 1
    2. u 不是 root 且存在 (u, v) 滿足 low[v] >= dfn[u]。

一條邊 (u, v) 是割邊若且唯若::

    dfn[u] < low[v]

************************
程式碼
************************

.. code-block:: cpp
    :linenos:

    #define st first
    #define nd second
    #define sz(x) (int(x.size()))
    typedef pair<int, int> pii;

    const int MAX_V = 100;
    const int INF = 0x3f3f3f3f;

    int dfn_idx;
    int dfn[MAX_V];
    int low[MAX_V];
    bool iscutv[MAX_V];
    vector<pii> cutE;

    int V;
    vector<int> g[MAX_V];

    void dfs(int u, int p) {
        dfn[u] = low[u] = dfn_idx++;

        int cnt = 0;
        for (int v : g[u]) {
            if (v == p) continue;

            if (dfn[v] == -1) {
                cnt++;
                dfs(v, u);
                low[u] = min(low[u], low[v]);
                // cut vertex (articulation point)
                if ((p == -1 && cnt > 1) || (p != -1 && dfn[u] <= low[v])) {
                    iscutv[u] = true;
                }
                // cut edge (bridge)
                if (dfn[u] < low[v]) {
                    cutE.push_back(pii(min(u, v), max(u, v)));
                }
            }
            else {
                low[u] = min(low[u], dfn[v]);
            }
        }
    }

    void find_cut() {
        dfn_idx = 0;
        fill(dfn, dfn + V, -1);
        fill(low, low + V, INF);
        fill(iscutv, iscutv + V , false);
        cutE.clear();
        for (int v = 0; v < V; v++) {
            if (dfn[v] == -1) {
                dfs(v, -1);
            }
        }
    }

************************
模板驗證
************************

`uva315 <../../uva/p315.html>`_ （cut vertex）
`uva796 <../../uva/p796.html>`_ （cut edge）
