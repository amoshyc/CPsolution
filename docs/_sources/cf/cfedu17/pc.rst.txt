###################################################
[cfedu17] C. Two strings
###################################################

.. sidebar:: Tags

    - ``tag_two_pointers``
    - ``tag_dp``
    - ``tag_binary_search``

.. contents:: TOC
    :depth: 2

******************************************************
`題目 <http://codeforces.com/contest/762/problem/C>`_
******************************************************

給定兩個字串 a, b。請移除 b 的一個區間（shortest consecutive characters) ，使得剩下的字串 b' 構成 a 的一個子序列（subsequence）。另外要最小化該區間的長度，請輸出移除區間後的 b。

※ 有可能把 b 的所有字元移除也有可能不移除任何字元。

************************
Specification
************************

::

    |a|, |b| <= 10^5

************************
分析
************************

.. note:: 「判定某個 b' 是不是 a 的子序列」可以用預建表解決

這題想了一個小時左右，終於被我想出來了，一開始還以為要用到 suffix array。

-----------

我們先定義變數與記號:

.. code-block:: none

    N = |a|
    M = |b|
    b sub a = b 是 a 的子序列
    b'[s, t] = 移除區間 [s, t] 的 b

----------------------

使用一個找尋區間的常見手法：

    枚舉區間開頭 s，二分搜（或爬行法）找出區間結尾 t

我們可以二分搜或爬行法是因為有以下單調性:

.. code-block:: none

    b'[s, t] sub a 可以保證 for all x >= t, b'[s, x] sub a

這個應該想想就知道了。但要使用這個方法，我們得解決如何快速判斷「 某個 b' 是否是 a 的子序列」，這可以透過預建表的方法解決，定義::

    pref[i] = min j such that b[0, i] sub a[0, j] (-1 if not exist)
    suff[i] = max j such that b[i, M-1] sub a[j, N-1] (-1 if not exist)

口語化地來講就是::

    b 的各個 prefix 是哪個 a 的 prefix 的子序列。
    b 的各個 suffix 是哪個 a 的 suffix 的子序列。

這個表可以使用爬行法來建，細節看程式碼吧。建好表後，判定「 b'[s, t] 是否是 a 的子序列」就可以透過檢查 pref[s - 1], suff[t + 1] 所對應該的這兩個區間是否檢查解決，讓我們直接圖解吧。

.. image:: http://i.imgur.com/j2EdQ5G.png

左圖是不重疊，即 b'[s, t] sub a；右圖是重疊了，所以 b'[s, t] 不是 a 的子序列。
因為 pref[i], suff[i] 可能是 -1，所以有一些情況要特判，整理起來就是:

.. code-block:: cpp

    bool C(int s, int t) { // [s, t] is removed
        if (s == 0 && t == M - 1) // 整個字串移除，空字串為任何子串的子序列
            return true;
        if (s == 0) // 區間頭在最前，只須看 suff
            return suff[t + 1] != -1;
        if (t == M - 1) // 區間尾在最後，只須看 pref
            return pref[s - 1] != -1;

        // 判區間有沒有重疊，記得檢查 -1，有 -1 就代表不是 a 的子序列
        return (pref[s - 1] != -1 &&
                suff[t + 1] != -1 &&
                pref[s - 1] < suff[t + 1]);
    }

到此，所有問題就解決了，可以開始寫程式碼了。

-----------------

這題存在另一種解法，對區間長度二分搜，這也是有單調性存在的，實作程式碼可以參考 `Abdelrahman_Ramadan_` 的 `程式碼 <http://codeforces.com/contest/762/standings/friends/true>`_

************************
AC Code
************************

.. code-block:: cpp
    :linenos:

    #include <bits/stdc++.h>
    using namespace std;

    #define sz(x) (int(x.size()))

    const int MAX_LEN = 100000;
    const int INF = 0x3f3f3f3f;
    int N, M;
    string a, b;
    int pref[MAX_LEN];
    int suff[MAX_LEN];
    // pref[i] = min j such that b[0, i] sub a[0, j] (-1 if not exist)
    // suff[i] = max j such that b[i, M-1] sub a[j, N-1] (-1 if not exist)

    void init() {
        fill(pref, pref + M, -1);
        fill(suff, suff + M, -1);

        int idx = 0;
        for (int i = 0; i < M; i++) {
            while (idx < N && a[idx] != b[i]) {
                idx++;
            }
            if (idx == N) break;
            pref[i] = idx;
            idx++;
        }

        idx = N - 1;
        for (int i = M - 1; i >= 0; i--) {
            while (idx >= 0 && a[idx] != b[i]) {
                idx--;
            }
            if (idx < 0) break;
            suff[i] = idx;
            idx--;
        }

        // cout << a << endl;
        // cout << b << endl;
        // for (int i = 0; i < M; i++) cout << pref[i] << " ";
        // cout << endl;
        // for (int i = 0; i < M; i++) cout << suff[i] << " ";
        // cout << endl;
    }

    bool C(int s, int t) { // [s, t] is removed
        if (s == 0 && t == M - 1) return true;
        if (s == 0) return suff[t + 1] != -1;
        if (t == M - 1) return pref[s - 1] != -1;
        return (pref[s - 1] != -1 &&
                suff[t + 1] != -1 &&
                pref[s - 1] < suff[t + 1]);
    }

    void solve() {
        if (pref[M - 1] != -1) { // no removal
            cout << b << endl;
            return;
        }

        auto search_tail = [&](int s) -> int {
            // binary search on t
            // 0 0 1 1 1
            int lb = s, ub = M - 1;
            if (C(s, lb)) return lb;
            if (!C(s, ub)) return -1;
            while (ub - lb > 1) {
                int m = (lb + ub) / 2;
                if (C(s, m)) ub = m;
                else lb = m;
            }
            return ub;
        };

        int mn_len = INF;
        int ss, tt;
        for (int s = 0; s < M; s++) { // [s, t]
            int t = search_tail(s);
            if (t != -1 && (t - s + 1) < mn_len) {
                mn_len = (t - s + 1);
                ss = s;
                tt = t;
            }
        }

        // cout << "ss: " << ss << endl;
        // cout << "tt: " << tt << endl;
        string res = b.substr(0, ss) + b.substr(tt + 1);
        if (sz(res) == 0)
            cout << "-" << endl;
        else
            cout << res << endl;
    }

    int main() {
        ios::sync_with_stdio(false);
        cin.tie(0);

        cin >> a >> b;
        N = sz(a);
        M = sz(b);

        init();
        solve();

        return 0;
    }
