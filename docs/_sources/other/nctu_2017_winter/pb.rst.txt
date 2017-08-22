#####################################
[hdu5438] B. Ponds
#####################################

.. sidebar:: Tags

    - ``tag_bfs``
    - ``tag_dfs``

.. contents:: TOC
    :depth: 2

*************************************************************
`題目 <https://vjudge.net/contest/147988>`_
*************************************************************

給定一個無向圖，每個節有其權重。不斷將圖中 degree 為 1 的點移除，直到沒有。此時圖應該被初為一個或多個連通單元，請問所有節點數為奇數的連通單元，總權重和是多少？

************************
Specification
************************

::

    1 <= V <= 10^4
    1 <= E <= 10^5

************************
分析
************************

.. note:: 模擬

直接用類似 bfs 的手法（類似 topological sort）模擬移除的過程，把 degree 為 1 的點一個一個拔掉，最後用 dfs 算出各 component 的 size 與權重和，接題意加一加就可以了。

************************
AC Code
************************

.. code-block:: cpp
    :linenos:

    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <queue>
    #include <algorithm>
    using namespace std;

    typedef long long ll;
    #define sz(x) (int(x.size()))

    const int MAX_N = 10000;
    int N, M;
    ll val[MAX_N];
    int deg[MAX_N];
    bool vis[MAX_N];
    vector<int> g[MAX_N];

    void init() {
        fill(deg, deg + N, 0);
        fill(val, val + N, 0);
        fill(vis, vis + N, false);
        for (int i = 0; i < N; i++)
            g[i].clear();
    }

    void remove() {
        queue<int> q;
        for (int u = 0; u < N; u++) {
            deg[u] = sz(g[u]);
            if (deg[u] == 1) {
                q.push(u);
            }
        }

        while (!q.empty()) {
            int u = q.front(); q.pop();
            deg[u] = 0;

            // printf("r %d\n", u);

            for (int i = 0; i < sz(g[u]); i++) {
                int v = g[u][i];
                deg[v]--;

                if (deg[v] == 1) {
                    q.push(v);
                }
            }
        }
    }

    int sz_comp;
    ll total_v;

    void dfs(int u) {
        // printf("%d, %lld\n", u, val[u]);

        sz_comp++;
        total_v += val[u];
        vis[u] = true;
        for (int i = 0; i < sz(g[u]); i++) {
            int v = g[u][i];
            if (!vis[v] && deg[v] > 0) {
                dfs(v);
            }
        }
    }

    ll solve() {
        remove();

        ll ans = 0;
        for (int u = 0; u < N; u++) {
            sz_comp = 0;
            total_v = 0;
            if (!vis[u] && deg[u] > 0) {
                dfs(u);

                // printf("%d: ", u);
                // printf("%d, %lld\n", sz_comp, total_v);
                // puts("");

                if (sz_comp % 2 == 1) {
                    ans += total_v;
                }
            }
        }

        return ans;
    }

    int main() {
        int TC;
        scanf("%d", &TC);
        while (TC--) {
            scanf("%d %d", &N, &M);
            init();
            for (int i = 0; i < N; i++) {
                scanf("%lld", &val[i]);
            }
            for (int i = 0; i < M; i++) {
                int u, v; scanf("%d %d", &u, &v); u--; v--;
                g[u].push_back(v);
                g[v].push_back(u);
            }
            printf("%lld\n", solve());
        }

        return 0;
    }
