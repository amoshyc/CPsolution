#####################################
[hdu5444] H. Elven Postman
#####################################

.. sidebar:: Tags

    - ``tag_easy``
    - ``tag_bst``

.. contents:: TOC
    :depth: 2

*************************************************************
`題目 <https://vjudge.net/contest/147988>`_
*************************************************************

在一個 binary tree 中，樹是「正常長」的（root 在下方，leaves 在上方），左邊是西方，右邊是東方。每個節點有編號，最東邊的節點編號為 1，次東邊節點為 2……。現給定一個數列，代表從 root 出發，走到節點 1 路徑上經過的節點編號，然後再走到節點 2，再走到節點 3……直到走完所有節點。請從這個數最重建原 binary tree。多筆詢問，每筆詢問為一個節點編號，請輸出從 root 走到該節點要經過的邊（往左或往右）

************************
Specification
************************

::

    1 <= V <= 10^3

************************
分析
************************

.. note:: 觀察

讓我們將樹倒過來看吧，畢竟大家還是習慣樹長這樣吧。此時 E 在左，W 在右。

自己畫幾個 binary tree，自己走訪一下應該就可發現::

    這個序列根本就是 bst 的中序遍歷啊~

根據題目節點編號的順序，將節點編號視為值，對於任一點 u, 左子節點 lch, 右子節點 rch 必滿足 lch < u < rch。所以這根本是棵二元搜尋樹，而題目給的序列就只是中序遍歷的結果。

所以::

    5 3 2 4 1 6 8 7 分解成 5, 3 2 4 1, 6 8 7
    5 是 root,
    3 2 4 1 是左子樹的節點 (E),
    6 8 7 是右子樹的節點 (W)

3, 6 分別是 5 之後，第一個小於 5 的樹與第一個大於 5 的數。
之後分別對左子樹、右子樹遞迴，最終整棵樹就建起來了。

-----

實作上小心沒有左子樹或沒有右子樹之類的情況。

************************
AC Code
************************

.. code-block:: cpp
    :linenos:

    #include <bits/stdc++.h>
    using namespace std;

    const int MAX_N = 1000;
    int N;
    int A[MAX_N];

    string res[MAX_N];

    // [l, r) 為子樹的區間
    void dfs(int idx, int l, int r, string path) {
        res[A[idx]] = path;
        if (r - l == 0) {
            return;
        }

        int m1 = -1;
        int m2 = -1;
        for (int i = l; i < r; i++) {
            if (m1 == -1 && A[i] < A[idx]) {
                m1 = i;
            }
            if (m2 == -1 && A[i] > A[idx]) {
                m2 = i;
            }
        }

        if (m1 != -1 && m2 != -1) {
            dfs(m1, m1 + 1, m2, path + 'E');
            dfs(m2, m2 + 1, r, path + 'W');
            return;
        }
        if (m1 != -1 && m2 == -1) {
            dfs(m1, m1 + 1, r, path + 'E');
        }
        if (m1 == -1 && m2 != -1) {
            dfs(m2, m2 + 1, r, path + 'W');
        }
    }

    int main() {
        ios::sync_with_stdio(false);
        cin.tie(0);

        int TC;
        cin >> TC;

        while (TC--) {
            cin >> N;

            for (int i = 0; i < N; i++) {
                cin >> A[i]; A[i]--;
            }

            dfs(0, 1, N, "");

            int Q; cin >> Q;
            while (Q--) {
                int q; cin >> q; q--;
                cout << res[q] << endl;
            }
        }

        return 0;
    }
