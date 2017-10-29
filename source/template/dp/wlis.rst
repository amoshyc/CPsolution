###################################################
WLIS 最大帶權遞增子序列
###################################################

.. sidebar:: Tags

    - ``tag_dp``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

給定長度為 N 整數序列 A 與每一項對應的權重 W。請問 A 的所有遞增序列中（Increasing Sequence)，最大的對應權重和是多少？

※ LIS 是這題的一個特例，即 W 每一項都是 1 的情況。

例如::

    N = 3
    A = [1 1 1]
    W = [2 4 3]

    N = 6
    A = [3 2 5 1 6 7]
    W = [3 2 1 6 1 3]

    N = 5
    A = [2 2 0 5 9]
    W = [8 0 7 2 9]

答案分別是::

    4
    10
    19


************************
程式碼 1: dp + seg
************************

使用線段樹優化原先 LIS O(N^2) 的做法，首先 dp 為::

    dp[i] = max total weight of IS ending at A[i]
    dp[i] = max(dp[j] | 0 <= j < i, A[i] > A[j]) + W[i]

找 max 的部份我們想優化成 O(lgN)，目前 O(N) 是因為我們不知道哪個 dp[j] 對應的 A[j] 小於 A[i]。我們在 dp 陣列外，增加一個容器存 **目前求出來的所有 dp 值（與他們對應的 A 值）**，假設這個容器可以在 O(lgN) 的時間回答你： **容器中，A 值小於某個詢問值的所有 dp 值中，最大的是誰**。那演算法就簡單了，虛擬碼可以寫成::

    # dp: dp 陣列
    # C: 容器

    # base case
    dp[0] = A[0]
    C.add((dp[0], A[0]))

    # 轉移
    for i in range(1, N):
        mx = C.query(A[i]) # ie. max(dp[j] | 0 <= j < i, A[i] > A[j])
        dp[i] = max(W[i], mx + W[i])
        C.add((dp[i], A[i]))

    res = max(dp)

假設這個容器用一維陣列 S 來實現， **陣列的 index 是 A 值，存的內容是 dp 值** ，那查詢就變成一個區間最大值的詢問。S 初使化為 -INF，另外因為我們會用 A 的值域當 index，值域可能很大，S 陣列可能開不出來，但 A 最多只有 N 個不同值，所以我們先對 A 離散化。

例如::

    C.add(dp=4, A=3)

變成::

    if 4 > S[3]: # 要比目前的值大，才更新
        S[3] = 4

而查詢::

    C.query(4)

變成::

    max(S[0:4]) # 半開區間

而動態的區間極值查詢，當然是用線段樹~

.. code-block:: cpp
    :linenos:

    int wlis(vector<int> A, vector<int> W) {
        int N = sz(A);

        vector<int> S = A;
        sort(S.begin(), S.end());
        for (int& a : A) {
            a = lower_bound(S.begin(), S.end(), a) - S.begin();
        }

        vector<int> dp(N, 0);

        SegTree<int> seg;
        seg.init(N, -INF);
        
        dp[0] = W[0];
        seg.update(A[0], dp[0], 0, 0, seg.NN);

        for (int i = 1; i < N; i++) {
            int mx = seg.query(0, A[i], 0, 0, seg.NN);
            dp[i] = max(W[i], mx + W[i]);

            int cur = seg.query(A[i], A[i] + 1, 0, 0, seg.NN);
            if (dp[i] > cur) {
                seg.update(A[i], dp[i], 0, 0, seg.NN);
            }
        }

        return *max_element(dp.begin(), dp.end());
    }

使用單點更新的線段樹模板，我就不給了，自行找詢。
注意第 22~25 行，要記得判斷新的值有比目前值大才更新，要不然範測第三筆會錯掉。
邏輯腦那本書把這種 dp + seg 的題型歸類為「使用資料結構加速 dp」。第一次遇到的這種題型的人可能得想一下，可以參我的同題型的題解，例如 poj 3171。

************************
程式碼 2: dp + map
************************

聽說有別種解法，坐等同學 AC 後解釋給我聽。

************************
模板驗證
************************

`學校競程的題目 <https://gist.github.com/anonymous/4436b0afdfac0e1dff2fabdc4ca61a07>`_