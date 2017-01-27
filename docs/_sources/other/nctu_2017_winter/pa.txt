#####################################
[hdu5437] A. Alisha’s Party
#####################################

.. sidebar:: Tags

    - ``tag_priority_queue``

.. contents:: TOC
    :depth: 2

*************************************************************
`題目 <https://vjudge.net/contest/147988>`_
*************************************************************

多筆測資。K 個朋友要來，給定他們的名字與他們會到的時間 B[i]，但到達的人會先停在門外等待開門。每個朋友有其權重 v[i]。門會在開 M 次，在時間 t[i] 讓在門外的 v[i] 最高的 n[i] 個人進來。當權重相同時，先來的先進門。最後有 Q 筆詢問，每筆詢問為一整數 q ，請輸出第 q 個進門的人是誰。

************************
Specification
************************

::

    1 <= K <= 150,000
    0 <= M <= K
    1 <= q <= 100
    1 <= v[i] <= 10^8

************************
分析
************************

.. note:: 模擬

priority_queue 搞一搞就可以了，不過這題最坑的是有許多小陷阱：

1. 給定 t[i], n[i] 時，不保證 t[i] 是遞增的，所以得先排個序
2. n[i] 可能大於正在門外等的人數

最後我的方法就是用 priority_queue 找出所有人的進門順序，再來處理 query

************************
AC Code
************************

.. code-block:: cpp
    :linenos:

    #include <iostream>
    #include <cstdio>
    #include <cstdlib>
    #include <vector>
    #include <algorithm>
    #include <functional>
    #include <queue>
    using namespace std;

    typedef pair<int, int> pii;
    #define st first
    #define nd second

    struct S {
        string name;
        int id;
        int val;

        bool operator < (const S& s) const {
            if (val != s.val)
                return val < s.val;
            return id > s.id;
        }
    };

    int N, M, Q;
    vector<S> v;
    vector<S> ans;
    vector<pii> query;

    int main() {
        ios::sync_with_stdio(false);
        cin.tie(0);

        int TC;
        cin >> TC;

        while (TC--) {
            v.clear();
            ans.clear();
            query.clear();

            cin >> N >> M >> Q;

            for (int i = 0; i < N; i++) {
                S inp; inp.id = i;
                cin >> inp.name >> inp.val;
                v.push_back(inp);
            }

            for (int i = 0; i < M; i++) {
                int t, n; cin >> t >> n;
                query.push_back(pii(t, n));
            }
            sort(query.begin(), query.end());

            priority_queue<S> pq;
            int cur = 0;
            for (int i = 0; i < M; i++) {
                int t = query[i].st;
                int n = query[i].nd;

                while (cur < t) {
                    pq.push(v[cur]);
                    cur++;
                }

                while (!pq.empty() && n--) {
                    ans.push_back(pq.top());
                    pq.pop();
                }
            }

            while (cur < N) {
                pq.push(v[cur]);
                cur++;
            }
            while (!pq.empty()) {
                ans.push_back(pq.top());
                pq.pop();
            }

            for (int q = 0; q < Q; q++) {
                int idx; cin >> idx; idx--;
                if (q != 0) cout << " ";
                cout << ans[idx].name;
            }
            cout << endl;
        }

        return 0;
    }
