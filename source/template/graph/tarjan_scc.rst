###################################################
Tarjan SCC Algorithm
###################################################

.. sidebar:: Tags

    - ``tag_scc``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
程式碼
************************

.. code-block:: cpp
    :linenos:

    #define sz(x) (int(x.size()))

    const int MAX_V = 10000;
    const int INF = 0x3f3f3f3f;
    int V;
    vector<int> g[MAX_V];

    int dfn_idx = 0;
    int scc_cnt = 0;
    int dfn[MAX_V];
    int low[MAX_V];
    int belong[MAX_V];
    bool in_st[MAX_V];
    vector<int> st;

    void scc(int u) {
        dfn[u] = low[u] = dfn_idx++;
        st.push_back(u);
        in_st[u] = true;

        for (int i = 0; i < sz(g[u]); i++) {
            const int v = g[u][i];
            if (dfn[v] == -1) {
                scc(v);
                low[u] = min(low[u], low[v]);
            }
            else if (in_st[v]) {
                low[u] = min(low[u], dfn[v]);
            }
        }

        if (dfn[u] == low[u]) {
            int k;
            do {
                k = st.back(); st.pop_back();
                in_st[k] = false;
                belong[k] = scc_cnt;
            } while (k != u);
            scc_cnt++;
        }
    }

    void tarjan() {
        st.clear();
        fill(dfn, dfn + V, -1);
        fill(low, low + V, INF);
        dfn_idx = 0;
        scc_cnt = 0;
        for (int v = 0; v < V; v++) {
            if (dfn[v] == -1) {
                scc(v);
            }
        }
    }

************************
模板驗證
************************

`poj2186 <../../poj/p2186.html>`_
