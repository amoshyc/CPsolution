###################################################
[cfedu17] B. USB vs. PS/2
###################################################

.. sidebar:: Tags

    - ``tag_easy``
    - ``tag_merge_sort``

.. contents:: TOC
    :depth: 2

******************************************************
`題目 <http://codeforces.com/contest/762/problem/B>`_
******************************************************

************************
Specification
************************

************************
分析
************************

.. note:: 水題

考 merge，話說這題應該直接用 C++ 內建的 merge 解決，不用自己寫

************************
AC Code
************************

.. code-block:: cpp
    :linenos:

    #include <bits/stdc++.h>
    using namespace std;

    #define sz(x) (int(x.size()))
    typedef long long ll;

    ll A, B, C;
    ll INF = 1e18;
    vector<ll> va, vb;

    void solve() {
        sort(va.begin(), va.end());
        sort(vb.begin(), vb.end());

        ll num = 0, cost = 0;
        ll n_a = min(A, ll(va.size()));
        ll n_b = min(B, ll(vb.size()));
        num += n_a + n_b;
        cost += accumulate(va.begin(), va.begin() + n_a, 0ll);
        cost += accumulate(vb.begin(), vb.begin() + n_b, 0ll);

        va.push_back(INF);
        vb.push_back(INF);
        int idx_a = n_a;
        int idx_b = n_b;
        while (C-- && (idx_a + 1 != sz(va) || idx_b + 1 != sz(vb))) {
            if (va[idx_a] < vb[idx_b]) {
                num++;
                cost += va[idx_a];
                idx_a++;
            }
            else {
                num++;
                cost += vb[idx_b];
                idx_b++;
            }
        }

        printf("%lld %lld\n", num, cost);
    }

    int main() {
        scanf("%lld %lld %lld", &A, &B, &C);
        int M;
        scanf("%d", &M);
        while (M--) {
            ll cost; char inp[10];
            scanf("%lld %s", &cost, inp);
            if (inp[0] == 'U') {
                va.push_back(cost);
            }
            else {
                vb.push_back(cost);
            }
        }

        solve();

        return 0;
    }
