###################################################
偏序集與 Dilworth
###################################################

.. sidebar:: Tags

    - ``tag_combination``
    - ``tag_template``

.. contents:: TOC
    :depth: 2

************************
原理
************************

偏序集的定義請自看 `維基`_。Dilworth 與其對偶定理適用於嚴格偏序，也適用於非嚴格偏序。

1. Dilworth：最小正鏈覆蓋 = 最長反鏈長度
2. 對偶定理：最小反鏈覆蓋 = 最長正鏈長度

詳細證明請自行查資料，不過從 Hasse Diagram 就可以感覺出來~

.. _維基: https://zh.wikipedia.org/wiki/偏序關係

競賽中我只遇過二維的情況，所以以下都只以二維為例，程式碼不適用高維。
如果有誰遇到，發個 issue 或送個 email 給我。

==============
往右走或往下走
==============

R x C 的格子中 N 個點 (r[i], c[i])。
定義一次旅行為從任一個點出發，只能 **往右走或往下走** 。
請問：
1. 經過最多點的那次旅行是經過了多少個點？
2. 最少須要旅行幾次才能走完所有點？

即非嚴格偏序集 :math:`P = (S, \le)`

.. math:: 

    S &= \{ (r_i, c_i | 0 \le i < N \} \\
    \le &= (r_i \le r_j \land c_i \le c_j)

題目所求分別為
1. 最長正鏈長度，即為最小反鏈覆蓋。
2. 最小正鏈覆蓋，即為最長反鏈長度。

-----------------------
最長正鏈長度
-----------------------

1. 將點排序，r 由小到大，相同時 c **由小到大**
2. 在 c 上求最長 **單調遞增** 子序列，長度即為所求

-----------------------
最長反鏈長度
-----------------------

1. 將點排序，r 由小到大，相同時 c **由小到大**
2. 在 c 上求最長 **嚴格遞減** 子序列，長度即為所求

-------------------

假設點分別為::

    A(0, 1), B(0, 3), C(1, 0), D(2, 1)

圖解如下。

待補

=====================
只能往斜右下走
=====================

如果題目改成 **只能往斜右下走，不能向右或向下**，則演算法改成

------------------------
最長正鏈長度
------------------------

1. 將點排序，r 由小到大，相同時 c **由大到小**
2. 在 c 上求最長 **嚴格遞增** 子序列，長度即為所求

------------------------
最長反鏈長度
------------------------

1. 將點排序，r 由小到大，相同時 c **由大到小**
2. 在 c 上求最長 **單調遞減** 子序列，長度即為所求

------------

c 由小到大是為了防止誤判，例如

待補

************************
程式碼（dp)
************************

最長 **嚴格遞增** 子序列、最長 **嚴格遞減** 子序列:

.. code-block:: cpp
    :linenos:

    const int INF = 0x3f3f3f3f;
    int lsis(const vector<int>& v) {
        vector<int> dp(sz(v), INF);
        for (auto x : v) {
            *lower_bound(dp.begin(), dp.end(), x) = x;
        }
        return find(dp.begin(), dp.end(), INF) - dp.begin();
    }
    int lsds(const vector<int>& v) {
        vector<int> rv = v;
        reverse(rv.begin(), rv.end());
        return lsis(rv);
    }

最長 **單調遞增** 子序列、最長 **單調遞減** 子序列:

.. code-block:: cpp
    :linenos: 

    const int INF = 0x3f3f3f3f;
    int lmis(const vector<int>& v) {
        vector<int> dp(sz(v), INF);
        for (auto x : v) {
            *upper_bound(dp.begin(), dp.end(), x) = x;
        }
        return find(dp.begin(), dp.end(), INF) - dp.begin();
    }
    int lmds(const vector<int>& v) {
        vector<int> rv = v;
        reverse(rv.begin(), rv.end());
        return lmis(rv);
    }

************************
程式碼（multiset)
************************

************************
模板驗證
************************

`uva11368 <https://gist.github.com/anonymous/de03aaf422ba3bff1dbf7e862653b83a>`_
`ncpc2016 初賽 B <https://gist.github.com/anonymous/4778427ec2ff50714569298820bc8865>`_
